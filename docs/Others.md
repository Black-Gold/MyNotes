# 其他兴趣爱好

## http状态码

```markdown
1xx（临时响应）表示临时响应并需要请求者继续执行操作的状态代码
100（继续）请求者应当继续提出请求。 服务器返回此代码表示已收到请求的第一部分，正在等待其余部分。

101（切换协议）请求者已要求服务器切换协议，服务器已确认并准备切换

2xx （成功）表示成功处理了请求的状态代码
200（成功）服务器已成功处理了请求。通常，这表示服务器提供了请求的网页

201（已创建）请求成功并且服务器创建了新的资源

202（已接受） 服务器已接受请求，但尚未处理

203（非授权信息）服务器已成功处理了请求，但返回的信息可能来自另一来源

204（无内容）服务器成功处理了请求，但没有返回任何内容

205（重置内容）服务器成功处理了请求，但没有返回任何内容

206（部分内容）服务器成功处理了部分 GET 请求

3xx （重定向）表示要完成请求，需要进一步操作。 通常，这些状态代码用来重定向
300（多种选择）针对请求，服务器可执行多种操作。 服务器可根据请求者 (user agent) 选择一项操作，或提供操作列表供请求者选择

301（永久移动）请求的网页已永久移动到新位置。 服务器返回此响应（对 GET 或 HEAD 请求的响应）时，会自动将请求者转到新位置

302（临时移动）服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求

303（查看其他位置）请求者应当对不同的位置使用单独的 GET 请求来检索响应时，服务器返回此代码

304（未修改）自从上次请求后，请求的网页未修改过。 服务器返回此响应时，不会返回网页内容

305（使用代理）请求者只能使用代理访问请求的网页。 如果服务器返回此响应，还表示请求者应使用代理

307（临时重定向）服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求

4xx（请求错误）这些状态代码表示请求可能出错，妨碍了服务器的处理
400（错误请求）服务器不理解请求的语法

401（未授权）请求要求身份验证。 对于需要登录的网页，服务器可能返回此响应

403（禁止）服务器拒绝请求

404（未找到）服务器找不到请求的网页

405（方法禁用）禁用请求中指定的方法

406（不接受）无法使用请求的内容特性响应请求的网页

407（需要代理授权）此状态代码与 401（未授权）类似，但指定请求者应当授权使用代理

408（请求超时）服务器等候请求时发生超时

409（冲突）服务器在完成请求时发生冲突。 服务器必须在响应中包含有关冲突的信息

410（已删除）如果请求的资源已永久删除，服务器就会返回此响应

411（需要有效长度）服务器不接受不含有效内容长度标头字段的请求

412（未满足前提条件）服务器未满足请求者在请求中设置的其中一个前提条件

413（请求实体过大）服务器无法处理请求，因为请求实体过大，超出服务器的处理能力

414（请求的 URI 过长）请求的 URI（通常为网址）过长，服务器无法处理

415（不支持的媒体类型）请求的格式不受请求页面的支持

416（请求范围不符合要求）如果页面无法提供请求的范围，则服务器会返回此状态代码

417（未满足期望值）服务器未满足”期望”请求标头字段的要求

5xx（服务器错误）这些状态代码表示服务器在尝试处理请求时发生内部错误。 这些错误可能是服务器本身的错误，而不是请求出错
500（服务器内部错误）服务器遇到错误，无法完成请求

501（尚未实施）服务器不具备完成请求的功能。 例如，服务器无法识别请求方法时可能会返回此代码

502（错误网关）服务器作为网关或代理，从上游服务器收到无效响应

503（服务不可用）服务器目前无法使用（由于超载或停机维护）。 通常，这只是暂时状态

504（网关超时）服务器作为网关或代理，但是没有及时从上游服务器收到请求

505（HTTP 版本不受支持）服务器不支持请求中所用的 HTTP 协议版本


RFC 6585 最近刚刚发布，该文档描述了 4 个新的 HTTP 状态码
HTTP 协议一直在演变，新的状态码对于开发 REST 服务或者说是基于 HTTP 的服务非常有用，下面这四个新的状态码以及是否应该使用

428 Precondition Required (要求先决条件) 先决条件是客户端发送 HTTP 请求时，如果想要请求能成功必须满足一些预设的条件
一个好的例子就是 If-None-Match 头，经常在 GET 请求中使用，如果指定了 If-None-Match ，那么客户端只在响应中的 ETag 改变后才会重新接收回应
先决条件的另外一个例子就是 If-Match 头，这个一般用在 PUT 请求上用于指示只更新没被改变的资源，这在多个客户端使用 HTTP 服务时用来防止彼此间不会覆盖相同内容
当服务器端使用 428 Precondition Required 状态码时，表示客户端必须发送上述的请求头才能执行请求，这个方法为服务器提供一种有效的方法来阻止 'lost update' 问题

429 Too Many Requests (太多请求)
当你需要限制客户端请求某个服务数量时，该状态码就很有用，也就是请求速度限制
在此之前，有一些类似的状态码，例如 '509 Bandwidth Limit Exceeded'. Twitter 使用 420 （这不是HTTP定义的状态码）
如果你希望限制客户端对服务的请求数，可使用 429 状态码，同时包含一个 Retry-After 响应头用于告诉客户端多长时间后可以再次请求服务

431 Request Header Fields Too Large (请求头字段太大)
某些情况下，客户端发送 HTTP 请求头会变得很大，那么服务器可发送 431 Request Header Fields Too Large 来指明该问题

511 Network Authentication Required (要求网络认证)
对我来说这个状态码很有趣，如果你在开发一个 HTTP 服务器，你不一定需要处理该状态码，但如果你在编写 HTTP 客户端，那这个状态码就非常重要
```

