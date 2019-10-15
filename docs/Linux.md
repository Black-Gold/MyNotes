# Linux

I. stunnel -> varnish -> HAProxy -> nginx -> nodeJS -> memcached(redis) (for session storage[Session 对象存储])

II. Apache Traffic Server/Squid/Vanish + HAProxy + Nginx + memcached(Redis) (for session storage[Session 对象存储])

III. nginx (for HTTP compression) –> Varnish cache (for caching) –> HTTP level load balancer (HAProxy, or nginx, or the Varnish built-in) –> webservers

## 分区方案参考

| 服务器类型 | 基本raid方案 |
| :------: | :------: |
| 单机服务器 | 视数据及性能要求，一般可采用raid5折中 |
| 负载均衡器（如LVS等） | 数据量小，重要性高，可采用RAID1 |
| 负载均衡下的RS server | 数据量大，重要性不高，有性能要求，数据要求低，可采用RAID0 |
| 数据库服务器 | 视数据及性能要求主库可采取raid10/raid5，从库可采用raid0提高性能（读写分离的情况下） |
| 存储服务器 | 可采取sata盘，raid5 |
| 共享存储服务器（如NFS） | 视性能及访问要求可以raid5,raid10,甚至raid0（要有高可用或双写方案） |
| 监控服务器 | 单盘或双盘raid1即可。三盘就RAID5，看容量要求加盘即可 |

## Linux文件系统

### linux磁盘详解

```markdown
磁盘分区有主分区、扩展分区、逻辑分区，一块硬盘最多可以有四个主分区，其中一个主分区的位置可以用一个扩展分区替换，且一块硬盘只能有一个扩展
分区，在这个扩展分区内可以划分多个逻辑分区
预计超过四个分区，至少一个扩展分区：3p+1e、2p+1e、1p+1e-----p：主分区、e：扩展分区

磁盘分区的设备名：
在linux中，通过设备名访问设备，设备名存放在/dev目录
* 系统的第一块IDE接口的硬盘名称为/dev/hda
* 系统的第二块IDE接口的硬盘名称为/dev/hdb
* 系统的第一块SCSI接口的硬盘名称为/dev/sda
* 系统的第二块SCSI接口的硬盘名称为/dev/sdb

分区则使用数字编号标识：
* 系统的第一块IDE接口的硬盘第一个分区名称为/dev/hda1
* 系统的第二块IDE接口的硬盘第一个分区名称为/dev/hdb1
* 系统的第一块SCSI接口的硬盘第一个分区名称为/dev/sdb1

注意：
分区数字编号1-4留给主分区或者扩展分区使用，逻辑分区是在扩展分区的基础上编号从5开始
SCSI/SAS/SATA/USB接口的硬盘的设备名均以/dev/sd开头，不同硬盘编号依次是/dev/sda、/dev/sdb
SAS/SATA是主流硬盘接口，SSD固态硬盘，速度性能SSD>SAS>SATA
```

### linux系统引导流程解析

```markdown
系统引导流程：
固件firmware (CMOS/BIOS)  post加电自检
对于固件的理解，是介于软件和硬件之间的软件代码，但同时烧录到硬件当中；CMOS固化在硬件中，作为固件的平台，BIOS是管理CMOS的管理界面

接着，自举程序bootloader (GRUB),载入内核
第一步的最后要读取硬盘中的MBR，包含了BootLoader,在MBR中还包含了分区表等信息

内核的工作，驱动硬件，启动初始进程init;在kernel中大部分都是程序；init启动后读取inittab文件，执行缺省默认的运行级别，从而继引导过程
在linux系统中，init是第一个存在的进程，它的PID恒为一，但也必须向一个更高级的功能负责：PID为零的内核调度器，从而获得cpu时间

关键点：对于父子进程的关系。首先，父进程中断，子进程同样会中断；如果父进程中断后，子进程没有中断，这种关系形象称之为孤儿进程，系统发现后
会将孤儿进程指向父进程init;如果父进程正常，而子进程中断的话，这样滴关系称之为僵尸进程；这两种进程在系统中不允许出现的

备注：linux中的运行级别类似于windows平台中的F8功能
单用户模式（级别1）类似windos平台中F8的安全模式，区别是没有图形界面，只有命令输入
运行级别2 和 3的区别就是2级别没有NFS功能，同时都是多用户模式
查看当前的运行级别：runlevel
运行级别的切换：init 0 1 2 3 4 5  S 或者 telinit 0 1 2 3 4 5

扩展：实际大家通过命令ls -l `which telinit`得知telinit名是init命令的一个软连接而已

grep -v "^#" /etc/inittabl #提取有效行，作为分析数据
文件格式分析：（字段组成结构如下所示：）
id:runlevels    action:process
id              标示符,一般都是两位字母或者数字
runlevels       指定运行级别，可以指定多个；例如::代表0-6
action          指定运行状态
process         指定要运行的脚本或者命令

action取值：
initdefault     表示系统缺省的启动运行级别
sysinit         系统启动时执行process中指定的命令
wait            执行process中指定命令，并等到结束后在运行其他的命令
one             执行process中指定的命令，不需要等待结束
ctrlaltdel      按下组合键执行process指定的命令；(关机的操作)

先后顺序：
1. id:3:initdefault：
2. l3:3:wait:/etc/rc.d/rc
3. /etc/rc.d/rc3.d

判断默认的运行级别调用/etc/rc.d/rc脚本，执行相关运行级别目录中的服务程序，完成相应运行级别的初始化配置；之后找到/etc/rc.d/rc3.d脚本
完成系统后续的引导过程，其作用是分别存放对应于运行级别的服务程序脚本的符号链接，链接到init.d目录中的相应脚本
/etc/rc.d/r3.d 目录包含了两种文件很重要，一种大写S开头，另一种是大写的K开头

S  start   K  kill  number 表示启动的顺序，数字越小优先启动；如果数字相同，那么就会按照创建的时间

Firmware
   |
BootLoader
   |
Kernel
   |
 init
   |
/etc/inittab
   |
initdefault
   |
/etc/rc.d/rc.sysinit
   |
/etc/rc.d/rc
   |
/etc/rc.d/rcN.d
   |
Login(username ,password)

启动系统首先固件的加电自检；典型为PC中cmos/bios作用是在物层次面上检测所有的硬件是否正常工作；如果硬件正常的话，会读取硬盘的上存放
物理数据的第一个位置MBR，MBR主要是存放自举程序BootLoader，在linux系统中我们大多数采用的是GRUB，GRUB的主要作用就是载入内核；载入
内核后，两个作用，第一是在linux操作系统层面上是驱动硬件和启动init进程；然后读取/etc/inittab配置文件，配置文件会判断系统缺省的运行
级别然后执行脚本/etc/rc.d/rc.sysinit加载基本的系统服务；接着根据设置的缺省系统运行级别（id:3,5:initdefault:）执行/etc/rc.d/rc
脚本，此脚本会判断initdefault，然后启动对应的运行级别目录下面的程序/etc/rc.d/rcN.d，最后出现登陆界面了，输入正确的用户名和密码即可
登录系统继而管理服务器

/etc/rc.d/init.d该目录下存放了各个运行级别的服务程序脚本
/etc/init.d 发现此文件是/etc/rc.d/init.d的软连接

```

### 添加硬盘或分区详细步骤

```bash

dmesg | grep sdb    # 检查硬盘设备sdb是否存在
fdisk -l /dev/sdb   # 查看此硬盘设备信息
fdisk /dev/sdb      # 对硬盘进行分区操作，默认时ext4类型，id=83，id=82是swap

: << comment
输入`m`列出可以执行的命令：
command (m for help): m

输入`p`列出磁盘目前的分区情况：
Command (m for help): p

输入`d`然后选择分区，删除现有分区：
Command (m for help): d
Partition number (1-4): 1

Command (m for help): d
Selected partition 2

查看分区情况，确认分区已经删除(对应分区删除后，不会再显示)：
Command (m for help): p

输入`n`建立新的磁盘分区，首先建立两个主磁盘分区：
Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
p    //建立主分区
Partition number (1-4): 1  //分区号
First cylinder (1-391, default 1):  //分区起始位置
Using default value 1
last cylinder or +size or +sizeM or +sizeK (1-391, default 391): 100  //分区结束位置，单位为扇区

Command (m for help): n  //再建立一个分区
Command action
   e   extended
   p   primary partition (1-4)
p
Partition number (1-4): 2  //分区号为2
First cylinder (101-391, default 101):
Using default value 101
Last cylinder or +size or +sizeM or +sizeK (101-391, default 391): +200M  //分区结束位置，单位为M

确认分区建立成功：
Command (m for help): p

再建立一个逻辑分区：
Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
e  //选择扩展分区
Partition number (1-4): 3
First cylinder (126-391, default 126):
Using default value 126
Last cylinder or +size or +sizeM or +sizeK (126-391, default 391):
Using default value 391

确认扩展分区建立成功：
Command (m for help): p

在扩展分区上建立两个逻辑分区：
Command (m for help): n
Command action
   l   logical (5 or over)
   p   primary partition (1-4)
l //选择逻辑分区
First cylinder (126-391, default 126):
Using default value 126
Last cylinder or +size or +sizeM or +sizeK (126-391, default 391): +400M

Command (m for help): n
Command action
   l   logical (5 or over)
   p   primary partition (1-4)
l
First cylinder (176-391, default 176):
Using default value 176
Last cylinder or +size or +sizeM or +sizeK (176-391, default 391):
Using default value 391

确认逻辑分区建立成功：
Command (m for help): p

最后对分区操作进行写入保存：
Command (m for help): w

# 建立好分区之后我们还需要对分区进行格式化才能在系统中使用磁盘
mkfs.ext4 /dev/sdb1     # 在sdb1上建立ext4分区

硬盘sdb我们建立了2个主分区（sdb1，sdb2），1个扩展分区（sdb3），2个逻辑分区（sdb5，sdb6）
注意：主分区和扩展分区的磁盘号位1-4，也就是说最多有4个主分区或者扩展分区，逻辑分区开始的磁盘号为5，因此在这个实验中没有sdb4

# 建立两个目录`/oracle`和`/web`，将新建好的两个分区挂载到系统
mkdir /oracle
mkdir /web
mount /dev/sdb1 /oracle
mount /dev/sdb6 /web

如果需要每次开机自动挂载则需要修改`/etc/fstab`文件，加入两行配置：
/dev/sdb1               /oracle                 ext4    defaults        0 0
/dev/sdb6               /web                    ext4    defaults        0 0
comment
```

### Linux目录结构FHS

