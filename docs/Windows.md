# SAMBA域控加WINDOWS管理

> 平台

```txt
主机系统：网卡IP设置192.168.6.1。
客户机：IP设置192.168.6.3。
```

> 安装

    先配置用光盘做源来安装development tools，也可以虚机设置两块网卡，一块桥接笔记本网卡连互联网直接yum。 注意，用双网卡的话，SAMBA产生的DNS文件会使用连互联网的网卡，要自己修改。
    cd /etc/yum.repos.d/
    mv CentOS-Base.repo base.repo.bak
    mv CentOS-Sources.repo source.repo.bak
    vi media.repo
    [DVD]
    name=CentOS Media
    baseurl=file:///media/cdrom
    gpgcheck=0
    enabled=1

:wq保存退出。然后建立光盘挂载目录：

    mkdir /media/cdrom
    mount /dev/cdrom /media/cdrom
    mount: /dev/sr0 写保护，将以只读方式挂载

安装development tools：

    yum -y groupinstall "development tools"

安装一下samba需要的组件，libacl-devel最重要，BIND9也一起用光盘里的RPM包安装。

    yum -y install libacl-devel libblkid-devel gnutls-devel readline-devel python-devel bind autoconf gdb rsyslog-gssapi cyrus-sasl-gssapi

把SAMBA源码包下载，然后通过SecureFX或者winscp上传到/tmp目录下，编译之前需要先从光盘安装python-devel.否则configure报错。 注意不能用光盘自带的RPM包安装samba，虽然有samba-dc的包，安装后测试没有samba-tools来配置域控

rpm -ivh /media/cdrom/Packages/python-devel-2.7.5-16.el7.x86_64.rpm

    tar -zxvf samba-latest.tar.gz

    解压之后先要运行autogen-waf.sh，否则./configure提示没有./buildtools/bin/waf文件或者目录:
    ./configure && make && make install

> 配置

修改主机名为DC1，把FQDN完全域名写上，好处是等下提升为域控免输域名了。

    vi /etc/hostname
    DC1.contoso.com

关机给虚拟机做快照，配置出错随时回滚，节约编译安装时间

重新开机登录系统，配置域控

```conf
cd /usr/local/samba/bin
[root@DC1 bin]# ./samba-tool domain provision
Realm [CONTOSO.COM]:
 Domain [CONTOSO]:
 Server Role (dc, member, standalone) [dc]:
 DNS backend (SAMBA_INTERNAL, BIND9_FLATFILE, BIND9_DLZ, NONE) [SAMBA_INTERNAL]: BIND9_FLATFILE  #这里选的BIND9，也可以默认用自带的DNS
Administrator password: 输入域控管理员密码，密码一定要复杂，大小写字母+数字，如Ab123456&
Retype password: 再输入一遍Ab123456&
Looking up IPv4 addresses
More than one IPv4 address found. Using 192.168.6.3
Looki 6 address will be assigned
Setting up secrets.ldb
Setting up the registry
Setting up the privileges database
Setting up idmap db
Setting up sam.ldb partitions and settings
Setting up sam.ldb rootDSE
Pre-loading the Samba 4 and AD schema
Adding DomainDN: DC=contoso,DC=com
Adding configuration container
Setting up sam.ldb schema
Setting up sam.ldb configuration data
Setting up display specifiers
Modifying display specifiers
Adding users container
Modifying users container
Adding computers container
Modifying computers container
Setting up sam.ldb data
Setting up well known security principals
Setting up sam.ldb users and groups
Setting up self join
Adding DNS accounts
Creating CN=MicrosoftDNS,CN=System,DC=contoso,DC=com
rndc: neither /etc/rndc.conf nor /etc/rndc.key was found
rndc: neither /etc/rndc.conf nor /etc/rndc.key was found
See /usr/local/samba/private/named.conf for an example configuration include file for BIND
and /usr/local/samba/private/named.txt for further documentation required for secure DNS updates
Setting up sam.ldb rootDSE marking as synchronized
Fixing provision GUIDs
A Kerberos configuration suitable for Samba 4 has been generated at /usr/local/samba/private/krb5.conf
O nce the above files are installed, your Samba4 server will be ready to use
Server Role:           active directory domain controller
Hostname:              DC1
NetBIOS Domain:        CONTOSO
DNS Domain:            contoso.com
DOMAIN SID:            S-1-5-21-3366851103-1622988557-2824442447
```

