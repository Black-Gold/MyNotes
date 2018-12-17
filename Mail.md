<h1 style="text-align:center">PostFix Dovecot邮件服务器+后台管理</h1>

## 官方postfix安装和配置

> PGP key下载地址：https://repo.dovecot.org/DOVECOT-REPO-GPG

## debian安装和配置

> Jessie (8.0)安装
```
创建/etc/apt/trusted.gpg.d/dovecot.gpg
curl https://repo.dovecot.org/DOVECOT-REPO-GPG | gpg --import
gpg --export ED409DA1 > /etc/apt/trusted.gpg.d/dovecot.gpg
创建/etc/apt/sources.list.d/dovecot.list 想要使用https，确保安装apt-transport-https
deb https://repo.dovecot.org/ce-2.3-latest/debian/jessie jessie main
```
>  Stretch (9.0)安装
```
创建/etc/apt/trusted.gpg.d/dovecot.gpg
curl https://repo.dovecot.org/DOVECOT-REPO-GPG | gpg --import
gpg --export ED409DA1 > /etc/apt/trusted.gpg.d/dovecot.gpg
创建/etc/apt/sources.list.d/dovecot.list 想要使用https，确保安装apt-transport-https
deb https://repo.dovecot.org/ce-2.3-latest/debian/stretch stretch main
```
>安装需要的包名
```
dovecot-core
dovecot-dbg
dovecot-dev
dovecot-gssapi
dovecot-imapd
dovecot-imaptest
dovecot-ldap
dovecot-lmtpd
dovecot-lua
dovecot-lucene
dovecot-managesieved
dovecot-mysql
dovecot-pgsql
dovecot-pigeonhole-dbg
dovecot-pop3d
dovecot-sieve-dev
dovecot-sieve
dovecot-solr
dovecot-sqlite
dovecot-submissiond
```
***
##  CentOS 6&7安装和配置
> 创建/etc/yum.repos.d/dovecot.repo
```
[dovecot-2.3-latest]
name=Dovecot 2.3 CentOS $releasever - $basearch
baseurl=http://repo.dovecot.org/ce-2.3-latest/centos/$releasever/RPMS/$basearch
gpgkey=https://repo.dovecot.org/DOVECOT-REPO-GPG
gpgcheck=1
enabled=1
```
>新安装需要的包名
```
dovecot
dovecot-debuginfo
dovecot-devel
dovecot-imaptest
dovecot-imaptest-debuginfo
dovecot-lua
dovecot-mysql
dovecot-pgsql
dovecot-pigeonhole
dovecot-pigeonhole-debuginfo
dovecot-pigeonhole-devel
```
***
##  Ubuntu安装和配置
> Trusty (14.04 LTS)安装
```
创建/etc/apt/trusted.gpg.d/dovecot.gpg
curl https://repo.dovecot.org/DOVECOT-REPO-GPG | gpg --import
gpg --export ED409DA1 > /etc/apt/trusted.gpg.d/dovecot.gpg
创建/etc/apt/sources.list.d/dovecot.list 想要使用https，确保安装apt-transport-https
deb https://repo.dovecot.org/ce-2.3-latest/ubuntu/trusty trusty main
```
> Xenial (16.04 LTS)安装
```
Create /etc/apt/trusted.gpg.d/dovecot.gpg
curl https://repo.dovecot.org/DOVECOT-REPO-GPG | gpg --import
gpg --export ED409DA1 > /etc/apt/trusted.gpg.d/dovecot.gpg
Create /etc/apt/sources.list.d/dovecot.list. If you want to use
https, make sure you have installed apt-transport-https.
deb https://repo.dovecot.org/ce-2.3-latest/ubuntu/xenial xenial main
```
>新安装需要的包名
```
dovecot-core
dovecot-dbg
dovecot-dev
dovecot-gssapi
dovecot-imapd
dovecot-imaptest
dovecot-ldap
dovecot-lmtpd
dovecot-lua
dovecot-lucene
dovecot-managesieved
dovecot-mysql
dovecot-pgsql
dovecot-pigeonhole-dbg
dovecot-pop3d
dovecot-sieve-dev
dovecot-sieve
dovecot-solr
dovecot-sqlite
dovecot-submissiond
```
***
##  source tarballs文件编译安装
```
下载地址：https://dovecot.org/download.html
https://dovecot.org/releases/2.3/dovecot-2.3.0.tar.gz
tar -zxvf dovecot-2.3.0
./configure
make && make install
自定义安装位置和库的路径
CPPFLAGS="-I/opt/openssl/include" LDFLAGS="-L/opt/openssl/lib" ./configure
```
> 从git克隆编译安装
```
git clone https://github.com/dovecot/core.git dovecot
接着进入目录运行./autogen.sh生成配置脚本和其他需要的文件
```
> 需要提前安装的包
```
autoconf
automake
libtool
pkg-config
gettext
pandoc (不是必须安装，除非要使用: PANDOC=false ./configure)
GNU make
```
```
建议添加--enable-maintainer-mode参数到configure脚本
./autogen.sh
./configure --enable-maintainer-mode
make && make install
```
##  其他
```
非官方补丁地址：https://dovecot.org/patches/
额外工具地址：https://dovecot.org/tools/
GitHub仓库地址：https://github.com/dovecot/
```
* Optional Configure Options
--help
gives a full list of available options
--help=short
just lists the options added by the particular package (= Dovecot)
Options are usually listed as --with-something or --enable-something. If you want to disable them, do it as --without-something or --disable-something. There are many default options that come from autoconf, automake or libtool. They are explained elsewhere.

Here is a list of options that Dovecot adds. You should not usually have to change these, but they are described here just for completeness:

