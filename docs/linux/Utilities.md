# Iproute2&Net-tools

NET-TOOLS VS IPROUTE对照

| NET-TOOLS COMMANDS | IPROUTE COMMANDS |
| :------: | :------: |
| arp -a | ip neigh |
| arp -v | ip -s neigh |
| arp -s 192.168.1.1 1:2:3:4:5:6 | ip neigh add 192.168.1.1 lladdr 1:2:3:4:5:6 dev eth1 |
| arp -i eth1 -d 192.168.1.1 | ip neigh del 192.168.1.1 dev eth1 |
| ifconfig -a | ip addr |
| ifconfig eth0 down | ip link set eth0 down |
| ifconfig eth0 up | ip link set eth0 up |
| ifconfig eth0 192.168.1.1 | ip addr add 192.168.1.1/24 dev eth0 |
| ifconfig eth0 netmask 255.255.255.0 | ip addr add 192.168.1.1/24 dev eth0 |
| ifconfig eth0 mtu 9000 | ip link set eth0 mtu 9000 |
| ifconfig eth0:0 192.168.1.2 | ip addr add 192.168.1.2/24 dev eth0 |
| netstat | ss |
| netstat -neopa | ss -neopa |
| netstat -g | ip maddr |
| route | ip route |
| route add -net 192.168.1.0 netmask 255.255.255.0 dev eth0 | ip route add 192.168.1.0/24 dev eth0 |
| route add default gw 192.168.1.1 | ip route add default via 192.168.1.1 |

## Iproute2

## 选型

```bash
ip [ OPTIONS ] OBJECT { COMMAND | help }
ip [ -force ] -batch filename
where  OBJECT := { link | address | addrlabel | route | rule | neigh | ntable |
                   tunnel | tuntap | maddress | mroute | mrule | monitor | xfrm |
                   netns | l2tp | fou | macsec | tcp_metrics | token | netconf | ila }
       OPTIONS := { -V[ersion] | -s[tatistics] | -d[etails] | -r[esolve] |
                    -h[uman-readable] | -iec |
                    -f[amily] { inet | inet6 | ipx | dnet | mpls | bridge | link } |
                    -4 | -6 | -I | -D | -B | -0 |
                    -l[oops] { maximum-addr-flush-attempts } | -br[ief] |
                    -o[neline] | -t[imestamp] | -ts[hort] | -b[atch] [filename] |
                    -rc[vbuf] [size] | -n[etns] name | -a[ll] |?-c[olor]}
-s：输出更详细的信息
-f：强制使用指定的协议族
-4：指定使用的网络层协议是IPv4协议
-6：指定使用的网络层协议是IPv6协议
-0：输出信息每条记录输出一行，即使内容较多也不换行显示
-r：显示主机时，不使用IP地址，而使用主机的域名

```
```
-h, --help      帮助信息
-V, --version   程序版本信息
-n, --numeric   不解析服务名称
-r, --resolve   解析主机名
-a, --all       显示所有套接字（sockets）
-l, --listening 显示监听状态的套接字（sockets）
-o, --options   显示计时器信息
-e, --extended  显示详细的套接字（sockets）信息
-m, --memory    显示套接字（socket）的内存使用情况
-p, --processes 显示使用套接字（socket）的进程
-i, --info      显示 TCP内部信息
-s, --summary   显示套接字（socket）使用概况
-4, --ipv4      仅显示IPv4的套接字（sockets）
-6, --ipv6      仅显示IPv6的套接字（sockets）
-0, --packet    显示 PACKET 套接字（socket）
-t, --tcp       仅显示 TCP套接字（sockets）
-u, --udp       仅显示 UCP套接字（sockets）
-d, --dccp      仅显示 DCCP套接字（sockets）
-w, --raw       仅显示 RAW套接字（sockets）
-x, --unix      仅显示 Unix套接字（sockets）
-f, --family=FAMILY  显示 FAMILY类型的套接字（sockets），FAMILY可选，支持  unix, inet, inet6, link, netlink
-A, --query=QUERY, --socket=QUERY
      QUERY := {all|inet|tcp|udp|raw|unix|packet|netlink}[,QUERY]
-D, --diag=FILE     将原始TCP套接字（sockets）信息转储到文件
 -F, --filter=FILE  从文件中都去过滤器信息
       FILTER := [ state TCP-STATE ] [ EXPRESSION ]
```

## 实例

```bash
ss -t -a    # 显示TCP连接
ss -s       # 显示 Sockets 摘要
ss -l       # 列出所有打开的网络连接端口
ss -pl      # 查看进程使用的socket
ss -lp | grep 3306  # 找出打开套接字/端口应用程序
ss -u -a    显示所有UDP Sockets
ss -o state established '( dport = :smtp or sport = :smtp )' # 显示所有状态为established的SMTP连接
ss -o state established '( dport = :http or sport = :http )' # 显示所有状态为Established的HTTP连接
ss -o state fin-wait-1 '( sport = :http or sport = :https )' dst 193.233.7/24  # 列举出处于 FIN-WAIT-1状态的源端口为 80或者 443，目标网络为 193.233.7/24所有 tcp套接字

# ss 和 netstat 效率对比
time netstat -at
time ss

# 匹配远程地址和端口号
# ss dst ADDRESS_PATTERN
ss dst 192.168.1.5
ss dst 192.168.119.113:http
ss dst 192.168.119.113:smtp
ss dst 192.168.119.113:443

# 匹配本地地址和端口号
# ss src ADDRESS_PATTERN
ss src 192.168.119.103
ss src 192.168.119.103:http
ss src 192.168.119.103:80
ss src 192.168.119.103:smtp
ss src 192.168.119.103:25
```

**将本地或者远程端口和一个数比较**

```bash
# ss dport OP PORT 远程端口和一个数比较
# ss sport OP PORT 本地端口和一个数比较
# OP 可以代表以下任意一个:
# <= or le : 小于或等于端口号
# >= or ge : 大于或等于端口号
# == or eq : 等于端口号
# != or ne : 不等于端口号
# < or gt : 小于端口号
# > or lt : 大于端口号
ss  sport = :http
ss  dport = :http
ss  dport \> :1024
ss  sport \> :1024
ss sport \< :32000
ss  sport eq :22
ss  dport != :22
ss  state connected sport = :http
ss \( sport = :http or sport = :https \)
ss -o state fin-wait-1 \( sport = :http or sport = :https \) dst 192.168.1/24
```

**用TCP 状态过滤Sockets**

```bash
ss -4 state closing
# ss -4 state FILTER-NAME-HERE
# ss -6 state FILTER-NAME-HERE
# FILTER-NAME-HERE 可以代表以下任何一个：
# established、 syn-sent、 syn-recv、 fin-wait-1、 fin-wait-2、 time-wait、 closed、 close-wait、 last-ack、 listen、 closing、
# all : 所有以上状态
# connected : 除了listen and closed的所有状态
# synchronized :所有已连接的状态除了syn-sent
# bucket : 显示状态为maintained as minisockets,如：time-wait和syn-recv.
# big : 和bucket相反.
```

 **显示ICP连接**

```
[root@localhost ~]# ss -t -a


 **显示 Sockets 摘要**

```
[root@localhost ~]# ss -s


列出当前的established, closed, orphaned and waiting TCP sockets

 **列出所有打开的网络连接端口**

```
[root@localhost ~]# ss -l


 **查看进程使用的socket**

```
[root@localhost ~]# ss -pl

 **找出打开套接字/端口应用程序**

```
[root@localhost ~]# ss -pl | grep 3306
0      0                            *:3306                          *:*        users:(("mysqld",1718,10))
```

 **显示所有UDP Sockets**

```
[root@localhost ~]# ss -u -a

```

#### 出所有端口为 22（ssh）的连接