见到DOMAIN SID才算配置成功，启动samba

/usr/local/samba/sbin/samba

> 测试

    /usr/local/samba/bin/smbclient -L localhost -U%

    /usr/local/samba/bin/smbclient //localhost/netlogon -Uadministrator
    Enter administrator's password:
    Domain=[CONTOSO] OS=[Unix] Server=[Samba 4.1.13]
    smb: \> q

> DNS

检查bind

rpm -qa|grep bind

在/etc/named.conf文件中可以看到bind9的目录是/var/named，进入该目录：

复制一份named.localhost作为contoso.com.zone，然后修改，作为contoso.com的正向解析文件。

cp named.localhost contoso.com.zone
vim contoso.com.zone
$TTL 1D
@       IN SOA  @ contoso.com. (
                                      0       ; serial
                                      1D      ; refresh
                                      1H      ; retry
                                      1W      ; expire
                                      3H )    ; minimum
        IN NS   DC1.contoso.com.
@       IN A    192.168.6.3
DC1     IN A    192.168.6.3

以上就是修改后的，双网卡的虚机，IP可能是另外一个的，要修改。
再把samba产生的DNS文件的后面部分复制到/usr/local/samba/private/dns。但是不要复制gc._msdcs这一条，我测试报错，删除了能启动bind

复制下面部分

```conf

79aef472-c658-49c0-a2b4-3988bc00338a._msdcs     IN CNAME        DC1
;
; global catalog servers
_gc._tcp                IN SRV 0 100 3268       DC1
_gc._tcp.Default-First-Site-Name._sites IN SRV 0 100 3268       DC1
_ldap._tcp.gc._msdcs    IN SRV 0 100 3268       DC1
_ldap._tcp.Default-First-Site-Name._sites.gc._msdcs     IN SRV 0 100 3268 DC1
;
; ldap servers
_ldap._tcp              IN SRV 0 100 389        DC1
_ldap._tcp.dc._msdcs    IN SRV 0 100 389        DC1
_ldap._tcp.pdc._msdcs   IN SRV 0 100 389        DC1
_ldap._tcp.8b2afba7-4d3a-4b88-8b45-381cf145c623.domains._msdcs          IN SRV 0 100 389 DC1
_ldap._tcp.Default-First-Site-Name._sites               IN SRV 0 100 389 DC1
_ldap._tcp.Default-First-Site-Name._sites.dc._msdcs     IN SRV 0 100 389 DC1
;
; krb5 servers
_kerberos._tcp          IN SRV 0 100 88         DC1
_kerberos._tcp.dc._msdcs        IN SRV 0 100 88 DC1
_kerberos._tcp.Default-First-Site-Name._sites   IN SRV 0 100 88 DC1
_kerberos._tcp.Default-First-Site-Name._sites.dc._msdcs IN SRV 0 100 88 DC1
_kerberos._udp          IN SRV 0 100 88         DC1
; MIT kpasswd likes to lookup this name on password change
_kerberos-master._tcp           IN SRV 0 100 88         DC1
_kerberos-master._udp           IN SRV 0 100 88         DC1
;
; kpasswd
_kpasswd._tcp           IN SRV 0 100 464        DC1
_kpasswd._udp           IN SRV 0 100 464        DC1
;
; heimdal 'find realm for host' hack
_kerberos               IN TXT  CONTOSO.COM

```

然后粘贴到/var/named/contoso.com.zone修改过的后面。具体操作中，可以在SecureCRT里克隆会话，进到目录，打开文件，拖选要复制的，然后切换到原来的会话点右键就粘贴上了，然后按ESC，：wq保存退出。

打开/etc/named.rfc1912.zones， 后面添加如下字段，增加正向解析区域

vim /etc/named.rfc1912.zones

    zone "contoso.com" IN {
        type master;
        file "contoso.com.zone";
        allow-update { none; };
    };

启动BIND服务，如果报错，需要检查etc/named.rfc1912.zones和contoso.com.zone文件配置

systemctl start named.service

systemctl status named.service

测试解析，需要host命令。默认未安装。