--enable-devel-checks
Enables some extra sanity checks. This is mainly useful for developers. It does quite a lot of unnecessary work but should catch some programming mistakes more quickly.
--enable-asserts
Enable assertion checks, enabled by default. Disabling them may slightly save some CPU, but if there are bugs they can cause more problems since they are not detected as early.
--without-shared-libs
Link Dovecot binaries with static libraries instead of dynamic libraries.
--disable-largefile
Specifies if we use 32bit or 64bit file offsets in 32bit CPUs. 64bit is the default if the system supports it (Linux and Solaris do). Dropping this to 32bit may save some memory, but it prevents accessing any file larger than 2 GB.
--with-mem-align=BYTES
Specifies memory alignment used for memory allocations. It is needed with many non-x86 systems and it should speed up x86 systems too. Default is 8, to make sure 64bit memory accessing works.
--with-ioloop=IOLOOP
Specifies what I/O loop method to use. Possibilities are select, poll, epoll and kqueue. The default is to use the best method available on your system.

--with-notify=NOTIFY
Specifies what file system notification method to use. Possibilities are dnotify, inotify (both on Linux), kqueue (FreeBSD) and none. The default is to use the best method available on your system. See Notify method above for more information.

--with-storages=FORMATS
Specifies what mailbox formats to support. Note: Independent of this option, the formats raw and shared will be always built.

--with-solr
Build with Solr full text search support
--with-zlib
Build with zlib compression support (default if detected)
--with-bzlib
Build with bzip2 compression support (default if detected)
SQL Driver Options
SQL drivers are typically used only for authentication, but they may be used as a lib-dict backend too, which can be used by plugins for different purposes.

--with-sql-drivers
Build with specified SQL drivers. Defaults to all that were found with autodetection.
--with-pgsql
Build with PostgreSQL support (requires pgsql-devel, libpq-dev or similar package)
--with-mysql
Build with MySQL support (requires mysql-devel, libmysqlclient15-dev or similar package)
--with-sqlite
Build with SQLite3 driver support (requires sqlite-devel, libsqlite3-dev or similar package)
Authentication Backend Options
The basic backends are built if the system is detected to support them:

--with-shadow
Build with shadow password support

--with-pam
Build with PAM support

--with-nss
Build with NSS support

--with-sia
Build with Tru64 SIA support
--with-bsdauth
Build with BSD authentication support (if supported by your OS)

Some backends require extra libraries and are not necessarily wanted, so they are built only if specifically enabled:

--with-sql
Build with generic SQL support (drivers are enabled separately)
--with-ldap
Build with LDAP support (requires openldap-devel, libldap2-dev or similar package)
--with-gssapi
Build with GSSAPI authentication support (requires krb5-devel, libkrb5-dev or similar package)
--with-vpopmail
Build with vpopmail support (requires vpopmail sources or a devel package)
It is also possible to build these as plugins by giving e.g. --with-sql=plugin.

Dynamic IMAP and POP3 Modules
The mail_plugins setting lists all plugins that Dovecot is supposed to load from the mail_plugin_dir directory at program start. These plugins can do anything they want. They are only expected to contain the <plugin name>_init and <plugin name>_deinit functions which are called at startup and at exit.

* The plugin filename is prefixed with a number which specifies the order in which the plugins are loaded. This is important if one plugin depends on another.
***
##  相关问题和配置