```markdown
基本文件系统类型：(可以用file命令来识别)
* 普通文件：如文本文件、c语言源代码、shell脚本等，可以用cat、less、more、vi等来察看内容，用mv来改名
* 目录文件：包括文件名、子目录名及其指针，可以用ls列出目录文件
* 链接文件：是指向一索引节点的那些目录条目，用ls来查看时，链接文件的标志用l开头，而文件后以"->"指向所链接的文件
* 特殊文件：如磁盘、终端、打印机等都在文件系统中表示出来，常放在/dev目录内

~：代表用户自己的宿主目录
.：表示当前目录，也可以用./表示
..：表示上一级目录，也可以用../表示

/bin      保存可执行的二进制文件（可执行的指令）权限对所有用户开放

/boot     存放开机启动加载程序的核心文件,包括引导文件、内核文件、驱动、 插件、模块等；(如kernel和grup)

/dev      存放了所有的硬件设备文件；网卡，声卡，硬盘，光驱；tty1,tty2终端，设备文件目录，虚拟文件系统，主要存放所有系统中device的相关
          信息，不论是使用的或未使用的设备，只要有可能使用到，就会在/dev中建立一个相对应的设备文件；设备文件分为2种类型：字符设备文件和
          块设备文件(目录中基本上都是设备文件，如硬盘设备文件/dev/sda)
    /dev/console    系统控制台，也就是直接和系统连接的监视器
    /dev/hd         IDE设备文件
    /dev/sd         sata、usb、scsi等设备文件
    /dev/fd         软驱设备文件
    /dev/tty        虚拟控制台设备文件
    /dev/pty        提供远程虚拟控制台设备文件
    /dev/null       所谓"黑洞"，所有写入该设备的信息都将消失，如当想将屏幕上的输出信息隐藏起来时，只要将输出信息输入到/dev/null即可

/etc    系统、网络或配置文件存放目录，简单的将/etc目录分为以下几类：
    基本文件：所有直接放在/etc目录下的文件归类为基本文件
            aliases：用于设置邮件别名
            auto.* ：代表的是一系列autofs服务所需要的配置文件，这个服务主要是让管理员可以事先定义出一些网络、本机或光驱等默认的路径
            auto.master：负责规划目录的分配与使用，目前默认提供三种自动挂载模式
            auto.misc：文件中的配置都以实体连接本机的磁盘驱动器为主
            auto.net：并不是一个配置文件，而是一个脚本文件，在使用上其实不须做任何调整
            auto.smb：与auto.net一样，都是以个脚本文件
            bashrc：用户登录功能配置，全局配置，对所有用户生效，主要配置别名
            profile：与系统环境配置或初始化软件的相关配置，全局配置，对所有用户生效，主要配置变量
            DIR_COLORS：用于配置ls命令的颜色，主要针对tty登录的用户
            DIR_COLORS.xterm：用于配置ls命令的颜色，主要针对xterm登录的用户
            fstab：系统启动时自动挂载文件系统的配置文件
            inittab：启动时系统所需要的第一个配置文件；也即是init进程的配置文件
            issue：用户本机登录时，看到的欢迎信息
            issue.net：用户网络登录时，看到的欢迎信息
            ld.so.conf：包含ld.so.conf.d/*.conf配置；主要是ld.so.conf.d/*.conf目录的作用
            localtime：系统所使用的时区对应的配置文件；对应的时区文件都存在于/usr/share/zoneinfo/
            motd：登录成功的用户显示的信息对应的配置文件
            mtab：可以当做是检查当前文件系统挂载情况的配置文件；与mount命令结果一致
            prelink.conf：定义哪些执行文件和函数库是需要预先连接的
            securetty：主要是login程序在使用的，只要是列在该文件中的接口，就表示是可以使用的接口，相反，若从列表中删除，则无法使用该接口
            shells：记录目前系统所拥有shell种类的路径，通过cssh命令使用
            sudoers：sudo命令对应的配置文件，用于配置权限的分配方式
            sysctl.conf：主要是帮助用户配置/proc/sys目录下所有文件的值，与sysctl命令对应
            syslogd.conf：是syslogd服务的配置文件
            host.conf：主机名解析配置文件，主要说明解析的方式及顺序
            hosts：主机名解析配置文件，主要列出所有需要本地解析的主机名与IP地址的对应关系
            hosts.allow和hosts.deny：linux网络安全机制TCP Wrapper对应的配置文件
            nsswitch.conf：主要记录系统应如何查询主机名、密码、用户组、网络等，或是查询顺序的编排
            resolv.conf：记录DNS服务器地址，用于DNS域名解析
            services：定义了网络服务的默认端口号
            xinetd.conf：xinetd的主配置文件，目的是为xinetd.d下的所有子服务建立一个标准的规范使其可以遵循
            anacrontab：属于一种任务计划软件的配置文件，anacrontab软件和crond其实有点相辅相成，crond负责任务计划，而anacrontab则是
                        负责以"间隔多久"为主要的目标
            at.deny：该文件属于拒绝列表，只要被记录在其中的用户，就无法使用at所提供的任务计划服务
            at.allow：与at.deny刚好相反
            crontab：crontab的主配置文件，crond默认会执行的文件可以参考此配置文件
            cron.deny：该文件属于拒绝列表，只要被记录在其中的用户，就无法使用crond所提供的任务计划服务
            cron.allow：与cron.deny刚好相反
            exports：是NFS服务的主配置文件，主要目的就是将本机的目录共享到网络上，供其他人使用
            group与gshadow：用户组配置文件，group主要保存用户组信息，gshadow主要保存群组密码
            login.defs：设置系统在建立账号时所参考的配置
            passwd：主要保存系统用户账号的信息
            shadow：linux系统通常包经过"hash"处理后的密码存储在这个文件中
            protocols：通信协议对应端口号的一个对照表，包含协议名称、协议号码、注释等
            wgetrc：wget程序对应的配置文件，其中有quota、mail header、重传文件的预设次数、firewall和proxy等相关设置
            init.d：RHEL中所有服务的默认启动脚本都存放在这里；这个是链接文件，链接到/etc/rc.d/init.d
            csh.cshrc和csh.login： 用户启动c shells执行的初始化配置文件
            printcap：linux系统中打印机设备对应的配置文件
    服务器目录：如samba、http、vsftpd等服务器配置相关目录
            cups：linux下的打印机服务器，目录下存放的是打印机服务的配置文件
            dnsmasq.d：dnsmasq是一种DNS的"轻薄机种"，转为区域或小型网络所设计，拥有比一般DNS更为方便简易的配置
            httpd：apache网页服务器的配置文件所在目录
            mail：Mail Server组件的主要配置目录，如sendmail
            ntp：网络时间服务器的配置目录，其主要配置文件为/etc/ntp.conf
            openldap：目录明显是LDAP的配置目录，软件名称为OpenLDAP
            postfix：postfix组件所提供的主要配置文件目录
            samba：文件共享服务samba的主要配置文件目录
            smrsh：这是sendmail为了限制用户可使用的命令设计的程序，将原本用户所使用的/bin/sh替换为/usr/sbin/smrsh
            snmp：简单网络管理软件的配置文件目录，存在snmpd.conf主配置文件
            squid：这是linux下的代理服务器squid的配置文件目录，主配置文件是squid.conf
            ssh：SSH服务的主要配置目录，主配置文件是sshd_config
            vsftpd：vsftpd服务器的主要配置目录，主配置文件是vsftpd.conf
            xinetd.d：xinetd是一个管理多个服务的daemon，这个目录下列出的服务都是由xinetd进程管理的，其主配置文件是/etc/xinetd.conf
    系统目录：如sysconfig、xen或网络配置等与系统运行相关的目录
            blkid：此目录所存放一个块设备ID的临时文件，主要记录系统中所有区块设备的标签名称、硬件的唯一识别码、文件系统的格式等基本信息
            bluetooth：linux下使用蓝牙设备所需的配置文件；启动蓝牙检测的主要服务仍是/etc/rc.d/init.d/bluetooth，该程序使用的是
                       hcid.conf配置文件
            cron.X：cron.X的目录都是给cron软件存放其需要任务计划的文件所使用的，按任务计划时间的长短及配置特性分为cron.d、cron.daily、
                    cron.hourly、cron.monthly、cron.weekly五个主要目录
            dbus-1：D-BUS的主要配置目录，D-BUS也是一种IPC交流的方式
            default：这里是存放一些系统软件默认值的目录，存放某些软件执行时的基本参数
            firmware：这个目录所存放的东西是非常底层的信息，是CPU所需的microcode的实体文件
            foomatic：与打印机相关的配置目录，实现打印一对多的方式，在foomatic中，可以记录多条打印机数据，让用户只在使用前先行配置所有
                      需要使用的打印机即可
            hal：全名Hardware Abstraction Layer，是linux一种管理硬件的机制，它会帮所有的应用程序或用户搜集所有PCI及USB等硬件信息
                因此用户可以很简单并实时地通过HAL的方式取得硬件的相关数据
            isdn：ISDN服务的主要配置目录，里面包含可拨号的用户、电话、联机方式等
            ld.so.conf.d：这个目录是ldconfig所使用的，更准确的说，它是由/etc/ld.so.conf文件所决定的；ldconfig命令的目的在于将系统
                          中的一些函数库预先存放到内存中，让系统使用时可以比以往通过硬盘的读取速度来的更快，这样可以大幅提高系统性能
                          尤其当要重复读取时更明显；ldconfig要将哪些函数库丢到内存中，则须看/etc/ld.so.conf文件中所记录的信息
            logrotate.d：此目录对系统管理员来说，是十分重要的一个目录，因为目录中的文件，记录了如何定期备份系统所需要备份的系统或软件
                         日志文件及备份方式，目录是由logrotate组件所提供的，而里面所有文件是由各软件各自产生的；其主要配置文件是
                         /etc/logrotate.conf
            logwatch：logrotate主要是实现如何备份日志文件，这个目录就是记载如何分析日志文件并告诉用户的软件logwatch的配置目录
            lsb-release.d：LSB是一个由很多人所执行的项目，其目的是将所有的Linux发行版定义为一些共同的标准
            lvm：这个目录是LVM的基本配置文件，但配置或操作一般都只需要通过LVM提供的命令，而不会用到这个目录，除非要使用到很高级的配置
                才会更改此文件
            makedev.d：MAKEDEV软件对应的配置文件目录，MAKEDEV主要用来产生设备文件，也就是说，在/dev目录下的文件都由这个命令产生的
                       此目录下的文件主要是针对设备文件的定义或属性，目录中存在的设备文件可以由MAKEDEV来创建，否则需要使用mknod命令
            modprobe.d：是modprobe命令的住配置目录，一般系统启动默认要加载的模块放在/etc/modprobe.conf中
            netplug和netplug.d：这两个目录和网络接口的联机与否由直接关系，因为主要是控制联机时的接口操作
            opt：此目录原本是定义为存放所有额外安装软件的主机配置文件，但目前并没有被使用到，此目录为空
            pcmcia：这是PCMCIA的配置文件目录，PCMCIA是笔记本电脑不可或缺的接口，需要即插即用的方式，此接口使用较少
            pm：由pm-utils组件所提供的目录，pm-utils是一套电源管理的工具软件，其中/usr/lib/pm-utils也是主要目录之一
            ppp：ppp相关的配置文件都放在这个目录中
            profile.d：目录存放系统部分的软件配置，但按不同的shell执行不同文件，默认所使用bash会直接执行该目录下所有扩展名为.sh的文件
            rc.d：主要用来定义在每一个执行阶段必须要执行哪些系统服务或程序，在目录中主要分为三个重要的部分：
                    --rc.sysinit：系统一开始启动时所遇到的第一个文件，此脚本记录服务启动之前所需准备的所有事情，包括启动时的欢迎画面
                    --rcX.d：在rc.sysinit文件之后所要执行的，X是系统启动时的initdefault值，值为几则会转到那个目录下，并执行其中的
                             所有文件，在此目录中，文件一律都由两个英文字母开始K和S，K代表kill，S代表Start
                    --rc.local：系统初始化过程中最后一个执行的脚本文件，可以将需要开机启动的程序或脚本放置在这个脚本文件中，以实现自动
                                运行的目的
            readahead.d：是readahead程序的主要配置目录，为了加速操作系统的使用速度，readahead_early和readahead_later这两个进程在
                         系统加载时，直接将日常所需要的一些文件，全部先放到硬盘的高速缓存中
            redhat-lsb：都lsb-release.d目录都是由程序redhat-lsb所提供的
            rwtab.d：这个目录是一个在启动时会去参考的目录，主要的文件在/etc/rwtab；这是一个系统初期的备份机制
            sane.d：这是在系统下要使用扫描仪所需的配置目录，主要配置文件是sane.conf，sane为了方便用户在各式的扫描仪连接时都可以使用
                    因此在这一目录中放置了很多种不同类型扫描仪的硬件信息，让系统在检测到扫描仪时可以直接使用
            setuptool.d：这个目录是"setup"系统配置工具的主要配置目录
            skel：用于初始化用户宿主目录的配置目录，当建立一个用户时，会把此目录下的所有文件复制一份到用户的宿主目录，作为用户的初始化配置
            sysconfig：非常重要的系统配置文件的存放目录，里面放置了大量系统启动及运行相关的配置文件
            sysconfig/network-scripts/ifcfg-eth0：网卡eth0对应的配置文件，设置内容包括设备名称、IP地址、广播地址、网关地址、网段、
                                                  开机是否激活等参数
            udev：udev程序本身是一套设备的管理机制，udev通过sysfs的文件系统，可以正确地掌握目前系统上存在的硬件设备，以及针对每一个硬件
                  设备做出不同的判断与执行
            yum和yum.repos.d：两个都是yum配置目录，可用来替代rpm包管理方式，主配置文件是/etc/yum.conf；yum是更新方式及外挂程序的
                             配置目录，yum.repos.d是存放定期更新组件内容的信息
    安全性目录：如selinux或pam.d等管理系统安全性的目录
            audit：这个目录所代表的是一种和目录名称一致的audit安全机制，主要以服务的方式协助管理员持续监控各文件被存取的情况；目录下的
                   audit.rules文件主要是定义一些必要的监控规则
            pam.d：此目录是Linux-PAM的所有配置文件，配合/lib/security目录中所有觉得函数库，提供Linux下的应用程序认证的机制
            pam_pkcs11：PAM机制中的一种登录模块，可以让用户通过smart card做登录的操作
            pki：PKI是一种公开密钥的管理方式，通过这样的管理模式，可以让所有网络传输有更多保障
            racoon：这个目录是由ipsec-tools组件所提供的，ipsec的主要目的是让系统实现VPN的网路技术，在racoon目录的主配置文件
                    racoon.conf中，定义在ipsec操作中所需要的加密算法种类以及其他细节的配置
            security：与pam.d目录相辅相成，pam.d中的所有PAM的规则都要用到/lib/security下的PAM函数库，而/etc/security目录中，就是
                      针对这些函数库，提供以配置文件的方式进行细节配置，对希望调整系统安全性部分增加了非常大的方便性
            selinux：selinux是一个很新的安全性方案，它是一种针对各种文件、目录、设备或daemon等在linux所需使用到的安全性机制，而且其
                     安全性的数据时直接记录在文件系统中
            wpa_supplicant：这个目录被归类到安全性目录中，是因为其属于无线中安全认证的部分，存在wpa_supplicant.conf配置文件，用户
                            可以在这个目录中加入已知可登陆的AP
    X Windows目录：如X11或gdm管理X windows启动或使用上的配置目录
            alternatives：linux下可辨识扩展名的"文件类型"选项，可以针对同一类型的文件，选出一个默认用户所要使用的程序去执行
                          /etc/alternatives目录下有所有目前已经定义的程序名称，都以软链接的方式存在，里面每一个文件其实都有定义好的
                          默认执行程序，可以使用alternatives命令查看及修改配置
            fonts：这个目录就是fontconfig软件的最主要配置目录，其中/etc/fonts/fonts.conf就是对应的配置文件，/etc/fonts目录下的配置
                   都是以XML的方式配置的
            gconf：这一目录是GConf2的组件所建立的，GConf的作用就是提供GNOME下的应用程序注册的机制，有些类似于windows下的regedit
            gdm：全名为GNOME Display Manger，也就是协助X Windows启动的管理软件，在GDM中的主配置文件是custom.conf，在X windows下
                 可以利用gdmsetup命令对这个文件进行配置
            gnome-vfs-2.0：GNOME VFS机制，让GNOME的系统可以知道每一种文件格式要如何开启或浏览，而所有的配置都需要有相对应的函数库
            gtk-2.0：由gtk+组件提供的目录，主要是提供X Windows窗口的颜色、按钮或图案，包含软件选项的画面、选项的按钮、滚动轴的样式等
            kde：KDE Desktop Manager的主要配置目录；主配置文件是kdmrc
            NetworkManager：此目录的目的是让用户不需要做任何操作和配置，只要用户曾经登陆过无线AP，系统就可以记录下来，以后再次登陆时就
                            可以方便的登陆
            pango：pango是一套协助GTK+将字体描绘出来的函数库，不论任何的字体或语言，都可以通过pango描绘出来
            rhgb：系统在进入X Windows之前，有一个前置配置的图形接口，这个接口就是rhgb，其主要目的是让系统启动变得漂亮
            scim：是Linux下目前很好用的输入法
            sound：GNOME下有许多的应用软件，很多都会有其特殊的声音，这个目录中存放所有声音的命令路径
            X11：X windows的核心配置目录；该目录下比较重要的文件有prefdm(判断X windows使用哪一个Display Manager)、主配置文件
                   xorg.conf(定了X windows所需使用的键盘、鼠标、显卡等相关硬件设备，重点是关于显卡的配置)、xinit子目录
                   (里面都是一些X windows资源相关的配置)
            xdg：X windows上的菜单画面，就是从这里出来的，所有在X windows中使用的菜单文字及分类，都可以在这个目录下做配置，其下的子目录
                 menu，可以通过配置里面的文件自定义应用程序、系统管理、外观等菜单内容
    其他目录：针对单一特殊软件的配置或未能按以上分类方式则放在此目录中
            a2ps.cfg和a2ps-site.cfg：用于将一份文件格式转换为postscript的格式，在某些打印机或将文件输出成一份标准格式文件时，会被用到
            alsa：主要任务在于提供linux声音及声音的功能，并试着让其性能达到最佳化
            ghostscript：linux下读取Adobe格式文件(如pdf)，就是使用ghostscript命令，此目录主要用于设置在显示时使用哪种字体作为默认字体
            gre.d：GRE是Mozilla注册的一种机制，目录中的配置文件gre.conf会注明所使用的Mozilla软件的路径和版本
            iproute2：iproute2是一套非常强大的网络管理软件，iproute2提供的功能有很多种，此目录中存放一些网络的基本配置值
            java：这个目录是由jpackage-utils软件提供的，这个目录是这个软件的主要配置目录，除此之外还有maven、jvm、jvm-common都是由
                  jpackage-utils软件产生的，jpackage是一个专门为了提供java程序与函数库所存在的软件
            mgetty+sendfax：主要用于使用linux架构一台fax server，可以使用mgetty.config来配置需要有关传真接收和发送的操作
            php.d：主要存放的各软件(如dbase、ldap、mysql等)与php相关的配置文件
            reader.conf.d：存放smart card配置文件的目录，由程序pcsc-lite提供，这个程序的主配置文件是/etc/reader.conf
            dumpdates：存放dump命令的执行日期，dump命令可以对ext2/ext3文件系统进行检查备份

/home      默认存放用户的宿主目录(除了root用户)
     /home/~/.bashrc：提供bash环境中所需使用的别名
     /home/~/.bash_profile：提供bash环境所需的变量；一般先执.bashrc后，才会再执行.bash_profile
     /home/~/.bash_history：用户历史命令文件，记录用户曾经输入的所有命令；(默认为1000条，可以通过HISTSIZE变量更改)
     /home/~/.bash_logout：当用户注销的同时，系统自动生成.bash_logout文件，如需要记录用户注销的其他信息，可以利用这个机制去完成

/root     管理员root的宿主目录

/lib      需要共享的函数库与kernel模块，系统kernel启动所使用的函数库，或者当执行一些在/bin和/sbin中的命令时使用的函数库

/mnt      挂载点目录；一般的自动挂在/media目录(如磁盘分区，网络共享)

/media    移动存储设备默认挂载点；(如光盘)

/opt      常用于放置大型的应用软件；数据库文件

/proc     只存在于内存中的文件系统，保存操作系统的实时信息；内存信息，CPU信息，虚拟内存信息，电源信息；cpuinfo ,meminfo ,acpi,scsi,
         vmstat,uptime;每次启动操作系统都会自动创建一个新的/porc文件，来记录系统的实时信息；并不是存放在硬盘上，而是内存镜像中的
         虚拟文件系统，此目录是kernel加载后，在内存里面建立的一个虚拟目录，有专属的文件系统，主要提供系统一些实时的信息，此目录下不能
         建立和删除文件；(某些文件可以修改)
         /proc主要作用可以整理为：
                整理系统内部的信息
                存放主机硬件信息
                调整系统执行时的参数
                检查及修改网络和主机的参数
                检查及调整系统的内存和性能
    /proc下常用的信息文件有：
    /proc/cpuinfo：cpu的硬件信息，如类型、厂家、型号和性能等
    /proc/devices：记录所有在/dev目录中相关的设备文件分类方式
    /proc/filesystems：当前运行内核所配置的文件系统
    /proc/interrupts：可以查看每一个IRQ的编号对应到哪一个硬件备
    /proc/loadavg：系统"平均负载"，3个数据指出系统当前的工作负载
    /proc/dma：当前正在使用的DMA通道
    /proc/ioports：将目前系统上所有可看到的硬件对应到内存位置的配表的详细信息呈现出来
    /proc/kcore：系统上可以检测到的物理内存，主机内存多大，这个件就有多大
    /proc/kmsg：在系统尚未进入操作系统阶段，把加载kernel和initr的信息先记录到该文件中，后续会将日志信息写入/var/log/messag文件中
    /proc/meminfo：记录系统的内存信息
    /proc/modules：与lsmod命令查看到的模块信息完全一致
    /proc/mtrr：负责内存配置的机制
    /proc/iomem：主要用于储存配置后所有内存储存的明细信息
    /proc/partitions：这个文件可以实时呈现系统目前看到的分区
    /proc/数字目录：数字目录很多，它们代表所有目前正在系统中运行所有程序
    /proc/bus：有关该主机上现有总线的所有信息，如输入设备、PCI口、PCMCIA扩展卡及USB接口信息
    /proc/net目录：存放的都是一些网络相关的虚拟配置文件，都ASCII文件，可以查看(与ifconfig、arp、netstat等有关)
    /proc/scsi：保存系统上所有的scsi设备信息(包括sata和usb设备信息)
    /proc/sys目录：存放系统核心所使用的一些变量，根据不同性质的件而存放在不同的子目录中，可以通过/etc/sysctl.conf文件设置更改其默认值
                  变量时实时的变更，有很多设置很象是开关，设置后上生效
    /proc/tty：存放有关目前可用的正在使用的tty设备的信息
    /proc/self：存放到查看/proc的程序的进程目录的符号连接，当2进程查看proc时，这将会是不同的连接；主要便于程序得到它自己的程目录
    /proc/stat：系统的不同状态信息
    /proc/uptime：系统启动的时间长度
    /proc/version：系统核心版本

/sbin    存放特权级二进制文件（特权级可执行命令）只有root用户可以执行；较危险的命令保存其中；(多数管理命令默认只有管理员可以使用)

/usr：安装除操作系统本身外的一些应用程序或组件，一般可以认为linux系统上安装的应用程序默认都安装在此目录中
    /usr/bin：一般用户有机会使用到的程序，或者该软件默认就是要所有用户使用才会放在该目录中
    /usr/sbin：一些系统有可能会用到的系统命令，与/sbin比起来，是一些较次要的文件
    /usr/etc：自行安装或非系统主要的配置文件目录
    /usr/games：只要是电脑游戏相关的软件，就都安装到这个目录
    /usr/include：存放的文件都是一些系统中用户所会使用到的C语header文件，保存的都是".h"的文件
    /usr/kerberos：kerberos是一种安全机制，让用户可以直接使用持kerberos机制系统上的部分资源
    /usr/lib：存放一些函数库、执行文件及连接文件，特别的是，存在这里面的文件都是不希望直接被用户或shell脚本所使用的文件，/usr/lib中有
              非常多的子目录，每一个软件都有其各自所需的函库
    /usr/libexec：这个目录下的文件及文件夹应该都可以放置/usr/lib下
    /usr/local：linux系统中安装的共享软件程序最好的方式是安装/usr/local下，按照linux标准目录结构，新建立的软件都应该放/usr/local下
        /usr/local/bin：存放软件执行文件的目录
        /usr/local/sbin：同样存放软件执行文件的目录，但此目录门针对系统所使用的文件
        /usr/local/lib：软件相关的函数库
        /usr/local/share：当文件性质不好归属时就会放在此，man册就放在这个目录下
        /usr/local/src：所安装软件的源代码放置在此
    /usr/share：此目录都是一些共享信息，最常被用到的就/usr/share/man这个目录，/usr/share里的信息时跨平台的
    /usr/share/doc：放置一些系统帮助文件的地方
    /usr/share/man：manpage的文件存放目录，也是使用man查看手册时查询的路径
    /usr/src：主要储存内核源代码的文件
    /usr/X11R6：存放一些X windows系统的相关文件

/var    动态文件或数据存放目录，默认日志文件都存放在这个目录下，一般建议把此目录单独划分一个分区
    /var/account：是linux系统下的审核机制(psacct)对应的目录
    /var/cache：该目录下的文件时所有程序所产生的缓存数据，也就是当应用程序启动时，会将数据留一份在这个目录中
    /var/empty：默认是sshd程序用到的这个目录，当建立ssh连接，ssh服务器必须使用该目录下的sshd子目录
    /var/ftp：ftp服务器软件一般默认会将匿名登陆的用户的宿主目录
    /var/gdm：gdm所使用的目录，里面存放一些系统当前所占用的console记录及通过gdm执行的X windows记录，只有通过gdm窗口的日志才会存放在此
    /var/lib：该目录下存放很多与应用程序名称同名的子目录，每个子目录下都是应用执行的状态信息
    /var/lock：每个服务一开始都会在这个目录下产生一个该服务的空文件，主要是避免服务启动冲突
    /var/log：常用目录，专门用来存放所有日志文件的目录，里面存放很多系统、软件、用户等相关的日志信息；里面有一些文件是比较常用的
        lastlog：记录用户最后一次登录的信息，使用lastlog命令读取
        message：记录系统的几乎所有信息，主要包括启动信息，syslogd服务记录的信息等
        wtmp：记录所有用户登陆及注销的信息，使用last命令读取
        secure：记录登录系统访问数据的文件，如ssh pop3 telnet ftp等都会记录在此文件中
        /var/log/httpd/access_log：httpd访问日志
        /var/log/httpd/error_log：httpd错误日志
        btmp：记录失败的用户登录
        utmp： 纪录当前登录的每个用户
        xferlog：ftp会话日志
        boot.log：记录开机或一些服务启动时所显示的启动和关闭信息
        /var/log/maillog或/var/log/mail/*：记录邮件访问或往来的用户信息
        cron： 记录crontab例行性服务的内容
        dmesg：开机引导日志信息
        sudolog：纪录使用sudo发出的命令
        sulog： 纪录使用su命令的使用
    /var/named：bind软件实现的DNS服务器的区域数据文件都存放在这个目录下
    /var/nis和/var/yp：都是NIS服务机制所使用的目录，nis主要记录所有网络中每一个client的连接信息；yp是linux的nis服务的日志文件存放的目录
    /var/run：此目录中的大部分文件都记载目前系统正在执行程序的PID值，每一个文件都是以个独立的PID记录；此目录下存放一个特殊文件utmp，此文件记录目前谁在使用系统，必须使用utmpdump命令才能看到其中的内容
    /var/spool：里面主要都是一些临时存放，随时会被用户所调用的数据；打印机、邮件、代理服务器等假脱机目录存放在该目录下
    /var/tmp：专门为了一些应用程序在安装或执行时，需要在重启后使用的某些文件时，能将该文件暂时存放在这个目录中，完成后再行删除
    /var/www：apache网页服务器的宿主目录

/lost+found:存放了一些系统出错的检查结果；异常断电情况、死机；默认目录是空的,通常使用fsck进行文件修复，修复后文件则被存放于此目录

/misc：自动挂载服务目录，对应autofs服务

/srv：默认为空，主要用于存放一些软件的配置文件，某些软件可能会把配置文件默认存放在这个目录下，多数都是/etc目录下，此目录没有被具体的定义

/temp  临时文件存放点；此目录有特殊的权限位粘滞位：1777

/tftpboot：远程启动tftpserver的根目录，这个目录只有安装了tftp-server软件后才会产生

备注：如果要做系统级备份的话，/etc ,/boot是必须要备份的目录，备份分为：系统备份和用户备份
/sbin   /usr/sbin是root账户可以执行命令的存放路径
/bin    /usr/bin对于普通用户可以执行命令的存放路径
        bin代表binnary ；sbin 代表super binnary
/usr目录的重要性是和windows系统上的windows目录的重要性是一样的，都是核心文件存放的位置

```

### GRUB配置文件讲解

```markdown
配置文件位置：/etc/grub.conf是个软连接文件
原始文件/boot/grub/grub.conf
grep -v "^#" /boot/grub/grub.conf | more
字段：
default定义缺省启动系统,0表示第一个，1表示第二个，以此类推
-------------------------------------------------
timeout     定义了缺省等待时间；默认5秒
-------------------------------------------------
splashimage 定义了GRUB启动时的界面图片
-------------------------------------------------
hiddenmenu  隐藏菜单
-------------------------------------------------
title       定义了菜单项名称
-------------------------------------------------
root        作用是设置GRUB的根设备也就是内核所在的分区
-------------------------------------------------
kernel      定义了内核文件所在的位置
-------------------------------------------------
initrd      命令加载镜像文件以及显示镜像文件的位置
-------------------------------------------------

GRUB设置密码

第一种方式：使用GRUB自带的grub-md5-crypt命令
grub-md5-crypt
在图形界面我可以选择复制选项进行复制，在字符终端界面上使用鼠标右键复制加密字符
编辑/etc/grub.conf配置文件，在hiddenmenu字段下面插入关于插入位置没有严格的要求）
password --md5 加密字符（-md5表示口令是md5加密的）
在插入模式下插入加密的字符串；然后退出保持即可

第二种方式：是在GRUB交互命令行界面使用md5crypt命令，效果是一样的
grub
grub>md5crypt
password:

GRUB的系统修复：（手工修复）故意修改kernel指定的内核文件
grub> cat /grub/grub.conf  为什么没有/boot原因就是grub存放在/boot分区
grub> 指定内核所在的分区 root (hd0,0)
grub> 指定内核文件所在的位置
grub> 指定镜像文件的位置
grub> boot
手工修复属于命令行操作；如修改了/etc/inittab中的默认启动级别的话，完全可以通过grub启动菜单来进行修复：在kernel行直接加入启动级别

终极修复模式：光盘修复
光盘引导，F5键，键入linux rescue
案例1：尝试删除/etc/inittab，之前先做好备份
cp /etc/inittab /etc/inittab.bak
rm -rf /etc/inittab

案例2：如果忘记了管理员密码，同时也忘记了GRUB的保护密码的话，我们可以使用GRUB的系统修复或者使用光盘的引导修复来解决此问题
```