## GoogleTricks

```markdown

"" 双引号
- 排除字符
好: baidu.com 使用冒号搜索特定的网站
am*zing 使用星号通配符
related:amazon.com 找到与其他网站类似的网站
Black or red 合并搜索
filetype:pdf 找到特定文件
site:www.so.com 在360搜索引擎中寻找
allintitle: allinurl:
@twitter 搜索社交媒体
info: 获取有关网站的详细信息
cache:  查看Google的网站缓存版本
google.com/history 搜索历史
inurl:  搜索的关键字在url链接中
intitle:    搜索的关键字在网页标题中
inauthor:"作者名"  根据作者名搜索相关书籍及信息

AND：返回查询两端的结果。例：growth hacks AND youtube
NOT：返回除NOT之后的所有结果。例：monty python NOT bbc
使用~搜索词的同义词
使用in的翻译和转换
process management inurl:forum|forums|discussion|viewthread|showthread|viewtopic|showtopic 论坛
google.com/ncr。NCR代表无国家/地区重定向
process automation filetype:pdf

搜索ftp
inurl:ftp:// -inurl:http:// -inurl:https://

-inurl(html|htm|php) intitle:"index of" +"last modified" +"parent directory" +description +size

-inurl:(htm|html|php) intitle:"index of" +"last modified" +"parent directory" +description +size +(wmv|avi)

-inurl:(htm|html|php) intitle:"index of" +"last modified" +"parent directory" +description +size +(jpg|gif)

-inurl:(htm|html|php) intitle:"index of" +"last modified" +"parent directory" +description +size +(wma|mp3)

[-inurl:(htm|html|php) intitle:" index of"  +" last modified"  +" parent directory" +description +size +(jpg|gif) “britney spears"]

```

| Function | Execution |
| :------: | :------: |
| To search for an exact phrase, with the same words in the same order | Place quotation marks (“) around the phrase you’d like to search for Ex: “to be, or not to be" |
| To exclude results that include a particular word or site when searching words with multiple meanings | Place a dash (-) before the word or site you want to omit Ex: phoenix -arizona |
| To search for Google+ pages or blood types | Place an addition sign (+) in front of the Google+ user or after the blood type Ex: +Chrome and AB+ |
| To search for social tags | Place the at symbol (@) before the social tag you’d like to search Ex: @digitaltrends |
| To search for prices | Place a dollar sign ($) before the value Ex: canon $400 |
| To search for a phrase with missing words | Place an asterisk \* within the search as a placeholder for any unknown terms Ex: if you give a * a * |
| To search for a range of numbers, usually pertaining to prices and measurements | Place two periods between the designated numbers you want to search between Ex: $75..$200 |
| To search popular hashtags for trending topics | Place a hashtag in front of the desired topicEx: #throwbackthursday |