> 出现Maildir权限问题解决方法
```
检查selinux状态并关闭
方法一：/usr/sbin/sestatus -v
方法二：
getenforce 0 #  设置selinux为permissive模式()临时关闭，不用重启，重启后失效
永久生效方法：
vi /etc/selinux/config
SELINUX=enforcing更改为SELINUX=disabled
```
> 建立防火墙规则
```
vi /usr/local/sbin/startmailf
#!/bin/bash
#create: 
#Creater: 
iptables -I INPUT -p tcp --dport 110 -j ACCEPT     #允许邮件收信110端口，POP3的IP入口
iptables -I INPUT -p udp --dport 110 -j ACCEPT     #允许邮件收信110端口，POP3的带数据包入口
iptables -I INPUT -p tcp --dport 25 -j ACCEPT      #允许邮件发信25端口，IP入口
iptables -I INPUT -p udp --dport 25 -j ACCEPT      #允许邮件发信25端口，带数据包入口
iptables -I INPUT -p udp --dport 143 -j ACCEPT     #允许邮件收信143端口，IMAP的IP入口
iptables -I INPUT -p tcp --dport 143 -j ACCEPT     #允许邮件收信143端口，IMAP的带数据包入口
iptables -I INPUT -p udp --dport 22 -j ACCEPT      #开放PUTTY和WINSCP 22号端口，方便管理员进入
iptables -I INPUT -p tcp --dport 22 -j ACCEPT      #开放PUTTY和WINSCP 22号端口，方便管理员进入
iptables -I INPUT -p tcp --dport 53 -j ACCEPT      #开放DNS查询端口，这样能解析到邮件服务器
iptables -I INPUT -p udp --dport 53 -j ACCEPT      #开放DNS查询端口，这样能解析到邮件服务器
service network restart                            #重启网络
service postfix restart                            #重启postfix
service dovecot restart                            #重启dovecot
service saslauthd restart                          #重启ASAL授权
#也可以再加456，1925，587等邮件SSL/TLS端口，80网页管理端口，根据你的邮件服务器而定。
```
> 将防火墙规则开机自启动
```
vi /etc/rc.local
#!/bin/sh
#
# This script will be executed *after* all the other init scripts.
# You can put your own initialization stuff in here if you don't
# want to do the full Sys V style init stuff.
touch /var/lock/subsys/local
sh /usr/local/sbin/startmailf  #把创建的防火墙规则加在这一行
```
***
> CentOS系统一般都自带sendmail的，如果不需要，可以使用以下命令删除
rpm -e sendmail
***
1. 检查默认邮件传输代理(MTA)
* alternatives --display mta
默认是sendmail，更改MTA为postfix
/usr/sbin/alternatives --set mta /usr/sbin/sendmail.postfix
2. 配置Postfix
* Postfix配置文件主要是两个master.cf和main.cf，这里我们只需要配置main.cf，即/etc/postfix/main.cf
> 更改以下内容
```
myhostname = mail.1a-centosserver.com
mydomain = 1a-centosserver.com
myorigin = $mydomain
inet_interfaces = all
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
mynetworks = 192.168.13.0/24, 127.0.0.0/8
relay_domains =
home_mailbox = Maildir/
```
> 配置文件解释：
```
mydomain:
mydomain参数是指email服务器的域名，请确保为正式域名（如centos.bz）
myhostname:
myhostname参数是指系统的主机名称（如我的服务器主机名称是mail.centos.bz）
myorigin:
myorigin参数指定本地发送邮件中来源和传递显示的域名。在我们的例子中，mydomain是centos.bz，也是我的域名。
对于下面的一行，我们的邮件地址是user@centos.bz而不是user@mail.centos.bz。
myorigin = $mydomain
mynetworks:
mynetworks参数指定受信任SMTP的列表，具体的说，受信任的SMTP客户端允许通过Postfix传递邮件。
mydestination:
mydestination参数指定哪些邮件地址允许在本地发送邮件。这是一组被信任的允许通过服务器发送或传递邮件的IP地址。用户试图通过发送从此处未列出的IP地址的原始服务器的邮件将被拒绝。
inet_interfaces:
inet_interfaces参数设置网络接口以便Postfix能接收到邮件。
relay_domains:
该参数是系统传递邮件的目的域名列表。如果留空，我们保证了我们的邮件服务器不对不信任的网络开放。
home_mailbox:
该参数设置邮箱路径与用户目录有关，也可以指定要使用的邮箱风格
```
> 测试Postfix SMTP连接
```
service postfix status
检测默认smtp端口25是否已经监听
netstat -an | grep 25
设置postfix开机启动
chkconfig postfix on
开始测试postfix是否工作正常
telnet localhost 25
所有邮件发送到/home/user/Maildir/new
```
> 邮件别名设置
```
配置文件位置：/etc/alises里，格式：
[Format]
Receiving Account or other aliaes:recipient A,recipient B,.....
例一:root:root,james	#表示root用户的邮件对于用户james和root都可以接收到
例二：class2017:james,anna,mark	#设置群邮件，表示设置了群邮件名class2017，当发送一封邮件到class2017@qq.com时，用户james、anna、mark都可以收到邮件
使用“newaliases”命令激活邮件别名功能
当编辑/etc/aliases文件后，必须执行newaliases命令更新别名数据库
POP/IMAP设置
为了使用户可以在本地机器下载邮件，必须在邮件服务器安装并设置POP或IMAP，docecot是适用在Linux邮件系统POP/IMAP邮件服务器之一，支持maildir和mbox格式
```
***
## 安装和配置dovecot
* 首先检测dovecot软件包是否已经安装
rpm -qa dovecot
用yum命令安装：yum -y install dovecot
Dovecot配置文件在/etc/dovecot.conf，需要改动的地方如下
```
vim /etc/dovecot.conf
# Protocols we want to be serving: imap imaps pop3 pop3s
# If you only want to use dovecot-auth, you can set this to "none".
protocols = imap imaps pop3 pop3s
删除protocols行前面的“#”来激活imap imaps pop3和pop3s服务
```
```
启动dovecot:
service dovecot start	#centos6.x
systemctl start dovecot #centos7.x
chkconfig dovecot on #centos6.x
systemctl enable dovecot  #centos7.x
使用telnet命令检测110（pop3）和143（imap）端口
telnet 127.0.0.1 110
telnet 127.0.0.1 143
ps -ef | grep dovecot #	显示dovecot守护进程
```
> 配置使用Dovecot SASL进行SMTP验证

1、编辑 /etc/dovecot.conf，确保auth default区域有如下设置值：
```
auth default {
  socket listen {
    client {
  path = /var/spool/postfix/private/auth
  mode = 0660
  user = postfix
  group = postfix
    }
  }
  mechanisms = plain login
}
```
２、编辑/etc/postfix/main.cf，增加如下代码启用sasl认证。
```
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_recipient_restrictions =  permit_mynetworks,
    permit_sasl_authenticated, reject_unauth_destination
broken_sasl_auth_clients = yes
```
３、重启服务   
service postfix restart   
service dovecot restart   
现在你可以使用邮件客户端代理软件和系统用户及密码来连接我们的Dovecot服务器了。  

# **详细配置文档说明**