```bash
ss state all sport = :ssh

```
```bash
OBJECT # 网络对象
link # 指网络设备，通过此对象命令，我们可以查看及更改网络设备的属性
addr # 地址管理
neigh # arp表管理
route # 路由管理
rule # 路由策略
maddr # 多址广播地址
mroute # 多播路由缓存管理
tunnel # 通道管理

ip link # 显示链路信息
ip link show dev eth0 # 显示eth0接口链路信息

# 显示路由ip route [类似route -n]
ip route | column -t
ip route del 192.168.0.0/24 dev eth1
ip route add 192.168.0.0/24 dev eth1
ip route del via 10.2.255.254  # 删除默认路由
ip route add via 10.2.255.254  # 增加默认路由
ip route add 192.168.1.0/24 via 192.168.0.1  # 增加静态路由，192.168.0.1为下一跳地址
ip route del 192.168.1.0/24 via 192.168.0.1  # 删除静态路由

显示arp信息ip neigh [可以取代arp -n]
删除则是ip neigh del IP地址 dev 设备名

路由策略数据库:如果你有一个大规模的路由器，需要同时满足不同用户对于路由的不通需求，路由策略数据库可以帮你通过多路由表技术来实现。当内核需要做出路由选择时，它会找出应该参考哪一张路由表。除了ip外,route也可以修改main和local表

默认规则
	# ip rule
	上面列出了规则的优先顺序。ip route命令默认显示的就是main表。ip route show table all显示所有规则中的表
	# ip route list table local
	broadcast 192.168.0.255 dev eth0  proto kernel  scope link  src 192.168.0.10
	broadcast 10.2.0.0 dev eth1  proto kernel  scope link  src 10.2.0.217
	broadcast 127.255.255.255 dev lo  proto kernel  scope link  src 127.0.0.1
	... ...
	default表为空
例 简单策略路由添加 [引用自Linux高级路由中文HOWTO]
让我们再来一个真实的例子。我有两个Cable Modem，连接到了一个 Linux的NAT (“伪装”) 路由器上。这里的室友们向我付费使用Internet。假如我其中的一个室友因为只想访问 hotmail 而希望少付一些钱。对我来说这没有问题,他们肯定只能使用那个比较次的Cable Modem
那个比较快的cable modem 的IP地址是 212.64.94.251，PPP 链路，对端IP是212.64.94.1。而那个比较慢的cable modem的IP地址是212.64.78.148，对端是195.96.98.253
local 表：
	#ip route list table local

查看“main”路由表：
	#ip route list table main

我们现在为我们的朋友创建了一个叫做“John”的规则。其实我们完全可以使用纯数字表示规则，但是不方便。我们可以向/etc/iproute2/rt_tables文件中添加数字与名字的关联：
# echo 200 John >> /etc/iproute2/rt_tables
# ip rule add from 10.0.0.10 table John
# ip rule

现在，剩下的事情就是为 John 的路由表创建路由项了。别忘了刷新路由缓存：
# ip route add default via 195.96.98.253 dev ppp2 table John
# ip route flush cache
总结主要是以下几步：
	echo 200 John >> /etc/iproute2/rt_tables #方便表示，把规则名字和数字对应加入到/etc/iproute2/rt_tables文件
	ip rule add from 10.0.0.10 table John #新增规则
	ip route add default via 195.96.98.253 dev ppp2 table John #规则中添加路由表
	ip route flush cache #刷新路由表
iproute2 ip 常用命令备忘
1.显示ip地址
	ip a
	ip address show
	ip addr show dev eth0
	ip a sh eth0
2.增加删除地址
	ip address add 192.0.2.1/24 dev eth0
	ip addr del 192.0.2.2/24 dev eth0
3.显示接口统计
	ip -s link ls eth0

网卡和链路配置
4.显示链路
	ip link show
	ip link sh eth0
4.修改接口状态
	ip link set eth0 up
	ip link s gre01 down
路由表管理
5.显示路由表
	ip route
	ip ro show dev gre01
6.增加新路由
	ip route add 10.2.2.128/27 dev gre01
7.增加默认路由
	ip route add default via 192.168.1.1
8.修改默认路由
	ip route chg default via 192.168.1.2
9.删除默认路由
	ip route del default
隧道配置
10.增加删除GRE隧道
	ip tunnel add gre01 mode gre local 10.1.1.1 remote 20.2.2.1 ttl 255
	ip tunnel del gre01
11.IPIP隧道
	ip tunl a ipip01 mode ipip local 10.1.1.1 remote 20.2.2.1 ttl 255
12.显示隧道
	ip tunnel show
13.显示隧道统计
	ip -s tunl ls gre01
邻居和arp表管理
13.查看arp表
	ip neigh show
14.手工增加删除arp项
	ip neighbor add 10.2.2.2 dev eth0
	ip neigh del 10.2.2.1 dev eth0

socket统计
15.显示当前监听
	ss -l
16.显示当前监听的进程
	ss -p
```


地址管理
在本节中，$ {address}值应该是点分十进制格式的主机地址，$ {mask}可以是点分十进制子网掩码或前缀长度。也就是说，192.0.2.10/24和192.0.2.10/255.255.255.0同样可以接受

如果您不确定某些东西是否为正确的主机地址，请使用ipcalc或类似程序进行检查

显示所有地址
ip地址显示
所有show命令都可以与-4或-6选项一起使用，以仅显示IPv4或IPv6地址
显示单个界面的地址
IP地址显示$ {接口名称}
例子：
ip地址显示eth0
仅显示正在运行的接口的地址
IP地址显示
仅显示静态或动态IPv6地址
仅显示静态配置的地址：
ip address show [dev $ {interface}]永久
仅显示通过自动配置学习的地址：
ip address show [dev $ {interface}] dynamic
向接口添加地址
ip address add $ {address} / $ {mask} dev $ {interface name}
例子：
ip address add 192.0.2.10/27 dev eth0
ip address add 2001：db8：1 :: / 48 dev tun10
您可以根据需要添加任意数量的地址

如果您添加多个地址，则您的机器将接受所有数据包。默认情况下，您添加的第一个地址将用作传出流量的源地址，它被称为主地址

您设置的所有其他地址都将成为次要地址

添加具有人类可读描述的地址
ip address add $ {address} / $ {mask} dev $ {interface name} label $ {interface name}：$ {description}
例子：
ip address add 192.0.2.1/24 dev eth0 label eth0：WANaddress
由于一些向后兼容性问题，标签必须以接口名称开头，后跟冒号，否则会出错。 保持标签短于十六个字符，否则你会得到这个错误：
RTNETLINK回答：数字结果超出范围
笔记
对于IPv6地址，此命令不起作用（将添加地址，但没有标签）

删除地址
ip address delete $ {address} / $ {prefix} dev $ {interface name}
例子：
ip地址删除192.0.2.1/24 dev eth0
ip address delete 2001：db8 :: 1/64 dev tun1
接口名称是必需的。Linux确实允许在多个接口上配置相同的地址，并且它具有有效的用例
从接口中删除所有地址
IP地址刷新开发$ {接口名称}
例子：
ip地址刷新dev eth1
默认情况下，该命令删除IPv4和IPv6地址。如果您只想移除IPv4或IPv6地址，请使用“ip-4地址刷新”或“ip-6地址刷新”

笔记
无法交换主地址和辅助地址，也无法显式设置新的主地址。尝试始终首先设置主地址

但是，如果sysctl变量net.ipv4.conf。$ {interface} .promote_secondaries设置为1，那么当您删除主地址时，第一个辅助地址将成为新的主地址

请注意，net.ipv4.conf.default.promote_secondaries = 1 不是所有Linux发行版中的通用默认设置，因此请在尝试之前检查您的设置。如果它设置为0，那么当你删除主地址时，所有的地址都将从接口中删除

如果主地址被删除，则辅助IPv6地址总是被提升为主地址，因此您不必担心sysctl设置

邻居（ARP和NDP）表管理
对于喜欢英国拼写的女士们，先生们，这个指挥家庭也支持“邻居”拼写

查看邻居表
IP邻居秀
所有“show”命令都支持-4和-6选项，以仅查看IPv4（ARP）或IPv6（NDP）邻居。默认显示所有的邻居

查看单个界面的邻居
ip neighbor show dev $ {interface name}
例子：
ip neighbor show dev eth0
刷新接口的表格
ip neighbor flush dev $ {interface name}
例子：
IP邻居刷新开发eth1
添加一个邻居表项
ip neighbor add $ {网络地址} lladdr $ {link layer address} dev $ {interface name}
例子：
ip neighbor add 192.0.2.1 lladdr 22：ce：e0：99：63：6f dev eth0
其中一个用例是为具有禁用ARP的接口添加静态条目，以限制具有特定MAC地址的主机的接口使用情况

删除一个邻居表项
ip邻居删除$ {网络地址} lladdr $ {链接层地址}开发$ {接口名称}
例子：
ip neighbor delete 192.0.2.1 lladdr 22：ce：e0：99：63：6f dev eth0
允许删除静态条目，或者在不刷新表格的情况下摆脱自动学习的条目

链接管理
链接是网络界面的另一个术语。来自“ip link”系列的命令执行所有接口类型通用的操作，例如查看链接信息或更改MTU

从历史上看，“ip link”命令可以创建除隧道（IPIP，GRE等），L2TPv3和VXLAN接口（它们各自具有命令）之外的所有类型的接口。在较新的iproute2版本中（至少3.16），它们可以创建除L2TPv3以外的所有类型的接口，尽管使用特殊命令系列对其中的一些更加方便

请注意，使用“ip link add”和“ip link set”命令的“name $ {name}”参数设置的接口名称可能是任意的，甚至可能包含unicode字符。但是，最好坚持使用ASCII，因为其他程序可能无法正确处理unicode

另请注意，其他程序（如iptables）可能有自己的链接名称格式和长度限制，所以最好使用短的字母数字名称，并在链接别名中提供其他信息

显示有关所有链接的信息
IP链接显示
IP链接列表
这些命令是等价的，可以用于相同的参数
显示有关特定链接的信息
ip link show dev $ {interface name}
例子：
ip link show dev eth0
ip link show dev tun10
单词“dev”可以省略
上下链接
ip link set dev $ {interface name} up
ip link set dev $ {interface name} down
例子：
ip link set dev eth0 down
ip链路设置dev br0 up
注意：下面描述的虚拟链路，如VLAN和网桥在创建后立即处于关闭状态。你需要提醒他们开始使用它们