host -t SRV _ldap._tcp.contoso.com.

注意：提示没有此命令时，重新挂载安装host

mount /dev/cdrom /media/cdrom

yum -y install bind-utils

测试

```sh

host -t SRV _ldap._tcp.contoso.com

_ldap._tcp.contoso.com has SRV record 0 100 389 DC1.contoso.com.

host -t SRV _kerberos._udp.contoso.com

_kerberos._udp.contoso.com has SRV record 0 100 88 DC1.contoso.com.

host -t A dc1.contoso.com

dc1.contoso.com has address 192.168.6.3

然后再开WIN7虚拟机，配置同网段IP如192.168.6.5， DNS配置192.168.6.3。 先用PING测试能ping通域名，如果不通尝试检查或清除IPTABLES防火墙规则：

然后WIN7测试加域，加入域以后在WIN7里可以下载安装远程管理工具包来管理域控了。

samba ubuntu域控服务器架设

wget http://www.samba.org/samba/ftp/stable/samba-4.1.4.tar.gz

apt-get install build-essential libacl1-dev libattr1-dev libblkid-dev libgnutls-dev libreadline-dev python-dev python-dnspython gdb pkg-config libpopt-dev libldap2-dev

    tar -zxvf samba-4.1.4.tar.gz
    cd samba-4.1.1/
    ./configure –enable-debug
    make && make install

设置一个新的domain

/usr/local/samba/bin/samba-tool domain provision

cd /etc/init.d/

创建一个脚本文件

    vim /etc/init.d/samba4
    chmod 755 /etc/init.d/samba4
    update-rc.d samba4 defaults
    shutdown -r now

测试SMB域的功能，看看是否所有需要的功能分区活动所需的份额正在工作

/usr/local/samba/bin/smbclient -L localhost -U%

验证主DNS服务器是您的本地接口的IP地址，更改IP并添加您的DNS名称服务器

    vim /etc/resolv.conf
    vim /etc/network/interfaces
    ping johny.local
    netstat -ln
    host -t SRV _kerberos._tcp.johny.local.
    host -t SRV _kerberos._udp.johny.local.
    host -t A lab1.johny.local

创建你的符号链接到适当的库

    ln -s /usr/local/samba/lib/libnss_winbind.so.2 /lib/libnss_winbind.so
    ln -s /lib/libnss_winbind.so /lib/libnss_winbind.so.2
    ln -s /usr/local/samba/lib/libnss_winbind.so.2 /lib64/libnss_winbind.so
    ln -s /lib64/libnss_winbind.so /lib64/libnss_winbind.so.2

首先从/etc/passwd和/etc/group/然后从Windows NT服务器解析用户和组信息。要为用户和组查找设置winbindd，再加上域控制器的身份验证，在nsswitch中使用如下设置。配置文件⇒passwd文件winbind⇒组:文件winbind指示系统使用nss winbind图书馆当搜索用户或组(允许用户和组条目从winbindd可见守护进程)

vim /etc/nsswitch.conf

winbindd守护进程所需的库将会在下一次系统重新启动时自动进入ldconfig缓存，但是如果您手动执行，它会更快(而且您不需要重新启动)。这使libnss_winbind可以访问winbindd，并报告动态链接加载器所使用的当前搜索路径。使用grep过滤ldconfig命令的输出，这样我们就可以看到这个库确实被动态链接加载器识别了。确认库已加载。

ldconfig -v | grep winbind

/usr/local/samba/bin/wbinfo -p # 测试如果winbind可以ping通

/usr/local/samba/bin/wbinfo -u # 测试Winbind的能够提供用户列表

getent passwd # 返回密码文件


id Administrator # 标识反馈相关的用户信息

apt-get install acl # 安装acl包

vim /etc/fstab  # 将acl设置为所需的分区以启用ACL

mount # 查看挂载的分区

mv /usr/local/samba/etc/smb.conf smb.conf.bak # 备份默认配置文件

mkdir /home/it /home/hr /home/commercial # 为smb.conf创建共享文件夹

chmod 770 it/ hr/ commercial/ # 操作完执行下面有误，重新系统再尝试

getfacl

setfacl -m g:it:rwx /home/it # 设置文件夹acl，修改组“it”对文件夹/ home / it具有完全权限

    mkdir /home/recycle
    chmod 777 /home/recycle/
    smbstatus # 随时检查哪些用户和哪台机器正在访问服务器上的共享
    setfacl -m u:pauly:r-x /home/hr/ # 设置用户johny只读取特定文件夹的权限
    getfacl /home/hr

## smb.conf

```

