# CrossGFW

## Shadowsocks

```sh
# Python,shadowsocks-python最初的版本是由clowwindy写的. 它的目的是提供一个简单易用和易于部署的实现，具有基本的shadowsocks功能
PyPI
首先确定Python是2.6或2.7.
$ python --version
Python 2.6.8
接着用pip安装
$ pip install shadowsocks
```

```sh
GitHub
Checkout源代码并直接运行脚本
$ git clone https://github.com/shadowsocks/shadowsocks.git
$ cd shadowsocks
$ python setup.py
```

```sh
Go,shadowsocks-go是用Go语言编写的最先进的端口，专为大型系统设计。它实现了多端口-多密码特性，适用于有用户管理和流量统计支持的付费服务提供商。该端口由cyfdecyf维护。

预编译二进制：
从http://dl.chenyufei.info/shadowsocks/下载

GitHub
Use go get to install the scripts.

$ go get github.com/shadowsocks/shadowsocks-go/cmd/shadowsocks-server
shadowsocks-go is licensed under the MIT license.
```

```sh
C with libev[shadowsocks-libev],shadowsocks-libev是一个轻量级的、功能齐全的嵌入式设备端口和低端的终端盒。这是一个纯粹的C实现，它占用了数千个连接的占用空间很小(几兆字节)。这个端口由madeye维护。

Debian/Ubuntu:
Shadowsocks-libev is available in the official repository for Debian 9("Stretch"), unstable, Ubuntu 16.10 and later derivatives:

sudo apt update
sudo apt install shadowsocks-libev
For Debian Jessie users, please install it from jessie-backports:

sudo sh -c 'printf "deb http://httpredir.debian.org/debian jessie-backports
main" > /etc/apt/sources.list.d/jessie-backports.list'
sudo apt-get update
sudo apt-get -t jessie-backports install shadowsocks-libev
GitHub
Build and install the project from source codes.

$ sudo apt-get install --no-install-recommends build-essential autoconf libtool \
        libssl-dev gawk debhelper dh-systemd init-system-helpers pkg-config asciidoc \
        xmlto apg libpcre3-dev zlib1g-dev libev-dev libudns-dev libsodium-dev libmbedtls-dev libc-ares-dev automake
$ git clone https://github.com/shadowsocks/shadowsocks-libev.git
$ cd shadowsocks-libev
$ git submodule update --init
$ ./autogen.sh && ./configure && make
$ sudo make install
```

```sh
C++ with Qt,libQtShadowsocks是一个轻量级的、超快的shadowsocks库，它是用c++编写的qt5。客户机shadowsocks-libqss可以在客户端和服务器端使用。该端口由librehat维护。

Prebuilt binaries
Download pre-built binaries from https://github.com/shadowsocks/libQtShadowsocks/releases

GitHub
$ git clone https://github.com/shadowsocks/libQtShadowsocks.git
$ cd libQtShadowsocks
$ qmake
$ make -j4
$ sudo make install
libQtShadowsocks is licensed under the GNU Lesser General Public License, version 3.0
```

```sh
Perl,Net::Shadowsocks is an asynchronous, non-blocking Shadowsocks client and server Perl module maintained by @zhou0.

Setting up
You need a Perl interpreter to execute Perl program. Any Unix like system , including Linux and Mac OS X, has Perl pre-installed. Windows does not have Perl installed by default, you need to install Strawberry Perl.The source code is available on CPAN and github. Download from CPAN https://metacpan.org/release/Net-Shadowsocks or download from github https://github.com/zhou0/shadowsocks-perl

Installing
On Unix like systems,either

$ perl Build.PL
$ ./Build
$ ./Build test
$ ./Build install
or

$ perl Makefile.PL
$ make
$ make test
$ make install
You might need to change make to dmake or nmake depending on the compiler toolchain used on Windows. If You have cpan, you can also install using this command

$ cpan Net::Shadowsocks
Running
There is a server.pl script under the eg directory. Put your config.json in the same directory as server.pl and run the server.pl script there.

```

## shadowsocks配置

```sh

Shadowsocks允许的JSON配置格式:

{
    "server":"my_server_ip",
    "server_port":8388,
    "local_port":1080,
    "password":"barfoo!",
    "timeout":600,
    "method":"chacha20-ietf-poly1305"
}

每个字段的解释:

server: your hostname or server IP (IPv4/IPv6).
server_port: server port number.
local_port: local port number.
password: a password used to encrypt transfer.
timeout: connections timeout in seconds.
method: encryption method.

加密方式：
最强的选项是AEAD密码。推荐的选择是“chacha20-ietf-poly1305”或“aes-256-gcm”。其他流密码被实现，但不提供完整性和真实性。除非另有规定，加密方法默认为“table”，这是不安全的。
```