设置人类可读的链接描述
ip link set dev $ {interface name}别名“$ {description}”
例子：
ip link set dev eth0别名“LAN接口”
链接别名显示在“ip link show”输出中，如：
2：eth0：<BROADCAST，MULTICAST，UP，LOWER_UP> mtu 1500 qdisc mq状态UP模式DEFAULT qlen 1000
    link / ether 22：ce：e0：99：63：6f brd ff：ff：ff：ff：ff：ff
    别名LAN接口

重命名一个接口
ip link set dev $ {old interface name} name $ {new interface name}
例子：
ip link set dev eth0 name lan
请注意，您不能重命名活动界面。在做之前你需要把它放下

更改链路层地址（通常是MAC地址）
ip link set dev $ {interface name} address $ {address}
链路层地址是一个相当广泛的概念。最着名的例子是以太网设备的MAC地址。要改变MAC地址，你需要这样的东西：

ip link set dev eth0 address 22：ce：e0：99：63：6f
更改链接MTU
ip link set dev $ {interface name} mtu $ {MTU value}
例子：
ip link set dev tun0 mtu 1480
MTU代表“最大传输单位”，即接口一次可以传输的最大帧数

除了像上面例子那样减少隧道中的分段之外，这也用于提高支持所谓的“巨型帧”（最多9000个字节的帧）的千兆以太网链路的性能。如果你所有的设备都支持千兆以太网，你可能想要做类似的事情

ip link set dev eth0 mtu 9000
请注意，您可能还需要在L2交换机上配置它，其中一些默认情况下已将其禁用

删除链接
ip link delete dev $ {interface name}
显然，只能删除像VLAN或网桥这样的虚拟链路

在接口上启用或禁用多播
ip link设置$ {interface name}多播
ip link设置$ {interface name}组播关闭
除非你真的明白你在做什么，最好不要碰这个

在接口上启用或禁用ARP
ip link设置$ {interface name} arp
ip link设置$ {interface name} arp off
人们可能希望禁用ARP来强制执行安全策略，并只允许特定的MAC与接口进行通信。在这种情况下，白名单MAC的邻居表条目应手动创建（请参见邻居表管理 部分），否则什么都不能与该接口通信

在大多数情况下，最好在接入层交换机上配置MAC策略。除非你确定你要做什么以及为什么，否则不要改变这个标志

创建一个VLAN接口
IP链接添加名称$ {VLAN接口名称}链接$ {父接口名称}键入vlan id $ {tag}
例子：
ip link add name eth0.110 link eth0 type vlan id 110
Linux支持的唯一VLAN类型是IEEE 802.1q VLAN，不支持ISL等传统实现

一旦创建了一个VLAN接口，所有由$ {parent interface}接收的id选项中指定的$ {tag}标记的帧将由该VLAN接口处理

eth0.100名称格式是传统的，但不是必需的，您可以根据需要命名该接口，就像使用其他接口类型一样

VLAN可以通过网桥，绑定和其他能够处理以太网帧的接口来创建

创建QinQ接口（VLAN堆叠）
ip link add name $ {service interface} link $ {physical interface} type vlan proto 802.1ad id $ {service tag}
ip link add name $ {client interface} link $ {service interface}输入vlan proto 802.1q id $ {client tag}
例：
ip link add name eth0.100 link eth0 type vlan proto 802.1ad id 100＃创建服务标签接口
ip link add name eth0.100.200 link eth0.100 type vlan proto 802.1q id 200＃创建客户端标签接口
VLAN堆叠（又名802.1ad QinQ）是一种在另一个VLAN上传输带VLAN标记的流量的方法。常见的用例如下所示：假设您是服务提供商，并且您有一位客户想要使用您的网络基础架构将其网络的部分连接到彼此。他们在网络中使用多个VLAN，因此普通的租用VLAN不是一种选择。通过使用QinQ，您可以在客户流量进入网络时添加第二个标签，并在退出时删除该标签，因此不存在冲突，您不需要浪费VLAN编号

服务标签是提供商用来通过网络承载客户端流量的VLAN标签。客户标签是客户设定的标签

请注意，客户端VLAN接口的链路MTU不会自动调整，您需要亲自处理，并将客户端接口MTU减少至少4个字节，或相应地增加父母MTU

符合标准的QinQ自Linux 3.10起可用

创建伪以太网（aka macvlan）接口
ip link add name $ {macvlan interface name} link $ {parent interface}键入macvlan
例子：
ip链接添加名称peth0链接eth0类型macvlan
您可以将macvlan接口视为父接口上的附加虚拟MAC地址。从用户的角度来看，它们看起来像普通的以太网接口，并处理所有通信的MAC地址，这些MAC地址是通过其父接口分配的

这通常用于测试，或者当只有一个物理接口可用时，使用由MAC标识的多个服务实例

它们也可以用于IP地址分隔，而不是将多个地址分配给相同的物理接口，特别是如果某些服务无法正确操作辅助地址

创建一个虚拟接口
ip link add name $ {dummy interface name} type dummy
例子：
IP链接添加名称dummy0类型虚拟
Dummy接口的工作方式与Loopback接口非常相似，只需要尽可能多的接口即可

它们的第一个目的是在主机内部进行程序的通信

第二个目的是利用他们总是起来的事实（除非在行政上被剥夺）。这通常用于在具有多个物理接口的路由器上为它们分配服务地址。只要分配给回送或伪接口的地址的流量路由到拥有它的机器，就可以通过它的任何接口访问它

创建一个桥接接口
IP链接添加名称$ {桥名称}类型桥
例子：
IP链接添加名称br0类型桥
网桥接口是虚拟以太网交换机。它们可以用于在以太网接口之间透明地中继流量，并且越来越普遍地用作运行在管理程序内部的虚拟机的以太网交换机

您可以将一个IP地址分配给一个网桥，并且它将从所有网桥端口可见

如果此命令失败，请检查是否加载了“网桥”模块

添加一个桥接接口
ip link set dev $ {interface name} master $ {bridge name}
例子：
ip link set dev eth0 master br0
您添加到网桥的接口将成为虚拟交换机端口。它仅在数据链路层上运行并停止所有网络层的操作

从桥上删除接口
ip link set dev $ {interface name} nomaster
例子：
ip link set dev eth0 nomaster
创建一个绑定界面
IP链接添加名称$ {名称}键入债券
例子：
IP链接添加名称bond1类型的债券
注意：这不足以以任何有意义的方式配置绑定（链接聚合）。您需要根据您的情况设置粘合参数。这远远超出了备忘单范围，因此请查阅文档

接口以与桥接组相同的方式添加到绑定组，请注意，除非您将其关闭，否则无法添加它

创建一个中间功能块接口
ip link add $ {interface name}键入ifb
例：
ip链接添加ifb10类型ifb
中间功能块设备与tc一起用于流量重定向和镜像。这也超出了本文档的范围，请参阅tc文档

创建一对虚拟以太网设备
虚拟以太网（veth）设备总是成对出现，并且作为双向管道工作，不管进入其中的哪一个，都会从另一个管道出来。它们与系统分区功能（如网络命名空间和容器（OpenVZ和LXC））结合使用，以将一个分区连接到另一个分区

IP链接添加名称$ {第一个设备名称}类型veth peer name $ {第二个设备名称}
例子：
IP链接添加名称veth-host类型veth对等名称veth-guest
注意：虚拟以太网设备是在UP状态下创建的，不需要在创建后手动启动它们

链接组管理
链接组与管理型交换机中的端口范围类似。您可以将网络接口添加到编号组，并立即在该组的所有接口上执行操作

未分配给任何组的链接属于组0，也称为“默认”

将界面添加到组
ip link set dev $ {interface name} group $ {group number}
例子：
ip link set dev eth0 group 42
ip link set dev eth1 group 42
从组中删除一个接口
这可以通过将其分配给默认组来完成

ip link set dev $ {interface name} group 0
ip link set dev $ {interface}组默认值
例子：
ip link set dev tun10 group 0
为组分配一个符号名称
组名存储在/ etc / iproute2 / group文件中。组0的符号名称“default”恰好来自那里。您可以按照相同的“$ {number} $ {name}”格式添加您自己的，每行一个。您最多可以有255个命名组

一旦配置了组名，在ip命令中可以交换使用数字和名称

例：
echo“10 customer-vlans”>> / etc / iproute2 / group
之后，您可以在所有操作中使用该名称，例如
ip link set dev eth0.100 group customer-vlans
对组执行操作
ip link set group $ {group number} $ {operation and arguments}
例子：
IP链路设置组42向下
ip链路设置组上行链路mtu 1200
查看来自特定组的链接的信息
使用通常的信息查看命令和“group $ {group}”修饰符

例子：
IP链路列表组42
IP地址显示组客户
Tun和Tap设备
Tun和tap设备允许用户空间程序模拟网络设备。当用户空间程序打开它们时，它们会得到一个文件描述符。从内核网络堆栈路由到设备的数据包从文件描述符中读取，用户空间程序写入文件描述符的数据将作为本地传出数据包注入到网络堆栈中。两者的区别在于：

