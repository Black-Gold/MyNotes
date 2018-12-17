# PPTPD VPN服务器架设

平台
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
#modprobe ppp-compress-18 && echo "OK"
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