* /etc/postfix/目录有两个主配置文件main.cf和master.cf，我们主要设置main.cf，master.cf基本保持默认就行。
postfix有100多个参数，但我们可以根据自己的要求去改少量的参数，其它保持默认就可。
```
修改任何文件之前必须把原文件备份。
cp /etc/postfix/main.cf /etc/postfix/main.cf.bak

vi /etc/postfix/main.cf
alias_database = hash:/etc/aliases       #用户别名数据文件，默认，可根据自己的要求可改成SQL,LDAP,VIRTUAL
alias_maps = hash:/etc/aliases           #用户别名配置文件，默认，可根据自己的要求可改成SQL,LDAP,VIRTUAL
broken_sasl_auth_clients = yes           #设定 SASL 支持非标准 E-mail Client 的认证动作，自己添加
command_directory = /usr/sbin            #命令目录      默认    
config_directory = /etc/postfix          #配置文件目录 默认
daemon_directory = /usr/libexec/postfix  #守护进程目录 默认
data_directory = /var/lib/postfix        #数据目录  默认
debug_peer_level = 2                     #排错管理级别 默认
home_mailbox = Maildir/                  #主目录存储格式，你也可以设成另外的格式mbox，
html_directory = no                      #是否支持网页格式管理
inet_interfaces = all                    #参数指定postfix系统监听的网络接口
inet_protocols = all                     #只支持IPv4协议，还是IPv4 IPv6都支持,还是主机，ip
mail_owner = postfix                     #邮件及邮件队列的所有者
#mail_spool_directory = /vbox            #指定UNIX风格的邮箱保存的目录。默认设置取决于系统类型。
mailq_path = /usr/bin/mailq.postfix      #指定查看邮件队列命令MAILQ的路径
manpage_directory = /usr/share/man       #Postfix的在线手册页的位置
mydestination = $myhostname,... #参数指定postfix接收邮件时收件人的域名，即您的postfix系统要接收到哪个域名的邮件
mydomain = davistest.com         #参数指定您的域名，默认情况下，postfix将myhostname的第一部分删除而作为mydomain的值
myhostname = mail.davistest.com  #参数指定运行postfix邮件系统的主机的主机名，默认情况下，其值被设定为本地机器名
mynetworks = 192.168.1.0/24 #参数指定你所在的网络的网络地址，postfix系统根据其值来区别用户是远程的还是本地的
myorigin = $mydomain                     #参数用来指明发件人所在的域名
newaliases_path = /usr/bin/newaliases.postfix #刷新别名命令newaliases路径
queue_directory = /var/spool/postfix      #指定队列的位置
readme_directory = /usr/share/doc/postfix-2.6.6/README_FILES #帮助文档位置
sample_directory = /usr/share/doc/postfix-2.6.6/samples   #模板文件位置
sendmail_path = /usr/sbin/sendmail.postfix #发送邮件命令的位置
setgid_group = postdrop  #这个组是提交邮件和对列管理命令
message_size_limit = 10240000  #限制发送人单个邮件的大小，单位是Byte
smtpd_recipient_restrictions = permit_mynetworks,  
permit_sasl_authenticated,reject_unauth_destination  #信件收件的限制
smtpd_sasl_security_options = noanonymous  #拒绝匿名登入
smtpd_sasl_auth_enable = yes    #Postfix SMTP server支援SASL验证
smtpd_sasl_path = private/auth  #postfix与dovecot通讯程序文件路径
smtpd_sasl_type = dovecot       #指定sasl验证程序
unknown_local_recipient_reject_code = 550
#拒绝不存在的本地帐户／「不明使用者」(unknown user)
#如果收信地址的人名部份，在任何对照表、别名表、系统帐户都查不出来，
#这个人会被视为「不明使用者」(unknown user)，系统会拒收。
#如果希望蒐集这类信件，使用下列设定，并指定集中收集的信箱
#以上的mydestination一定要有本机主机名，要不然会收不到信

伟大的postfix也自带很多反垃圾设置，可根据实际情况以及自己的需要进行调整。
smtpd_helo_required = yes #握手要求
smtpd_delay_reject = yes  #中继要求
smtpd_client_restrictions =
check_client_access hash:/etc/postfix/client_access #客户端访问控制列表
smtpd_helo_restrictions=
reject_invalid_hostname,check_helo_access hash:/usr/local/etc/postfix/helo_access#主机和握手控制列表
smtpd_sender_restrictions =                 #客户端发送人控制列表
 reject_non_fqdn_sender,                    #拒收没有注册域名的发送者
 reject_unknown_sender_domain,              #拒收不知发件人域的发送者
 check_sender_access hash:/usr/local/etc/postfix/sender_access  #发件人控制列表
smtpd_recipient_restrictions=               #收件控制
 permit_mynetworks,                         #允许我的网络定义的客户端收信
 permit_sasl_authenticated,                 #允许simple authenticated security layer 授权的客户端。
 reject_non_fqdn_hostname,                  #拒收没有注册的主机  
 reject_non_fqdn_sender,                    #拒收没有注册的发送人
 reject_non_fqdn_recipient,                 #拒收没注册的接收者
 reject_unauth_destination,                 #拒收没授权的目的主机
 reject_invalid_hostname,                   #拒收不可用的主机
 #还有很多，就不写了 
 smtpd_data_restrictions = reject_unauth_pipelining         #拒绝没有授权的通道
    header_checks = regexp:/postfix/head_checks      #绝对路径邮件头（主题，附件名等）控制列表
    body_checks = regexp:/etc/postfix/body_checks    ##绝对路径邮件内容控制列表
  创建刚才指定的文件就不一一写出来了，照这个方法就行了
 touch /usr/local/etc/postfix/head_checks
 touch /usr/local/etc/postfix/body_checks
 postmap /usr/local/etc/postfix/head_checks
 postmap /usr/local/etc/postfix/body_checks

还是例举一下吧，要不然想了解的都不知文档怎么写。
假如/etc/postfix/mail.cf 下有如下反垃圾设置： 
smtpd_client_restrictions = check_client_access hash:/etc/postfix/access_client
smtpd_helo_restrictions = check_client_access hash:/etc/postfix/helo_client
smtpd_sender_restrictions = check_client_access hash:/etc/postfix/sender_client
smtpd_recipient_restrictions = check_client_access hash:/etc/postfix/recipient_client
header_checks = regexp:/etc/postfix/header_check
body_checks = regexp:/etc/postfix/body_check

那么我们在access_client/helo_client/sender_client/recipient_client 文件的语法如下：
列表分三项，第一项是规则内容，第二项是对满足规则时所采取的行动，第三项是返回给客户端的信息
客户端域名/ip   550/REJECT/OK.. 你不可以通过我们的服务器发邮件！access_client
客户端域名/ip   550/REJECT/OK.. 禁止davis通过本服务器发送邮件  sender_client
客户端域名/ip   550/REJECT/OK.. 服务器拒收来自davis的邮件.     recipient_client
客户端IP/域名   550/REJECT/OK.. 服务器拒绝你的连接.             helo_client
不详说了，知道它是怎么工作的就行了，现在有很多软件,比如Mailscanner..功能强大，易管理。
 配完之后，你可以用postconf -n main.cf去查看没有注释的内容。
 我们用pam去调用连接系统用户为邮件用户验证saslauthd,当然也可以用LDAP,MYSQL等，或SYRUS的shadow.后面我们会讲到。
# Directory in which to place saslauthd's listening socket, pid file, and so
# on.  This directory must already exist.
SOCKETDIR=/var/run/saslauthd
# Mechanism to use when checking passwords.  Run "saslauthd -v" to get a list
# of which mechanism your installation was compiled with the ablity to use.
MECH=pam                                                                         #这行MECH设成pam
# Options sent to the saslauthd. If the MECH is other than "pam" uncomment the next line.
# DAEMONOPTS=--user saslauth
# Additional flags to pass to saslauthd on the command line.  See saslauthd(8)
# for the list of accepted flags.
FLAGS=
我们用的是dovecot给客户端验证收信，所以我们先确定pam下的dovecot授权如下。
Vi /etc/pam.d/dovecot   
#%PAM-1.0
auth       required     pam_nologin.so
auth       include      password-auth
account    include      password-auth
session    include      password-auth
接着配置dovecot的收信
```
```
protocols = imap pop3 lmtp               #开放IMAP POP3 等协议 路径/etc/dovecot/dovecot.conf 
auth_mechanisms = plain login            #授权机制，如果我们用POSTADMIN网页管理，也可用什么MD5等等
                                             #路径/etc/dovecot/conf.d/10-auth.conf
disable_plaintext_auth = no              #允许用明文认证登陆，路径/etc/dovecot/conf.d/10-auth.conf
listen = *                               #我们只监听IPV4端口。路径/etc/dovecot/dovecot.conf 
mail_location = maildir:/vbox/%u/Maildir #邮件目录，%u表示用户名。路径/etc/dovecot/conf.d/10-mail.conf
mbox_write_locks = fcntl                 #邮箱锁的方法。路径/etc/dovecot/conf.d/10-mail.conf
passdb {                                 #帐户授权 
args = dovecot                           #就是/etc/pam.d/下的dovecot文件
driver = pam                             #帐户授权数据提供商 
}                                        # 路径/etc/dovecot/conf.d/auth-system.conf.ext
service auth {                           #服务授权
unix_listener /var/spool/postfix/private/auth {  #UNIX监听的授权文件
  group = postfix                        #指定监听授权文件的属组为POSTFIX
  mode = 0666                            #指定监听授权文件组权限为666
  user = postfix                         #指定监听授权文件的属主为postfix
  }                                      #路径 /etc/dovecot/conf.d/auth-system.conf.ext
}
userdb {                                 #用户数据
driver = passwd                          #共用系统密码驱动
}
 ```