### fstab文件含义如下

* 物理分区/卷标；可以使用UUID、设备路径/dev/、协议/文件系统类型或网络共享名称表示
* 挂载点(注意：swap没有挂载点)
* 文件系统,auto通常用于可移动设备，特定文件系统或网络共享网络协议
* 挂载时使用的参数；此选项取决于文件系统，default=rw，suid，dev，exec，auto，nouser和async
  * sync/async     文件系统的所有I/O同步或异步完成
  * auto           文件系统可以自动挂载（在启动时或通过-a选项传递挂载时）。确实没有必要，因为无论如何这是mount -a的默认操作
  * noauto         在启动时或通过-a挂载时不会自动挂载文件系统。您必须显式挂载文件系统
  * dev/nodev      解释或不解释字符或阻止文件系统上的特殊设备
  * exec/noexec    允许或阻止从文件系统执行二进制文件
  * suid/nosuid    允许或阻止suid和sgid位的操作
  * ro             挂载为只读
  * rw             挂载为读写
  * user           允许任何用户挂载文件系统。除非被覆盖，这将自动表示noexec，nosuid，nodev除非被额外选项覆盖
  * nouser         仅允许root挂载文件系统。这也是默认设置
  * defaults       使用默认设置。等效于rw，suid，dev，exec，auto，nouser，async
  * _netdev        这是一个网络设备，在启动网络后将其安装。仅对fstype nfs有效
  * noatime,nodiratime 禁止记录最近访问时间戳，设置noatime可不必再设置nodiratime
  * relatime       实时更新inode访问记录时间戳
  * owner          允许设备被那个所有者挂载
* 启用或禁用设备/分区的备份(dump是备份工具)，1表示开启，0表示忽略
* 控制fsck在引导时检查设备/分区的错误的顺序，根设备应为1。其他分区应为2或0以禁用检查

## Linux_用户、组和权限

### Linux用户和用户组

```markdown
# 查看系统中有多少的用户数量
wc -l /etc/passwd
cut -d " : " -f 1 /etc/passwd

# 用户和组的文件
/etc/passwd
/etc/group

# 影子口令：（真正口令文件路径）
用户：/etc/shadow
组：/etc/gshadow

# /etc/passwd中7段意义：用户名：密码：UID:GID：用户描述信息：家目录：默认SHELL
account: 用户名
password: 密码
UID：用户ID，分为三类：root用户：0，普通用户：500-60000，伪用户：1-499
GID：组ID，每个用户至少属于一个组，每个组可以有多个用户，同一用户组的用户继承改组的权限
用户描述信息:针对用户的简介
HOME DIR：家目录
SHELL：用户的默认shell

# /etc/group中4段意义：组名：密码：GID:以此组为其附加组的用户列表

# /etc/shadow中8段意义：
字段1：用户帐号的名称
字段2：加密的密码字串信息
字段3：上次修改密码的时间（1970/1/1距修改时间的天数）
字段4：密码的最短有效天数，默认值为0
字段5：密码的最长有效天数，默认值为99999
字段6：提前多少天警告用户口令将过期，默认值为7
字段7：在密码过期之后多少天禁用此用户
字段8：帐号失效时间，默认值为 空
字段9：保留字段（未使用)

# 伪用户：
伪用户就是系统和程序操作时调用的用户，与系统和程序服务相关。例如：bin、daemon、shutdown、halt等，任何linux操作系统都默认
有这些伪用户，是系统自动生成的，例如：mail、apache、ftp等，与linux系统的进程相关，而且此类的用户不需要登录或者是无法登录
系统当中，同时也可以没有相应的宿主目录

# 扩展知识点：
关于建立用户设置密码的过程分析：密码先写入/etc/passwd文件的第二个字段，接着通过指令转到/etc/shadow文件中，转化的命令是psconv
过程是自动完成，回写的命令是pwunconv，执行此命令后密码会在/etc/passwd中的第二个字段X中出现且是加密的，这也说明了密码位保留的原因

登陆信息文件：/etc/motd  登录成功后会看到提示信息
修改/etc/issue文件的话，是在登录之前就可以看到；为了安全起见，防止看到操作系统的版本信息，可以修改/etc/issue文件的内容或者直接删除即可
/etc/issue.net文件中的信息是作为telnet服务的提示信息

issue 内的各代码意义表示如下所示：（管理人员可以自行添加）
/l 显示第几个终端机接口
/m 显示硬件的等级 (i386/i486/i586/i686...)
/n 显示主机的网络名称
/o 显示 domain name
/r 操作系统的版本 (相当于 uname -r)
/t 显示本地端时间的时间
/s 操作系统的名称
/v 操作系统的版本
```

### 权限：r, w, x

```markdown
# 文件权限：
r--4：可读
w--2：可写
x--1: 可执行，eXacutable
# 目录权限：
w: 可以在此目录创建文件
r: 可以对此目录执行ls以列出内部的所有文件
x: 可以使用cd切换进此目录，也可以使用ls -l查看内部文件的详细信息
# 权限三位一体：
rwx:可读可学可执行
r--:只读
r-x:读和执行
---：无权限
# 八进制表示：
0 000 ---：无权限
1 001 --x: 执行
2 010 -w-: 写
3 011 -wx: 写和执行
4 100 r--: 只读
5 101 r-x: 读和执行
6 110 rw-: 读写
7 111 rwx: 读写执行
# 例如：
rw-r-----: 640
rw-r--r--: 644
rw-rw----: 660
rwxr-xr-x: 755
rwxrwxr-x: 775
```

### 三、特殊权限

```markdown
特殊权限：s,s,t
1. SUID: 运行某程序时，相应进程的属主是程序文件自身的属主，而不是动者
格式：chmod u+s FILE  ，chmod u-s FILE
注意：如果FILE本身原来就有执行权限，则SUID显示为s；否则显示S

2. SGID: 运行某程序时，相应进程的属组是程序文件自身的属组，而不是动者所属的基本组
格式：chmod g+s FILE  ， chmod g-s FILE
注意：如果FILE本身原来就有执行权限，则SUID显示为s；否则显示S

3. Sticky bit(粘滞位): 在一个公共目录，每个都可以创建文件，删除自己的文件，但不能删除别人的文件
                      当⽬录被设置了粘滞位权限以后，即便⽤户对该⽬录有写⼊权限，也不能删除该⽬录
                      中其他⽤户的⽂件数据，⽽是只有该⽂件的所有者和root⽤户才有权将其删除
格式：chmod o+t DIR  ， chmod o-t DIR
注意：如果FILE本身原来就有执行权限，则SUID显示为t；否则显示T；粘滞位权限是针对目录的，对文件无效

4. umask：
文件默认权限：666-umask
文件夹默认权限：777-umask
特殊权限默认为0
默认umask：umask=0022
更改umask：umask 0023
注意：文件默认不能具有执行权限，如果算得的结果中有执行权限，则将权限加1
```

## 系统命令行快捷键

```markdown
# 说明
Ctrl – k      # 先按住 Ctrl 键，然后再按 k 键
Alt – k       # 先按住 Alt 键，然后再按 k 键
M – k         # 先单击 Esc 键，然后再按 k 键

# 移动光标
Ctrl - 左右键  # 在单词之间跳转
Ctrl – a      # 移到行首
Ctrl – e      # 移到行尾
Ctrl – b      # 往回(左)移动一个字符
Ctrl – f      # 往后(右)移动一个字符
Alt – b       # 往回(左)移动一个单词
Alt – f       # 往后(右)移动一个单词
Ctrl – xx     # 在命令行尾和光标之间移动
M-b           # 往回(左)移动一个单词
M-f           # 往后(右)移动一个单词

# 编辑命令
Ctrl – h      # 删除光标左方位置的字符
Ctrl – d      # 删除光标右方位置的字符（注意当前命令行没有任何字符时，会注销系统或结束终端）
Ctrl – w      # 由光标位置开始，往左删除单词。往行首删
Alt – d       # 由光标位置开始，往右删除单词。往行尾删
M – d         # 由光标位置开始，删除单词，直到该单词结束
Ctrl – k      # 由光标所在位置开始，删除右方所有的字符，直到该行结束
Ctrl – u      # 由光标所在位置开始，删除左方所有的字符，直到该行开始
Ctrl – y      # 粘贴Ctrl+u或ctrl+k剪切的内容
ctrl – t      # 交换光标处和之前两个字符的位置
Alt + .       # 使用上一条命令的最后一个参数
Ctrl – _      # 回复之前的状态。撤销操作
Ctrl -a + Ctrl -k 或 Ctrl -e + Ctrl -u 或 Ctrl -k + Ctrl -u # 组合可删除整行

# 控制命令
Ctrl – l      # 清除屏幕，然后，在最上面重新显示目前光标所在的这一行的内容
Ctrl – o      # 执行当前命令，并选择上一条命令
Ctrl – s      # 阻止屏幕输出
Ctrl – q      # 允许屏幕输出
Ctrl – c      # 终止命令
Ctrl – z      # 挂起命令

# Bang(!)命令
!!            # 执行上一条命令
^foo^bar      # 把上一条命令里的foo替换为bar，并执行
!wget         # 执行最近的以wget开头的命令
!wget p       # 仅打印最近的以wget开头的命令，不执行
!$            # 上一条命令的最后一个参数， 与 Alt - . 和 $_ 相同
!*            # 上一条命令的所有参数
!* p          # 打印上一条命令是所有参数，也即 !*的内容
^abc          # 删除上一条命令中的abc
^foo^bar      # 将上一条命令中的 foo 替换为 bar
^foo^bar^     # 将上一条命令中的 foo 替换为 bar
!-n           # 执行前n条命令，执行上一条命令      !-1， 执行前5条命令的格式是!-5

# 查找历史命令
Ctrl – p      # 显示当前命令的上一条历史命令
Ctrl – n      # 显示当前命令的下一条历史命令
Ctrl – r      # 搜索历史命令，输入后显示历史命令中一条匹配命令，Enter键执行匹配命令；ESC键在命令行显示而不执行匹配命令
Ctrl – g      # 从历史搜索模式（Ctrl – r）退出

# 重复执行操作动作
M – 操作次数 操作动作   # 指定操作次数，重复执行指定的操作
```

## Centos源设置

```bash
# 更改yum源为阿里云    # 备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
# 下载新的对应版本CentOS-Base.repo 到/etc/yum.repos.d/
## CentOS 5
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo

## CentOS 6
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo

## CentOS 7
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

# 更改yum源为163    # 备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
# 下载对应版本repo文件, 放入/etc/yum.repos.d/(操作前请做好相应备份)
CentOS5 http://mirrors.163.com/.help/CentOS5-Base-163.repo
CentOS6 http://mirrors.163.com/.help/CentOS6-Base-163.repo
CentOS7 http://mirrors.163.com/.help/CentOS7-Base-163.repo

yum clean all       # 运行以下命令生成缓存
yum makecache fast  # 之后运行yum makecache生成缓存
yum -y upgrade  # 只升级所有包，不升级软件和系统内核
yum -y update   # 升级所有包同时也升级软件和系统内核

# 在线安装epel源
rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum -y install epel-release
```

## ubuntu Desktop相关设置

```bash
xdotool mouseup 1 # 挂起后进入系统输入,然后注销重新登录
gsettings set com.canonical.unity.launcher launcher-position bottom # 将ubuntu启动栏移动到最下方
gsettings get com.canonical.unity.launcher launcher-position    # 得到启动栏位置

# 设置临时文件不再放置在硬盘加快速度，而放在虚拟RAM磁盘上。总内存必须大于4G
cp -v /usr/share/systemd/tmp.mount /etc/systemd/system/
systemctl enable tmp.mount
systemctl reload tmp.mount # 不生效可重启计算机。systemctl status tmp.mount检查状态
# 取消tmpfs,删除后重启生效
rm -v /etc/systemd/system/tmp.mount

: << comment
# 减少文件写入(当用于非服务器时)在/etc/fstab写入以下内容：
tmpfs    /tmp        tmpfs    defaults    0  0
tmpfs    /var/tmp    tmpfs    defaults    0  0
tmpfs    /var/log    tmpfs    defaults    0  0
comment

# 自定义terminal位置和大小
cp /usr/share/applications/gnome-terminal.desktop ~/.local/share/applications
sed -i 's/^Exec=gnome-terminal$/& --geometry=80x24+100+100/' ~/.local/share/applications/gnome-terminal.desktop
gsettings set org.gnome.desktop.default-applications.terminal exec 'gnome-terminal --geometry=80x24+100+100'
# 删除自定义
rm ~/.local/share/applications/gnome-terminal.desktop
gsettings set org.gnome.desktop.default-applications.terminal exec 'gnome-terminal'

```

## BASH环境变量的设置方法

```bash
# 方法一：修改用户主目录下的.profile或.bashrc文件(推荐)，在文件末尾加入PATH的设置如下：该方式添加的变量只对当前用户有效
export PATH="$PATH:your path1:your path2"

# 方法二：修改编辑系统目录下的profile文件(谨慎)：在最后加入PATH的设置如下：该方式添加的变量对所有的用户都有效
export PATH="$PATH:your path1:your path2"

# 方法三：修改系统目录下的 environment 文件的PATH变量(谨慎),各个path之间冒号分割，影响所有用户

# 方法四：直接在终端下输入,这种方式变量立即生效，但用户注销或系统重启后设置变成无效，适合临时变量的设置
export PATH="$PATH:your path1:your path2"

# 保存后执行source命令生效
```

## Linux安装设置java环境

```markdown
# 在CentOS7安装Oracle Java JDK8
rpm -qa | grep -E '^open[jre|jdk]|j[re|dk]'    # 查找已安装的jdk组件
yum remove java-$Version   # 卸载之前已安装的jdk
# 在/usr目录创建java文件夹，下载jdk包http://www.oracle.com/technetwork/java/javase/downloads/index.html
mkdir /usr/java
wget http://download.oracle.com/otn-pub/java/jdk/8u161-b12/...tar.gz
tar -zxvf jdk..
# 编辑.bashrc文件，在export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTCONTROL下面添加如下代码：
export JAVA_HOME=/usr/java/jdk-$version
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
# 配置生效并验证
source .bashrc && java --version
```

## SSH相关命令和配置

### ssh基本加固建议

```markdown
使用非密码登陆方式如密钥对，请忽略此项。在 /etc/login.defs 中将 PASS_MAX_DAYS 参数设置为 60-180之间
如 PASS_MAX_DAYS 90。需同时执行命令设置root密码失效时间： chage --maxdays 90 root

在 /etc/login.defs 中将 PASS_MIN_DAYS 参数设置为7-14之间,建议为7： PASS_MIN_DAYS 7 需同时执行命令
为root用户设置： chage --mindays 7 root

编辑/etc/security/pwquality.conf，把minlen（密码最小长度）设置为9-32位，把minclass（至少包含小写字母、
大写字母、数字、特殊字符等4类字符中等3类或4类）设置为3或4。如： minlen=10 minclass=3
```

### sed命令更改默认ssh连接端口

```bash
iptables -I INPUT -p tcp --dport 22 -j ACCEPT # iptables开放22端口
sed -i "/A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT/d" /etc/sysconfig/iptables
iptables save
service iptables restart

sed -i 's/#Port 22/Port 2121/' /etc/ssh/sshd_config     # 修改sshd_config文件，将#Port 22直接替换成Port 2121

```

### sshd_config配置详解（/etc/ssh/sshd_config）