***
***
***

## shadowsocks优化

## 系统层面

```sh
基于kvm架构vps的优化
这方面SS给出了非常详尽的优化指南，主要有：优化内核参数，开启TCP Fast Open
####1.1优化内核参数 编辑vi /etc/sysctl.conf
复制进去

# max open files
fs.file-max = 51200
# max read buffer
net.core.rmem_max = 67108864
# max write buffer
net.core.wmem_max = 67108864
# default read buffer
net.core.rmem_default = 65536
# default write buffer
net.core.wmem_default = 65536
# max processor input queue
net.core.netdev_max_backlog = 250000
# max backlog
net.core.somaxconn = 4096

# resist SYN flood attacks
net.ipv4.tcp_syncookies = 1
# reuse timewait sockets when safe
net.ipv4.tcp_tw_reuse = 1
# turn off fast timewait sockets recycling
net.ipv4.tcp_tw_recycle = 0
# short FIN timeout
net.ipv4.tcp_fin_timeout = 30
# short keepalive time
net.ipv4.tcp_keepalive_time = 1200
# outbound port range
net.ipv4.ip_local_port_range = 10000 65000
# max SYN backlog
net.ipv4.tcp_max_syn_backlog = 8192
# max timewait sockets held by system simultaneously
net.ipv4.tcp_max_tw_buckets = 5000

net.ipv4.tcp_fastopen = 3
net.ipv4.tcp_mem = 25600 51200 102400

# TCP receive buffer
net.ipv4.tcp_rmem = 4096 87380 67108864
# TCP write buffer
net.ipv4.tcp_wmem = 4096 65536 67108864
# turn on path MTU discovery
net.ipv4.tcp_mtu_probing = 1

# for high-latency network
net.ipv4.tcp_congestion_control = hybla
# forward ipv4
net.ipv4.ip_forward = 1

保存生效
执行sysctl -p命令reload配置文件
其中最后的hybla是为高延迟网络（如美国，欧洲）准备的算法，需要内核支持，测试内核是否支持，在终端输入：
sysctl net.ipv4.tcp_available_congestion_control
如果结果中有hybla，则证明你的内核已开启hybla，如果没有hybla，可以用命令modprobe tcp_hybla开启。

对于低延迟的网络（如日本，香港等），可以使用htcp，可以非常显著的提高速度，首先使用modprobe tcp_htcp开启，再将net.ipv4.tcp_congestion_control = hybla改为net.ipv4.tcp_congestion_control = htcp，建议EC2日本用户使用这个算法。

```

## TCP优化