> 建立邮件存放目录邮件备份目录
```
mkdir /vbox                              #建立邮件MAILDIR目录
chmod 755 /vbox                          #给权限
mkdir /backup                            #删除用户的备份目录
chmod 755 /backup                        #备份目录权限
mkdir /etc/postfix/Mail_Group            #邮件组软链目录，以后做管理面版脚本要用
```
> 启动服务  
service postfix start    
service dovecot start   
service saslauthd start   
> 设置服务开机自动启动
chkconfig postfix on
chkconfig dovecot on
chkconfig saslauthd on
##  脚本编写
> 管理员控制面版
```
vi /usr/local/sbin/main
#!/bin/bash
#create: 15-Nov-2014
#Creater: Davis Dai
echo "1 新建邮件帐号"            #在屏幕上输出菜单 
echo "2 查看邮件帐号"
echo "3 删除邮件帐号"
echo "4 新建邮件组"
echo "5 删除邮件组"
echo "6 查看邮件组"
echo "7 添加用户到组"
echo "8 从组里删除用户"
echo "0 退出邮件管理"
order=""                          #设参数为你要先择的菜单
echo "请选择任务代码："
read order
echo $order >/tmp/order.txt
if [ "$order" == "1" ];then         #设条件根据你的选择跳到相应的管理模块
    mailacounts;
elif [ "$order" == "2" ];then
    viewusers;
elif [ "$order" == "3" ];then
    dusers;
elif [ "$order" == "4" ];then
    groupacounts;
elif [ "$order" == "5" ];then
    dgroup;
elif [ "$order" == "6" ];then
  viewgroup;
elif [ "$order" == "7" ];then
    AMFG;
elif [ "$order" == "8" ];then
    RMFG;
elif [ "$order" == "0" ];then
   exit 0;
else                                           #如果你的选择不是以上菜单，提示错误并返回控制面版。
    echo "任务代码错误，请重新输入！"
    main
fi
```
> 新建邮件模块
```
vi /usr/local/sbin/mailacounts
home="/vbox/"                                           #没置目录参数
useraccount=""                                          #设置用户参数       
echo "请给新用户名字："            
read useraccount
echo $useraccount > /tmp/user.txt                       #给用户参数赋值
userpass=""                                             #设置密码参数
echo "请输入用户密码："
read userpass
echo $userpass > /tmp/pass.txt                          #给密码参数赋值
useradd -d $home/$useraccount $useraccount              #添加用户并指定邮件主目录
echo $userpass |passwd --stdin $useraccount             #设置邮箱密码
mkdir -p $home/$useraccount/Maildir/{cur,new,tmp}       #建邮件目录和文件
mkdir -p $home/$useraccount/Maildir/.Drafts/{cur,new,tmp}
mkdir -p $home/$useraccount/Maildir/.Sent/{cur,new,tmp}
mkdir -p $home/$useraccount/Maildir/.Trash/{cur,new,tmp}
chown -R $useraccount $home/$useraccount/*    #设置属主－R是做个递归，也就是说文件夹下所以有子文件夹和文件
chmod 700 -R $home/$useraccount/*                       #设置属主有完全权限，其它人没任何权限。
main                                                    #回到管理员控制面版
```
> 新建邮件组模块
```
vim /usr/local/sbin/groupacounts
#/bin/bash
#Create: 14-Nov-2014
#Creater: Davis Dai
ls /etc/postfix/Mail_Group/                          #显示现有组
home="/vbox/"                                        #没置目录参数      
groupaccount=""                                      #设置邮件组参数  
echo "请输入新邮件组名："
read groupaccount
echo $groupaccount > /tmp/group.txt                  #给组邮箱参数赋值
grouppass=""                                         #设置密码参数
echo "请输入新邮件组密码："
read grouppass
echo $grouppass > /tmp/pass.txt                      #给密码参数赋值
useradd -d $home/$groupaccount $groupaccount         #添加组邮箱并指定邮件主目录
echo $grouppass |passwd --stdin $groupaccount        #设置组邮箱密码
mkdir -p $home/$groupraccount/Maildir/{cur,new,tmp}  #建组邮件目录和文件
mkdir -p $home/$groupaccount/Maildir/.Drafts/{cur,new,tmp}
mkdir -p $home/$groupaccount/Maildir/.Sent/{cur,new,tmp}
mkdir -p $home/$groupaccount/Maildir/.Trash/{cur,new,tmp}
chown -R $groupaccount $home/$groupaccount/*    #设置属主权限－R是做个递归，也就是说文件夹下所以有子文件夹和文件
chmod 700 -R $home/$groupaccount/*                   #设置属主有完全权限，其它人没任何权限。
touch $home/$groupaccount/.forward                   #建组邮件名单表
chown root $home/$groupaccount/.forward              #设置权限给名单表
chmod 755 $home/$groupaccount/.forward 
ln -s $home/$groupaccount/.forward /etc/postfix/Mail_Group/$groupaccount #做一个软连结，便于查找
main                                                 #回到管理员控制面版
```
> 添加邮箱到指定邮箱组模块
```
vim /usr/local/sbin/AMFG
#!/bin/bash
#Create Date: 15-Nov-2014
#Creater: Davis Dai
ls /etc/postfix/Mail_Group
Groupname=""
echo "请输入你要加入的组全称："
read Groupname
echo $Groupname >/tmp/Groupname.txt
cat -n /vbox/$Groupname/.forward
usermail=""
echo "请输入你要加组的用户邮件地址："
read usermail
echo $usermail >/tmp/usermail.txt
echo $usermail >> /vbox/$Groupname/.forward
main
```
> 删除邮箱帐号模块
```
vim /usr/local/sbin/dusers
#!/bin/bash
#create: 15-Nov-2014
#Creater: Davis Dai
ls /vbox/
deluser=""
echo "请键入你要删除的用户："
read deluser
echo $deluser > /tmp/deluser.txt
cp -R -u -i /vbox/$deluser /backup/
userdel $deluser
rm -rf /vbox/$deluser
main
删除邮箱组模块

vim /usr/local/sbin/dgroup
#!/bin/bash
#create: 15-Nov-2014
#Creater: Davis Dai
ls /etc/postfix/Mail_Group/
degroup=""
echo "请键入你要删除的邮件组："
read degroup
echo $degroup > /tmp/degroup.txt
cp -R -u -i /vbox/$degroup /backup/
userdel $degroup
rm -rf /vbox/$degroup
main
```
> 查找邮箱用户模块
```
vim /usr/local/sbin/viewusers
#!/bin/bash
#create: 15-Nov-2014
#Creater: Davis Dai
ls /vbox/ >/tmp/userview.txt
cat -n /tmp/userview.txt
svuser=""
echo "请输入你要查找的用户名："
read svuser
echo $svuser > /tmp/svuser.txt
if grep -q $svuser /tmp/userview.txt; then
    echo "用户$svuser已经存在!";
else
  echo "用户$svuser不存在!";
fi
grep -n $svuser /tmp/userview.txt
main
```
> 查找用户组模块
```
vim /usr/local/sbin/viewgroup
#!/bin/bash
#create: 15-Nov-2014
#Creater: Davis Dai
ls /etc/postfix/Mail_Group/ >/tmp/groupview.txt
cat -n /tmp/groupview.txt
svgroup=""
echo "请输入你要查找的邮件组："
read svgroup
echo $svuser > /tmp/svgroup.txt
if grep -q $svgroup /tmp/groupview.txt; then
     echo "邮件组$svgroup已经存在!";
else
    echo "邮件组$group不存在!";
fi
grep -n $svgroup /tmp/groupview.txt
main
```
> 从邮箱组里删除用户模块
```
vi /usr/local/sbin/RMFG
#!/bin/bash
#create: 15-Nov-2014
#Creater: Davis Dai
ls /etc/postfix/Mail_Group >/tmp/groupview.txt
cat -n /tmp/groupview.txt
sgroup=""
echo "请键入你要删除的用户属组名称："
read sgroup
echo $scgoup >/tmp/sgroup.txt
cat -n /vbox/$sgroup/.forward
rguser=""
echo "请键入你要删除的用户编号："
read rguser
echo $rguser >/tmp/rguser.txt
sed -i ''$rguser'd' /vbox/$sgroup/.forward #按编号删除
#sed -i “/$rguser/d” /vbox/$sgroup/.forward #按地址删除
main
```
##  管理员手册
> 进入服务器，输入邮件管理命令main,管理所有的任务
[root@mail sbin]# main  
1 新建邮件帐号  
2 查看邮件帐号  
3 删除邮件帐号  
4 新建邮件组 
5 删除邮件组 
6 查看邮件组 
7 添加用户到组  
8 从组里删除用户 
0 退出邮件管理  
请选择任务代码：  