```markdown
# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value

#
# AddressFamily指定sshd应当使用哪种地址族。取值范围是："any"(默认)、"inet"(仅IPv4)、"inet6"(仅IPv6)
#AddressFamily any

# 指定sshd监听的网络地址，默认监听所有地址。可以使用下面的格式：
# ListenAddress host|IPv4_addr|IPv6_addr
# ListenAddress host|IPv4_addr:port
# ListenAddress [host|IPv6_addr]:port
# 如果未指定 port ，那么将使用 Port 指令的值
# 可以使用多个 ListenAddress 指令监听多个地址
#ListenAddress 0.0.0.0
#ListenAddress ::

# HostKey
# 主机私钥文件的位置。如果权限不对，sshd可能会拒绝启动
# SSH-1默认是 /etc/ssh/ssh_host_key
# SSH-2默认是 /etc/ssh/ssh_host_rsa_key 和 /etc/ssh/ssh_host_dsa_key
# 一台主机可以拥有多个不同的私钥。"rsa1"仅用于SSH-1，"dsa"和"rsa"仅用于SSH-2
#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key

# 指定SSH-2允许使用的加密算法。多个算法之间使用逗号分隔。可以使用的算法如下：
# "aes128-cbc", "aes192-cbc", "aes256-cbc", "aes128-ctr", "aes192-ctr", "aes256-ctr",
# "3des-cbc", "arcfour128", "arcfour256", "arcfour", "blowfish-cbc", "cast128-cbc"
# 默认值是可以使用上述所有算法
# Ciphers and keying
#RekeyLimit default none

# 指定sshd将日志消息通过哪个日志子系统(facility)发送。有效值是：DAEMON, USER, AUTH(默认), LOCAL0, LOCAL1, LOCAL2, LOCAL3,
# LOCAL4, LOCAL5, LOCAL6, LOCAL7
# Logging
#SyslogFacility AUTH

# 指定 sshd(8) 的日志等级(详细程度)。可用值如下：
# QUIET, FATAL, ERROR, INFO(默认), VERBOSE, DEBUG, DEBUG1, DEBUG2, DEBUG3
# DEBUG 与 DEBUG1 等价；DEBUG2 和 DEBUG3 则分别指定了更详细、更罗嗦的日志输出
# 比 DEBUG 更详细的日志可能会泄漏用户的敏感信息，因此反对使用
#LogLevel INFO

# Authentication:

# 限制用户必须在指定的时限内认证成功，0 表示无限制。默认值是 120 秒
#LoginGraceTime 2m

# 是否允许 root 登录。可用值如下：
# "yes"(默认) 表示允许。"no"表示禁止
# "without-password"表示禁止使用密码认证登录
# "forced-commands-only"表示只有在指定了command选项时才允许使用公钥认证登录。同时其它认证方法全部被禁止。该值常用于做远程备份之类的事情
#PermitRootLogin prohibit-password

# 指定是否要求sshd在接受连接请求前对用户主目录和相关的配置文件进行宿主和权限检查。强烈建议使用默认值"yes"来预防可能出现的低级错误
#StrictModes yes

# 指定每个连接最大允许的认证次数。默认值是 6
# 如果失败认证的次数超过这个数值的一半，连接将被强制断开，且会生成额外的失败日志消息
#MaxAuthTries 6

#MaxSessions 10

# 是否允许公钥认证。仅可以用于SSH-2。默认值为"yes"
#PubkeyAuthentication yes

######################################################################################################################

# 存放该用户可以用来登录的 RSA/DSA 公钥
# 该指令中可以使用下列根据连接时的实际情况进行展开的符号：
# %% 表示'%'、%h 表示用户的主目录、%u 表示该用户的用户名
# 经过扩展之后的值必须要么是绝对路径，要么是相对于用户主目录的相对路径
# 默认值是".ssh/authorized_keys"
# Expect .ssh/authorized_keys2 to be disregarded by default in future
#AuthorizedKeysFile .ssh/authorized_keys .ssh/authorized_keys2

######################################################################################################################

#AuthorizedPrincipalsFile none

#AuthorizedKeysCommand none
#AuthorizedKeysCommandUser nobody

# 是否使用强可信主机认证(通过检查远程主机名和关联的用户名进行认证)。仅用于SSH-1
# 这是通过在RSA认证成功后再检查 ~/.rhosts 或 /etc/hosts.equiv 进行认证的
# 出于安全考虑，建议使用默认值"no"
#RhostsRSAAuthentication

# 这个指令与 RhostsRSAAuthentication 类似，但是仅可以用于SSH-2。推荐使用默认值"no"
# 推荐使用默认值"no"禁止这种不安全的认证方式
# For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
#HostbasedAuthentication no

#HostbasedUsesNameFromPacketOnly
# 在开启 HostbasedAuthentication 的情况下
# 指定服务器在使用 ~/.shosts ~/.rhosts /etc/hosts.equiv 进行远程主机名匹配时，是否进行反向域名查询
# "yes"表示sshd信任客户端提供的主机名而不进行反向查询。默认值是"no"

# 是否在 RhostsRSAAuthentication 或 HostbasedAuthentication 过程中忽略用户的 ~/.ssh/known_hosts 文件
# 默认值是"no"。为了提高安全性，可以设为"yes"
# Change to yes if you don't trust ~/.ssh/known_hosts for
# HostbasedAuthentication
#IgnoreUserKnownHosts no

# 是否在 RhostsRSAAuthentication 或 HostbasedAuthentication 过程中忽略 .rhosts 和 .shosts 文件
# 不过 /etc/hosts.equiv 和 /etc/shosts.equiv 仍将被使用。推荐设为默认值"yes"
# Don't read the user's ~/.rhosts and ~/.shosts files
#IgnoreRhosts yes


# To disable tunneled clear text passwords, change to no here!

# 是否允许使用基于密码的认证。默认为"yes"
#PasswordAuthentication yes

# 是否允许密码为空的用户远程登录。默认为"no"
#PermitEmptyPasswords no

# 是否允许质疑-应答(challenge-response)认证。默认值是"yes"
# 所有 login.conf(5) 中允许的认证方式都被支持
# Change to yes to enable challenge-response passwords (beware issues with
# some PAM modules and threads)
ChallengeResponseAuthentication no

# Kerberos options

# 是否要求用户为 PasswordAuthentication 提供的密码必须通过 Kerberos KDC 认证，也就是是否使用Kerberos认证
# 要使用Kerberos认证，服务器需要一个可以校验 KDC identity 的 Kerberos servtab 。默认值是"no"
#KerberosAuthentication no

# 如果 Kerberos 密码认证失败，那么该密码还将要通过其它的认证机制(比如 /etc/passwd)
# 默认值为"yes"
#KerberosOrLocalPasswd yes

# 是否在用户退出登录后自动销毁用户的 ticket 。默认值是"yes"
#KerberosTicketCleanup yes

# 如果使用了 AFS 并且该用户有一个 Kerberos 5 TGT，那么开启该指令后
# 将会在访问用户的家目录前尝试获取一个 AFS token 。默认为"no"
#KerberosGetAFSToken no

# GSSAPI options

# 是否允许使用基于 GSSAPI 的用户认证。默认值为"no"。仅用于SSH-2
#GSSAPIAuthentication no

# 是否在用户退出登录后自动销毁用户凭证缓存。默认值是"yes"。仅用于SSH-2
#GSSAPICleanupCredentials yes


#GSSAPIStrictAcceptorCheck yes
#GSSAPIKeyExchange no

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin without-password"
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'
UsePAM yes

#AllowAgentForwarding yes

# 是否允许TCP转发，默认值为"yes"
# 禁止TCP转发并不能增强安全性，除非禁止了用户对shell的访问，因为用户可以安装他们自己的转发器
AllowTcpForwarding yes

######################################################################################################################

# 是否允许远程主机连接本地的转发端口。默认值是"no"
# sshd(8) 默认将远程端口转发绑定到loopback地址。这样将阻止其它远程主机连接到转发端口
# GatewayPorts 指令可以让 sshd 将远程端口转发绑定到非loopback地址，这样就可以允许远程主机连接了
# "no"表示仅允许本地连接，"yes"表示强制将远程端口转发绑定到统配地址(wildcard address)
# "clientspecified"表示允许客户端选择将远程端口转发绑定到哪个地址
GatewayPorts yes

######################################################################################################################

# 是否允许进行 X11 转发。默认值是"no"，设为"yes"表示允许
# 如果允许X11转发并且sshd(8)代理的显示区被配置为在含有通配符的地址(X11UseLocalhost)上监听
# 那么将可能有额外的信息被泄漏。由于使用X11转发的可能带来的风险，此指令默认值为"no"
# 需要注意的是，禁止X11转发并不能禁止用户转发X11通信，因为用户可以安装他们自己的转发器
# 如果启用了 UseLogin ，那么X11转发将被自动禁止
X11Forwarding yes

# 指定sshdX11 转发的第一个可用的显示区(display)数字。默认值是 10
# 这个可以用于防止 sshd 占用了真实的 X11 服务器显示区，从而发生混淆
#X11DisplayOffset 10

# sshd是否应当将X11转发服务器绑定到本地loopback地址。默认值是"yes"
# sshd 默认将转发服务器绑定到本地loopback地址并将 DISPLAY 环境变量的主机名部分设为"localhost"
# 这可以防止远程主机连接到 proxy display 。不过某些老旧的X11客户端不能在此配置下正常工作
# 为了兼容这些老旧的X11客户端，你可以设为"no"
#X11UseLocalhost yes
#PermitTTY yes

# 指定sshd是否在每一次交互式登录时打印 /etc/motd 文件的内容。默认值是"yes"
PrintMotd no

# 指定sshd是否在每一次交互式登录时打印最后一位用户的登录时间。默认值是"yes"
#PrintLastLog yes

# 指定系统是否向客户端发送TCPkeepalive消息。默认"yes"。消息可以检测到死连接、连接不当关闭、客户端崩溃等异常。可以设为"no"关闭这个特性
#TCPKeepAlive yes

# 是否在交互式会话的登录过程中使用 login(1) 。默认值是"no"
# 如果开启此指令，那么 X11Forwarding 将会被禁止，因为 login(1) 不知道如何处理 xauth(1) cookies
# 需要注意的是，login(1) 是禁止用于远程执行命令的
# 如果指定了 UsePrivilegeSeparation ，那么它将在认证完成后被禁用
#UseLogin no

# 是否让sshd通过创建非特权子进程处理接入请求的方法来进行权限分离。默认值是"yes"
# 认证成功后，将以该认证用户的身份创建另一个子进程,这样做的目的是为了防止通过有缺陷的子进程提升权限，从而使系统更加安全
#UsePrivilegeSeparation sandbox

# 指定是否允许 sshd(8) 处理 ~/.ssh/environment 以及 ~/.ssh/authorized_keys 中的 environment= 选项
# 默认值是"no"。如果设为"yes"可能会导致用户有机会使用某些机制(比如 LD_PRELOAD)绕过访问控制，造成安全漏洞
#PermitUserEnvironment no

# 是否对通信数据进行压缩，还是延迟到认证成功之后再对通信数据进行压缩
# 可用值："yes", "delayed"(默认), "no"
#Compression delayed

######################################################################################################################

# 设置一个以秒记的时长，如果超过这么长时间没有收到客户端的任何数据
# sshd(8) 将通过安全通道向客户端发送一个"alive"消息，并等候应答
# 默认值 0 表示不发送"alive"消息。这个选项仅对SSH-2有效
#ClientAliveInterval 0

######################################################################################################################

# sshd在未收到任何客户端回应前最多允许发送多少个"alive"消息。默认值是 3
# 到达这个上限后，sshd(8) 将强制断开连接、关闭会话
# 需要注意的是，"alive"消息与 TCPKeepAlive 有很大差异
# "alive"消息是通过加密连接发送的，因此不会被欺骗；而 TCPKeepAlive 却是可以被欺骗的
# 如果 ClientAliveInterval 被设为 15 并且将 ClientAliveCountMax 保持为默认值
# 那么无应答的客户端大约会在45秒后被强制断开。这个指令仅可以用于SSH-2协议
#ClientAliveCountMax 3

######################################################################################################################

# 指定sshd是否应该对远程主机名进行反向解析，以检查此主机名是否与其IP地址真实对应。默认值为"yes"
#UseDNS no

# 指定在哪个文件中存放SSH守护进程的进程号，默认为 /var/run/sshd.pid 文件
#PidFile /var/run/sshd.pid

# 最大允许保持多少个未认证的连接。默认值是 10
# 到达限制后，将不再接受新连接，除非先前的连接认证成功或超出 LoginGraceTime 的限制
#MaxStartups 10:30:100

# 是否允许 tun(4) 设备转发。可用值如下：
# "yes", "point-to-point"(layer 3), "ethernet"(layer 2), "no"(默认)
# "yes"同时蕴含着"point-to-point"和"ethernet"
#PermitTunnel no

#ChrootDirectory none
#VersionAddendum none

# 这个特性仅能用于SSH-2，默认什么内容也不显示。"none"表示禁用这个特性
# no default banner path
#Banner none

######################################################################################################################
# AcceptEnv
# 指定客户端发送的哪些环境变量将会被传递到会话环境中。[注意]只有SSH-2协议支持环境变量的传递
# 细节可以参考/etc/ssh/ssh_config中的 SendEnv 配置指令
# 指令的值是空格分隔的变量名列表(其中可以使用'*'和'?'作为通配符)。也可以使用多个 AcceptEnv 达到同样的目的
# 需要注意的是，有些环境变量可能会被用于绕过禁止用户使用的环境变量。由于这个原因，该指令应当小心使用
# 默认是不传递任何环境变量
# Allow client to pass locale environment variables
AcceptEnv LANG LC_*

######################################################################################################################

# 系统内开启ssh服务和sftp服务都是通过/usr/sbin/sshd这个后台程序监听22端口，而sftp服务作为一个子服务，是通过/etc/ssh/sshd_config配置
# 文件中的Subsystem实现的，如果没有配置Subsystem参数，则系统是不能sftp访问的
# override default of no subsystems
Subsystem   sftp    /usr/lib/openssh/sftp-server

# AllowGroups
# 这个指令后面跟着一串用空格分隔的组名列表(其中可以使用"*"和"?"通配符)。默认允许所有组登录
# 如果使用了这个指令，那么将仅允许这些组中的成员登录，而拒绝其它所有组
# 这里的"组"是指"主组"(primary group)，也就是/etc/passwd文件中指定的组
# 这里只允许使用组的名字而不允许使用GID。相关的 allow/deny 指令按照下列顺序处理：
# DenyUsers, AllowUsers, DenyGroups, AllowGroups
#AllowGroups<组名1> <组名2> <组名3> ..
##指定允许通过远程访问的组，多个组以空格隔开。当多个用户需要通过ssh登录系统时，可将所有用户加入一个组中

# DenyGroups
# 这个指令后面跟着一串用空格分隔的组名列表(其中可以使用"*"和"?"通配符)。默认允许所有组登录
# 如果使用了这个指令，那么这些组中的成员将被拒绝登录
# 这里的"组"是指"主组"(primary group)，也就是/etc/passwd文件中指定的组
# 这里只允许使用组的名字而不允许使用GID。相关的 allow/deny 指令按照下列顺序处理：
# DenyUsers, AllowUsers, DenyGroups, AllowGroups
#DenyGroups<组名1> <组名2> <组名3> ..
##指定禁止通过远程访问的组，多个组以空格隔开

######################################################################################################################

# AllowUsers
# 这个指令后面跟着一串用空格分隔的用户名列表(其中可以使用"*"和"?"通配符)。默认允许所有用户登录
# 如果使用了这个指令，那么将仅允许这些用户登录，而拒绝其它所有用户
# 如果指定了 USER@HOST 模式的用户，那么 USER 和 HOST 将同时被检查。# override default of no subsystems
# 这里只允许使用用户的名字而不允许使用UID。相关的 allow/deny 指令按照下列顺序处理：Subsystem sftp /usr/lib/openssh/sftp-server
# DenyUsers, AllowUsers, DenyGroups, AllowGroups
#AllowUsers<用户名1> <用户名2> <用户名3> ..
##指定允许通过远程访问的用户，多个用户以空格隔开

# DenyUsers
# 这个指令后面跟着一串用空格分隔的用户名列表(其中可以使用"*"和"?"通配符)。默认允许所有用户登录
# 如果使用了这个指令，那么这些用户将被拒绝登录
# 如果指定了 USER@HOST 模式的用户，那么 USER 和 HOST 将同时被检查
# 这里只允许使用用户的名字而不允许使用UID。相关的 allow/deny 指令按照下列顺序处理：
# DenyUsers, AllowUsers, DenyGroups, AllowGroups
#DenyUsers<用户名1> <用户名2> <用户名3> ..
##指定禁止通过远程访问的用户，多个用户以空格隔开

######################################################################################################################

# Match
# 引入一个条件块。块的结尾标志是另一个 Match 指令或者文件结尾
# 如果 Match 行上指定的条件都满足，那么随后的指令将覆盖全局配置中的指令
# Match 的值是一个或多个"条件-模式"对。可用的"条件"是：User, Group, Host, Address
# 只有下列指令可以在 Match 块中使用：AllowTcpForwarding, Banner,
# ForceCommand, GatewayPorts, GSSApiAuthentication,
# KbdInteractiveAuthentication, KerberosAuthentication,
# PasswordAuthentication, PermitOpen, PermitRootLogin,
# RhostsRSAAuthentication, RSAAuthentication, X11DisplayOffset,
# X11Forwarding, X11UseLocalHost

# 指定TCP端口转发允许的目的地，可以使用空格分隔多个转发目标。默认允许所有转发请求
# 合法的指令格式如下：
# PermitOpen host:port
# PermitOpen IPv4_addr:port
# PermitOpen [IPv6_addr]:port
# "any"可以用于移除所有限制并允许一切转发请求

# Example of overriding settings on a per-user basis
#Match User anoncvs
#   X11Forwarding no
#   AllowTcpForwarding no
#   PermitTTY no

#   ForceCommand cvs server
#   ForceCommand
#   强制执行这里指定的命令而忽略客户端提供的任何命令。这个命令将使用用户的登录shell执行(shell -c)
#   这可以应用于 shell 、命令、子系统的完成，通常用于 Match 块中
#   这个命令最初是在客户端通过 SSH_ORIGINAL_COMMAND 环境变量来支持的

PermitRootLogin yes

# 指定sshd守护进程监听的端口号，默认为 22 。可以使用多条指令监听多个端口
# 默认将在本机的所有网络接口上监听，但是可以通过 ListenAddress 指定只在某个特定的接口上监听
Port 22

```

### SSH端口转发

SSH端口转发将其他TCP端口的网络数据通过SSH链路转发，并自动加密和解密

#### 本地转发实例

```markdown
# 命令格式：ssh -L <local port>:<remote host>:<remote port> <SSH hostname>

# 实例：一台LDAP server，但限制远程机器连接，此时利用ssh端口转发实现从远程机器测试server的目的(这里的远程机器是相对于server来讲的)
# LDAP client执行此命令建立ssh本地端口转发，实现通过ssh隧道从本地7001端口连接远端的389端口
ssh -L 7001:localhost:389 LDAPServer

# SSH 同时提供了 GatewayPorts 关键字，我们可以通过指定它与其他机器共享这个本地端口转发
ssh -g -L <local port>:<remote host>:<remote port> <SSH hostname>

......................|||.........................
......LDAP Client.....|||......LDAP Server........
......................|||.........................
.......########.......|||.........................
.......########.......|||.....############........
.......########.......|||.....############........
......@@@@@@@@@@......|||.....#@@@@#######........
.....############.....|||.....############........
.....############.....|||.....############........
...Local Port 7001....|||.......LDAP Port 389.....
........↘.............|||..............↗..........
.....SSH Clinet...▒▒▒▒▒▒▒.....SSH Server.....
...................SSH tunnel.....................
..................................................

# 本地服务器LDAP Client若提示无法解析hostname LDAPServer,则在/etc/hosts文件添加localhost LDAPServer即可
# 在选择端口号时要注意非管理员帐号是无权绑定 1-1023 端口的，所以一般是选用一个 1024-65535 之间的并且尚未使用的端口号即可
# 然后我们可以将远程机器（LDAP Client）上的应用直接配置到本机的 7001 端口上（而不是 LDAP 服务器的 389 端口上）
```

#### 远程转发实例

```markdown
假设由于网络或防火墙的原因我们不能用SSH直接从LDAP Client Host连接到LDAP服务器（Ldap Server Host），但是反向连接却是被允许的,
此时我们的选择自然就是远程端口转发了
# 命令格式：
ssh -R <local port>:<remote host>:<remote port> <SSH hostname>
# 在LDAP服务器（LdapServertHost）端执行如下命令：
# -n阻止从stdin读取，因为您不想从命令行使用隧道
# -N表示您不想执行远程命令，只需执行端口转发
ssh -nN -R 7001:localhost:389 LdapClientHost

......................|||.........................
......LDAP Client.....|||......LDAP Server........
......................|||.........................
.......########.......|||.........................
.......########.......|||.....############........
.......########.......|||.....############........
......@@@@@@@@@@......|||.....#@@@@#######........
.....############.....|||.....############........
.....############.....|||.....############........
...Local Port 7001....|||.......LDAP Port 389.....
........↘.............|||..............↗..........
.....SSH Server...▒▒▒▒▒▒▒.....SSH Client.....
...................SSH tunnel.....................
..................................................
```

#### 多主机转发应用实例

```markdown
# 本地转发命令中的<remote host>和<SSH hostname>可以是不同的机器
ssh -L <local port>:<remote host>:<remote port> <SSH hostname>
# 看一个涉及到四台机器 (A,B,C,D) 的例子,在 SSH Client(C) 执行下列命令来建立 SSH 连接以及端口转发：
ssh -g -L 7001:<B>:389 <D>

然后应用客户端（A）上配置连接机器（C ）的 7001 端口即可,在上述连接中，（A）<-> (C) 以及 (B)<->(D) 之间的连接并不是安全连接
它们之间没有经过 SSH 的加密及解密

........................................................................................
......................................|||...............................................
......................................|||...............................................
.............#############............|||...............................................
.............#############............|||...............................................
.............LDAP Clinet(A)...........|||...........LADP Server(B)......................
.............#############............|||...............................................
.............#############............|||..........#################....................
.............@@@@@@@@@@@@@............|||..........##@@@@@@#########....................
...........#################..........|||..........#################....................
...........#################..........|||..........#################....................
..............LDAP App................|||.............LDAP Port 389.....................
..................↑...................|||...................↑...........................
..................↓...................|||...................↓...........................
..............SSH Client(C)...........|||..............SSH Server(D)....................
...........@@@@@@@@@@@@@@@@@..........|||............@@@@@@@@@@@@@@@@@..................
...........#################..........|||............#################..................
...........#################..........|||............#################..................
............Local Port 7001...........|||...............................................
..............SSH Client...▒▒▒▒▒▒▒▒▒▒▒▒▒▒...SSH Server........................
....................................SSH tunnel..........................................
........................................................................................
```

#### 动态转发实例

```markdown
# 当我们在一个不安全的 WiFi 环境下上网，用 SSH 动态转发来保护我们的网页浏览及 MSN 信息无疑是十分必要的。实际是创建了一个socks代理服务
# 命令格式：ssh -D <local port> <SSH Server>
此时直接使用 localhost:7001 来作为正常的 SOCKS 代理来使用,SSH 所包护的范围只包括从浏览器端（SSH Client 端）到 SSH Server 端的连接
并不包含从 SSH Server 端 到目标网站的连接。如果后半截连接的安全不能得到充分的保证的话，这种方式仍不是合适的解决方案

...........................................................................................
.....................................|||...................................................
.....................................|||...................................................
...........Brower、MSN Client........|||.......________________________________............
............@############@...........|||.......|::::::::::::::::::::::::::::::|............
............@############@...........|||.......|::::::::WEB Site,MSN...:::::::|............
............@############@...........|||.......|::::::::::::::::::::::::::::::|............
..........@################@.........|||.......|::::::::::::::::::::::::::::::|............
..........@################@.........|||.......|::::::::::::::::::::::::::::::|............
..........@################@.........|||.......--------------------------------............
..............Port 7001..............|||.............Dynamic Port(动态端口).................
..............SSH Clinet....▒▒▒▒▒▒▒▒▒▒▒▒.....SSH Server...........................
...................................SSH tunnel..............................................
.....................................|||...................................................
.....................................|||...................................................
...........................................................................................
```

#### X协议转发实例