| Function | Execution |
| :------: | :------: |
| To search for results from certain sites and domains | Place “site:" in front of the site or domain from which you want to pull results Ex: apple watch site:digitaltrends.com |
| To search for pages that link to a certain page | Place “link:" in front of the site or domain you want to find pages linking to Ex: link:digitaltrends.com |
| To search for sites that are similar to a designated site or domain | Place “related:" in front of the site or domain you want to find similar results of Ex: related:digitaltrends.com |
| To search for pages that just have one of several words | Place “OR" between the two words you are searching for Ex: world series 2013 OR 2014 |
| To search for designated information about a specific site or domain, including cached pages, and those linking to the site | Place “info:" in front of the site or domain you want information about Ex: info:digitaltrends.com |
| To search what a page looked like the last time Google crawled the site | Place “cache:" in front of the page housing the cache you’d like to view Ex: cache:digitaltrends.com |
| To search for a specific file type | Place “filetype:" in front of the specific file type you’re looking for Ex: matthew mcconaughey filetype:gif |

| Function | Execution |
| :------: | :------: |
| To search Google using voice commands | Click the microphone icon in the search bar and begin talking |
| To search Google for a specific image | Click the camera icon in the search bar and paste the image URL |
| To set a timer | Enter “set timer for" followed by the desired amount of time |
| To check the weather for a specific area | Enter “weather" followed by a zipcode or city |
| To search for the sunrise and sunset times for a specific area | Enter “sunrise" or “sunset" followed by a zipcode or city |
| To look up the definition for a given word | Enter “define" followed by your desired term |
| To look up the origins for a given word | Enter “etymology" followed by your desired word |
| To look up the time for a specific region | Enter “time" followed by the particular region |
| To look up your IP address | Enter “ip address" in the search bar |
| To check the status of a flight | Enter the flight number in the search bar |
| To look up stock quotes | Enter the desired stock symbol in the search bar |
| To look up the date for a specific holiday | Enter the name of the holiday in the search bar |
| To track a package | Enter the tracking number in the search bar |
| To use the calculator | Enter the equation in the search bar |
| To define a word | Enter “define" followed by your desired word |
| To convert currency or measurements | Enter the first amount and unit, type “to", and then enter the second unit |
| To look up film showings | Enter “movies" followed by your zipcode or city |
| To look up sports scores | Enter the sports team in the search bar |
| To look up nutritional facts about an item, or compare nutritional facts | Enter the name of the product, or enter “compare" followed by the items you want to compare |
| To look up a celebrity’s Bacon Number | Enter “bacon number" followed by the name of the celebrity |
| To roll a six-sided die | Enter [roll a dice] in the search bar |

## Security

## Shadowsocks

```bash
# Python,shadowsocks-python最初的版本是由clowwindy写的. 它的目的是提供一个简单易用和易于部署的实现，具有基本的shadowsocks功能
PyPI
首先确定Python是2.6或2.7.
$ python --version
Python 2.6.8
接着用pip安装
pip install shadowsocks
```

```bash
GitHub
Checkout源代码并直接运行脚本
git clone https://github.com/shadowsocks/shadowsocks.git
cd shadowsocks
python setup.py
```

```bash
Go,shadowsocks-go是用Go语言编写的最先进的端口，专为大型系统设计。它实现了多端口-多密码特性，适用于有用户管理和流量统计支持的付费服务提供商。该端口由cyfdecyf维护

预编译二进制：
从http://dl.chenyufei.info/shadowsocks/下载

GitHub
Use go get to install the scripts.

$ go get github.com/shadowsocks/shadowsocks-go/cmd/shadowsocks-server
shadowsocks-go is licensed under the MIT license.
```