```sh

1.修改文件句柄数限制
如果是ubuntu/centos均可修改/etc/sysctl.conf
找到fs.file-max这一行，修改其值为1024000，并保存退出。然后执行sysctl -p使其生效
修改vi /etc/security/limits.conf文件，加入

*               soft    nofile           512000
*               hard    nofile          1024000

针对centos,还需要修改vi /etc/pam.d/common-session文件，加入
session required pam_limits.so

2.修改vi /etc/profile文件，加入
ulimit -SHn 1024000
然后重启服务器执行ulimit -n，查询返回1024000即可。

sysctl.conf报错解决方法
修复modprobe的：
rm -f /sbin/modprobe
ln -s /bin/true /sbin/modprobe
修复sysctl的：
rm -f /sbin/sysctl
ln -s /bin/true /sbin/sysctl

1.3 锐速

锐速是TCP底层加速软件,官方已停止推出永久免费版本,但网上有破解版可以继续使用。需要购买的话先到锐速官网注册帐号,并确认内核版本是否支持锐速的版本。

一键安装速锐破解版

wget -N --no-check-certificate https://github.com/91yun/serverspeeder/raw/master/serverspeeder.sh && bash serverspeeder.sh

一键卸载

chattr -i /serverspeeder/etc/apx* && /serverspeeder/bin/serverSpeeder.sh uninstall -f

设置

Enter your accelerated interface(s) [eth0]: eth0
Enter your outbound bandwidth [1000000 kbps]: 1000000
Enter your inbound bandwidth [1000000 kbps]: 1000000
Configure shortRtt-bypass [0 ms]: 0
Auto load ServerSpeeder on linux start-up? [n]:y #是否开机自启
Run ServerSpeeder now? [y]:y #是否现在启动

执行lsmod，看到有appex0模块即说明锐速已正常安装并启动。

至此，安装就结束了，但还有后续配置。
修改vi /serverspeeder/etc/config文件的几个参数以使锐速更好的工作

accppp="1" #加速PPTP、L2TP V-P-N；设为1表示开启，设为0表示关闭
advinacc="1" #高级入向加速开关；设为 1 表示开启，设为 0 表示关闭；开启此功能可以得到更好的流入方向流量加速效果；
maxmode="1" #最大传输模式；设为 1 表示开启；设为 0 表示关闭；开启后会进一步提高加速效果，但是可能会降低有效数据率。
rsc="1" #网卡接收端合并开关；设为 1 表示开启，设为 0 表示关闭；在有些较新的网卡驱动中，带有 RSC 算法的，需要打开该功能。
l2wQLimit="512 4096" #从 LAN 到 WAN 加速引擎在缓冲池充满和空闲时分别能够缓存的数据包队列的长度的上限；该值设置的高会获得更好的加速效果，但是会消耗更多的内存
w2lQLimit="512 4096" #从 WAN 到 LAN 加速引擎在缓冲池充满和空闲时分别能够缓存的数据包队列的长度的上限；该值设置的高会获得更好的加速效果，但是会消耗更多的内存

重读配置以使配置生效/serverspeeder/bin/serverSpeeder.sh reload

查看锐速当前状态/serverspeeder/bin/serverSpeeder.sh stats

查看所有命令/serverspeeder/bin/serverSpeeder.sh help

停止/serverspeeder/bin/serverSpeeder.sh stop

启动/serverspeeder/bin/serverSpeeder.sh start

重启锐速/serverspeeder/bin/serverSpeeder.sh restart
1.4 开启TCP Fast Open

这个需要服务器和客户端都是Linux 3.7+的内核，一般Linux的服务器发行版只有debian jessie有3.7+的，客户端用Linux更是珍稀动物，所以这个不多说，如果你的服务器端和客户端都是Linux 3.7+的内核，那就在服务端和客户端的vi /etc/sysctl.conf文件中再加上一行。

# turn on TCP Fast Open on both client and server side
net.ipv4.tcp_fastopen = 3

然后把vi /etc/shadowsocks.json配置文件中"fast_open": false改为"fast_open": true。这样速度也将会有非常显著的提升。
1. 加密层面

####2.1 安装M2Crypto 这个可以提高SS的加密速度，安装办法：
Debian/Ubuntu
apt-get install python-m2crypto
安装之后重启SS，速度将会有一定的提升

CentOS
先安装依赖包：
yum install -y openssl-devel gcc swig python-devel autoconf libtool
安装setuptools：

wget --no-check-certificate https://raw.githubusercontent.com/iMeiji/shadowsocks_install/master/ez_setup.py
python ez_setup.py install

再通过pip安装M2Crypto：
pip install M2Crypto 或者pip install M2Crypto --upgrade
2.3 使用CHACHA20加密算法

首先，安装libsodium，让系统支持chacha20算法。
Debian/Ubuntu

apt-get install build-essential
wget https://download.libsodium.org/libsodium/releases/LATEST.tar.gz
tar xf LATEST.tar.gz && cd libsodium*
./configure && make && make install
运行命令:ldconfig

CentOS

yum groupinstall "Development Tools"
wget https://download.libsodium.org/libsodium/releases/LATEST.tar.gz
tar zxf LATEST.tar.gz
cd libsodium*
./configure
make
make install
vi /etc/ld.so.conf
添加一行：
/usr/local/lib
保存退出后，运行命令：
ldconfig

然后修改ss加密方式：
vi /etc/shadowsocks.json "method":"aes-256-cfb"改成"method":"chacha20",重启SS即可/etc/init.d/shadowsocks restart

3.网络层面

此外，选择合适的端口也能优化梯子的速度，广大SS用户的实践经验表明，检查站（GFW）存在一种机制来降低自身的运算压力，即常用的协议端口（如http，smtp，ssh，https，ftp等）的检查较少，所以建议SS绑定这些常用的端口（如：21，22，25，80，443），速度也会有显著提升。
如果你还要给小伙伴爬，那我建议开启多个端口而不是共用，这样网络会更加顺畅。
3.1 防火墙设置（如有）

自动调整MTU
iptables -I FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu

开启 NAT （记得把 eth0 改成自己的网卡名，openvz 的基本是 venet0 ）
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

开启 IPv4 的转发
sysctl -w net.ipv4.ip_forward=1

打开 443 端口

iptables -I INPUT -p tcp --dport 443 -j ACCEPT
iptables -I INPUT -p udp --dport 443 -j ACCEPT

重启防火墙iptables：
service iptables restart

```

***
***
***

## shadowsocks定时脚本.sh