```markdown
# 可用作远程桌面连接，被远程端是Windows可安装xming作为xserver。ssh client可以选择putty配置ssh访问并建立x-forward（转发）
在X Server端执行一下命令，发起一个SSH连接。然后在X Client端运行远程X桌面连接客户端
ssh -X <SSH Server>

..................................................
......X Server........|||......X Clinet...........
......................|||.........................
.......########.......|||.........................
.......########.......|||.....############........
.......########.......|||.....############........
......@@@@@@@@@@......|||.....#@@@@#######........
.....############.....|||.....############........
.....############.....|||.....############........
..........↑...........|||..DISPLAY=locahost:10.1..
..........↓...........|||..............↓..........
.....SSH Client...▒▒▒▒▒▒▒.....SSH Server.....
...................SSH tunnel.....................
..................................................

```

## 主机调整 [es.net]

[网络故障排查参考](http://fasterdata.es.net/performance-testing/troubleshooting)

```markdown
## 关于超大型数据集传输问题(引用es.net)
在Unix环境中，scp，sftp和rsync通常用于在主机之间复制数据。虽然这些工具在本地环境中运行良好，但它们在WAN上的性能很差。scp和sftp
的openssh版本具有内置的1MB缓冲区，严重限制了WAN上的性能。尽管rsync不是openssh发行版的一部分，但rsync通常使用ssh作为传输，因此
受到底层ssh实现所施加的限制。 如果您需要在RTT超过20毫秒的网络路径上传输大型数据集，请不要使用这些工具
匹兹堡超级计算机中心提供了解决scp和sftp关于这个问题的补丁：HPN-SSH：https://www.psc.edu/hpn-ssh

# 根据es net官网TCP注意事项
注意：应该单独使用net.tcp_mem，因为默认设置没问题。一些性能专家说，还要增加net.core.optmem_max以匹配net.core.rmem_max
和net.core.wmem_max，但我们没有发现这有任何区别。一些专家还说将net.ipv4.tcp_timestamps和net.ipv4.tcp_sack设置为0
这样做可以减少CPU负载，但默认值是1对于性能其实更有帮助

对于较旧的操作系（例如CentOS5/6），增加net.core.netdev_max_backlog值也可以提高性能

# UDP调整

1. 使用jumbo frames在9k MTUs下可以使性能提高4-5倍
2. 数据包大小：最好的性能是MTU大小减去数据包头大小，例如：9000Byte MTU，ipv4是8972，ipv6是8952
3. socket缓存大小：对于UDP，缓冲区大小与TCP的方式无关，但默认值仍然不够大。在大多数情况下，将套接字缓冲区设置为4M似乎有很大帮助
4. CPU选择：10G的UDP通常是CPU限制，因此需要考虑内核，尤其在Sandy或lvy Bridge主板

# 虚拟机调整

## XEN、KVM类型
要将所有的主机处理器功能传递给guest虚拟机执行
qemu -cpu host

如果希望保留兼容性，则可以向guest虚拟机公开所选功能。如果所有主机都具有这些功能，则会保留兼容性
qemu -cpu qemu64,+ssse3,+sse4.1,+sse4.2,+x2apic

要查看主机CPU与guest虚拟机功能之间的区别，只需比较每个系统上以下命令的输出
grep flags /proc/cpuinfo | uniq

qemu默认使用用户模式网络(slirp)，无需事先安装即可在主机使用管理权限，但是很慢，获得高性能可通过-net tap命令行开关切换到桥接模式
qemu -net nic,model = virtio,mac=...-net tap,ifname=...
或
qemu -net nic,model = virtio,mac=... -net bridge,br=...,helper=/usr/local/libexec/qemu-bridge-helper

除了virtio之外，vt-d passthrough方法可提供更高的性能
qemu -net none -device vfio-pci,host=...

QEMU支持各种存储格式和后端。最容易使用的是raw和qcow2格式，但为了获得最佳性能，最好使用原始分区。创建逻辑卷或分区并将其分配给guest虚拟机：
qemu -drive file=/dev/mapper/ImagesVolumeGroup-Guest1,cache=none,if=virtio

QEMU还支持各种缓存模式。如果您使用的是原始卷或分区，最好完全避免缓存，这会减少数据副本和总线流量

qemu -drive file=/dev/mapper/ImagesVolumeGroup-Guest1,cache=none,if=virtio

与网络一样，QEMU支持多种存储接口。默认值IDE受到来宾的高度支持，但可能很慢，尤其是磁盘阵列。如果您的guest虚拟机支持它，请使用virtio接口
不要在主机上使用linux文件系统btrfs来获取图像文件。这将导致低IO性能。当客户端进行高IO流量时，kvm guest虚拟机甚至可能会冻结

qemu -drive file=/dev/mapper/ImagesVolumeGroup-Guest1,cache=none,if=virtio

## VMware类型

[VMware兼容性指南](http://www.vmware.com/resources/compatibility/search.php)

```

## 优化内核参数 [根据实际情况调整]

修改内核参数方法:

echo追加到文件里如：echo "1" >/proc/sys/net/ipv4/tcp_syn_retries，或使用sysctl -w命令，但重启后失效,把参数添加到
/etc/sysctl.conf中，然后执行sysctl -p使参数生效，永久生效。内核生产环境常用优化参数参考，具体优化值要参考应用场景和设备

```markdown
# 下列参数设置后，文件所在目录为：/proc/sys/kernel/

# 开启execshild,即内存缓冲区溢出保护
kernel.exec-shield = 1
kernel.randomize_va_space = 1

kernel.sysrq = 0    # 控制内核的系统请求调试功能，为安全起见设为0关闭
kernel.core_uses_pid = 1    # 控制核心转储是否将PID附加到核心文件名,用于调试多线程应用程序
kernel.msgmnb = 65536   # 每个消息队列的大小（单位：字节）限制
kernel.msgmax = 65536   # 整个系统最大消息队列数量限制
kernel.shmmax = 67108864 # 增加分配给shm(单个共享内存段)的最大内存量(单位：字节)限制，计算公式64G * 1024 * 1024 * 1024(字节)
kernel.shmall = 4294967296  # 所有内存大小(单位：页，1页 = 4Kb)，计算公式16G * 1024 * 1024 * 1024/4KB(页)

# 下列参数设置后，文件所在目录为：/proc/sys/net/bridge/

# bridge-nf使得netfilter可以对Linux网桥上的IPv4/ARP/IPv6包过滤，以下是常用配置，1代表启用，0代表不启用
net.bridge.bridge-nf-call-arptables = 0     # 是否启用在arptables的FORWARD中过滤网桥的ARP包(默认：0)
net.bridge.bridge-nf-call-ip6tables = 0     # 是否启用在ip6tables链中过滤IPv6包(默认：0)
net.bridge.bridge-nf-call-iptables = 0      # 是否启用在iptables链中过滤IPv4包(默认：0)
net.bridge.bridge-nf-filter-vlan-tagged = 0 # 是否启用在iptables/arptables中过滤打了vlan标签的包(默认：0)

# 下列参数设置后，文件所在目录为：/proc/sys/net/core/

net.core.rmem_default = 65535   # 为TCP socket预留用于接收缓冲的内存默认值（单位：字节）
net.core.rmem_max = 25165824     # 为TCP socket预留用于接收缓冲的内存最大值（单位：字节）(24MB)
net.core.wmem_default = 65535   # 为TCP socket预留用于发送缓冲的内存默认值（单位：字节）
net.core.wmem_max = 25165824     # 为TCP socket预留用于发送缓冲的内存最大值（单位：字节）
net.core.hot_list_length = 256  # 增加要缓存的skb-heads数目
net.core.optmem_max = 40960     # 增加内存缓冲区的最大数量(默认：20480)
net.core.busy_poll = 50 # 轮询有助于减少网络接收路径中的延迟,允许套接字层代码轮询网络设备的接收队列，并禁用网络中断(默认：0)
net.core.netdev_max_backlog = 16384 # 当接口接收数据包的速度超过内核可以处理的速度时，增加incoming连接的数量，backlog队列设置
                                    在INPUT端排队的最大数据包数,高负载可以增加该值(默认：1000)
net.core.somaxconn = 1024  # 增加incoming连接数,somaxconn定义每个listen调用分配的request_sock结构的数量。此队列在监听套接字的
                           生命周期中是持久性的,web应用环境非常有必要调整此值

net.core.default_qdisc = fq     # 推荐用于Centos7、Debian8主机
                                在centos5或6主机需要在/etc/rc.local添加：/sbin/ifconfig ethN txqueuelen 10000
                                (N代表10G NIC的编号，即万兆网卡的系统编号)

# 下列参数设置后，文件所在目录下：/proc/sys/fs/

fs.file-max = 2097152    # 增加文件句柄和inode缓存的大小

# 下列参数设置后，文件所在目录为：/proc/sys/net/ipv4/

net.ipv4.ip_forward = 1 # 值为0表示禁止数据转发，1则开启
net.ipv4.tcp_no_metrics_save = 1    # 不要在关闭连接时缓存metrics
net.ipv4.tcp_congestion_control = cubic # 设置默认拥塞控制算法是cubic,(推荐使用htcp)
net.ipv4.tcp_mtu_probing = 1    # 设置主机启用jumbo frames，避免MTU黑洞问题
net.ipv4.tcp_rfc1337 = 1    # 防止TCP时间等待
net.ipv4.icmp_echo_ignore_broadcasts = 1     # 设置为1表示忽略icmp广播请求,避免放大攻击
net.ipv4.icmp_ignore_bogus_error_responses = 1      # 开启恶意icmp错误消息保护
net.ipv4.icmp_echo_ignore_all = 1    # 忽略所有icmp请求,即关闭ping
net.ipv4.tcp_keepalive_time = 600   # TCP发送keepalive消息的频率(单位：秒),用于确认TCP连接是否有效，防止建立连接但不发送数据的攻击
net.ipv4.tcp_keepalive_probes = 5   # 发送keepalive消息超时之前的探测次数    # 默认值：9，推荐5
net.ipv4.tcp_keepalive_intvl = 15   # 确定了isAlive间隔探测之间的等待时间   (默认75s,建议15-30s)
net.ipv4.tcp_retries1 = 3   # 放弃回应一个TCP连接请求前进行重试的次数。RFC规定值最低为3，默认值为3
net.ipv4.tcp_retries2 = 5   # 在丢弃激活已建立通讯状况的TCP连接之前﹐需要进行多少次重试。默认值为15
net.ipv4.tcp_orphan_retries = 3 # 本地丢弃TCP连接前进行的重试次数，默认7
net.ipv4.tcp_abort_on_overflow = 0  # 守护进程过于繁忙不能接受新的连接时，就发送reset信息，默认0
net.ipv4.tcp_syncookies = 1 # 开启SYN Cookies，当出现SYN等待队列溢出时，启用cookies来处理，可以做SYN洪水攻击保护(默认：0)
net.ipv4.tcp_stdurg = 0 # 默认使用tcp urg pointer字段中的主机请求解释功能，打开可能导致不能正常通信(默认：0)
net.ipv4.tcp_window_scaling = 1 # 支持更大的TCP/ip会话的滑动窗口,如果TCP窗口最大超过65535(64K), 必须设置该数值为1
net.ipv4.tcp_timestamps = 1 # 可防范伪造的sequence号码，启用实现更好的网络性能(默认：1)
net.ipv4.tcp_sack = 1   # 开启有选择的应答,用于查看特定的遗失数据包，用于快速恢复连接状态(默认1)
net.ipv4.tcp_fack = 1  # 打开fack拥塞避免和快速重传功能，注意：当tcp_sack设置为0时，此值设置1也是无效的(默认：1)
net.ipv4.tcp_dsack = 1 # 允许TCP发送"两个完全相同"的SACK(默认：1)
net.ipv4.tcp_ecn = 0   # TCP的直接拥塞通告功能(默认：0)
net.ipv4.tcp_reordering = 5 # TCP流中重排序的数据报最大数量。一般推荐把这个数值略微调整大一些(默认：3)
net.ipv4.tcp_retrans_collapse = 0  # 对于某些有bug的打印机提供针对其bug的兼容性。一般不需要这个支持,可以关闭它(默认：1)
net.ipv4.tcp_app_win = 31   # 保留max_window/2^tcp_app_win数量的用于应用缓存，0表示不需要缓存(默认：31)
net.ipv4.tcp_adv_win_scale = 2  # 计算缓冲开销(默认：2)
net.ipv4.tcp_low_latency = 0    # 允许tcp/ip栈在高吞吐下低延迟，一般禁用，但在beowulf集群中打开很有帮助(默认：0)
net.ipv4.tcp_westwood = 0   # 启用发送端的拥塞控制算法，可对带宽利用情况进行优化(默认：0)
net.ipv4.tcp_bic = 0    # 为快速长距离网络启用binary increase congestion；可更好利用(默认：0)
net.ipv4.ip_local_port_range = 1024 65000   # 对外连接端口范围，RHEL 7 default: 32768 61000
net.ipv4.ip_conntrack_max = 65536   # 系统最大支持的ipv4连接数(默认：65536)，但如果内存128M以下，最大只有8192
net.ipv4.tcp_syn_retries = 3        # 在内核放弃建立连接之前发送SYN包的数量,此值不应大于255，默认是5，高负载物理
                                    通信良好时，建议为2，此值仅针对对外连接，对进来的连接则由tcp_retries决定
net.ipv4.tcp_synack_retries = 2 # 对于远端的连接请求SYN，内核会发送SYN+ACK数据包以确认收到上一个SYN请求。即三次握手的第二次握手
                                此设置决定内核放弃连接之前发送SYN+ACK包的数量，不应大于255，默认为5
net.ipv4.tcp_fin_timeout = 15   # 此参数决定了TCP连接保持在FIN-WAIT-2状态的时间，(默认60s，建议15-30s)对于生成或支持高级别网络
                                流量的工作负载或系统，此值设置到10秒以下
net.ipv4.tcp_max_syn_backlog = 2048 # 记录尚未收到客户端确认信息的连接请求的最大值，内存大于128Mb默认1024，内存小于128时，则值
                                      默认时128,服务器过载时可增加此值
net.ipv4.tcp_limit_output_bytes = 131072 # 默认是262144字节；限制设备上的字节数，以减少较大队列大小导致的延迟效应；对于延迟优先
                                         级高于吞吐量的工作负载或环境，降低此值可以改善延迟，推荐131072字节

# 增加time-wait存储池大小可防止简单DOS攻击
net.ipv4.tcp_max_tw_buckets = 1440000 # 指定允许在任何时间存在的“time-wait”状态的最大sockets数。如果超过最大值，则会立即销毁处于
                                    “time-wait”状态的套接字并显示警告。此设置用于阻止某些类型的拒绝服务攻击。在降低此值之前应小心
                                    谨慎。更改后，应增加其值，尤其是在系统中添加了更多内存或网络需求较高且环境较少受外部威胁影响时
                                    默认262144，当网络需求很高且环境较少受到外部威胁时，可以增加到450000
net.ipv4.tcp_tw_recycle = 1 # 开启TCP连接中time_wait sockets的快速回收,默认为0
net.ipv4.tcp_tw_reuse = 1   # 开启TCP连接复用，允许将time_wait sockets重新用于新的TCP连接(主要针对time_wait连接)启用后，此参数
                            可以绕过通常与套接字创建相关的分配和初始化开销，从而节省CPU周期，系统负载和时间
net.ipv4.tcp_max_orphans = 32768  # 告诉内核有多少TCP套接字未附加到系统保留的任何用户文件句柄的最大数量,如果超过此数量，则立即重置
                                    孤立连接并打印警告。此限制仅用于防止简单的DoS攻击，但不能人为地依赖此限制或降低限制，而是增加
                                    它（可能在增加已安装的内存之后），如果网络条件需要超过默认值，调整网络服务以延迟并更积极地杀死
                                    这些状态。再次提醒：每个orphan都会吃掉大约64K的unswappable内存(默认：8192)

# 增加读取缓冲区空间
net.ipv4.udp_rmem_min = 16384
net.ipv4.tcp_rmem = 4096 87380 25165824 # 三个值表示TCP套接字接收缓冲区的最小值、默认值和最大值

# 增加可分配的写缓冲区空间
net.ipv4.udp_wmem_min = 16384
net.ipv4.tcp_wmem = 4096 65536 25165824 # 三个参数依次为：最小值，默认值和最大值；最小值表示新创建的
                                            套接字作为其创建的一部分有权获得的最小接收缓冲区大小；默认值表示TCP套接字接收缓冲区的
                                            初始大小；最大值表示TCP套接字的自动调整发送缓冲区的最大接收缓冲区大小，该值不会覆盖
                                            net.core.rmem_max。根据系统中可用的内存量，此设置的默认值介于64K字节和4M字节之间
                                            建议使用16M字节或更高的最大值（取决于内核级别），尤其是10千兆位适配器

# 增加可分配的最大总缓冲区空间,此为以page为单位（4096字节）测量
net.ipv4.udp_mem = 65536 131072 262144
net.ipv4.tcp_mem = 65536 131072 262144  # 第一次低于此值,TCP没有内存压力,第二次进入内存压力阶段,
                                        第三次TCP拒绝分配socket(默认：5814 7754 11628)

# 下列参数设置后，文件所在目录及其目录下：/proc/sys/net/ipv4/conf/

#启用源路由核查功能，反向路径过滤
# 0 ——未进行源验证
# 1 ——处于如 RFC3704 所定义的严格模式
# 2 ——处于如 RFC3704 所定义的松散模式
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1

# 确保无人能修改路由表
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.secure_redirects = 0
net.ipv4.conf.default.accept_redirects = 0  # 禁止icmp重定向检查
net.ipv4.conf.default.secure_redirects = 0

# 不充当路由器
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0

# 记录在不可达的地址的数据包(错误的路由)；开启并记录欺骗、源路由、重定向数据包
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1

net.ipv4.conf.all.accept_source_route = 0   # 处理无源路由的包
net.ipv4.conf.default.accept_source_route = 0   # 禁用所有IP源路由

# 下列参数设置后，文件所在目录下：/proc/sys/net/ipv4/route/

net.ipv4.route.gc_timeout = 100     # 路由刷新频率

# 下列参数设置后，文件所在目录下(防火墙相关)：/proc/sys/net/netfilter/

# nf_conntrack是Linux内核连接跟踪的模块：与nf_conntrack相关的内核参数有三个：
# 1、nf_conntrack_max：连接跟踪表的大小，建议根据内存计算该值CONNTRACK_MAX = RAMSIZE (in bytes) / 16384 / (x / 32)
     并满足nf_conntrack_max=4*nf_conntrack_buckets，默认262144
# 2、nf_conntrack_buckets：哈希表的大小，(nf_conntrack_max/nf_conntrack_buckets就是每条哈希记录链表的长度)，默认65536
# 3、nf_conntrack_tcp_timeout_established：tcp会话的超时时间，默认：432000 (5天)

# 以下为64内存推荐配置
net.nf_conntrack_max = 262144
net.netfilter.nf_conntrack_max = 65536   # 系统支持的最大ipv4连接数(默认：65536)
net.netfilter.nf_conntrack_tcp_timeout_established = 300 # 已建立的tcp连接超时时间(默认：43200，即5天)
net.netfilter.nf_conntrack_buckets = 1048576(默认：65535)
net.netfilter.nf_conntrack_tcp_timeout_time_wait = 30  # time_wait状态超时时间，超过该时间就清除该连接(默认：120)
net.netfilter.nf_conntrack_tcp_timeout_close_wait = 60  # close_wait状态超时时间，超过该时间就清除该连接(默认：60)
net.netfilter.nf_conntrack_tcp_timeout_fin_wait = 30    # fin_wait状态超时时间，超过该时间就清除该连接(默认：120)

```

## 不常用设置和记录

### 服务器配置双机互信

```bash
# 服务器1的IP：192.168.1.1   服务器2的IP：192.168.1.2
# 在两台服务器上分别按照以下步骤操作
ssh-keygen -t rsa -b 4096   # 创建公钥和密钥
cat ~/.ssh/id_rsa.pub >> authorized_keys    # 将自己的公钥追加到认证公钥

# 将公钥互相复制到对应的主机
# 方法一:使用ssh-copy-id命令
ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.1.2 # 在1主机执行，复制公钥到远程主机192.168.1.2,cat追加公钥到authorized_keys
ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.1.1 # 在2主机执行，复制公钥到远程主机192.168.1.1,cat追加公钥到authorized_keys

# 方法二：使用ssh命令并用cat追加到authorized_keys中
ssh 192.168.1.2 cat ~/.ssh/id_rsa.pub >> authorized_keys    # 在服务器1上操作，将服务器2的公钥追加到认证公钥
ssh 192.168.1.1 cat ~/.ssh/id_rsa.pub >> authorized_keys    # 在服务器2上操作，将服务器1的公钥追加到认证公钥
ssh 192.168.1.2 ifconfig    # 测试,在服务器1上执行下面的命令，如果配置正确的话就无需输入密码也能显示服务器2的网卡信息
```

### centos完全重装python和yum

```bash
# 删除现有Python
rpm -qa|grep python| xargs rpm -ev --allmatches --nodeps # 强制删除已安装程序及其关联
whereis python | xargs rm -frv # 删除所有残余文件,xargs，允许你对输出执行其他某些命令
whereis python # 验证删除，返回无结果

# 删除现有的yum
rpm -qa|grep yum| xargs rpm -ev --allmatches --nodeps
whereis yum | xargs rm -frv

# 从http://mirrors.aliyun.com/centos/7/os/x86_64/Packages/下载相应的包,由于源中版本会更新，具体请查看URL中的版本！
rpm -Uvh --replacepkgs *.rpm    # 可能之间还需要zlib和zlib-devel包，根据情况下载并安装！安装完进行测试

# 源码编译安装yum
wget http://yum.baseurl.org/download/3.4/yum-3.4.3.tar.gz
tar xvf yum-3.4.3.tar.gz && cd yum-3.4.3
yummain.py install yum
# 如果结果提示错误： CRITICAL:yum.cli:Config Error: Error accessing file for config file:///etc/缺少配置文件
# 在etc目录下面新建yum.conf文件，然后再次运行 yummain.py install yum，顺利完成安装
```

### 普通账号赋予root权限

```bash
# 建议使用方法二，不要轻易使用方法三
: << comment
# 方法一： 修改 /etc/sudoers 文件，找到%wheel一行，把前面的注释（#）去掉
Allows people in group wheel to run all commands
%wheel    ALL=(ALL)    ALL
然后修改用户，使其属于root组（wheel），命令如下：
#usermod -g root tommy
修改完毕，现在可以用tommy帐号登录，然后用命令 sudo su - ，即可获得root权限进行操作
comment

: << comment
# 方法二： 修改 /etc/sudoers 文件，找到root一行，在root下面添加一行，如下所示：
Allow root to run any commands anywhere
root    ALL=(ALL)     ALL
tommy   ALL=(ALL)     ALL
修改完毕，现在可以用tommy帐号登录，然后用命令 sudo su - ，即可获得root权限进行操作
comment

: << comment
# 方法三： 修改 /etc/passwd 文件，找到如下行，把用户ID修改为 0 ，如下所示：
tommy:x:500:500:tommy:/home/tommy:/bin/bash
修改后如下
tommy:x:0:500:tommy:/home/tommy:/bin/bash
保存，用tommy账户登录后，直接获取的就是root帐号的权限
comment
```

### 未采用双机热备防止服务中断--float IP(浮动IP配置)

```bash
: << comment
有两台Linux服务器，其中一台主机（IP：192.168.2.2）对外提供了一定的网络服务，另一台从机（IP：192.168.2.3）能提供相同的服务，但IP地址
没有对外部公开,客户端连接的都是192.168.2.2这个IP地址，如果主机故障，则会使网络服务暂时中断
由于没有采用双机热备份技术,考虑用Linux脚本来实现简单的浮动IP技术，当主机故障时从机获得192.168.2.2这个IP，暂时替代主机提供服务，当主机
恢复时，从机自动释放这个IP
思路：
利用单个网卡绑定多个IP地址的技术和crontab自动执行技术
为主机的网卡多绑定一个静态IP，如192.168.2.8,这个地址便于从机判断的
为从机的网卡多绑定一个动态IP，127.0.0.1，它在主机故障时将会被脚本修改为192.168.2.2
在从机上添加一个脚本 /root/autoFloatIP.sh，使用crontab技术让这个脚本每分钟执行一次，这个脚本的作用是判断主机的地址192.168.2.8能否
Ping通，一旦不正常则将让自己的网卡多余的那个IP地址改为192.168.2.2，如果主机恢复，则将这个地址改回为127.0.0.1
comment
# 步骤如下：
# 1. 为主机添加一个静态IP192.168.2.8，在/etc/sysconfig/network-scripts目录修改类似ifcfg-eth0:1的文件，内容为:
DEVICE=eth0:1
IPADDR=139.24.214.82
NETMASK= 255.255.255.0
ONBOOT= yes

# 2. 在从机上，在/root下建立一个脚本autoFloatIP.sh,内容如下：

#!/bin/bash
c1=$(ping 139.24.214.82 -c 1|grep Unreachable|wc -l)
if [ $c1 -gt 0 ] ; then
c2=$(ping 139.24.214.82 -c 10|grep Unreachable|wc -l)
if [ $c2 -gt 9 ] ; then
    c3=$(ping 139.24.214.22 -c 10|grep Unreachable|wc -l)
      if [ $c3 -gt 9 ] ; then
         /sbin/ifconfig eth0:1 139.24.214.22 netmask 255.255.254.0
echo "float ip to 22"
      fi
fi
echo "can not connect"
else
c4=$(/sbin/ifconfig|grep 139.24.214.22|wc -l)
if [ $c4 -gt 0 ] ; then
    /sbin/ifconfig eth0:2 127.0.0.1 netmask 255.255.254.0
    echo "reset ip"
fi
echo "connection is ok"
fi

# 其中关键的命令如下，用这个方法来动态修改IP，动态IP在电脑重启会消失
/sbin/ifconfig eth0:1 139.24.214.22 netmask 255.255.254.0
/sbin/ifconfig eth0:2 127.0.0.1 netmask 255.255.254.0

# 3. 从机上建立crontab，加上如下一行并保存
* * * * * /root/autoFloatIP.sh > /dev/null 2>&1
```

### 安装最新稳定centos内核&更改为静态ip、网卡名称

```bash
# 在CentOS主机上安装最新的稳定内核
# 导入key（内核3.0以上需要导入key）,如果已经修改了repo的gpgcheck=0也可以不导入key
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
# To install ELRepo for RHEL-6, SL-6 or CentOS-6
rpm -Uvh http://www.elrepo.org/elrepo-release-6-8.el6.elrepo.noarch.rpm
# 安装elrepo的yum源,To install ELRepo for RHEL-7, SL-7 or CentOS-7
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
# 安装内核,在yum的ELRepo源中，有mainline颁布的，可以这样安装
yum --enablerepo=elrepo-kernel install  kernel-ml-devel kernel-ml -y

yum --enablerepo=elrepo-kernel  install  kernel-lt -y   # 安装long term的内核

# 配置Grub使用新内核，配置完后重启启用新内核
## CentOS 6配置，修改/boot/grub/grub.conf
  default=0 # 更改为 0
  fallback=1 # 添加此项

## CentOS 7
grub2-set-default 0
grub2-install /dev/sda
grub2-mkconfig -o /boot/grub2/grub.cfg

# 更改为静态IP：/etc/sysconfig/network-scripts/ifcfg-ens33
BOOTPROTO="static" #dhcp改为static
ONBOOT="yes" #开机启用本配置
IPADDR=192.168.7.106 #静态IP
GATEWAY=192.168.7.1 #默认网关
NETMASK=255.255.255.0 #子网掩码
DNS1=192.168.7.1 #DNS 配置

vim /etc/sysconfig/grub     # 编辑 grub 配置文件,实际是/etc/default/grub的软连接
# 为GRUB_CMDLINE_LINUX变量增加2个参数，具体内容如下(加粗)：
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=cl/root rd.lvm.lv=cl/swap net.ifnames=0 biosdevname=0 rhgb quiet"
# 重新生成 grub 配置文件，然后重新启动 Linux 操作系统，通过 ip addr 可以看到网卡名称已经变为 eth0
grub2-mkconfig -o /boot/grub2/grub.cfg
# 修改网卡配置文件，原网卡配置文件名称为 ifcfg-ens33，这里需要修改为ethx的格式，并适当调整网卡配置文件
mv /etc/sysconfig/network-scripts/ifcfg-ens33 /etc/sysconfig/network-scripts/ifcfg-eth0
# 修改ifcfg-eth0文件如下内容(其它内容不变)
NAME=eth0
DEVICE=eth0

systemctl restart network.service    # 重启网络服务，注意：ifcfg-ens33 文件最好删除掉，否则重启 network 服务时候会报错
```

## Linux数据恢复：最好是备份而不是尝试恢复

### SystemRescueCd

```bash
# 官网：http://www.system-rescue-cd.org/


```

### 其他

```markdown
# 参考链接：https://help.ubuntu.com/community/DataRecovery

情况一：丢失分区：如果分区出错且分区表不显示该分区，只要未向该空间写入数据，则数据仍会存在
# GNU Parted
1. 使用live cd或额外系统，进入系统终端首先执行，swapoff -a，挂载问题磁盘
2. 运行partted，parted /dev/sda,这里/dev/sda表示需要恢复的设备
3. 运行恢复选项：rescue START END，如果找到潜在的分区则会提示是否添加到分区表中

# Testdisk
testdisk也可以恢复分区。安装testdisk软件包。 运行testdisk，它将扫描您的计算机中的介质，并为您提供菜单驱动的方法来恢复分区

# Gpart
安装gpart，执行gpart /dev/sda，/dev/sda表示需要恢复的设备,最好是gpart -W /dev/sda /dev/sdb写入到另一个设备


情况二：映像损坏的设备，文件系统或驱动器
# GNU ddrescue(安装后命令为ddrescue)
网址：http://www.gnu.org/software/ddrescue/ddrescue.html

# dd_rescue
网址：http://www.garloff.de/kurt/linux/ddrescue/

如果您正在成像的文件系统的大小大于4 gig，则将无法使用MSDOS（VFAT）文件系统（通常在USB驱动器上找到）来存储图像，因为最大大小限制为4G
这样的文件系统上的文件。 使用EXT3或其他可以处理此类文件大小的文件系统

# 将在/dev/hd2的分区恢复到/dev/hdb2
ddrescue -r3 /dev/hda2 /dev/hdb2 logfie
e2fsck -v -f /dev/hdb2
mount -t ext2 -o ro /dev/hdb2 /mnt

# 恢复在/dev/cdrom中的一个CD-ROM,将cdimage写入空白的CD-ROM
ddrescue -b 2048 /dev/cdrom cdimage logfie

从恢复的映像提取文件系统来恢复文件
安装sleuthkit软件
mmls file -b    此命令来显示在映像中找到的分区
mount -o loop,offset=16384 file mnt 显示多个分区，此示例从块32处安装dos分区，计算字节数需要乘512，得出16384
mount -t ntfs -or,force,loop,offset=32256 file mnt  挂载windows创建的ntfs分区(63乘512=32256)

利用foremost恢复文件,假设需要恢复的是/dev/hda
foremost -i /dev/hda -o /recovery/foremost
foremost -t jpg -i /dev/hda -o /recovery/foremost  恢复特定类型文件

scalpel软件也可用来恢复：scalpel File -o dir  File是映像文件或设备，dir是输出目录

magicrescue使用magic bytes扫描文件来识别文件的存在和类型
从/dev/sda1分区恢复gzip和png文件：magicrescue -r gzip -r png -d~/output /dev/sda1

Photorec旨在从数码相机的内存或硬盘中恢复丢失的照片，已扩展支持80多种不同类型文件，photorec是testdisk软件包的一部分

ntfsprogs可以从NTFS系统恢复已删除文件，Windows和linux下都有此软件，大致有三种模式：
1. 扫描：搜索已删除的文件并查找有关它们的信息，nftsundelete /dev/sda1
2. 取消删除：ntfsundelete /dev/sda1 -u -i 3689 -o work.doc -d~/output
3. 复制：ntfsdelete /dev/sda1 -u -m *.jpg
ntfsundelete /dev/sda1 -u -i 14159  根据文件的inode恢复

sleuth kit和autopsy是两个基于GUI的恢复软件

# extundelete 主页：http://extundelete.sourceforge.net/
下载地址：http://nchc.dl.sourceforge.net/project/extundelete/extundelete/0.2.0/extundelete-0.2.0.tar.bz2
ubuntu用户可直接安装: apt-get install extundelete
./configure && make && make install    # 如果提示找不到ext2fs库，使用 yum -y install e2fsprogs* 安装

# 数据恢复
1. 卸载需要恢复文件的分区
fuser -k /mnt/test/               <-- 结束使用某分区的进程树
umount /mnt/test                  <-- 卸载分区

2. 使用extundelete查看分区上存在的文件
extundelete --inode 2 /dev/sdb1    # --inode为查找某i节点中的内容，使用2则说明为搜索，如果需进入目录搜索，只须要指定目录I节点即可

# --restore-inode 恢复指定的I节点文件，默认全将恢复出来的文件放在当前路径RECOVERED_FILES/ 目录下，文件名为 file.I节点号
extundelete --restore-inode 13 /dev/sdb1

ls RECOVERED_FILES/
mount /dev/sdb1 /mnt/test/
mv RECOVERED_FILES/file.13 /mnt/test/resolv.conf
mv RECOVERED_FILES/file.14 /mnt/test/hosts
cat /mnt/test/hosts            # 查看被恢复出来的文件 是否与源文件一致
cat /mnt/test/resolv.conf
```

## 基本服务搭建

### 时间同步

```markdown
http://www.ntp.org
https://chrony.tuxfamily.org

## ntp配置

driftfile /var/lib/ntp/drift

# Permit time synchronization with our time source, but do not
# permit the source to query or modify the service on this system.
restrict default nomodify notrap nopeer noquery

# Permit all access over the loopback interface.  This could
# be tightened as well, but to do so would effect some of
# the administrative functions.
restrict 127.0.0.1
restrict ::1

# Hosts on local network are less restricted.
#restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap

# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst

#broadcast 192.168.1.255 autokey        # broadcast server
#broadcastclient                        # broadcast client
#broadcast 224.0.1.1 autokey            # multicast server
#multicastclient 224.0.1.1              # multicast client
#manycastserver 239.255.254.254         # manycast server
#manycastclient 239.255.254.254 autokey # manycast client

# Enable public key cryptography.
#crypto

includefile /etc/ntp/crypto/pw

# Key file containing the keys and key identifiers used when operating
# with symmetric key cryptography.
keys /etc/ntp/keys

# Specify the key identifiers which are trusted.
#trustedkey 4 8 42

# Specify the key identifier to use with the ntpdc utility.
#requestkey 8

# Specify the key identifier to use with the ntpq utility.
#controlkey 8

# Enable writing of statistics records.
#statistics clockstats cryptostats loopstats peerstats

# Disable the monitoring facility to prevent amplification attacks using ntpdc
# monlist command when default restrict does not include the noquery flag. See
# CVE-2013-5211 for more details.
# Note: Monitoring will not be disabled with the limited restriction flag.
disable monitor

----------------------------------------------------------------------------------------------------------------------

## 内外网隔离情况chrony master client配置
# Master,假设网段为192.168.165.x
initstepslew 1 client1 client3 client6
driftfile /var/lib/chrony/drift
local stratum 8
manual
allow 192.168.165.0/24
smoothtime 400 0.01
rtcsync

# Client,客户端均是相同配置
server master iburst
driftfile /var/lib/chrony/drift
allow 192.168.165.0/24
makestep 1.0 3
rtcsync


如果没有合适的计算机被指定为主服务器，或者要求即使客户端出现故障也要使客户端保持同步 ，则本地指令的orphan选项将启用一种特殊模式，
在该模式下，可以从多台计算机中自动选择主服务器。他们都需要使用相同的 本地配置并相互轮询。具有最小引用ID（基于其IP地址）的服务器将
充当主服务器的角色，其他服务器将与其同步。当它失败时，具有第二小的参考ID的服务器将接管等

第一个服务器的配置文件可能是(假设有三个服务器分别称为master1，master2和master3)。
其他服务器将是相同的，`但要修改initstepslew和server指令中的主机名以指定其他服务器`。他们的客户端可能配置为轮询所有三台服务器
initstepslew 1 master2 master3
server master2
server master3
driftfile /var/lib/chrony/drift
local stratum 8 orphan
manual
allow 192.168.165.0/24
rtcsync

-----------------------------------------------------------------------------------------------------------------------

## chrony配置

# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).[必须要有]
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst

# Record the rate at which the system clock gains/losses time.[必须要有]
driftfile /var/lib/chrony/drift

# Allow the system clock to be stepped in the first three updates
# if its offset is larger than 1 second.[必须要有]
makestep 1.0 3

# Enable kernel synchronization of the real-time clock (RTC).[必须要有]
rtcsync

# Enable hardware timestamping on all interfaces that support it.
#hwtimestamp *

# Increase the minimum number of selectable sources required to adjust
# the system clock.
#minsources 2

# Allow NTP client access from local network.
#allow 192.168.0.0/16

# Serve time even if not synchronized to a time source.
#local stratum 10

# Specify file containing keys for NTP authentication.
#keyfile /etc/chrony.keys

# Specify directory for log files.
logdir /var/log/chrony

# Select which information is logged.
#log measurements statistics tracking

```

### Vsftp

```markdown
VSFTPv3.0.3版本配置vsftpd.conf文件详解

vsftpd.conf的格式非常简单。每一行都是注释或指令。注释行以＃开头并被忽略。指令行的格式为：选项=值
重要的是要注意在选项，=和值之间放置任何空格是错误的。每个设置都有一个默认编译，可以在配置文件中修改

/etc/rc.d/init.d/vsftpd # 启动脚本
/etc/pam.d/vsftpd   # PAM认证文件
/etc/vsftpd/ftpusers    # 禁止使用Vsftpd的用户列表文件
/etc/vsftpd/user_list   # 禁止或允许使用Vsftpd的用户列表文件
/var/ftp    # 匿名用户主目录

布尔选项列表，布尔选项可以设置为yes或no

allow_anon_ssl
仅在ssl_enable 处于活动状态时适用 。如果设置为YES，则允许匿名用户使用安全SSL连接
默认值：NO

anon_mkdir_write_enable
如果设置为YES，则允许匿名用户在特定条件下创建新目录。为此， 必须激活选项 write_enable，并且匿名ftp用户必须具有父目录的写权限
默认值：NO

anon_other_write_enable
如果设置为YES，则允许匿名用户执行除上载和创建目录之外的写入操作，例如删除和重命名。通常不建议这样做，但为了完整性而包括在内
默认值：NO

anon_upload_enable
如果设置为YES，则允许匿名用户在特定条件下上载文件。为此， 必须激活选项 write_enable，并且匿名ftp用户必须具有所需上载位置的写入权限
虚拟用户上传也需要此设置,默认情况下，虚拟用户使用匿名（即最大限制）权限进行处理
默认值：NO

anon_world_readable_only
启用后，将只允许匿名用户下载世界可读的文件。这是认识到ftp用户可能拥有文件，尤其是在上传的情况下
默认值：是

anonymous_enable
控制是否允许匿名登录。如果启用，则用户名 ftp 和 anonymous 都将被识别为匿名登录
默认值：YES

ascii_download_enable
启用后，ASCII模式数据传输将在下载时受到尊重
默认值：NO

ascii_upload_enable
启用后，上传时将遵循ASCII模式数据传输
默认值：NO

async_abor_enable
启用后，将启用称为“异步ABOR”的特殊FTP命令。只有不明智的FTP客户端才会使用此功能。此外，此功能难以处理，因此默认情况下禁用。遗憾的是，除非
此功能可用，否则某些FTP客户端将在取消传输时挂起，因此您可能希望启用它
默认值：NO

background
启用后，vsftpd以“listen”模式启动，vsftpd将为侦听器进程提供背景。即控件将立即返回到启动vsftpd的shell
默认值：NO

check_shell
注意！此选项仅对vsftpd的非PAM构建有效。如果禁用，vsftpd将不会检查/ etc / shells是否有用于本地登录的用户shell
默认值：是

chmod_enable
启用后，允许使用SITE CHMOD命令。注意！这仅适用于本地用户。匿名用户永远不会使用SITE CHMOD
默认值：是

chown_uploads
如果启用，则所有匿名上载的文件都将更改为设置chown_username中指定的用户 。从管理（可能是安全性）的角度来看，这很有用
默认值：NO

chroot_list_enable
设为yes可以在登录时提供放置在其主目录中的chroot（）jail中的本地用户列表。如果chroot_local_user设置为YES，则含义略有不同。在这种情况下
列表不被放置在chroot（）jail中的用户列表。默认情况下，包含此列表文件是/etc/vsftpd.chroot_list，但可使用chroot_list_file设置覆盖它
默认值：NO

chroot_local_user
如果设置为YES，则登录后本地用户（默认情况下）将放置在其主目录中的chroot（）jail中。 警告： 此选项具有安全隐患，尤其是在用户具有上载权限或
shell访问权限的情况下。只有在您知道自己在做什么时才启用。请注意，这些安全隐患不是vsftpd特定的
它们适用于所有提供将本地用户放入chroot（）jail的FTP守护进程
默认值：NO

connect_from_port_20
这可以控制PORT样式数据连接是否在服务器计算机上使用端口20（ftp-data）。出于安全原因，一些客户可能会坚持认为是这种情况。相反，禁用此选项
可使vsftpd以较低的权限运行
默认值：NO（但是示例配置文件启用它）

debug_ssl
如果为true，则将OpenSSL连接诊断转储到vsftpd日志文件。（在v2.0.6中添加）
默认值：NO

delete_failed_uploads
如果为true，则删除任何失败的上载文件。（在v2.0.7中添加）
默认值：NO

deny_email_enable
如果激活，您可以提供匿名密码电子邮件响应列表，这会导致登录被拒绝。默认情况下，包含此列表中的文件是/etc/vsftpd.banned_emails，但你可以与
覆盖这个 banned_email_file 设置
默认值：NO

dirlist_enable
如果设置为NO，则所有目录列表命令都将拒绝权限
默认值：是

dirmessage_enable
如果启用，FTP服务器的用户首次进入新目录时可以显示消息。默认情况下，会扫描目录以查找文件.message，但可以使用配置设置message_file覆盖该目录
默认值：NO（但是示例配置文件启用它）

download_enable
如果设置为NO，则所有下载请求都将拒绝权限
默认值：是

dual_log_enable
如果启用，则会并行生成两个日志文件，默认情况下为 / var / log / xferlog 和 /var/log/vsftpd.log。前者是一个wu-ftpd样式的传输日志，可以
通过标准工具解析。后者是vsftpd自己的样式日志
默认值：NO

force_dot_files
如果激活，则以。开头的文件和目录。即使客户端未使用“a”标志，也将显示在目录列表中。此覆盖不包括“。” 和“..”条目
默认值：NO

force_anon_data_ssl
仅 在激活ssl_enable时适用 。如果激活，则强制所有匿名登录使用安全SSL连接，以便在数据连接上发送和接收数据
默认值：NO

force_anon_logins_ssl
仅 在激活ssl_enable时适用 。如果激活，则强制所有匿名登录使用安全SSL连接以发送密码
默认值：NO

force_local_data_ssl
仅 在激活ssl_enable时适用 。如果激活，则强制所有非匿名登录使用安全SSL连接，以便在数据连接上发送和接收数据
默认值：是

force_local_logins_ssl
仅 在激活ssl_enable时适用 。如果激活，则强制所有非匿名登录使用安全SSL连接以发送密码
默认值：是

guest_enable
如果启用，则所有非匿名登录都被归类为“访客”登录。guest 虚拟机登录将重新映射到guest_username 设置中指定的用户
默认值：NO

hide_ids
如果启用，目录列表中的所有用户和组信息将显示为“ftp”
默认值：NO

implicit_ssl
如果启用，则SSL握手是所有连接（FTPS协议）上的第一件事。要支持显式SSL和/或纯文本，还应运行单独的vsftpd侦听器进程
默认值：NO

听
如果启用，vsftpd将以独立模式运行。这意味着vsftpd不能从某种类型的inetd运行。相反，vsftpd可执行文件直接运行一次
然后，vsftpd将负责监听和处理传入的连接
默认值：是

listen_ipv6
与listen参数一样，除了vsftpd将侦听IPv6套接字而不是IPv4套接字。此参数和listen参数是互斥的
默认值：NO

local_enable
控制是否允许本地登录。如果启用，则可以使用/ etc / passwd中的普通用户帐户（或PAM配置引用的任何位置）登录
必须启用此功能才能使任何非匿名登录工作，包括虚拟用户
默认值：NO

lock_upload_files
启用后，所有上载都会继续对上载文件进行写锁定。所有下载都继续下载文件上的共享读锁定
警告！在启用此功能之前，请注意恶意阅读器可能会使想要添加文件的作者感到饥饿
默认值：是

log_ftp_protocol
启用后，将记录所有FTP请求和响应，前提是未启用xferlog_std_format选项。用于调试
默认值：NO

ls_recurse_enable
启用后，此设置将允许使用“ls -R”。这是一个较小的安全风险，因为大型站点顶层的ls -R可能会消耗大量资源
默认值：NO

mdtm_write
启用后，此设置将允许MDTM设置文件修改时间（根据通常的访问检查）
默认值：是

no_anon_password
启用后，这会阻止vsftpd请求匿名密码 - 匿名用户将直接登录
默认值：NO

no_log_lock
启用后，这会阻止vsftpd在写入日志文件时进行文件锁定。通常不应启用此选项。它存在以解决操作系统错误，例如Solaris/Veritas文件系统组合
已经观察到有时会出现试图锁定日志文件的挂起
默认值：NO

one_process_model
如果您有Linux 2.4内核，则可以使用不同的安全模型，每个连接只使用一个进程。它是一种不太纯粹的安全模型，但会提高您的性能
除非您知道自己在做什么，并且您的站点支持大量同时连接的用户，否则您真的不想启用它
默认值：NO

passwd_chroot_enable
如果启用，则与 chroot_local_user一起 ，然后可以基于每个用户指定chroot（）jail位置。每个用户的jail都是从/etc/passwd中
的主目录字符串派生的。主目录字符串中出现/./表示jail位于路径中的特定位置
默认值：NO

pasv_addr_resolve
如果要在pasv_address 选项中使用主机名（而不是IP地址），请设置为YES
默认值：NO

pasv_enable
如果要禁用PASV获取数据连接的方法，请设置为NO
默认值：是

pasv_promiscuous
如果要禁用PASV安全检查，则设置为YES，以确保数据连接源自与控制连接相同的IP地址。只有在你知道自己在做什么的情况下才能启用对此的唯一合法
用途是采用某种形式的安全隧道方案，或者可能是为了促进FXP支持
默认值：NO

port_enable
如果要禁止PORT方法获取数据连接，请设置为NO
默认值：是

port_promiscuous
如果要禁用PORT安全检查，则设置为YES，以确保传出数据连接只能连接到客户端。只有在你知道自己在做什么的情况下才能启用
默认值：NO

require_cert
如果设置为yes，则需要所有SSL客户端连接来提供客户端证书。应用于此证书的验证程度由validate_cert控制 （在v2.0.6中添加）
默认值：NO

require_ssl_reuse
如果设置为yes，则需要所有SSL数据连接以展示SSL会话重用（这证明它们知道与控制通道相同的主密钥）。虽然这是一个安全的默认设置，但它可能会破坏
许多FTP客户端，因此您可能希望禁用它
默认值：是

run_as_launching_user
如果您希望vsftpd以启动vsftpd的用户身份运行，请设置为YES。在根访问不可用的情况下，这很有用。大规模警告！除非您完全知道自己在做什么
否则不要启用此选项，因为天真地使用此选项会产生大量安全问题。具体来说，当设置此选项时，vsftpd不会/不能使用chroot技术来限制文件访问
（即使由root启动）。一个糟糕的替代品可能是使用 deny_file 设置如{/* }，但这种可靠性无法与chroot相比，并且不应该依赖。如果使用此选项
则适用对其他选项的许多限制。例如，需要权限的选项（例如非匿名登录，上载所有权更改，从端口20连接和小于1024的侦听端口）预计不起作用
其他选项可能会受到影响
默认值：NO

secure_email_list_enable
如果您只想接受匿名登录的指定电子邮件密码列表，请设置为YES。这非常有用，可以在不需要虚拟用户的情况下限制对低安全性内容的访问。启用后，除非
提供的密码列在email_password_file 设置指定的文件中，否则将阻止匿名登录 。文件格式是每行一个密码，没有额外的空格
默认文件名是/etc/vsftpd.email_passwords
默认值：NO

session_support
这可以控制vsftpd是否尝试维护登录会话。如果vsftpd维护会话，它将尝试更新utmp和wtmp。如果使用PAM进行身份验证，它也会打开一个pam_session
并且只有在注销时关闭它。如果您不需要会话日志记录，您可能希望禁用此功能，并且您希望为vsftpd提供更多机会以更少的进程和/或更少的权限运行
注 - utmp和wtmp支持仅在PAM启​​用的版本中提供
默认值：NO

setproctitle_enable
如果启用，vsftpd将尝试在系统进程列表中显示会话状态信息。换句话说，报告的进程名称将更改以反映vsftpd会话正在执行的操作（空闲，下载等）
出于安全考虑，您可能希望将其关闭
默认值：NO

ssl_enable
如果启用，并且vsftpd是针对OpenSSL编译的，则vsftpd将通过SSL支持安全连接。这适用于控制连接（包括登录）以及数据连接
您也需要一个支持SSL的客户端。注意！！请注意启用此选项。只有在需要时才启用它。vsftpd无法保证OpenSSL库的安全性。通过启用此选项
您声明您信任已安装的OpenSSL库的安全性
默认值：NO

ssl_request_cert
如果启用，vsftpd会要求（但不一定需要,见 require_cert）一个证书上的传入 SSL 连接。通常这 不应该造成任何麻烦
默认值：是

ssl_sslv2
仅 在激活ssl_enable时适用 。如果启用，此选项将允许SSL v2协议连接。TLS v1连接是首选
默认值：NO

ssl_sslv3
仅 在激活ssl_enable时适用 。如果启用，此选项将允许SSL v3协议连接。TLS v1连接是首选
默认值：NO

ssl_tlsv1
仅 在激活ssl_enable时适用 。如果启用，此选项将允许TLS v1协议连接。TLS v1连接是首选
默认值：是

strict_ssl_read_eof
如果启用，则需要通过SSL终止SSL数据上载，而不是套接字上的EOF。需要此选项以确保攻击者未使用伪造的TCP FIN过早终止上载。不幸的是
默认情况下它没有启用，因为很少有客户端能够正确使用它。（v2.0.7中的新功能）
默认值：NO

strict_ssl_write_shutdown
如果启用，则需要通过SSL终止SSL数据下载，而不是套接字上的EOF。默认情况下这是关闭的，因为我无法找到执行此操作的单个FTP客户端
这是次要的。它影响的是我们判断客户是否确认完全收到该文件的能力。即使没有此选项，客户端也能够检查下载的完整性。（v2.0.7中的新功能）
默认值：NO

syslog_enable
如果启用，则任何已转至/var/log/vsftpd.log的日志输出将转到系统日志。记录在FTPD工具下完成
默认值：NO

tcp_wrappers的
如果启用，并且vsftpd是使用tcp_wrappers支持编译的，则传入连接将通过tcp_wrappers访问控制提供。此外，还有一种基于每个IP的配置机制
如果tcp_wrappers设置VSFTPD_LOAD_CONF环境变量，则vsftpd会话将尝试加载此变量中指定的vsftpd配置文件
默认值：NO

text_userdb_names
默认情况下，数字ID显示在目录列表的用户和组字段中。您可以通过启用此参数来获取文本名称。出于性能原因，它默认是关闭的
默认值：NO

tilde_user_enable
如果启用，vsftpd将尝试解析路径名，例如~chris / pics，即代字号后跟用户名。请注意，vsftpd将始终解析路径名〜和〜/ something
（在这种情况下，〜解析为初始登录目录）。请注意，只有 在_current_ chroot（）jail中找到文件/ etc / passwd时，〜用户路径才会解析
默认值：NO

use_localtime
如果启用，vsftpd将显示当前时区中包含时间的目录列表。默认为显示GMT。MDTM FTP命令返回的时间也受此选项的影响
默认值：NO

use_sendfile
用于测试在平台上使用sendfile（）系统调用的相对好处的内部设置
默认值：是

userlist_deny
如果 激活userlist_enable，则检查此选项 。如果将此设置设置为NO，则将拒绝用户登录，除非它们明确列在userlist_file指定的文件中
拒绝登录时，将在要求用户输入密码之前发出拒绝
默认值：是

userlist_enable
如果启用，vsftpd将从userlist_file给出的文件名加载用户名列表 。如果用户尝试使用此文件中的名称登录，则在要求输入密码之前
他们将被拒绝。这可能有助于防止传输明文密码。另请参见 userlist_deny
默认值：NO

validate_cert
如果设置为yes，则收到的所有SSL客户端证书都必须验证OK。自签名证书不构成OK验证。（v2.0.6中的新功能）
默认值：NO

virtual_use_local_privs
如果启用，虚拟用户将使用与本地用户相同的权限。默认情况下，虚拟用户将使用与匿名用户相同的权限，这往往更具限制性（特别是在写访问方面）
默认值：NO

WRITE_ENABLE
这可以控制是否允许任何更改文件系统的FTP命令。用户对ftp服务器的写权限。这些命令是：STOR，DELE，RNFR，RNTO，MKD，RMD，APPE和SITE
默认值：NO

xferlog_enable
如果启用，将保留日志文件，详细说明上载和下载。默认情况下，此文件将放在/var/log/vsftpd.log中，但可以使用配置设置vsftpd_log_file覆盖此位置
默认值：NO（但是示例配置文件启用它）

xferlog_std_format
如果启用，传输日志文件将以标准xferlog格式写入，如wu-ftpd所使用。这很有用，因为您可以重用现有的传输统计信息生成器 但是
默认格式更具可读性。此样式的日志文件的缺省位置是/ var / log / xferlog，但您可以使用xferlog_file设置进行 更改
默认值：NO

数字选项
以下是数字选项列表。必须将数字选项设置为非负整数。支持八进制数，以方便umask选项。要指定八进制数，请使用0作为数字的第一个数字
accept_timeout
远程客户端与PASV样式数据连接建立连接的超时（以秒为单位）
默认值：60

anon_max_rate
匿名客户端允许的最大数据传输速率（以字节/秒为单位）
默认值：0（无限制）

anon_umask
为匿名用户设置用于文件创建的umask的值。注意！如果要指定八进制值，请记住“0”前缀，否则该值将被视为基数为10的整数！
默认值：077

chown_upload_mode
要强制进行chown（）ed匿名上传的文件模式。（在v2.0.6中添加）
默认值：0600

connect_timeout
远程客户端响应我们的PORT样式数据连接的超时（以秒为单位）
默认值：60

data_connection_timeout
超时（以秒为单位），大致是允许数据传输停止而没有进度的最长时间。如果超时触发，则启动远程客户端
默认值：300

delay_failed_login
报告登录失败之前暂停的秒数
默认值：1

delay_successful_login
允许成功登录之前暂停的秒数
默认值：0

file_open_mode
用于创建上载文件的权限。Umasks应用于此值之上。如果您希望上传的文件可执行，您可能希望更改为0777
默认值：0666

ftp_data_port
PORT样式连接源自的端口（只要 启用名称不佳的 connect_from_port_20）
默认值：20

idle_session_timeout
超时（以秒为单位），即远程客户端在FTP命令之间可能花费的最长时间。如果超时触发，则启动远程客户端
默认值：300

listen_port
如果vsftpd处于独立模式，则它将侦听传入FTP连接的端口
默认值：21

local_max_rate
本地身份验证用户允许的最大数据传输速率（以字节/秒为单位）
默认值：0（无限制）

local_umask
为本地用户设置用于文件创建的umask的值。注意！如果要指定八进制值，请记住“0”前缀，否则该值将被视为基数为10的整数！
默认值：077

max_clients
如果vsftpd处于独立模式，则这是可以连接的最大客户端数。连接的任何其他客户端都将收到错误消息
默认值：0（无限制）

max_login_fails
在这么多登录失败之后，会话被终止
默认值：3

max_per_ip
如果vsftpd处于独立模式，则这是可以从同一源Internet地址连接的最大客户端数。如果客户端超过此限制，将收到错误消息
默认值：0（无限制）

pasv_max_port
为PASV样式数据连接分配的最大端口。可用于指定窄端口范围以协助防火墙
默认值：0（使用任何端口）

pasv_min_port
为PASV样式数据连接分配的最小端口。可用于指定窄端口范围以协助防火墙
默认值：0（使用任何端口）

trans_chunk_size
您可能不想更改此设置，但尝试将其设置为类似8192的设备，以获得更平滑的带宽限制器
默认值：0（让vsftpd选择合理的设置）

STRING OPTIONS
以下是字符串选项列表
anon_root
此选项表示vsftpd在匿名登录后尝试更改的目录。失败被默默地忽略了
默认值:(无）

banned_email_file
此选项是包含不允许的匿名电子邮件密码列表的文件的名称。如果 启用了选项deny_email_enable，则会查询此文件
默认值：/etc/vsftpd.banned_emails

banner_file
此选项是包含要在有人连接到服务器时显示的文本的文件的名称。如果设置，它将覆盖ftpd_banner 选项提供的标题字符串
默认值:(无）

ca_certs_file
此选项是用于加载证书颁发机构证书的文件的名称，用于验证客户端证书。遗憾的是，由于vsftpd使用受限制的文件系统空间（chroot）
因此未使用默认的SSL CA证书路径。（在v2.0.6中添加）
默认值:(无）

chown_username
这是获得匿名上传文件所有权的用户的名称。仅当设置了另一个选项chown_uploads时，此选项才有意义
默认值：root

chroot_list_file
该选项是包含本地用户列表的文件的名称，该列表将放置在其主目录中的chroot（）jail中。仅当 启用了选项chroot_list_enable时
此选项才有意义 。如果 启用了选项 chroot_local_user，则列表文件将成为未放置在chroot（）jail中的用户列表
默认值：/etc/vsftpd.chroot_list
chroot_list文件，"/etc/vsftpd.chroot_list"文件中包含的用户，在登录后将不能切换到自己目录以外的其他目录，由FTP服务器自动
地"chrooted"到用户自己的home目录下。这将使得chroot_list文件中的用户不能随意转到其他用户的FTP home目录下，从而有利于FTP服
务器的安全管理和隐私保护

cmds_allowed
此选项指定允许的FTP命令的逗号分隔列表（登录后.USER，PASS和QUIT以及其他始终允许登录前）。其他命令被拒绝
这是一种真正锁定FTP服务器的强大方法。示例：cmds_allowed = PASV，RETR，QUIT
默认值:(无）

cmds_denied
此选项指定以逗号分隔的拒绝FTP命令列表（登录后。始终允许登录前使用USER，PASS，QUIT等）
如果此命令和cmds_allowed上都出现命令， 则拒绝优先。（在v2.1.0中添加）
默认值:(无）

deny_file
此选项可用于设置文件名（和目录名等）的模式，这些模式不应以任何方式访问。受影响的项目不会被隐藏，但任何对它们做任何事情的尝试
（下载，更改到目录，影响目录中的内容等）都将被拒绝。此选项非常简单，不应用于严格的访问控制 - 应优先使用文件系统的权限。但是
此选项在某些虚拟用户设置中可能很有用。特别要注意的是，如果文件名可以通过各种名称访问（可能是由于符号链接或硬链接），那么必须注意
拒绝访问所有名称。如果项目的名称包含hide_file给出的字符串，或者它们与hide_file指定的正则表达式匹配，则将拒绝访问项目
注意vsftpd正则表达式匹配代码是一个简单的实现，它是完整正则表达式功能的子集。因此，您需要仔细而详尽地测试此选项的任何应用程序
并且由于其更高的可靠性，建议您对任何重要的安全策略使用文件系统权限。支持的正则表达式语法是任意数量的* ,? 和unnested {，}运算符
仅在路径的最后一个组件上支持正则表达式匹配，例如a / b /？支持，但/？/ c不支持。示例：deny_file = {*。mp3，*。mov，.private}
并且由于其更高的可靠性，建议您对任何重要的安全策略使用文件系统权限。支持的正则表达式语法是任意数量的* ,? 和unnested {，}运算符
仅在路径的最后一个组件上支持正则表达式匹配，例如a / b /？支持，但/？/ c不支持。示例：deny_file = {*。mp3，*。mov，.private}
并且由于其更高的可靠性，建议您对任何重要的安全策略使用文件系统权限。支持的正则表达式语法是任意数量的* ,? 和unnested {，}运算符
仅在路径的最后一个组件上支持正则表达式匹配，例如a / b /？支持，但/？/ c不支持。示例：deny_file = {*。mp3，*。mov，.private}
默认值:(无）

dsa_cert_file
此选项指定用于SSL加密连接的DSA证书的位置
默认值:(无 - RSA证书就足够了）

dsa_private_key_file
此选项指定用于SSL加密连接的DSA私钥的位置。如果未设置此选项，则预期私钥与证书位于同一文件中
默认值:(无）

email_password_file
此选项可用于提供secure_email_list_enable 设置使用的备用文件
默认值：/etc/vsftpd.email_passwords

ftp_username
这是我们用于处理匿名FTP的用户的名称。该用户的主目录是匿名FTP区域的根目录
默认值：ftp

ftpd_banner
此字符串选项允许您在首次进入连接时覆盖vsftpd显示的问候语横幅
默认值:(无 - 显示默认的vsftpd横幅）

guest_username
有关 访客登录的描述，请参阅布尔值设置 guest_enable。此设置是访客用户映射到的真实用户名
默认值：ftp

hide_file
此选项可用于设置文件名（和目录名称等）的模式，这些模式应该从目录列表中隐藏。尽管被隐藏，但是知道实际使用的名称的客户可以完全访问文件/目录等
如果项目的名称包含hide_file给出的字符串，或者它们与hide_file指定的正则表达式匹配，则将隐藏这些项目。请注意，vsftpd的正则表达式匹配代码
是一个简单的实现，它是完整正则表达式功能的子集。有关 具体支持的正则表达式语法的详细信息
请参阅 deny_file。示例：hide_file = {*。mp3，.hidden，hide *，h？}
默认值:(无）

listen_address
如果vsftpd处于独立模式，则此设置可能会覆盖（所有本地接口的）默认侦听地址。提供数字IP地址
默认值:(无）

listen_address6
与listen_address类似，但指定IPv6侦听器的默认侦听地址（如果设置了listen_ipv6，则使用该地址）。格式是标准IPv6地址格式
默认值:(无）

local_root
此选项表示vsftpd在本地（即非匿名）登录后尝试更改的目录。失败被默默地忽略了
默认值:(无）

message_file
此选项是输入新目录时我们查找的文件的名称。内容显示给远程用户。仅当 启用了选项dirmessage_enable时，此选项才有意义
默认值：.message

nopriv_user
这是vsftpd在完全没有特权的情况下使用的用户名。请注意，这应该是专用用户，而不是任何人。在大多数机器上，用户没有倾向于使用很多重要的东西
默认值：没人

pam_service_name
此字符串是vsftpd将使用的PAM服务的名称
默认值：ftp

pasv_address设置
使用此选项可覆盖vsftpd将响应PASV命令而通告的IP地址。提供数字IP地址，除非 启用了pasv_addr_resolve
在这种情况下，您可以提供在启动时为您解析DNS的主机名
默认值:(无 - 地址取自传入的连接套接字）

rsa_cert_file
此选项指定用于SSL加密连接的RSA证书的位置
默认值：/usr/share/ssl/certs/vsftpd.pem

rsa_private_key_file
此选项指定用于SSL加密连接的RSA私钥的位置。如果未设置此选项，则预期私钥与证书位于同一文件中
默认值:(无）

secure_chroot_dir
此选项应该是空目录的名称。此外，ftp用户不应该写入该目录。此目录有时用作安全chroot（）jail，vsftpd不需要文件系统访问
默认值：/ usr / share / empty

的ssl_ciphers
此选项可用于选择vsftpd允许加密SSL连接的SSL密码。有关 更多详细信息，请参见 密码手册页。请注意，限制密码可能是一种有用的安全预防措施
因为它可以防止恶意远程方强制使用已发现问题的密码
默认值：DES-CBC3-SHA

user_config_dir
这个功能强大的选项允许基于每个用户覆盖手册页中指定的任何配置选项。用法很简单，最好用一个例子来说明。如果将 user_config_dir 设置
为 / etc / vsftpd_user_conf 然后以用户“chris”身份登录，则vsftpd将 在会话期间应用文件 / etc / vsftpd_user_conf / chris
中的设置。此文件的格式详见本手册页！请注意，并非所有设置都对每个用户有效。例如，许多设置仅在用户会话启动之前。不会影响每个用户的任何
行为的设置示例包括listen_address，banner_file，max_per_ip，max_clients，xferlog_file等
默认值:(无）

user_sub_token
此选项与虚拟用户结合使用非常有用。它用于根据模板为每个虚拟用户自动生成主目录。例如，如果通过guest_username指定的真实用户的主目录 是
 / home / virtual / $ USER，并且 user_sub_token 设置为 $ USER，那么当虚拟用户fred登录时，他将结束通常是在目录
 / home / virtual / fred中。如果local_root 包含 user_sub_token，则此选项也会 生效
默认值:(无）

userlist_file
此选项是userlist_enable 选项处于活动状态时加载的文件的名称
默认值：/etc/vsftpd.user_list

vsftpd_log_file
此选项是我们编写vsftpd样式日志文件的文件的名称。仅当 设置了选项xferlog_enable并且未设置xferlog_std_format时， 才会写入此日志
或者，如果已设置选项dual_log_enable，则会写入 。另一个复杂因素 - 如果您设置了 syslog_enable，则不会写入此文件，而是将输出发送到系统日志
默认值：/var/log/vsftpd.log

xferlog_file
此选项是我们编写wu-ftpd样式传输日志的文件的名称。仅当 设置了 xferlog_enable选项以及xferlog_std_format时才会写入传输日志
或者，如果已设置选项dual_log_enable，则会写入
默认值：/var/log/xferlog
```

### rsync

#### 选项

```markdown
rsync [OPTION]... SRC DEST
rsync [OPTION]... SRC [USER@]host:DEST
rsync [OPTION]... [USER@]HOST:SRC DEST
rsync [OPTION]... [USER@]HOST::SRC DEST
rsync [OPTION]... SRC [USER@]HOST::DEST
rsync [OPTION]... rsync://[USER@]HOST[:PORT]/SRC [DEST]

对应于以上六种命令格式，rsync有六种不同的工作模式：

* 同步本地目录。当SRC和DES路径信息都不包含有单个冒号":"分隔符时就启动这种工作模式
  如：`rsync -a /data /backup`

* 使用一个远程shell程序(如rsh、ssh)来实现将本地机器的内容拷贝到远程机器。当DST路径地址包含单个冒号":"分隔符时启动该模式
  如：`rsync -avz *.c foo:src`

* 使用一个远程shell程序(如rsh、ssh)来实现将远程机器的内容拷贝到本地机器。当SRC地址路径包含单个冒号":"分隔符时启动该模式
  如：`rsync -avz foo:src/bar /data`

* 拉取：从远程rsync服务器中拷贝文件到本地机。当SRC路径信息包含"::"分隔符时启动该模式
  如：`rsync -av root@192.168.78.192::www /databack`

* 推送：从本地机器拷贝文件到远程rsync服务器中。当DST路径信息包含"::"分隔符时启动该模式
  如：`rsync -av /databack root@192.168.78.192::www`

* 列远程机的文件列表。这类似于rsync传输，不过只要在命令中省略掉本地机信息即可
  如：`rsync -v rsync://192.168.78.192/www`

https://rsync.samba.org/resources.html
原理论文：https://www.samba.org//~tridge/phd_thesis.pdf
how_rsync_works:https://rsync.samba.org/how-rsync-works.html
https://rsync.samba.org/examples.html

```

```markdown
-v, --verbose               详细模式输出
    --info=FLAGS            细粒度的info输出
    --debug=FLAGS           细粒度的debug输出
    --msgs2stderr           special output handling for debugging
-q, --quiet                 suppress non-error messages
    --no-motd               suppress daemon-mode MOTD (see manpage caveat)
-c, --checksum              根据checksum跳过文件，而不是mod-time和size
-a, --archive               归档模式;等同于-rlptgoD (no -H,-A,-X)
    --no-OPTION             turn off an implied OPTION (e.g. --no-D)
-r, --recursive             对子目录进行递归处理
-R, --relative              使用相对路径的名称
    --no-implied-dirs       don't send implied dirs with --relative
-b, --backup                make backups (see --suffix & --backup-dir)
    --backup-dir=DIR        make backups into hierarchy based in DIR
    --suffix=SUFFIX         set backup suffix (default ~ w/o --backup-dir)
-u, --update                跳过接收方比发送方较新的文件，即跳过所有已存在于DST且文件时间晚于SRC的文件
    --inplace               update destination files in-place (SEE MAN PAGE)
    --append                append data onto shorter files
    --append-verify         like --append, but with old data in file checksum
-d, --dirs                  transfer directories without recursing
-l, --links                 保留软链接
-L, --copy-links            将符号链接转换为对应的引用文件或引用目录
    --copy-unsafe-links     only "unsafe" symlinks are transformed
    --safe-links            ignore symlinks that point outside the source tree
    --munge-links           munge symlinks to make them safer (but unusable)
-k, --copy-dirlinks         transform symlink to a dir into referent dir
-K, --keep-dirlinks         treat symlinked dir on receiver as dir
-H, --hard-links            保留硬链接
-p, --perms                 保留文件权限
-E, --executability         preserve the file's executability
    --chmod=CHMOD           affect file and/or directory permissions
-A, --acls                  preserve ACLs (implies --perms)
-X, --xattrs                preserve extended attributes
-o, --owner                 保留文件属主信息(super-user only)
-g, --group                 保留文件属组信息
    --devices               preserve device files (super-user only)
    --copy-devices          copy device contents as regular file
    --specials              preserve special files
-D                          same as --devices --specials
-t, --times                 保留文件修改信息
-O, --omit-dir-times        omit directories from --times
-J, --omit-link-times       omit symlinks from --times
    --super                 receiver attempts super-user activities
    --fake-super            store/recover privileged attrs using xattrs
-S, --sparse                对稀疏文件(它能尝试在文件内容大多为空时更有效率地使用文件系统的空间)进行特殊处理以节省DST空间
    --preallocate           allocate dest files before writing them
-n, --dry-run               不进行实际的传输，只作为测试运行
-W, --whole-file            copy files whole (without delta-xfer algorithm)
-x, --one-file-system       don't cross filesystem boundaries
-B, --block-size=SIZE       强制校检块大小
-e, --rsh=COMMAND           指定rsh、ssh方式进行传输
    --rsync-path=PROGRAM    指定远程服务器rsync命令所在路径
    --existing              skip creating new files on receiver
    --ignore-existing       skip updating files that already exist on receiver
    --remove-source-files   sender removes synchronized files (non-dirs)
    --del                   an alias for --delete-during
    --delete                从DST删除在SRC中没有的文件(慎用)
    --delete-before         receiver deletes before transfer, not during
    --delete-during         receiver deletes during the transfer
    --delete-delay          find deletions during, delete after
    --delete-after          receiver deletes after transfer, not during
    --delete-excluded       also delete excluded files from destination dirs
    --ignore-missing-args   ignore missing source args without error
    --delete-missing-args   delete missing source args from destination
    --ignore-errors         delete even if there are I/O errors
    --force                 force deletion of directories even if not empty
    --max-delete=NUM        don't delete more than NUM files
    --max-size=SIZE         don't transfer any file larger than SIZE
    --min-size=SIZE         don't transfer any file smaller than SIZE
    --partial               keep partially transferred files
    --partial-dir=DIR       put a partially transferred file into DIR
    --delay-updates         put all updated files into place at transfer's end
-m, --prune-empty-dirs      prune empty directory chains from the file-list
    --numeric-ids           don't map uid/gid values by user/group name
    --usermap=STRING        custom username mapping
    --groupmap=STRING       custom groupname mapping
    --chown=USER:GROUP      simple username/groupname mapping
    --timeout=SECONDS       set I/O timeout in seconds
    --contimeout=SECONDS    set daemon connection timeout in seconds
-I, --ignore-times          不跳过那些具有相同size和mod-time的文件
-M, --remote-option=OPTION  send OPTION to the remote side only
    --size-only             skip files that match in size
    --modify-window=NUM     compare mod-times with reduced accuracy
-T, --temp-dir=DIR          在DIR中创建临时文件
-y, --fuzzy                 find similar file for basis if no dest file
    --compare-dest=DIR      also compare destination files relative to DIR
    --copy-dest=DIR         ... and include copies of unchanged files
    --link-dest=DIR         hardlink to files in DIR when unchanged
-z, --compress              传输过程中对文件数据进行压缩处理
    --compress-level=NUM    explicitly set compression level
    --skip-compress=LIST    skip compressing files with a suffix in LIST
-C, --cvs-exclude           像CVS一样自动忽略文件
-f, --filter=RULE           add a file-filtering RULE
-F                          same as --filter='dir-merge /.rsync-filter'
                            repeated: --filter='- .rsync-filter'
    --exclude=PATTERN       排除与PATTERN相匹配的文件
    --exclude-from=FILE     排除与FILE文件中pattern匹配的文件
    --include=PATTERN       不排除与PATTERN相匹配的文件
    --include-from=FILE     不排除FILE文件中指定pattern匹配的文件
    --files-from=FILE       从FILE读取源文件名列表匹配要传输的文件
-0, --from0                 all *-from/filter files are delimited by 0s
-s, --protect-args          no space-splitting; only wildcard special-chars
    --address=ADDRESS       bind address for outgoing socket to daemon
    --port=PORT             指定其他的rsync服务端口
    --sockopts=OPTIONS      specify custom TCP options
    --blocking-io           use blocking I/O for the remote shell
    --stats                 give some file-transfer stats
-8, --8-bit-output          leave high-bit chars unescaped in output
-h, --human-readable        output numbers in a human-readable format
    --progress              显示传输过程
-P                          same as --partial --progress
-i, --itemize-changes       output a change-summary for all updates
    --out-format=FORMAT     output updates using the specified FORMAT
    --log-file=FILE         log what we're doing to the specified FILE
    --log-file-format=FMT   log updates using the specified FMT
    --password-file=FILE    从FILE文件读取rsync后台程序访问密码
    --list-only             list the files instead of copying them
    --bwlimit=RATE          限制socket I/O带宽
    --outbuf=N|L|B          set output buffering to None, Line, or Block
    --write-batch=FILE      write a batched update to FILE
    --only-write-batch=FILE like --write-batch but w/o updating destination
    --read-batch=FILE       read a batched update from FILE
    --protocol=NUM          force an older protocol version to be used
    --iconv=CONVERT_SPEC    request charset conversion of filenames
    --checksum-seed=NUM     set block/file checksum seed (advanced)
-4, --ipv4                  prefer IPv4
-6, --ipv6                  prefer IPv6

--checksum详解：改变了rsync检查文件是否已更改以及是否需要传输的方式。如果没有此选项，rsync将使用“快速检查”（默认情况下），检查每个
文件的大小和上次修改时间在发送方和接收方之间是否匹配。此选项将其更改为比较每个具有匹配大小的文件的128位校验和。生成校验和意味着双方将
花费大量的磁盘I / O来读取传输中文件中的所有数据（这是在进行任何读取以传输更改的文件之前），因此这会减慢速度显著
```

#### 实例

```bash
: << comment
注意事项：
# 创建密码文件，用户名可以和密码可以随便定义,服务端需要user:pass形式，客户端只需要pass就行
echo "work:abc123" > /etc/rsyncd.passwd
chmod 600 /etc/rsyncd.passwd

windows下rsync权限注意事项：密码文件执行：chmod 600 rsync.passwd,用当前用户执行chown 用户名 rsync.passwd

将文件发送到Microsoft文件系统：如果文件的修改时间是一个奇数值，但是接收文件系统只能存储偶数值，
那么rsync将重新传输太多文件。您可以通过指定--modify-window = 1选项来避免这种情况
comment

# 使用SSH方式rsync进行同步
rsync -vzrtopg --progress -e ssh --delete work@172.16.78.192:/www/* /databack/experiment/rsync
```

##### 后台服务方式

```markdown
https://download.samba.org/pub/rsync/rsyncd.conf.html
# 默认安装好rsync程序后，配置文件为“/etc/rsyncd.conf”
# 全局参数，使用[global]模块名称指示一个或多个全局参数的开始，名称必须小写
[global]
motd file = path_to_file  # 指定每次连接时显示给客户端的消息
# pid file = /var/run/rsyncd.pid    # 将进程id写入此文件
port = PORT # 指定rsync进程端口号，默认873
address = x # 通过指定此值来覆盖守护程序默认监听的IP地址
socket options = xx # 设置各种套接字选项，这些选项可能会使传输更快或更慢,具体参考setsockopt()系统调用的手册页
listen backlog = x  # 覆盖rsync进程对于连接的backlog value，默认为5

# 模块名称不能包含斜杠或右方括号,名称内部包含多个空格都作为一个空格对待，首尾空格会被丢弃
[module]
comment = x  #  当客户端获取可用模块列表时，此参数指定在模块名称旁边显示的描述字符串，默认没注释
path = x    # 指定rsync守护程序文件系统中的目录，必须为每个module指定此参数。可以使用百分号将变量名称括起来，从而使路径的值基于环境变量。
              也可以引用用户连接时由rsync设置的变量。例如在路径中使用授权用户的名称：path = /home/%RSYNC_USER_NAME%
use chroot = yes  # 值为yes时，rsync守护程序将chroot到“ path”，然后再开始与客户端进行文件传输。好处是可以提供额外的保护，以防止可能的
                    安全漏洞，但是它的缺点是需要超级用户特权
daemon chroot = x   # 此参数指定守护程序在开始与客户端通信之前将其更改为chroot的路径
numeric ids = x # 启用此参数将禁用当前守护程序模块的按名称映射用户和组。这样可以防止守护程序尝试加载任何与用户/组相关的文件或库。
                  默认情况下，此参数对chroot模块启用，对非chroot模块禁用。启用chroot的模块不应启用此参数
charset = x # 指定了存储模块文件名的字符集。如果客户端使用--iconv选项，则守护程序将使用“字符集”参数的值，而与客户端实际传递的字符集无关。
              这使守护进程rsync可以在chroot模块中支持字符集转换且还可以确保以一致的方式完成名称转换。如果未设置“ charset”参数，
              则--iconv选项将被拒绝
max connections = x #

# uid = nobody
# gid = nobody
# max connections = 4
# exclude = lost+found/
# transfer logging = yes
# timeout = 900
# ignore nonreadable = yes
# dont compress   = *.gz *.tgz *.zip *.z *.Z *.rpm *.deb *.bz2

# [ftp]
#        path = /home/ftp
#        comment = ftp export area
#
#



```

### inotify-tools

```markdown
https://github.com/rvoicilas/inotify-tools/wiki

inotifywait [-hcmrq] [-e <event> ] [-t <seconds> ] [--format <fmt> ] [--timefmt <fmt> ] <file> [ ... ]


```

### samba

```markdown

```

### Bind-DNS

```bash
# DNS-bind安装和配置
: << comment
https://ftp.isc.org/
http://www.isc.org/downloads/

解决bind9 dumping master file tmp-xxxx open permission denied问题，解决方法如下：
对于Redhat Linux，只要在slave那台的dns主机上修改/etc/sysconfig/named加上ENABLE_ZONE_WRITE=yes，重启named即可
对于Ubuntu系统，修改/etc/apparmor.d/usr.sbin.named，查找/etc/bind/** r，修改成/etc/bind/** rw
然后重启apparmor：/etc/init.d/apparmor rstart或者reload配置：cat /etc/apparmor.d/usr.sbin.named | sudo apparmor_parser -r
comment

wget https://www.isc.org/downloads/file/bind-9-10-6-p1/?version=tar-gz
tar -zxvf
./configure --with-openssl --enable-threads --with-libxml2
make && make install
touch /etc/named.conf
named -V
named -g

```

### Bind-DHCP

```markdown
https://www.isc.org/dhcp/
https://www.isc.org/kea/
```

### mail server

* Postfix（MTA，邮件传输代理）通过SMTP协议（简单邮件传输协议）将邮件从您的服务器传输到其他服务器
* Dovecot（MDA，邮件传递代理）通过IMAP（Internet邮件访问协议）或POP3协议（邮局协议3）将邮件从服务器传递到
* Thunderbird（MUA，邮件用户代理）

[邮件服务器比较参考](https://en.wikipedia.org/wiki/Comparison_of_mail_servers)

```bash
# PostFix Dovecot邮件服务器和后台管理
dovecot.org
postfix.org
```

#### email安装过程相关问题和配置

```bash
# 出现Maildir权限问题解决方法
# 检查selinux状态并关闭
sestatus -v # 检查selinux状态

getenforce 0 #  设置selinux为permissive模式()临时关闭，不用重启，重启后失效
# 永久生效方法：修改/etc/selinux/config文件，SELINUX=enforcing更改为SELINUX=disabled
```