```bash
C with libev[shadowsocks-libev],shadowsocks-libev是一个轻量级的、功能齐全的嵌入式设备端口和低端的终端盒。这是一个纯粹的C实现，它占用了数千个连接的占用空间很小(几兆字节)。这个端口由madeye维护

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

sudo apt-get install --no-install-recommends build-essential autoconf libtool \
        libssl-dev gawk debhelper dh-systemd init-system-helpers pkg-config asciidoc \
        xmlto apg libpcre3-dev zlib1g-dev libev-dev libudns-dev libsodium-dev libmbedtls-dev libc-ares-dev automake
git clone https://github.com/shadowsocks/shadowsocks-libev.git
cd shadowsocks-libev
git submodule update --init
./autogen.sh && ./configure && make
sudo make install
```

```bash
C++ with Qt,libQtShadowsocks是一个轻量级的、超快的shadowsocks库，它是用c++编写的qt5。客户机shadowsocks-libqss可以在客户端和服务器端使用。该端口由librehat维护

Prebuilt binaries
Download pre-built binaries from https://github.com/shadowsocks/libQtShadowsocks/releases

GitHub
git clone https://github.com/shadowsocks/libQtShadowsocks.git
cd libQtShadowsocks
qmake
make -j4
sudo make install
libQtShadowsocks is licensed under the GNU Lesser General Public License, version 3.0
```

```bash
Perl,Net::Shadowsocks is an asynchronous, non-blocking Shadowsocks client and server Perl module maintained by @zhou0.

Setting up
You need a Perl interpreter to execute Perl program. Any Unix like system , including Linux and Mac OS X, has Perl pre-installed. Windows does not have Perl installed by default, you need to install Strawberry Perl.The source code is available on CPAN and github. Download from CPAN https://metacpan.org/release/Net-Shadowsocks or download from github https://github.com/zhou0/shadowsocks-perl

Installing
On Unix like systems,either

perl Build.PL
./Build
./Build test
./Build install
or

perl Makefile.PL
make
make test
make install
You might need to change make to dmake or nmake depending on the compiler toolchain used on Windows. If You have cpan, you can also install using this command

$ cpan Net::Shadowsocks
Running
There is a server.pl script under the eg directory. Put your config.json in the same directory as server.pl and run the server.pl script there.

```

### shadowsocks配置

```bash

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
最强的选项是AEAD密码。推荐的选择是“chacha20-ietf-poly1305”或“aes-256-gcm”。其他流密码被实现，但不提供完整性和真实性。除非另有规定，加密方法默认为“table”，这是不安全的
```

***
***
***

### shadowsocks优化

#### 系统层面

```bash
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
如果结果中有hybla，则证明你的内核已开启hybla，如果没有hybla，可以用命令modprobe tcp_hybla开启

对于低延迟的网络（如日本，香港等），可以使用htcp，可以非常显著的提高速度，首先使用modprobe tcp_htcp开启，再将net.ipv4.tcp_congestion_control = hybla改为net.ipv4.tcp_congestion_control = htcp，建议EC2日本用户使用这个算法

```

#### TCP优化