点击发送并接收原始以太网帧
tun发送并接收原始IP数据包
有两种类型的tun / tap设备：persistent和transient。用户空间程序在打开特殊设备时创建瞬时tun / tap设备，并在关联的文件描述符关闭时自动销毁。这里列出的命令操纵持久性设备

查看tun / tap设备
ip tuntap显示
注意：这个命令可以缩写为“ip tuntap”

该命令是查明某个设备是处于tun还是tap模式的唯一方法

添加可供root用户使用的tun / tap设备
ip tuntap add dev $ {interface name} mode $ {mode}
例子：
ip tuntap add dev tun0模式
ip tuntap add dev tap9模式点击
添加普通用户可使用的tun / tap设备
ip tuntap add dev $ {interface name} mode $ {mode} user $ {user} group $ {group}
例：
ip tuntap add dev tun1模式tun用户我组mygroup
ip tuntap add dev tun2 mode tun user 1000 group 1001
使用备用数据包格式添加tun / tap设备
将元信息添加到通过文件描述符接收的每个数据包。很少有程序期望这些信息，包括它在不期望的情况下会破坏事情

ip tuntap add dev $ {interface name} mode $ {mode} pi
例：
ip tuntap add dev tun1 mode tun pi
添加忽略流量控制的tun / tap
通常，发送到tun / tap设备的数据包以与发送到任何其他设备的数据包相同的方式传播：它们被置于由流量控制引擎（由tc命令配置）处理的队列中。这可以被绕过，从而禁用该tun / tap设备的流量控制引擎

ip tuntap add dev $ {interface name} mode $ {mode} one_queue
例：
ip tuntap add dev tun1 mode tun one_queue
删除tun / tap设备
ip tuntap del dev $ {接口名称}模式$ {mode}
例子：
ip tuntap删除dev tun0模式tun
ip tuntap删除dev tap1模式点击
注意：您必须指定模式。该模式不会显示在“ip link show”中，因此如果您不知道它是TUN还是TAP，请参阅“ip tuntap show”的输出

隧道管理
隧道是看起来像普通接口的“网络虫洞”，但通过它们发送的数据包被封装到另一个协议中，并通过多个主机发送到隧道的另一端，然后按照通常的方式进行解封装和处理，这样就可以假装两台机器有直接的连通性，而他们实际上没有

这通常用于虚拟专用网络（与IPsec之类的加密传输协议结合使用），或通过不使用它的中间网络连接使用某种协议的网络（例如，通过仅支持IPv4的网段分隔的IPv6网络）

注意：隧道自己提供零安全性。它们与其底层网络一样安全。因此，如果您需要安全性，请通过加密传输（例如IPsec）使用它们

Linux目前支持IPIP（IPv4中的IPv4），SIT（IPv4中的IPv6），IP6IP6（IPv6中的IPv6），IPIP6（IPv4中的IPv6），GRE（几乎任何东西）以及最近的版本中的VTI IPsec）的

请注意，隧道是在DOWN状态下创建的，您需要将它们提起

在本节中，$ {本地端点地址}和$ {远程端点地址}是指分配给端点物理接口的地址。$ {address}是指分配给tunnel接口的地址

创建一个IPIP隧道
ip tunnel add $ {interface name}模式ipip local $ {本地端点地址} remote $ {远程端点地址}
例子：
ip tunnel add tun0 mode ipip local 192.0.2.1 remote 198.51.100.3
ip link set dev tun0 up
ip address add 10.0.0.1/30 dev tun0
创建一个SIT隧道
sudo ip tunnel add $ {interface name}模式坐本地$ {本地端点地址} remote $ {远程端点地址}
例子：
ip tunnel add tun9模式坐本地192.0.2.1远程198.51.100.3
ip link set dev tun9 up
ip address add 2001：db8：1 :: 1/64 dev tun9
这种类型的隧道通常用于为IPv4连接的网络提供IPv6连接。有所谓的“隧道经纪人”提供给所有感兴趣的人，如Hurricane Electric tunnelbroker.net

创建一个IPIP6隧道
 ip -6 tunnel add $ {interface name}模式ipip6 local $ {本地端点地址}远程$ {远程端点地址}
例子：
ip -6 tunnel add tun8 mode ipip6 local 2001：db8：1 :: 1 remote 2001：db8：1 :: 2
这种类型的隧道将在中转运营商分阶段IPv4（即不会很快）时广泛使用

创建一个IP6IP6隧道
ip -6 tunnel add $ {interface name}模式ip6ip6 local $ {本地端点地址} remote $ {远程端点地址}
例子：
ip -6 tunnel add tun3 mode ip6ip6 local 2001：db8：1 :: 1 remote 2001：db8：1 :: 2
ip链接设置dev tun3 up
ip address add 2001：db8：2：2 :: 1/64 dev tun3
就像IPIP6一样，这些不会很快有用

创建一个gretap（以太网over GRE）设备
IP链接添加$ {接口名称}类型gretap本地$ {本地端点地址}远程$ {远程端点地址}
例子：
ip link add gretap0 type gretap local 192.0.2.1 remote 203.0.113.3
这种类型的隧道将以太网帧封装为IPv4数据包

最近的内核和iproute2版本也支持IPv6上的gretap，你需要用“ip6gretap”替换模式来创建一个基于IPv6的链接

这可能应该是在“链接管理”部分，但由于它涉及封装，它在这里。以这种方式创建的隧道接口看起来像一个L2链路，并且可以将其添加到桥组。这用于通过路由网络连接L2段

创建一个GRE隧道
ip tunnel add $ {接口名称}模式gre本地$ {本地端点地址}远程$ {远程端点地址}
例子：
ip tunnel add tun6 mode gre local 192.0.2.1 remote 203.0.113.3
ip链接设置dev tun6 up
ip address add 192.168.0.1/30 dev tun6
ip address add 2001：db8：1 :: 1/64 dev tun6
GRE可以同时封装IPv4和IPv6。但是，默认情况下，它使用IPv4进行传输，对于基于IPv6的GRE，存在单独的隧道模式“ip6gre”

创建多个GRE隧道到同一个端点
ip tunnel add $ {接口名称}模式gre本地$ {本地端点地址}远程$ {远程端点地址}键$ {键值}
例子：
ip tunnel add tun4 mode gre local 192.0.2.1 remote 203.0.113.6 key 123
ip tunnel add tun5 mode gre local 192.0.2.1 remote 203.0.113.6 key 124
键控隧道也可以同时使用来解除密码。密钥可能采用点分十进制IPv4类格式

请注意，密钥不会为隧道添加任何安全性。这只是一个用来区分一条隧道和另一条隧道的标识符

创建一个点对多点的GRE隧道
ip tunnel add $ {接口名称}模式gre本地$ {本地端点地址}密钥$ {密钥值}
例子：
ip tunnel add tun8 mode gre local 192.0.2.1 key 1234
ip链接设置dev tun8 up
ip address add 10.0.0.1/27 dev tun8
请注意缺少$ {远程端点地址}。这与Cisco IOS中所谓的“mode gre multipoint”相同

在没有远程端点地址的情况下，密钥是识别隧道流量的唯一方法，因此需要$ {key value}

这种类型的隧道允许您通过使用相同的隧道接口与多个端点进行通信。它通常用于具有多个端点的复杂VPN设置（在思科术语中称为“动态多点VPN”）

由于没有明确的远程端点地址，显然仅仅创建隧道是不够的。你的系统需要知道其他端点在哪里

在现实生活中，使用NHRP（下一跳解析协议）。对于测试，您可以手动添加对等端（给定远程端点在其物理接口上使用203.0.113.6地址，在隧道上使用10.0.0.2）：

ip neighbor add 10.0.0.2 lladdr 203.0.113.6 dev tun8
您也必须在远程端点上执行此操作，例如：

ip neighbor add 10.0.0.1 lladdr 192.0.2.1 dev tun8
请注意，链路层地址和邻居地址都是IP地址，因此它们位于同一个OSI层。链路层地址概念变得有趣的情况之一

通过IPv6创建GRE隧道
最近的内核和iproute2版本支持GRE over IPv6。没有密钥的点对点：

ip-6隧道添加名称$ {接口名称}模式ip6gre本地$ {本地端点}远程$ {远程端点}
它应该支持上述IPv4 GRE支持的所有选项和功能

删除隧道
ip tunnel del $ {interface name}
例子：
ip tunnel del gre1
请注意，在较旧的iproute2版本中，此命令不支持完整的“删除”字，只有“del”。最近的版本允许全部和缩写形式（在iproute2-ss131122中测试）

修改隧道
IP隧道更改$ {接口名称} $ {选项}
例子：
ip tunnel change tun0 remote 203.0.113.89
ip隧道更改tun10密钥23456
注意：显然，您无法将密钥添加到之前未加密的隧道。不知道这是一个错误还是一个功能。此外，由于显而易见的原因，您无法即时更改隧道模式

查看隧道信息
IP隧道表演
ip tunnel show $ {interface name}
例子：
$ ip tun show tun99
tun99：gre / ip remote 10.46.1.20 local 10.91.19.110 ttl inherit