```conf
# Global parameters the file is divided into sections
[global] the first is always the ”[global]” section, which contains the general server options		
workgroup = JOHNY the name of the workgroup
realm = JOHNY.LOCAL
netbios name = LAB5 server name
server role = active directory domain controller	the server was configured as a AD and DC		
dns forwarder = 8.8.8.8			
vfs objects = recycle, full_audit	VFS module records selected client operations to the system log		
recycle:keeptree = yes	Specifies whether the directory structure should be preserved or whether the files in a directory that is being deleted should be kept separately in the repository		
recycle:versions = yes	If this option is True, two files with the same name that are deleted will both be kept in the repository. Newer deleted versions of a file will be called “Copy #x of filename”.		
recycle:repository = /home/recycle	Path of the directory where deleted files should be moved		
recycle:exclude = *.tmp, *.log, ~*.*, *.bak, *.iso	List of files that should not be put into the repository when deleted, but deleted in the normal way. Wildcards such as * and ? are supported.		
recycle::exclude_dir = tmp, cache	List of directories whose files should not be put into the repository when deleted, but deleted in the normal way. Wildcards such as * and ? are supported		
full_audit:facility = local5	all this audit logs are going to system log(/var/log/syslog)		
full_audit:priority = notice			
full_audit:prefix = %u	%I	%s	adds additional useful information to audit log file.%u – User; %I – User IP address; %S – Server share name
full_audit:sucess = open, write, rename, rmdir, mkdir, chmod, chown			
full_audit:failure = none	do not give a list of VFS operations that should be recorded if they failed		
log level = 5			
			
[netlogon]	indicates the name of sharing,describes a shared resource (known as a “share”).		
path = /usr/local/samba/var/locks/sysvol/johny.local/scripts	share folder path		
read only = No			
			
[sysvol]			
path = /usr/local/samba/var/locks/sysvol			
read only = No			
[public]	name of the share folder		
path = /home/public	path of the share folder		
comment = Pasta Publicco	description of the share folder		
browseable = yes	This controls whether this share is seen in the list of available shares in a net view and in the browse list.		
create mask = 0777	This setting tells samba what permissions to mask against the DOS/Windows assigned permissions for a new file when it is created from a Windows/DOS client		
writeable = yes		

```

[it]

    path = /home/it
    comment = Pasta IT
    browseable = yes
    create mask = 0770
    writeable = yes
    directory mask = 0770
    force directory mode = 0770
    map acl inherit = yes

[hr]

    path = /home/hr
    comment = Pasta HR
    browseable = yes
    create mask = 0770
    writeable = yes
    directory mask = 0770
    force directory mode = 0770
    map acl inherit = yes

[commercial]

    path = /home/commercial
    comment = Pasta Commercial
    browseable = yes
    create mask = 0770
    writeable = yes
    directory mask = 0770
    force directory mode = 0770
    map acl inherit = yes

## 基本概念

| 名字 | 功能 | 描述 |
| :------: | :------: | :------: |
| winbindd | winbindd提供的服务称为“winbind”，可以用于从Windows NT服务器解析用户和组信息。该服务还可以通过一个相关的PAM模块提供身份验证服务。名称服务开关守护进程，用于解析NT服务器的名称。 | 名称服务开关允许从不同的数据库服务(如NIS或DNS)获得用户和系统信息。确切的行为可以通过/etc/nsswitch.conf进行配置。conf文件。用户和组被分配，因为它们被解析为Samba系统管理员指定的一系列用户和组id。 |

> 重要的配置文件路径和服务命令

| Description | configuring file path | Start up Script |
| :------: | :------: | :------: |
| Network | /etc/hosts; /etc/resolv.conf; /etc/network/interfaces | /etc/init.d/networking restart |
| Samba | /usr/local/samba/etc/smb.conf;/etc/inite.d/samba4 | /etc/init.d/samba4 restart |
| Winbind | /etc/nsswitch.conf |  |