```bash

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
然后重启服务器执行ulimit -n，查询返回1024000即可

sysctl.conf报错解决方法
修复modprobe的：
rm -f /sbin/modprobe
ln -s /bin/true /sbin/modprobe
修复sysctl的：
rm -f /sbin/sysctl
ln -s /bin/true /sbin/sysctl

1.3 锐速

锐速是TCP底层加速软件,官方已停止推出永久免费版本,但网上有破解版可以继续使用。需要购买的话先到锐速官网注册帐号,并确认内核版本是否支持锐速的版本

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

执行lsmod，看到有appex0模块即说明锐速已正常安装并启动

至此，安装就结束了，但还有后续配置
修改vi /serverspeeder/etc/config文件的几个参数以使锐速更好的工作

accppp="1" #加速PPTP、L2TP V-P-N；设为1表示开启，设为0表示关闭
advinacc="1" #高级入向加速开关；设为 1 表示开启，设为 0 表示关闭；开启此功能可以得到更好的流入方向流量加速效果
maxmode="1" #最大传输模式；设为 1 表示开启；设为 0 表示关闭；开启后会进一步提高加速效果，但是可能会降低有效数据率
rsc="1" #网卡接收端合并开关；设为 1 表示开启，设为 0 表示关闭；在有些较新的网卡驱动中，带有 RSC 算法的，需要打开该功能
l2wQLimit="512 4096" #从 LAN 到 WAN 加速引擎在缓冲池充满和空闲时分别能够缓存的数据包队列的长度的上限；该值设置的高会获得更好的加速效果，但是会消耗更多的内存
w2lQLimit="512 4096" #从 WAN 到 LAN 加速引擎在缓冲池充满和空闲时分别能够缓存的数据包队列的长度的上限；该值设置的高会获得更好的加速效果，但是会消耗更多的内存

重读配置以使配置生效/serverspeeder/bin/serverSpeeder.sh reload

查看锐速当前状态/serverspeeder/bin/serverSpeeder.sh stats

查看所有命令/serverspeeder/bin/serverSpeeder.sh help

停止/serverspeeder/bin/serverSpeeder.sh stop

启动/serverspeeder/bin/serverSpeeder.sh start

重启锐速/serverspeeder/bin/serverSpeeder.sh restart
1.4 开启TCP Fast Open

这个需要服务器和客户端都是Linux 3.7+的内核，一般Linux的服务器发行版只有debian jessie有3.7+的，客户端用Linux更是珍稀动物，所以这个不多说，如果你的服务器端和客户端都是Linux 3.7+的内核，那就在服务端和客户端的vi /etc/sysctl.conf文件中再加上一行

# turn on TCP Fast Open on both client and server side
net.ipv4.tcp_fastopen = 3

然后把vi /etc/shadowsocks.json配置文件中"fast_open": false改为"fast_open": true。这样速度也将会有非常显著的提升
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

首先，安装libsodium，让系统支持chacha20算法
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

此外，选择合适的端口也能优化梯子的速度，广大SS用户的实践经验表明，检查站（GFW）存在一种机制来降低自身的运算压力，即常用的协议端口（如http，smtp，ssh，https，ftp等）的检查较少，所以建议SS绑定这些常用的端口（如：21，22，25，80，443），速度也会有显著提升
如果你还要给小伙伴爬，那我建议开启多个端口而不是共用，这样网络会更加顺畅
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

#### shadowsocks定时脚本

```bash

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

### PPTPD VPN服务器架设

```bash
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

```

## SipCode

```txt
1xx 通知性应答
100 正在尝试
180 正在拨打
181 正被转接
182 正在排队
183 通话进展
2xx = 成功应答
200 OK
202 被接受：用于转介
3xx = 转接应答
300 多项选择
301 被永久迁移
302 被暂时迁移
305 使用代理服务器
380 替代服务
4xx = 呼叫失败
400 呼叫不当
401 未经授权：只供注册机构使用，代理服务器应使用代理服务器授权407
402 要求付费（预订为将来使用)
403 被禁止的
404 未发现：未发现用户
405 不允许的方法
406 不可接受
407 需要代理服务器授权
408 呼叫超时：在预定时间内无法找到用户
410 已消失：用户曾经存在，但已从此处消失
413 呼叫实体过大
414 呼叫URI过长
415 不支持的媒体类型
416 不支持的URI方案
420 不当扩展：使用了不当SIP协议扩展，服务器无法理解该扩展
421 需要扩展
423 时间间隔过短
480 暂时不可使用
481 通话/事务不存在
482 检测到循环
483 跳数过多
484 地址不全
485 模糊不清
486 此处太忙
487 呼叫被终止
488 此处不可接受
491 呼叫待批
493 无法解读：无法解读 S/MIME文体部分
5xx = 服务器失败
500 服务器内部错误
501 无法实施：SIP呼叫方法在此处无法实施
502 不当网关
503 服务不可使用
504 服务器超时
505 不支持该版本：服务器不支持SIP协议的这个版本
513 消息过长
6xx = 全局失败
600 各处均忙
603 拒绝
604 无处存在
606 不可使用
```

## Freeswitch

学习freeswitch需要掌握的内容：c/c++编程, socket编程 ，sip ,sdp，rtp ,tcp/ip 协议，XML，脚本语言JavaScript，lua，erlang,perl。数据库sqlite，mysql。系统编程知识：多进程线程同步（临界区，互斥量，信号灯，事件），APR，模块动态共享机制