L2TPv3伪线管理
L2TPv3是L2伪线通常使用的隧道协议

在许多发行版中，L2TPv3被编译为模块，并且可能不会默认加载。如果你得到一个“RTNETLINK答案：没有这样的文件或目录”和“错误的对内核”消息给任何“ip l2tp”命令，情况可能如此。加载l2tp_netlink和 l2tp_eth模块。如果你想通过IP而不是UDP使用L2TPv3，还需要加载 l2tp_ip

与Linux中的其他隧道协议实现相比，L2TPv3术语有所逆转。您创建一个隧道，然后绑定会话吧。您可以将具有不同标识符的多个会话绑定到相同的隧道。虚拟网络接口（默认名称为l2tpethX）与会话相关联

注意： Linux内核仅实现数据帧的处理，因此您只能创建带有iproute2的非托管隧道，并且双方都手动配置所有设置。如果您想要使用L2TP进行远程访问VPN或其他非固定伪线，则需要用户空间守护程序来处理它。这超出了本文档范围

通过UDP创建L2TPv3隧道
ip l2tp添加隧道\
tunnel_id $ {本地隧道数字标识符} \
peer_tunnel_id $ {远程隧道数字标识符} \
udp_sport $ {源端口} \
udp_dport $ {目标端口} \
encap udp \
本地$ {本地端点地址} \
远程$ {远程端点地址}
例子：
ip l2tp添加隧道\
tunnel_id 1 \
peer_tunnel_id 1 \
udp_sport 5000 \
udp_dport 5000 \
encap udp \
本地192.0.2.1 \
远程203.0.113.2
注意：两个端点上的隧道标识符和其他设置必须匹配

通过IP创建L2TPv3隧道
ip l2tp添加隧道\
tunnel_id $ {本地隧道数字标识符} \
peer_tunnel_id {远程隧道数字标识符} \
encap ip \
本地192.0.2.1 \
远程203.0.113.2

直接封装成IP的L2TPv3提供较少的开销，一般无法通过NAT

创建一个L2TPv3会话
ip l2tp add session tunnel_id $ {local tunnel identifier} \
session_id $ {本地会话数字标识符} \
peer_session_id $ {远程会话数字标识符}

例子：
ip l2tp add session tunnel_id 1 \
session_id 10 \
peer_session_id 10

注意： tunnel_id值必须匹配先前创建的隧道的值。两个端点上的会话标识符必须匹配

一旦你创建了一个隧道和一个会话，l2tpethX界面就会出现，处于关闭状态。将状态更改为启动并将其与另一个界面桥接或分配地址

删除一个L2TPv3会话
ip l2tp del session tunnel_id $ {tunnel identifier} \
session_id $ {会话标识符}

例子
ip l2tp del session tunnel_id 1 session_id 1
删除L2TPv3隧道
ip l2tp del tunnel tunnel_id $ {tunnel identifier}
例子
ip l2tp del tunnel tunnel_id 1
注意：删除之前，您需要删除与隧道相关的所有会话

查看L2TPv3隧道信息
ip l2tp显示隧道
ip l2tp show tunnel tunnel_id $ {tunnel identifier}
例子：
ip l2tp show tunnel tunnel_id 12
查看L2TPv3会话信息
ip l2tp显示会话
ip l2tp show session session_id $ {session identifier} \
tunnel_id $ {隧道标识符}

例子：
ip l2tp show session session_id 1 tunnel_id 12
VXLAN管理
VXLAN是第2层隧道协议，通常与虚拟化系统（如KVM）结合使用，以将运行在不同虚拟机管理程序节点上的虚拟机彼此连接并连接到外部世界

与点对点的GRE或L2TPv3不同，VXLAN通过使用IP多播来复制多路访问交换网络的一些属性。此外，它还通过与帧一起发送网络标识符来支持虚拟网络分离

缺点是您需要使用多播路由协议（通常是PIM-SM）才能使其通过路由网络工作

VXLAN的底层封装协议是UDP

创建单播VXLAN链路
ip link add name $ {interface name}键入vxlan \
   id <0-16777215> \
   dev $ {source interface} \
   远程$ {远程端点地址} \
   本地$ {本地端点地址} \
   dstport $ {VXLAN目标端口}
例：
ip link add name vxlan0 type vxlan \
   ID 42 dev eth0远程203.0.113.6本地192.0.2.1 dstport 4789
注意： id选项表示VXLAN网络标识符（VNI）

创建组播VXLAN链路
ip link add name $ {interface name}键入vxlan \
   id <0-16777215> \
   dev $ {source interface} \
   组$ {多播地址} \
   dstport $ {VXLAN目标端口}
例：
ip link add name vxlan0 type vxlan \
   id 42 dev eth0组239.0.0.1 dstport 4789
之后，您需要将链接与其他接口桥接或分配地址

路由管理
对于IPv4路由，您可以使用前缀长度或点分十进制子网掩码。也就是说，192.0.2.0/24和192.0.2.0/255.255.255.0都可以接受

注意：根据下面的部分，如果您设置了静态路由，并且由于链接关闭而无法访问，它将被删除，并且 永远不会自行恢复。您可能没有注意到这种行为，因为在许多情况下，附加软件（例如NetworkManager或rp-pppoe）负责在链接上下时恢复路由

如果您打算将您的Linux机器用作路由器，请考虑安装诸如Quagga 或BIRD之类的路由协议套件。它们跟踪接口状态并在路由器断开后恢复路由时恢复路由。当然，他们也允许你使用动态路由协议，如OSPF和BGP

连接的路线
某些路线在系统中显示，没有明确的配置（违背您的意愿）

一旦为接口分配地址，系统将计算其网络地址并为其创建路由（这就是为什么需要子网掩码）。这些路由被称为连接路由

例如，如果将203.0.113.25/24分配给eth0，则会创建到203.0.113.0/24网络的已连接路由，并且系统将知道可以直接到达该网络中的主机

当接口出现故障时，与其关联的连接路由将被删除。这用于不可访问的网关检测，因此通过不可访问的网关的路由被删除。相同的机制阻止您通过无法访问的网关创建路由

查看所有路线
ip路由
ip路由显示
您可以使用-4和-6选项仅查看IPv4或IPv6路由。如果没有给出选项，则显示IPv4路由。要查看IPv6路由，请使用：
ip-6路由
查看到网络及其所有子网的路由
ip route show to root $ {address} / $ {mask}
例如，如果在网络中使用192.168.0.0/24子网，并且该子网分为192.168.0.0/25和192.168.0.128/25，则可以看到所有这些路由：
ip route显示为根192.168.0.0/24
注意：这个和其他show命令中的“to”是可选的
查看到网络和所有超网的路由
ip route show to match $ {address} / $ {mask}
如果要查看路由到192.168.0.0/24和所有更大的子网，请使用：
IP路由显示匹配192.168.0.0/24
由于路由器更偏向于更具体的路由，因此通常用于调试特定子网的流量发送错误的情况，因为缺少路由但存在到较大子网的路由
查看路由到确切的子网
ip route显示精确$ {address} / $ {mask}
如果您想查看192.168.0.0/24的路由，但不想查看路由192.168.0.0/25和192.168.0.0/16，则可以使用：
IP路由显示确切的192.168.0.0/24
仅查看内核实际使用的路线
ip route get $ {address} / $ {mask}
例：
ip route获得192.168.0.0/24
请注意，在复杂路由场景（如多路径路由）中，结果可能“正确但不完整”，因为它总是显示首先使用的一条路由。在大多数情况下，这不是问题，但不要忘记查看相应的“show”命令输出
查看路由缓存（仅限于3.6内核之前）
IP路由显示缓存
在版本3.6之前，Linux使用路由缓存。在较早的内核中，此命令显示路由缓存的内容。它可以与上述的修饰符一起使用。在较新的内核中，它什么都不做
通过网关添加路由
ip route通过$ {next hop}添加$ {address} / $ {mask}
例子：
ip route通过192.0.2.1添加192.0.2.128/25
ip route add 2001：db8：1 :: / 48 via 2001：db8：1 :: 1
通过界面添加路线
ip route add $ {address} / $ {mask} dev $ {interface name}
例：
ip route add 192.0.2.0/25 dev ppp0
接口路由通常用于点对点接口，如PPP隧道，其中不需要下一跳地址
更改或替换路线
您可以使用“更改”命令来更改现有路线的参数。“替换”命令可用于添加新路由或修改现有路由（如果不存在）。例子：

IP路由通过10.0.0.1更改192.168.2.0/24
ip route替换192.0.2.1/27 dev tun0
删除路线
ip route delete $ {路由语句的其余部分}
例子：
IP路由通过10.0.0.1删除10.0.1.0/25
ip route删除默认dev ppp0
默认路由

有一个添加默认路由的快捷方式
ip route通过$ {address} / $ {mask}添加默认值
ip route add default dev $ {interface name}
这些相当于：
ip route通过$ {address} / $ {mask}添加0.0.0.0/0
ip route add 0.0.0.0/0 dev $ {interface name}
对于IPv6路由，它也可以工作，相当于:: / 0
ip-6路由通过2001：db8 :: 1添加默认值
黑洞路线
ip route add blackhole $ {address} / $ {mask}
例子：
ip route add blackhole 192.0.2.1/32
与黑洞路线相匹配的目的地的交通流被无声地丢弃