```sh

#!/usr/bin/env bash
#=================================================================#
#   System Required:  CentOS, Debian, Ubuntu                      #
#   Description: Check Shadowsocks Server is running or not       #
#   Author: Teddysun <i@teddysun.com>                             #
#   Visit: https://shadowsocks.be/6.html                          #
#=================================================================#

name=(Shadowsocks Shadowsocks-Python ShadowsocksR Shadowsocks-Go Shadowsocks-libev)
path=/var/log
[[ ! -d ${path} ]] && mkdir -p ${path}
log=${path}/shadowsocks-crond.log

shadowsocks_init[0]=/etc/init.d/shadowsocks
shadowsocks_init[1]=/etc/init.d/shadowsocks-python
shadowsocks_init[2]=/etc/init.d/shadowsocks-r
shadowsocks_init[3]=/etc/init.d/shadowsocks-go
shadowsocks_init[4]=/etc/init.d/shadowsocks-libev

i=0
for init in ${shadowsocks_init[@]}; do
    pid=""
    if [ -f ${init} ]; then
        ss_status=`${init} status`
        if [ $? -eq 0 ]; then
            pid=`echo $ss_status | sed 's/[^0-9]*//g'`
        fi

        if [ -z ${pid} ]; then
            echo "`date +"%Y-%m-%d %H:%M:%S"` ${name[$i]} is not running" >> ${log}
            echo "`date +"%Y-%m-%d %H:%M:%S"` Starting ${name[$i]}" >> ${log}
            ${init} start &>/dev/null
            if [ $? -eq 0 ]; then
                echo "`date +"%Y-%m-%d %H:%M:%S"` ${name[$i]} start success" >> ${log}
            else
                echo "`date +"%Y-%m-%d %H:%M:%S"` ${name[$i]} start failed" >> ${log}
            fi
        else
            echo "`date +"%Y-%m-%d %H:%M:%S"` ${name[$i]} is running with pid $pid" >> ${log}
        fi

    fi
    ((i++))
done

```

## PPTPD VPN服务器架设

```sh
一、pptpd安装
1.查看是否安装了dkms:
rpm -q dkms
若未安装，则：
rpm -ivh dkms-2.0.17.5-1.noarch.rpm,
2.查看是否支持ppp
rpm -q ppp
查看已安装的ppp，是否支持MPPE
strings '/usr/sbin/pppd' |grep -i mppe|wc -l
若结果显示为0表示不支持，显示一较大的数表示支持
不支持的话，下高版本安装
3.查看pptpd版本
rpm -q pptpd
若未安装，则安装
rpm -ivh pptpd-1.*.**.rpm
4.确保linux内核支持MPPE加密协议
内核版本低于2.6.14要安装一MPPE内核补丁
rpm -ivh kernel-mppe-2.4.20-8.i686.rpm
安装后检查kernel MPPE是否安装成功
# modprobe ppp-compress-18 && echo "OK"
i386版本在64位机器上可以装，但装完后拨号不能使用
配置
/etc/pptpd.conf、/ETC/PPP/OPTIONS.PPTPD ,/etc/ppp/chap-secrets三个文件含有配置信息
/etc/pptpd.conf中配置debug信息、ppp组件使用的配置文件、localip和remoteip
localip是VPN服务器的地址，remoteip是VPN服务器分给客户端的IP(可以是一个网段)

debug------把所有debug信息记入系统日志/var/log/messages

option /etc/ppp/options.pptpd------------PPP组件将使用的配置文件

localip 221.6.15.150--------------分配给VPN服务器的地址，可以任意起
remoteip 192.168.1.1-100--VPN服务器分配给客户端的IP段，可以任意起

/etc/ppp/options.pptpd
配置dns等

配置/etc/ppp/chap-secrets文件
chap-secrets文件中规定了客户端的用户名和密码、服务程序、IP地址

如：xyz * 123456 *
“xyx”是Client端的VPN用户名；“server”对应的是VPN服务器的名字，该名字必须和/etc/ppp/options.pptpd文件中指明的一样，或者设
置成“*”号来表示自动识别服务器；“secret”对应的是登录密码；“IP addresses”对的是可以拨入的客户端IP地址，如果不需要做特别限
制，可以将其设置为“*”号

连接测试


OPENVPN 服务器架设

安装
在线安装.
Yum –y install openvpn* lzo* libpcap*
配置
复制样本文件到主目录 /etc/openvpn
cp -a /usr/share/openvpn/easy-rsa/2.0/ /etc/openvpn/
复制样本配置文件到主目录 /etc/openvpn/
cp /usr/share/doc/openvpn-2.1/sample-config-files/server.conf /etc/openvpn

source ./vars
./clean-all
./build-ca
./build-key-server server
./build-key client
./build-dh
复制server keys
cp /etc/openvpn/easy-rsa/2.0/keys/ca.* /etc/openvpn/
cp /etc/openvpn/easy-rsa/2.0/keys/server.* /etc/openvpn/
复制dh
cp /etc/openvpn/easy-rsa/2.0/keys/dh1024.pem /etc/openvpn/
vi /etc/openvpn/server.conf