> 用户管理命令

User and Group manager	command
Add a user test	/usr/local/samba/bin/samba-tools user add test
Add group level1	/usr/local/samba/bin/samba-tool group add level1
Add user1 to group level1	/usr/local/samba/bin/samba-tool group addmembers “level1” user1
Remove user1 from group level1	/usr/local/samba/bin/samba-tool group removemembers “level1” user1
List the current group	/usr/local/samba/bin/samba-tool group list
List the current user	/usr/local/samba/bin/samba-tool user list
Add a user test	/usr/local/samba/bin/samba-tool user create test
Delete a user test	/usr/local/samba/bin/samba-tool user delete test
Delete a group test	/usr/local/samba/bin/samba-tool group delete test
Add a user test to ou yanling	/usr/local/samba/bin/samba-tools user add test –userou=OU=yanling
Add a user group to ou yanling	/usr/local/samba/bin/samba-tools group add grouptest –groupou=OU=yanling

ACL permission	command
Set user “pauly” have read-only permission to the other department's share folder	setfacl -R -m u:pauly:r-x /home/hr/
Set group “pauly” have read-only permission to the other department's share folder	setfacl -m g:pauly:r-x /home/hr/
Removed user “pauly” access permission share folder	setfacl -x u:pauly /home/hr/
Removed group “pauly” access permission share folder	setfacl -x g:pauly /home/hr/
Set user “pauly” be default user of share folder	setfacl -d –set u:pauly:rx /home/hr/
Set group “pauly” be default group of share folder	setfacl -d –set g:pauly:rx /home/hr/
Removed all group and user access permission of share folder	setfacl -b /home/hr/
Add “Domain Users” to ACL	setfacl -m g:Domain\ Users:rwx /home/public/


> create the samba4 start script

```

vim /etc/init.d/samba4
#! /bin/sh
  ### BEGIN INIT INFO
  # Provides: samba
  # Required-Start: $network $local_fs $remote_fs
  # Required-Stop: $network $local_fs $remote_fs
  # Default-Start: 2 3 4 5
  # Default-Stop: 0 1 6
  # Short-Description: start Samba daemons
  ### END INIT INFO
  #
  # Start/stops the Samba daemon (samba).
  # Adapted from the Samba 3 packages.
  #
  SAMBAPID=/var/run/samba/samba.pid
  # clear conflicting settings from the environment
  unset TMPDIR
  # See if the daemon and the config file are there
  test -x /usr/local/samba/sbin -a -r /usr/local/samba/etc/ || exit 0
  . /lib/lsb/init-functions
  case "$1" in
  start)
  log_daemon_msg "Starting Samba 4 daemon" "samba"
  if ! start-stop-daemon --start --quiet --oknodo --exec /usr/local/samba/sbin/samba -- -D; then
  log_end_msg 1
  exit 1
  fi
  log_end_msg 0
  ;;
  stop)
  log_daemon_msg "Stopping Samba 4 daemon" "samba"
  start-stop-daemon --stop --quiet --name samba $SAMBAPID
  # Wait a little and remove stale PID file
  sleep 1
  if [ -f $SAMBAPID ] && ! ps h `cat $SAMBAPID` > /dev/null
  then
  # Stale PID file (samba was succesfully stopped),
  # remove it (should be removed by samba itself IMHO.)
  rm -f $SAMBAPID
  fi
  log_end_msg 0
  ;;
  restart|force-reload)
  $0 stop
  sleep 1
  $0 start
  ;;
  *)
  echo "Usage: /etc/init.d/samba {start|stop|restart|force-reload}"
  exit 1
  ;;
  esac
  exit 0

  ```

chmod 755 /etc/init.d/samba4

update-rc.d samba4 defaults

下载远程管理工具：

http://www.microsoft.com/zh-cn/download/details.aspx?id=7887

***
***
***

<h1 style="text-align:center">SAMBA网页管理界面安装</h1>

安装

Yum –y install samba*
Yum –y install xinetd*

http://mirror.centos.org/centos/7/os/x86_64/Packages/xinetd-2.3.15-13.el7.x86_64.rpm

配置目录

vi /etc/xinetd.d/swat

测试

http://sambaserver:901