黑洞路线有双重目的。首先很简单，就是丢弃发送到不想要的目的地的流量，例如已知的恶意主机

第二个不太明显，按照RFC1812使用“最长匹配规则”。在某些情况下，您可能希望路由器认为它具有通往更大子网的路由，而不是整体使用它，例如，通过动态路由协议通告整个子网时。大型子网通常会分成较小的部分，因此如果您的子网是192.0.2.0/24，并且您已将192.0.2.1/25和192.0.2.129/25分配给您的接口，则系统会创建连接到/ 25的连接路由，但不是整个/ 24，并且路由守护进程可能不想通告/ 24，因为您没有路由到该确切的子网。解决方案是将黑洞路由设置为192.0.2.0/24

其他特殊路线
ip route add unreachable $ {address} / $ {mask}
ip route add prohibit $ {address} / $ {mask}
ip route add throw $ {address} / $ {mask}
这些路由使系统丢弃数据包，并向发件人回复ICMP错误消息
无法访问
发送ICMP“主机不可达”
禁止
发送ICMP“管理禁止”
扔
发送“网络不可达”
与黑洞路由不同，这些不能用于阻止不需要的流量（例如DDoS），因为它们会为每个丢弃的数据包生成一个回复数据包，从而创建更大的流量。它们可以很好地实施内部访问策略，但首先考虑用于此目的的防火墙

“抛出”路由可用于实施基于策略的路由，在非默认表中它们会停止当前表查找，但不发送ICMP错误消息

具有不同度量的路由
ip route通过$ {gateway}公制$ {number}添加$ {address} / $ {mask}
例子：
ip route通过10.0.1.1 metric添加192.168.2.0/24
ip route add 192.168.2.0 dev ppp0 metric 10
如果有多条路由通向具有不同度量值的同一个网络，则具有最低度量值的路由将是首选

这个概念的重要组成部分是，当一个接口关闭时，这个事件无用的路由从路由表中消失（参见连接路由部分），系统将回退到更高的度量路由

此功能通常用于实现到重要目的地的备份连接

多路径路由
ip route通过$ {gateway}添加$ {addresss} / $ {mask} nexthop通过$ {number} nexthop通过$ {gateway 2}加权$ {number}
多路径路由使得系统根据权重在多个链路上平衡数据包（较高的权重是首选，所以权重为2的网关/接口将比另一个权重为1的网关/接口大约多两倍）。您可以根据需要配置尽可能多的网关，并混合网关和接口路由，如：

ip route添加默认nexthop通过192.168.1.1权重1 nexthop dev ppp0权重10
警告：这种平衡的缺点是数据包不能保证通过它们进入的相同链路发回。这称为“不对称路由”。对于简单地转发数据包而不做任何本地流量处理（如NAT）的路由器，这通常是正常的，并且在某些情况下甚至是不可避免的

如果您的系统除了在接口之间转发数据包之外什么都做，这可能会导致传入连接出现问题，应采取一些措施来防止它们

基于策略的路由
Linux中基于策略的路由（PBR）的设计方式如下：首先创建自定义路由表，然后创建规则以告知内核应该使用这些表而不是默认表进行特定流量

一些表格是预定义的：

本地（表255）
包含控制路由本地和广播地址
主要（表254）
包含所有非PBR路由。如果您在添加路线时未指定表格，则会显示在此处
默认（表253）
保留用于后期处理，通常未使用
用户定义的表格会在您向其添加第一条路线时自动创建

创建一个策略路由
ip route add $ {route options} table $ {table id or name}
例子：
ip route通过203.0.113.1表10添加192.0.2.0/27
ip route通过192.168.0.1表ISP2添加0.0.0.0/0
ip route add 2001：db8 :: / 48 dev eth1 table 100
注意：您也可以使用策略路由中的“路由管理”部分中描述的任何路由选项，唯一的区别是最后的“表$ {表id /名称}”部分

数字表格标识符和名称可以互换使用。要创建您自己的符号名称，请编辑/ etc / iproute2 / rt_tables配置文件

“删除”，“更改”，“替换”或任何其他路由操作也可以与任何表一起使用

“ip route ... table main”或“ip route ... table 254”对没有表格部分的命令具有完全相同的效果

查看策略路由
ip route show table $ {表ID或名称}
例子：
ip route show table 100
ip route show table test
注意：在这种情况下，您需要“显示”字，像“ip route table 120”这样的简写不起作用，因为该命令不明确

一般规则语法
ip rule add $ {options} <lookup $ {table id or name} | blackhole | prohibit | unreachable>
如果使用“查找”操作，匹配$ {options}（如下所述）的流量将根据具有指定名称/ id的表进行路由，而不是“主”/ 254表

“黑洞”，“禁止”和“无法访问”的动作，其工作方式与使用相同名称的类型相同。在大多数例子中，我们将使用“查找”操作作为最常见的操作

对于IPv6规则，使用“ip -6”，其余语法是相同的

“table $ {table id or name}”可以用作“查找$ {表id或名称}”的别名

创建一个规则以匹配源网络
ip规则添加从$ {源网络} $ {动作}
例子：
ip规则添加从192.0.2.0/24查找10
从2001年ip-6规则添加：db8 :: / 32禁止
注意： “all”可以用作0.0.0.0/0或者:: / 0的简写

创建一个规则以匹配目标网络
ip规则添加到$ {目标网络} $ {action}
例子：
ip规则添加到192.0.2.0/24黑洞
ip -6规则添加到2001：db8 :: / 32查找100
创建一个规则以匹配ToS字段值
ip rule add tos $ {ToS value} $ {action}
例子：
ip规则添加tos 0x10查找110
创建一个匹配防火墙标记值的规则
ip规则添加fwmark $ {mark} $ {action}
例子：
ip规则添加fwmark 0x11查找100
注意：请参阅iptables文档以了解如何设置标记

创建一个匹配入站接口的规则
ip规则添加IIF $ {接口名称} $ {动作}
例子：
ip规则添加iif eth0查找10
ip规则添加iif查找20
“iif lo”（回送）规则将匹配本地生成的流量

创建一个匹配出站接口的规则
ip rule add oif $ {interface name} $ {action}
例子：
ip规则添加oif eth0查找10
注意：这仅适用于本地生成的流量

设置规则优先级
ip规则添加$ {options} $ {action}优先级$ {value}
例子：
ip规则添加从192.0.2.0/25查找10优先级10
ip规则添加从192.0.2.0/24查找20优先级20
注意：由于规则从最低优先级到最高优先级进行遍历，并且处理在第一次匹配时停止，所以您需要在不太具体的情况下放置更具体的规则。以上示例演示了192.0.2.0/24及其子网192.0.2.0/25的规则。如果优先顺序被颠倒过来，而且/ 25的规则被置于24的规则之后，那永远不会达到

显示所有规则
ip规则显示
ip-6规则显示
删除规则
ip rule del $ {options} $ {action}
例子：
ip rule del 192.0.2.0/24查找10
注意：您可以从“ip rule show”/“ip -6 rule show”的输出复制/粘贴

删除所有规则
ip规则刷新
ip-6规则刷新
注：此操作具有高度破坏性。即使您没有配置任何规则，默认情况下也会初始化“从所有主查找”规则。在未配置的机器上，您可以看到：

$ ip规则显示
0：从所有查找本地
32766：从所有查找主要
32767：从所有查找默认

$ ip -6规则显示
0：从所有查找本地
32766：从所有查找主要
“从所有查找本地”规则是特殊的，不能被删除。“从所有查找主要”不是，可能有没有它的正当理由，例如，如果你只想路由你创建显式规则的流量。作为一个副作用，如果你做“ip rule flush”，这个规则将被删除，这将使系统停止路由任何流量，直到你恢复你的规则

网络命名空间管理
网络名称空间是单个机器中隔离的网络堆栈实例。它们可用于安全域分离，管理虚拟机之间的流量等

每个命名空间都是具有自己的接口，地址，路由等的网络堆栈的完整副本。您可以在命名空间中运行进程，并将命名空间桥接到物理接口

创建一个名称空间
ip netns添加$ {命名空间名称}
例子：
ip netns添加foo
列出现有的命名空间
ip netns列表
删除一个名字空间
ip netns删除$ {namespace name}
例子：
ip netns删除foo
在命名空间内运行一个进程
ip netns exec $ {namespace name} $ {command}
例子：
ip netns exec foo / bin / sh
注意：将进程分配给非默认名称空间需要root权限

你可以在命名空间内运行任何进程，特别是你可以运行“ip”本身，本节中的命令“ip netns exec foo ip link list”不是一种特殊的语法，而是简单地执行另一个“ip”副本一个名字空间。您也可以在名称空间内运行交互式shell

列出分配给名称空间的所有进程
ip netns pids $ {namespace name}
输出将是一个PID列表