```sys

修改freeswitch的systemd的服务配置文件，修改后的配置如下：

[Unit]
Description=FreeSWITCH
After=syslog.target network.target
After=postgresql.service postgresql-9.3.service postgresql-9.4.service mysqld.service httpd.service

[Service]
User=freeswitch
EnvironmentFile=-/etc/sysconfig/freeswitch
LimitNOFILE=65000
LimitNPROC=40960
# RuntimeDirectory is not yet supported in CentOS 7. A workaround is to use /etc/tmpfiles.d/freeswitch.conf
# RuntimeDirectory=/run/freeswitch
# RuntimeDirectoryMode=0750
WorkingDirectory=/opt/app/freeswitch/var/run/freeswitch/
ExecStart=/opt/app/freeswitch/bin/freeswitch -nc -nf $FREESWITCH_PARAMS
ExecReload=/usr/bin/kill -HUP $MAINPID

[Install]
WantedBy=multi-user.target

默认的配置没有 LimitNOFILE 和 LimitNPROC 这 2 个配置，重新加载 systemd 的配置，重启服务即可：

systemctl daemon-reload
systemctl restart freeswitch.service

```

### 错误处理

普通用户运行 FreeSWITCH 不能调整进程优先级：
Systemd 是以 freeswitch 用户运行 FreeSWITCH 的，可能是以 root 启动以后再对进程降级，变成 freeswitch 用户（这个是我猜的），不知道 systemd 的启动方式和 root 用户运行 /opt/app/freeswitch/bin/freeswitch -nc -nf -u freeswitch -g freeswitch 有什么区别，反正启动以后是没有 nice 的权限了，导致日志有如下报错：

freeswitch[31890]: ERROR: Failed to set SCHED_FIFO scheduler (Operation not permitted)
freeswitch[31890]: ERROR: Could not set nice level
不想 set uid，给文件加上 nice 的能力即可：(如下命令)
setcap CAP_SYS_NICE+ep /opt/app/freeswitch/bin/freeswitch

## Asterisk

## Raspberrypi