> 新建邮件帐号  
```
[root@mail ~]# main
1 新建邮件帐号
2 查看邮件帐号
3 删除邮件帐号
4 新建邮件组
5 删除邮件组
6 查看邮件组
7 添加用户到组
8 从组里删除用户
0 退出邮件管理
请选择任务代码：
1
请给新用户名字：
john
请输入用户密码：
123456
Changing password for user john.
passwd: all authentication tokens updated successfully.
```
> 客户端测试   
只测试收发信，其它组呀，安全等都可以自行设置

# PostfixAdmin网页管理界面
> 依赖包的安装    
yum install －y mysql-server php php-mysql php-imap php-mbstring

> [PostfixAdmin软件源码包下载网址](https://sourceforge.net/projects/postfixadmin/files/postfixadmin/)
> 下载解压后添加权限
tar -xzvf postfixadmin-2.92.tar.gz  
chmod 777 /postfixadmin/ -R
修改PostfixAdmin网页主配置文件
 vim /postfixadmin/config.inc.php
 $CONF['configured'] = true; 
 $CONF['postfix_admin_url'] = '/postfixadmin';
 $CONF['database_type'] = 'mysqli';
 $CONF['database_host'] = 'localhost';
 $CONF['database_user'] = 'vmail';
 $CONF['database_password'] = 'vmail';
 $CONF['database_name'] = 'vmail';
 $CONF['domain_path'] = 'YES';
 $CONF['domain_in_mailbox'] = 'NO';
 $CONF['encrypt'] = 'dovecot:CRAM-MD5'; 
 $CONF['emailcheck_resolve_domain] = 'NO';
 $CONF['dovecotpw'] = "/usr/bin/doveadm pw";
      
新建PostfixAdmin数据库

启动MySql服务
service mysqld start

进入MySql命令工具，默认的MySql用户root的密码是空的
mysql -u root -p

mysql> CREATE DATABASE vmail;                                   
mysql> GRANT ALL PRIVILEGES ON vmail.* TO 'vmail'@'localhost' IDENTIFIED BY 'vmail';
mysql> flush privileges;     
mysql> \q         
touch /postfixadmin/DATABASE_MYSQL.TXT
mysql -uvmail -p vmail < /postfixadmin/DATABASE_MYSQL.TXT
如果显示ACCESS DENIY, mysql -u vmail -p vmail 
mysql> set password = password ('newpassword')


配置PostfixAdmin网页管理应用

安装网页程序apache, 默认是已安装的，所以这你可以省了，你可以先查看一下，是否有安装，再决定。
rpm -qa|grep httpd      #查看是否安装
yum install httpd -y    #安装
新建网页索引文件
vim /etc/httpd/conf.d/postfixadmin.conf
Alias /postfixadmin /postfixadmin 
启动Apache服务
service httpd start
POSTFIX的虚拟用户MYSQL设置和sasl设置

建立邮件目录和管理用户

useradd -u 1000 -d /vmail -s /sbin/nologin vmail
mkdir /vmail
chmod 770 /vmail/
chown vmail:vmail /vmail/
vim /etc/postfix/main.cf  加入如下设置。

######################Virtual Settings##########################
virtual_mailbox_domains = proxy:mysql:/etc/postfix/mysql_virtual_domains_maps.cf
virtual_alias_maps = proxy:mysql:/etc/postfix/mysql_virtual_alias_maps.cf
virtual_mailbox_maps = proxy:mysql:/etc/postfix/mysql_virtual_mailbox_maps.cf
virtual_mailbox_base = /vmail
virtual_minimum_uid = 1000
virtual_uid_maps = static:1000
virtual_gid_maps = static:1000
virtual_transport = dovecot
#####################SASL Settings###############################
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous
broken_sasl_auth_clients = yes
smtpd_recipient_restrictions = permit_mynetworks,
                             permit_sasl_authenticated,
                             reject_non_fqdn_hostname,
                             reject_non_fqdn_sender,
                             reject_non_fqdn_recipient,
                             reject_unknown_sender_domain,
                             reject_unknown_recipient_domain,
                             reject_unauth_pipelining,
                             reject_unauth_destination,
                             reject_invalid_hostname
###################Mail Quota Settings###############################
message_size_limit = 204800000
maximal_queue_lifetime = 1d
bounce_queue_lifetime = 1d
vim /etc/postfix/master.cf  最后行加入，flages 前一定要有两个空格哦
dovecot unix -  n  n  -  -  pipe
  flags=DRhu user=vmail:vmail argv=/usr/libexec/dovecot/deliver -d ${recipient}
[root@mail ~]# vim /etc/postfix/mysql_virtual_domains_maps.cf
user = vmail
password = vmail
hosts = localhost
dbname =vmail
table = domain
select_field = domain
where_field = domain
additional_conditions = and backupmx = '0' and active = '1'
[root@mail ~]# vim /etc/postfix/mysql_virtual_alias_maps.cf
user = vmail
password = vmail
hosts = localhost
dbname = vmail
table = alias
select_field = goto
where_field = address
additonal_conditions = and active = '1'
[root@mail ~]# vim /etc/postfix/mysql_virtual_mailbox_maps.cf
user = vmail
password = vmail
hosts =localhost
dbname = vmail
table = mailbox
select_field = CONCAT(domain,'/',maildir)
where_field =username
additional_conditions = and active = '1'
vim /etc/postfix/mysql_virtual_mailbox_limit_maps.cf
user = vmail
password vmail
hosts = localhost
dbname= vmail
table = mailbox
select_field = quota
where_field = username
additional_conditions = and active ='1'
DOVECOT收信和SMTPD认证

[root@mail ~]# vim /etc/dovecot/dovecot.conf 修改如下选项
protocols = imap pop3 lmtp
listen = *
base_dir = /var/run/dovecot/
#first_valid_uid = 89
#last_valid_uid = 89
#maildir_copy_with_hardlinks = yes
[root@mail ~]# vim /etc/dovecot/conf.d/10-auth.conf  修改如下选项
disable_plaintext_auth = no
auth_mechanisms = plain login cram-md5
!include auth-sql.conf.ext
[root@mail ~]# vim /etc/dovecot/conf.d/10-master.conf   修改如下选项
unix_listener /var/spool/postfix/private/auth {
  mode = 0666
  user = vmail
  group = vmail
}
[root@mail ~]# vim /etc/dovecot/conf.d/10-mail.conf 修改如下选项
mail_location = maildir:/vmail/%u/Maildir
[root@mail ~]# vim /etc/dovecot/conf.d/15-lda.conf  修改如下选项
protocol lda {
  postmaster_address = postmaster@dggd.com
  sendmail_path = /usr/lib/sendmail
  auth_socket_path = /var/run/dovecot/auth-master
}
[root@mail ~]# vim /etc/dovecot/conf.d/20-pop3.conf  修改如下选项
pop3_uidl_format = %08Xu%08Xv
[root@mail ~]# vim /etc/dovecot/conf.d/auth-sql.conf.ext  修改如下选项
passdb {
  driver = sql
  args = /etc/dovecot/dovecot-sql.conf.ext
}
userdb {
  driver = sql
  args = /etc/dovecot/dovecot-sql.conf.ext
}
[root@mail ~]# vim /etc/dovecot/dovecot-sql.conf.ext
driver = mysql
connect = host=localhost dbname=vmail user=vmail password=vmail
default_pass_scheme = PLAIN-MD5
password_query = SELECT username as user, password, '/vmail/%d/%n' as userdb_home, 'maildir:/vmail/%d/%n/Maildir' as userdb_mail, 1000 as userdb_uid, 1000 as userdb_gid FROM mailbox WHERE username = '%u' AND active = '1'
user_query = SELECT '/vmail/%d/%n' as home, 'maildir:/vmail/%d/%n/Maildir' as mail, 1000 AS uid, 1000 AS gid, concat('dirsize:storage=', quota) AS quota FROM mailbox WHERE username = '%u' AND active = '1'
改文件权限
[root@mail ~]# chmod 600 /etc/dovecot/* -R
[root@mail ~]# chown vmail /etc/dovecot/* -R
[root@mail ~]# chomd 777 /var/run/dovecot/auth-master
打开PostfixAdmin管理界面 http://localhost/postfixadmin/setup.php 填写安装密码
复制产生出来的安装密码写入到主配置文件/postfixadmin/config.inc.php的$CONF['setup_password'] =''; 然后设置超级管理员和密码 
接着成功的登陆PostfixAdmin管理你的邮件帐户
RoundCube网页访问客户端
下载解压改名，放入网页引导路径。

下载
wget http://sourceforge.net/projects/roundcubemail/files/roundcubemail/1.0.3/roundcubemail-1.0.3.tar.gz/download
解压
tar -zxvf roundcubemail-1.0.3.tar.gz 
放入网页引导路径
cp roundcubemail-1.0.3 /var/www/html/ -R
改名
mv /var/www/html/roundcubemail-1.0.3 /var/www/html/usermail
Apache添加php模块

vim /etc/httpd/conf/httpd.conf 添加下面两行
  AddType application/x-httpd-php .php
  PHPIniDir "/etc/php.ini"
添加PHP模块时间区域

vim /etc/php.ini 添加下面一行
   date.timezone = Asia/Shanghai
修改权限
chmod 777 /var/www/html/usermail/temp
chmod 777 /var/www/html/usermail/logs
chmod 777 /var/www/html/usermail/config -R
chmod 777 /var/lib/php/session -R
配置CONFIG文件与postfixadmin连接同一个数据库。

启用CONFIG文件
cp /var/www/html/usermail/config/config.inc.php.sample  /var/www/html/usermail/config/config.inc.php
改写MYSQL数据查询与POSTFIXADMIN一样
vim /var/www/html/usermail/config/config.inc.php 修改下面一行。
  $config['db_dsnw'] = 'mysql://vmail:vmail@localhost/vmail';
  $config['enable_installer'] = true;
安装PHP-DOM
yum install php53-dom
yum -y install php-dom
网页配置
 1: http://域名或IP/usermail/installer 打开IE，点击NEXT(下一步).
 2: Create config，设置你的配置文件，显示/服务器名/网络/数据库连接(与postfixadmin相同数据)..等等。自己揣摸。
 3: TEST CONFIG
  出现如下NOT OK.数据库没有初始化
    Check DB config
    DSN (write):  OK
    DB Schema:  NOT OK(Database not initialized)
 点击 Initialize Database, 初始化数据，它会返回结果OK.
    Check DB config
    DSN (write):  OK
    DB Schema:  OK
    DB Write:  OK
    DB Time:  OK 
  输入POSTFIXADMIN建的邮件地址测试SMTP和IMAP.会返回结果OK.
  
  完成了，http://域名或IP/usermail

***
***
***
<h1 style="text-align:center">OPENWEBMAIL网页邮件架设</h1>

> 安装

安装前提：yum –y install perl-suidperl perl-Compress-Zlib perl-Text-Iconv或yum install -y perl*
```
如果PERL-TEST-ICOV 安装失败，请参照如下去下载:
wget http://openwebmail.org/openwebmail/download/redhat/rpm/packages/centos5/perl-Text-Iconv/i386/perl-Text-Iconv-1.7-2.el5.i386.rpm

安装：rpm -ivh perl-Text-Iconv-1.7-2.el5.i386.rpm
检查是否安装成功： rpm –qa |grep perl-

安装APACHE: Yum –y install httpd
进入YUM源目录：cd /etc/yum.repos.d
下载Yum源：wpget http://openwebmail.org/openwebmail/download/redhat/rpm/release/openwebmail.repo
在线安装：Yum –y install openwebmail
离线安装：http://openwebmail.org/openwebmail/download/release/openwebmail-2.53.tar.gz
```

> 配置
```
修改配置文件：
vim /var/www/cgi-bin/openwebmail/etc/default/dbm.conf
vim /var/www/cgi-bin/openwebmail/etc/dbm.conf
dbm_ext                 .pag
dbmopen_ext             none
dbmopen_haslock         yes
smtpserver                  192.168.222.41
cd /var/www/cgi-bin/openwebmail/
./openwebmail-tool.pl –-init
```

> 测试

IE访问：http://mail.davis.dg/cgi-bin/openwebmail/openwebmail.pl

***
***
***
<h1 style="text-align:center">POSTFIX邮件恢复</h1>

```
例如：恢复“DNS”邮件目录
从邮件服务器backup目录/restore/davis_dai找到要恢复的
cd /restore/davis_dai/Maildir

drwx—— 5 davis_dai 2000 4.0K Nov 14 14:36 .Boss

drwx—— 5 davis_dai 2000 4.0K Oct 8 2012 .Deleted Items

drwx—— 5 davis_dai 2000 4.0K May 6 09:12 .Deleted Messages

drwx—— 5 davis_dai users 4.0K Nov 14 14:36 .DNS

cp -a .DNS /home/davis_dai/Maildir

Fix the permission chown davis_dai /home/davis_dai/Maildir/.DNS -R
```



```
sendmail安装
ftp://ftp.sendmail.org/pub/sendmail/sendmail.8.15.2.tar.gz
```