确定进程的主名称空间
ip netns标识$ {pid}
例子：
ip netns识别9000
将网络接口分配给名称空间
ip link set dev $ {interface name} netns $ {namespace name}
ip link set dev $ {interface name} netns $ {pid}
例子：
ip link set dev eth0.100 netns foo
注意：一旦你给一个命名空间分配了一个接口，它将从默认的命名空间中消失，你将不得不通过“ip netns exec $ {netspace name}”执行所有的操作，如“ip netns exec $ {netspace name} ip link set dev dummy0 down“

而且，当您将接口移动到另一个名称空间时，它会丢失所有已配置的配置，例如配置的IP地址并进入DOWN状态。您需要将其恢复并重新配置

如果您指定一个PID而不是命名空间名称，则该接口将被分配到具有该PID的进程的主名称空间。通过这种方式，您可以使用例如“ip netns exec $ {namespace name} ip link set dev $ {intf} netns 1”将接口重新分配回默认名称空间（因为初始化或其他PID 1的进程几乎可以保证处于默认状态命名空间）

将一个命名空间连接到另一个
这可以通过创建两个veth链接并为它们分配两个不同的名称空间来完成。假设你想将命名空间“foo”连接到默认的命名空间

创建一对veth设备：
ip链接添加名称veth1类型veth peer name veth2
将veth2移动到名称空间foo：
ip link set dev veth2 netns foo
带上veth2并在“foo”命名空间中添加一个地址：
ip netns exec foo ip link set dev veth2 up
ip netns exec foo ip address add 10.1.1.1/24 dev veth2
向veth1添加一个地址，该地址保留在默认名称空间中：
ip address add 10.1.1.2/24 dev veth1
现在，您可以ping 10.1.1.1，如果在foo命名空间中，并且设置路由到在该命名空间的其他接口中配置的子网的路由

如果您想要切换而不是路由，则可以将这些veth接口与相应名称空间中的其他接口进行桥接。可以使用相同的技术将名称空间连接到物理网络

监视网络名称空间子系统事件
ip netns监视器
显示事件，例如创建和删除名称空间

多播管理
多播主要由应用程序和路由守护进程处理，所以你可以做的并不多，而且应该在这里手动完成。多播相关的ip命令主要用于调试

查看多播组
ip maddress节目
ip maddress show $ {interface name}
例：
$ ip maddress show dev lo
1：lo
	inet 224.0.0.1
	inet6 ff02 :: 1
	inet6 ff01 :: 1
添加链路层多播地址
您不能手动加入IP多播组，但可以添加多播MAC地址（尽管很少需要）

ip maddress add $ {MAC地址} dev $ {接口名称}
例：
ip maddress添加01：00：5e：00：00：ab dev eth0
查看组播路由
组播路由无法手动添加，因此此命令只能显示由路由守护程序安装的组播路由。它支持相同的修饰符来单播路由查看命令（iif，table，from等）

ip mroute显示
网络事件监控
您可以使用iproute2监视某些网络事件，例如网络配置，路由表和ARP / NDP表中的更改

监控所有事件
您可以不带参数地调用该命令，或者明确指定“全部”

IP监视器
ip监视所有
监视特定事件
ip monitor $ {event type}
事件类型可以是：

链接
链路状态：接口上下，虚拟接口被创建或销毁等
地址
链接地址更改
路线
路由表更改
mroute
组播路由更改
嘶
邻居（ARP和NDP）表中的更改
当存在不同的IPv4和IPv6子系统时，通常的“-4”和“-6”选项允许您仅显示指定协议的事件。如：

ip -4监控路由
ip-6监视器邻居
ip -4监视器地址
阅读rtmon生成的日志文件
iproute2包含一个名为“rtmon”的程序，其功能基本相同，但将事件写入二进制日志文件而不是显示它们。“ip monitor”命令允许您读取由程序创建的文件“

ip monitor $ {event type}文件$ {日志文件的路径}
rtmon语法与“ip monitor”类似，除了事件类型仅限于链接，地址，路由和全部; 和地址族在“-family”选项中指定

rtmon [-family <inet | inet6>] [<路由|链接|地址|所有>]文件$ {日志文件路径}
netconf（sysctl配置查看）
查看所有接口的sysctl配置
ip netconf显示
查看特定接口的sysctl配置
ip netconf show dev $ {interface}
例子：
ip netconf show dev eth0

## Net-tools

### netstat



```markdown
netstat [-vWeenNcCF] [<Af>] -r
netstat [-vWnNcaeol] [<Socket> ...]
netstat { [-vWeenNac] -i | [-cnNe] -M | -s [-6tuw] }

<Socket>={-t|--tcp} {-u|--udp} {-U|--udplite} {-S|--sctp} {-w|--raw}
         {-x|--unix} --ax25 --ipx --netrom
<AF>=Use '-6|-4' or '-A <af>' or '--<af>'；默认： inet
列出所有支持的协议：
  inet (DARPA Internet) inet6 (IPv6) ax25 (AMPR AX.25)
  netrom (AMPR NET/ROM) ipx (Novell IPX) ddp (Appletalk DDP)
  x25 (CCITT X.25)

-r,--route              显示路由表
-i,--interfaces         显示网络接口表
-g,--groups             显示组播组成员关系
-s,--statistics         display networking statistics (like SNMP)
-t,--tcp                显示TCP传输协议的连线状况
-u,--udp                显示UDP传输协议的连线状况
-M,--masquerade         显示伪装的网络连接
-v,--verbose            显示详细信息
-w,--raw                显示RAW传输协议的连线状况
-W,--wide               don't truncate IP addresses
-n,--numeric            不解析名称,直接使用IP
   --numeric-hosts      不解析主机名
   --numeric-ports      忽略端口名称
   --numeric-users      忽略用户名
-A,--protocol=family    列出对应协议类型连线中的相关地址(family：inet，inet6，unix，ipx，ax25，netrom，econet，ddp和bluetooth)
-x,--unix               此参数的效果和指定"-A unix"参数相同
--ip,--inet             此参数的效果和指定"-A inet"参数相同
-N,--symbolic           解析硬件、外围设备的符号连接名称
-e,--extend             显示网络中其他更多信息
-p,--programs           显示正在使用Socket的进程PID和程序名称
-o,--timers             display timers(显示计时器)
-c,--continuous         持续列出网络状态(默认1s)
-l,--listening          显示侦听服务器sockets
-a,--all                显示所有连线中的Socket(default: connected)
-F,--fib                从FIB(forwarding information base)打印路由信息(default)
-C,--cache              从路由缓存中打印路由信息
-Z,--context            显示sockets的SELinux security context
```

### netstat实例

```bash
# 列出所有端口 (包括监听和未监听的)
netstat -a     #列出所有端口
netstat -at    #列出所有tcp端口
netstat -au    #列出所有udp端口
netstat -ng    #查看组播情况

# 列出所有处于监听状态的 Sockets
netstat -l        #只显示监听端口
netstat -lt       #只列出所有监听 tcp 端口
netstat -lu       #只列出所有监听 udp 端口
netstat -lx       #只列出所有监听 UNIX 端口

# 显示每个协议的统计信息
netstat -s   显示所有端口的统计信息
netstat -st   显示TCP端口的统计信息
netstat -su   显示UDP端口的统计信息

netstat -pt     # 在netstat输出中显示 PID 和进程名称
```

`netstat -p`可以与其它开关一起使用，就可以添加“PID/进程名称”到netstat输出中，这样debugging的时候可以很方便的发现特定端口运行的程序

## 在netstat输出中不显示主机，端口和用户名(host, port or user)

```bash
# 当你不想让主机，端口和用户名显示，使用`netstat -n`。将会使用数字代替那些名称。同样可以加速输出，因为不用进行比对查询
netstat -an

# 如果只是不想让这三个名称中的一个被显示，使用以下命令:
netsat -a --numeric-ports
netsat -a --numeric-hosts
netsat -a --numeric-users

# 找出程序运行的端口,并不是所有的进程都能找到，没有权限的会不显示，使用 root 权限查看所有的信息
netstat -ap | grep ssh

# 找出运行在指定端口的进程
netstat -an | grep ':80'
```

| 命令拆分 | 详解 |
| ----- | ---- |
| /^tcp/ | 过滤出以tcp开头的行，“^”为正则表达式用法，以...开头，这里是过滤出以tcp开头的行。 |
| S[] | 定义了一个名叫S的数组，在awk中，数组下标通常从 1 开始，而不是 0。 |
| NF | 当前记录里域个数，默认以空格分隔，如上所示的记录，NF域个数等于6 |
| $NF | 表示一行的最后一个域的值，如上所示的记录，$NF也就是$6，表示第6个字段的值，也就是SYN_RECV或TIME_WAIT等。 |
| S[$NF] | 表示数组元素的值，如上所示的记录，就是S[TIME_WAIT]状态的连接数 |
| ++S[$NF] | 表示把某个数加一，如上所示的记录，就是把S[TIME_WAIT]状态的连接数加一 |
| END | 结束 |
| forkey in S | 遍历S[]数组 |
| print key,”\t”,S[key] | 打印数组的键和值，中间用\t制表符分割，显示好一些。 |