```bash
关于HDMI设置
/opt/vc/bin/tvservice
tvservice [OPTION]...
  -p, --preferred                   Power on HDMI with preferred settings
  -e, --explicit="GROUP MODE DRIVE" Power on HDMI with explicit GROUP (CEA, DMT, CEA_3D_SBS, CEA_3D_TB, CEA_3D_FP, CEA_3D_FS)
                                      MODE (see --modes) and DRIVE (HDMI, DVI)
  -t, --ntsc                        Use NTSC frequency for HDMI mode (e.g. 59.94Hz rather than 60Hz)
  -c, --sdtvon="MODE ASPECT [P]"    Power on SDTV with MODE (PAL or NTSC) and ASPECT (4:3 14:9 or 16:9) Add P for progressive
  -o, --off                         Power off the display
  -m, --modes=GROUP                 Get supported modes for GROUP (CEA, DMT)
  -M, --monitor                     Monitor HDMI events
  -s, --status                      Get HDMI status
  -a, --audio                       Get supported audio information
  -d, --dumpedid <filename>         Dump EDID information to file
  -j, --json                        Use JSON format for --modes output
  -n, --name                        Print the device ID from EDID


查找当前树莓派CPU模块名
cat /lib/modules/$(uname -r)/modules.builtin | grep wdt

还可以设置内存耗尽就重启，如min-memory =1 前的注释#号去掉
还可以设置监控的间隔，如 interval = 1 前的注释#号去掉，该1为任意数字，单位是秒，默认是10秒一次健康检查

/etc/init.d/watchdog start


chkconfig watchdog on

/etc/sysctl.conf
kernel.panic = 10

sysctl -P


1、通过shell编程获得cup温度值



进入树莓派中断控制台，依次输入以下指令获取实时温度值：

#进入根目录

   cd /

#读取temp文件，获得温度值

   cat sys/class/thermal/thermal_zone0/temp

#系统返回实时值

   40622


[说明]

      1）通过cat命令读取存放在sys/class/thermal/thermal_zone0目录下的温度文件temp获得返回值

      2）返回值为一个5位数的数值，实际温度为将该值除以1000即可！单位为摄氏度！


2、通过C语言编程获得cpu温度值

#选定一个目录，并在目录中创建cpu_temp.c文件，将以下代码输入：

#include <stdio.h>
#include <stdlib.h>

//导入文件控制函数库
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

#define TEMP_PATH "/sys/class/thermal/thermal_zone0/temp"
#define MAX_SIZE 20
int main(void)
{
    int fd;
    double temp = 0;
    char buf[MAX_SIZE];

    // 以只读方式打开/sys/class/thermal/thermal_zone0/temp
    fd = open(TEMP_PATH, O_RDONLY);

        //判断文件是否正常被打开
    if (fd < 0)
{
        fprintf(stderr, "failed to open thermal_zone0/temp\n");
        return -1;
    }

    // 读取内容
    if (read(fd, buf, MAX_SIZE) < 0)
    {
        fprintf(stderr, "failed to read temp\n");
        return -1;
    }

    // 转换为浮点数打印
    temp = atoi(buf) / 1000.0;
    printf("temp: %.3f\n", temp);

    // 关闭文件
    close(fd);
}

#编译C代码，输入以下指令：

  gcc -o cpu_temp cpu_temp.c

#运行程序

  ./cpu_temp

#系统返回实时值

  temp : 40.622

程序解读：

  1)关于open()、read()、close()函数使用，可看：【fcntl.h函数库的常用函数使用】

  2)atoi(buf)函数是将buf中的字符串数据转换层整形数

  3)gcc -o cpu_temp cpu_temp.c ：gcc为编译器、 -o参数表示将cpu_temp.c文件编译成可执行文件并存放到 cpu_temp文件夹中

2、通过python语言编程获得cpu温度值

#选定一个目录，并在目录中创建cpu_temp.py文件，将以下代码输入：


# -*- coding: utf-8 -*-  
# 打开文件  
file = open("/sys/class/thermal/thermal_zone0/temp")  
# 读取结果，并转换为浮点数  
temp = float(file.read()) / 1000  
# 关闭文件  
file.close()  
# 向终端控制台打印  
print "temp : %.3f" %temp


#执行脚本

  Python cpu_temp.py

cpu温度获取这样写会不会更轻巧些
def getCPUtemperature():
return os.popen( ‘/opt/vc/bin/vcgencmd measure_temp’ ).read()[5:9]
或者直接读取系统自己的状态更新，省去模拟shell
def getCPUtemperature():
with open( “/sys/class/thermal/thermal_zone0/temp” ) as tempFile:
res = tempFile.read()
res=str(float(res)/1000)
return res

同理，getRaminfo()可以写成： return os.popen(‘free|tail -n +2’).readline().split()[1:4]
getDiskSpace()可以写成：return os.popen(‘df -h /|tail -n +2’).readline().split()[1:5]

popen的效率不高，但是配合shell相当灵活～～

```

```torrent
kickasstorrent
thepiratebay
```

```bash
# 获取公网ip
# 普通文本格式
curl icanhazip.com
curl http://members.3322.org/dyndns/getip
curl ip.6655.com/ip.aspx
curl ident.me
curl ipecho.net/plain
curl whatismyip.akamai.com
curl ifconfig.me
curl myip.dnsomatic.com
curl ifconfig.co
curl http://ipv4.ip.sb

# json格式
curl ipinfo.io

# 返回ip和地区
curl ip.6655.com/ip.aspx?area=1
curl cip.cc
/usr/bin/wget -q -O - http://icanhazip.com/ | /usr/bin/tail

https://pv.sohu.com/cityjson?ie=utf-8

# 动态dns
https://github.com/NewFuture/DDNS

# vagrant box下载链接，以此类推
https://app.vagrantup.com/ansible/boxes/tower/versions/3.5.1/providers/virtualbox.box
https://vagrantcloud.com/centos/boxes/7/versions/1905.1/providers/virtualbox.box

技术点梳理
https://docs.bk.tencent.com/technology/

```