## IP和TCP分析

```bash
#查看连接某服务端口前20个IP地址及连接数：
netstat -ntu | grep :80 | awk '{print $5}' | cut -d: -f1 | awk '{++ip[$1]} END {for(i in ip) print ip[i],"\t",i}' | sort -nr | head -n 20
netstat -anlp|grep 80|grep tcp|awk '{print $5}'|awk -F: '{print $1}'|sort|uniq -c|sort -nr | head -n 20
netstat -nat |awk '/:80/{split($5,ip,":");++A[ip[1]]}END{for(i in A) print A[i],i}' |sort -rn | head -n 20
netstat -nat | grep ":80" | grep ESTABLISHED | awk '{printf "%s %s\n",$5,$6}' | sort -rn | head -n 20

# 根据端口列出进程
netstat -ntlp | grep 80 | awk '{print $7}' | cut -d: -f1

# 用tcpdump嗅探80端口前的访问量
tcpdump -i eth0 -tnn dst port 80 -c 1000 | awk -F"." '{print $1"."$2"."$3"."$4}' | sort | uniq -c | sort -nr | head -20

# 查看phpcgi进程数，如果接近预设值，说明不够用，需要增加
netstat -anpo | grep "php-cgi" | wc -l

# 查看TCP连接状态、并发及连接数的命令
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
netstat -nat | awk '{print $NF}' | grep -v '[a-z]' | sort | uniq -c | sort -rn
netstat -n | awk '/^tcp/ {++state[$NF]}; END {for(key in state) print key,"\t",state[key]}'
netstat -n | awk '/^tcp/ {++arr[$NF]};END {for(k in arr) print k,"\t",arr[k]}'
netstat -nt | grep -e 127.0.0.1 -e 0.0.0.0 -e ::: -v | awk '/^tcp/ {++state[$NF]} END {for(i in state) print i,"\t",state[i]}'

# 查找前20的time_wait连接
netstat -n|grep TIME_WAIT|awk '{print $5}'|sort|uniq -c|sort -rn | head -n 20

# 查找查前20的SYN连接
netstat -an | grep SYN | awk '{print $5}' | awk -F: '{print $1}' | sort | uniq -c | sort -nr | head -n 20

# 统计服务器当前单IP连接数最大的IP地址前十
netstat -an | grep EST | awk -F '[ :]+' '{++S[$6]} END {for (key in S) print "ip:"key"----->",S[key]}' | sort -rn -k2

```

### TCP状态说明

| 状态 | 描述 |
| :------: | :------: |
| CLOSED | 无连接是活动的或正在进行 |
| LISTEN | 服务器在等待进入呼叫 |
| SYN_RECV | 一个连接请求已经到达 |
| SYN_SENT | 应用已经开始 |
| ESTABLISHED | 正常数据传输状态 |
| FIN_WAIT1 | 应用说它已经完成 |
| FIN_WAIT2 | 另一边已同意释放 |
| ITMED_WAIT | 等待所有分组死掉 |
| CLOSING | 两边同时尝试关闭 |
| TIME_WAIT | 另一边已初始化一个释放 |
| LAST_ACK | 等待所有分组死掉 |

### tcp连接状态的描述说明(netstat输出)

Active Internet connections (w/o servers)

Proto Recv-Q Send-Q Local AddressForeign AddressState

Proto

The protocol (tcp, udp, raw) used by the socket.

第一列为socket使用的协议

Recv-Q

The count of bytes not copied by the user program connected to this socket.

第二列为接到的但是还没处理的字节数

Send-Q

The count of bytes not acknowledged by the remote host.

第三列为已经发送的但是没有被远程主机确认收到的字节数

Local Address

Address and port number of the local end of the socket.Unless the --numeric(-n)

optionisspecified,thesocketaddress is resolved to its canonical host name

(FQDN), and the port number is translated into the corresponding service name.

第四列为 本地的地址及端口

Foreign Address

Address and port number of the remote endofthesocket.Analogousto"Local Address."

第五列为外部的地址及端口

State

Thestateofthesocket.Sincethere are no states in raw mode and usually no

states used in UDP, this column may be left blank. Normally this can be one of sev-

eral values:

第六列为socket的状态，通常仅仅有tcp的状态，状态值可能有ESTABLISHED，SYN_SENT，SYN_RECV FIN_WAIT1，FIN_WAIT2，TIME_WAIT等，详见下文。其中，最重要的是第六列

netstat第六列State的状态信息

State

Thestateofthesocket.Sincethere are no states in raw mode and usually no

states used in UDP, this column may be left blank. Normally this can be one of sev-

eral values:

第六列为socket的状态，通常仅仅有tcp的状态，状态值可能有ESTABLISHED，SYN_SENT，SYN_RECV FIN_WAIT1，FIN_WAIT2，TIME_WAIT等，详见下文。其中，最重要的是第六列

ESTABLISHED

The socket has an established connection.

socket已经建立连接，表示处于连接的状态，一般认为有一个ESTABLISHED认为是一个服务的并发连接。这个连接状态在生产场景很重要，要重点关注

SYN_SENT

The socket is actively attempting to establish a connection.

socket正在积极尝试建立一个连接，即处于发送后连接前的一个等待但未匹配进入连接的状态

SYN_RECV

A connection request has been received from the network.

已经从网络上收到一个连接请求

FIN_WAIT1

The socket is closed, and the connection is shutting down.

socket已关闭，连接正在或正要关闭

FIN_WAIT2

Connectionisclosed,andthesocket is waiting for a shutdown from the remote end.

连接已关闭，并且socket正在等待远端结束

TIME_WAIT

The socket is waiting after close to handle packets still in the network.

socket正在等待关闭处理仍在网络上的数据包，这个连接状态在生产场景很重要，要重点关注

CLOSED The socket is not being used.| socket不在被占用了

CLOSE_WAIT

The remote end has shutdown, waiting for the socket to close.

远端已经结束，等待socket关闭

LAST_ACK

The remote end has shut down, and the socket is closed. Waiting for acknowl-edgement.|

远端已经结束，并且socket也已关闭，等待acknowl-edgement

LISTEN Thesocketislisteningforincoming connections.Such sockets are not

included in the output unless you specify the --listening (-l) or --all (-a)

option.

socket正在监听连接请求

CLOSING

Both sockets are shut down but we still don’t have all our data sent.

sockets关闭，但是我们仍旧没有发送数据

UNKNOWN

The state of the socket is unknown

未知的状态

```bash
# 日志分析篇(Apache)：
# 得访问前10位的ip地址
cat access.log|awk '{print $1}'|sort|uniq -c|sort -nr|head -10
cat access.log|awk '{counts[$(11)]+=1}; END {for(url in counts) print counts[url], url}'

# 问次数最多的文件或页面,取前20
cat access.log|awk '{print $11}'|sort|uniq -c|sort -nr|head -20

# 出传输最大的几个exe文件（分析下载站的时候常用）
cat access.log |awk '($7~/\.exe/){print $10 " " $1 " " $4 " " $7}'|sort -nr|head -20

# 出输出大于200000byte(约200kb)的exe文件以及对应文件发生次数
cat access.log |awk '($10 > 200000 && $7~/\.exe/){print $7}'|sort -n|uniq -c|sort -nr|head -100

# 果日志最后一列记录的是页面文件传输时间，则有列出到客户端最耗时的页面
cat access.log |awk  '($7~/\.php/){print $NF " " $1 " " $4 " " $7}'|sort -nr|head -100

# 出最最耗时的页面(超过60秒的)的以及对应页面发生次数
cat access.log |awk '($NF > 60 && $7~/\.php/){print $7}'|sort -n|uniq -c|sort -nr|head -100

# 出传输时间超过 30 秒的文件
cat access.log |awk '($NF > 30){print $7}'|sort -n|uniq -c|sort -nr|head -20

# 计网站流量（G)
cat access.log |awk '{sum+=$10} END {print sum/1024/1024/1024}'

# 计404的连接
awk '($9 ~/404/)' access.log | awk '{print $9,$7}' | sort

# 计http status.
cat access.log |awk '{counts[$(9)]+=1}; END {for(code in counts) print code, counts[code]}'
cat access.log |awk '{print $9}'|sort|uniq -c|sort -rn

# 蛛分析--查看是哪些蜘蛛在抓取内容
/usr/sbin/tcpdump -i eth0 -l -s 0 -w - dst port 80 | strings | grep -i user-agent | grep -i -E 'bot|crawler|slurp|spider'

# 日志分析篇(Squid)
# 按域统计流量
zcat squid_access.log.tar.gz| awk '{print $10,$7}' |awk 'BEGIN{FS="[ /]"}{trfc[$4]+=$1}END{for(domain in trfc){printf "%s\t%d\n",domain,trfc[domain]}}'

# 数据库篇
# 查看数据库执行的sql
/usr/sbin/tcpdump -i eth0 -s 0 -l -w - dst port 3306 | strings | egrep -i 'SELECT|UPDATE|DELETE|INSERT|SET|COMMIT|ROLLBACK|CREATE|DROP|ALTER|CALL'
```
