# Linux Virtual Server

```txt
Linux虚拟服务器是一个高度可扩展且高度可用的服务器，构建在真实服务器集群上。服务器群集的体系结构对最终用户完全透明，用户与群集系统进行交互，就好像它只是一个高性能的虚拟服务器一样。
```

![figure](http://www.linuxvirtualserver.org/VirtualServer.png)

```txt
真实服务器和负载平衡器可以通过高速LAN或地理上分散的WAN互连。负载均衡器可以将请求分派给不同的服务器，并使群集的并行服务在单个IP地址上显示为虚拟服务，请求分派可以使用IP负载平衡技术或应用级负载均衡技术。通过透明地添加或删除集群中的节点来实现系统的可伸缩性。通过检测节点或守护程序故障并适当地重新配置系统来提供高可用性。
```

```txt
构建服务器集群方法：
DNS负载平衡：
简单，利用域名系统将域名解析到服务器不同的IP来分发请求，DNS服务器根据调度策略(如循环方式)发出服务器IP地址，然后使用相同的本地缓存域名服务器在指定的域名解析生存时间(TTL)中发送到同一个服务器。
但是，由于客户端和分层DNS系统的缓存特性，很容易导致服务器之间的动态负载不平衡，因此服务器不容易处理其峰值负载。在DNS服务器上无法很好地选择名称映射的TTL值，小值DNS流量很高且DNS服务器将成为瓶颈，并且具有高值，动态负载不平衡将变得更糟。即使TTL值设置为零，调度粒度是每个主机，不同用户的访问模式可能会导致动态负载不平衡，因为有些人可能从网站上抽取大量页面，而其他人可能只是浏览几页然后去远。此外，它不太可靠，当服务器节点发生故障时，将名称映射到IP地址的客户端将发现服务器已关闭

基于调度程序的负载平衡群集：
可用于在群集中的服务器之间分配负载，以便服务器的并行服务可以在单个IP地址上显示为虚拟服务，并且最终用户可以像单个服务器一样进行交互不知道集群中的所有服务器。与基于DNS的负载平衡相比，调度程序可以以精细的粒度（例如每个连接）调度请求，以便在服务器之间实现更好的负载平衡。当一台或多台服务器发生故障时，可以屏蔽故障。服务器管理变得越来越容易，管理员可以随时使用服务器或更多服务器，这不会中断最终用户的服务。

负载均衡可以在两个级别完成，即应用级和IP级。将HTTP请求转发到集群中的不同Web服务器，获取结果，然后将其返回给客户端。由于在应用程序级别处理HTTP请求和回复的开销很高，当服务器节点数量增加到5或更多时，应用程序级负载均衡器将成为新的瓶颈，这取决于每个服务器节点的吞吐量服务器
```

```txt
LinuxVirtualServer以三种IP负载均衡技术(数据包转发方法)实现。
1. NAT方式
2. IP隧道
3. 直接路由
```

VS/NAT，VS/TUN和VS/DR的比较：
|      对比方面       |   VS/NAT   |  VS/TUN   |   VS/DR    |
| :-----------------: | :--------: | :-------: | :--------: |
|     realserver      |    任何    |   隧道    | 非ARP设备  |
| realserver network  |    私有    |  lan/wan  |    lan     |
| realserver numbers  | low(10-20) |   high    |    high    |
| realserver gateway  |  director  | ownrouter | own router |
| client connnects to |    VIP     |    VIP    |    VIP     |

LVS集群通常采用三连接架构：
![architecture](http://www.linuxvirtualserver.org/lvs_architecture.jpg)

三连接结构包括：

**负载均衡器**它是整个集群系统的前端机器，并在一组服务器之间平衡来自客户端的请求，以便客户端认为所有服务都来自单个IP地址。

**服务器群集**它是一组运行实际网络服务的服务器，例如Web，邮件，FTP，DNS和媒体服务。

**共享存储**为服务器提供共享存储空间，以便服务器可以轻松拥有相同的内容并提供相同的服务。

NAT:
NAT(Network Address Translation)网络地址转换即将IP地址从一个组映射到另一个组的功能。当地址映射为N到N时，称为静态网络地址转换; 当映射是M到N（M> N）时，它被称为动态网络地址转换。网络地址端口转换是基本NAT的扩展，因为许多网络地址及其TCP/UDP端口被转换为单个网络地址及其TCP/UDP端口。这是N-to-1映射，实现了Linux IP Masquerading(伪装)。Linux上的NAT虚拟服务器是通过网络地址端口转换完成的
![architecture](http://www.linuxvirtualserver.org/VS-NAT.gif)
当用户访问服务器集群提供的服务时，发往虚拟IP地址的请求数据包（负载均衡器的外部IP地址）到达负载均衡器。负载均衡器检查数据包的目标地址和端口号。如果根据虚拟服务器规则表匹配虚拟服务器服务，则通过调度算法从集群中选择真实服务器，并将连接添加到记录已建立连接的哈希表中。然后，将目标地址和数据包的端口重写为所选服务器的目标地址和端口，并将数据包转发到服务器。当传入数据包属于此连接并且可以在哈希表中找到所选服务器时，将重写该数据包并将其转发到所选服务器。当回复数据包返回时，负载均衡器将数据包的源地址和端口重写为虚拟服务的源地址和端口。连接终止或超时后，连接记录将在哈希表中删除。

IP隧道：
IP隧道（IP封装）是一种将IP数据包封装在IP数据报中的技术，它允许将发往一个IP地址的数据报包装并重定向到另一个IP地址。IP封装现在通常用于外联网，移动IP，IP多播，隧道主机或网络。
![architecture](http://www.linuxvirtualserver.org/VS-IPTunneling.gif)
当用户访问由服务器集群提供的虚拟服务时，到达虚拟IP地址的分组（虚拟服务器的IP地址）到达。负载均衡器检查数据包的目标地址和端口。如果它们匹配虚拟服务，则根据连接调度算法从群集中选择真实服务器，并将连接添加到记录连接的哈希表中。然后，负载均衡器将数据包封装在IP数据报中，并将其转发到所选服务器。当传入数据包属于此连接并且可以在哈希表中找到所选服务器时，将再次封装数据包并将其转发到该服务器。当服务器收到封装的数据包时，它会解封装数据包并处理请求，最后根据自己的路由表将结果直接返回给用户。连接终止或超时后，连接记录将从哈希表中删除。

直接路由:
此请求调度方法类似于IBM的NetDispatcher中实现的方法。虚拟IP地址由真实服务器和负载均衡器共享。负载均衡器的接口也配置了虚拟IP地址，用于接受请求数据包，并直接将数据包路由到选定的服务器。所有真实服务器的非arp别名接口都配置了虚拟IP地址，或者将发往虚拟IP地址的数据包重定向到本地套接字，以便真实服务器可以在本地处理数据包。负载均衡器和真实服务器必须通过HUB / Switch物理连接其中一个接口。通过直接路由的虚拟服务器的体系结构如下所示：
![architecture](http://www.linuxvirtualserver.org/VS-DRouting.gif)
当用户访问由服务器集群提供的虚拟服务时，到达虚拟IP地址的分组（虚拟服务器的IP地址）到达。负载均衡器（LinuxDirector）检查数据包的目标地址和端口。如果它们匹配虚拟服务，则通过调度算法从群集中选择真实服务器，并将连接添加到记录连接的哈希表中。然后，负载均衡器直接将其转发到所选服务器。当传入数据包属于此连接并且可以在哈希表中找到所选服务器时，该数据包将再次直接路由到服务器。当服务器收到转发的数据包时，服务器发现该数据包是用于其别名接口上的地址或本地套接字的地址，所以它处理请求并最终将结果直接返回给用户。连接终止或超时后，连接记录将从哈希表中删除。

作业调度算法：
循环调度Round Robin PR
加权循环调度Weighted Round Robin WPR
最小连接调度Least Connections LC
加权最小连接调度Weighted Least Connections WLC
基于位置的最小连接调度Locality-Based Least Connections LBLC
基于位置的最小连接与复制调度Locality-Based Least Connections with Replication LBLCR
目的地哈希调度Destination Hashing DH
源哈希调度Source Hashing SH
最短的预期延迟调度Shortest Expected Delay Scheduling SED
从不排队调度Never Queue Scheduling NQ

循环调度
循环调度算法将每个传入请求发送到其列表中的下一个服务器。因此，在三服务器集群（服务器A，B和C）中，请求1将转到服务器A，请求2将转到服务器B，请求3将转到服务器C，请求4将转到服务器A，从而完成骑自行车或服务器'循环'。无论每个服务器遇到的传入连接数或响应时间如何，它都将所有真实服务器视为相等。与传统的循环DNS相比，Virtual Server具有一些优势。循环DNS将单个域解析为不同的IP地址，调度粒度是基于主机的，并且DNS查询的缓存阻碍了基本算法，这些因素导致真实服务器之间显着的动态负载不平衡。

加权循环调度
加权循环调度旨在更好地处理具有不同处理能力的服务器。可以为每个服务器分配一个权重，一个表示处理能力的整数值。具有较高权重的服务器首先接收新连接，而不是权重较小的服务器，具有较高权重的服务器比具有较少权重的服务器获得更多连接，具有相同权重的服 例如，真实服务器A，B和C分别具有权重4,3,2，在调度周期（mod sum（Wi））中，良好的调度序列将是AABABCABC。在加权循环调度的实现中，在修改虚拟服务器的规则之后，将根据服务器权重生成调度序列。

当真实服务器的处理能力不同时，加权循环调度优于循环调度。但是，如果请求的负载变化很大，则可能导致真实服务器之间的动态负载不平衡。简而言之，大多数需要大响应的请求可能会被引导到同一个真实服务器。

实际上，循环调度是加权循环调度的一个特殊实例，其中所有权重都相等。

最小连接调度
最少连接调度算法将网络连接定向到具有最少数量的已建立连接的服务器。这是动态调度算法之一; 因为它需要动态计算每个服务器的实时连接。对于管理具有类似性能的服务器集合的虚拟服务器，当请求的负载变化很大时，最少连接调度可以平滑分发。虚拟服务器将使用最少的活动连接将请求定向到真实服务器。

乍一看，即使存在各种处理能力的服务器，最少连接调度也可能表现良好，因为更快的服务器将获得更多的网络连接。实际上，由于TCP的TIME_WAIT状态，它无法很好地执行。TCP的TIME_WAIT通常是2分钟，在这2分钟内，一个繁忙的网站经常收到数千个连接，例如，服务器A的功能是服务器B的两倍，服务器A处理数千个请求并将它们保存在TCP的TIME_WAIT状态，但服务器B正在爬行以完成其数千个连接。因此，最少连接调度不能在具有各种处理能力的服务器之间很好地平衡负载。

加权最小连接调度
加权最小连接调度是最小连接调度的超集，您可以在其中为每个真实服务器分配性能权重。具有较高权重值的服务器在任何时候都将获得更大比例的实时连接。虚拟服务器管理员可以为每个真实服务器分配权重，并且将网络连接安排到每个服务器，其中每个服务器的当前活动连接数的百分比是与其权重的比率。默认权重为1。

加权最小连接调度的工作原理如下：
假设有n个真实服务器，每个服务器i的权重为W i（i = 1，..，n），而活动连接C i （i = 1，..，n），ALL_CONNECTIONS是C i的总和 （i = 1，..，n），下一个网络连接将被定向到服务器j，其中
（C j / ALL_CONNECTIONS）/ W j = min {（C i / ALL_CONNECTIONS）/ W i }（i = 1，..，n）
由于ALL_CONNECTIONS在此查找中是常量，因此无需将C i除以ALL_CONNECTIONS，它可以优化为
C j / W j = min {C i / W i }（i = 1，..，n）
加权最小连接调度算法需要比最小连接更多的划分。为了在服务器具有相同的处理能力时最小化调度的开销，实现最少连接调度和加权最小连接调度算法。

基于位置的最小连接调度
基于位置的最小连接调度算法用于目标IP负载平衡。它通常用于缓存集群。如果服务器处于活动状态且负载不足，此算法通常会将发往IP地址的数据包定向到其服务器。如果服务器过载（其活动连接数大于其权重）并且服务器处于半负载状态，则将加权最小连接服务器分配给此IP地址。

基于位置的最小连接与复制调度
基于位置的最小连接与复制调度算法也用于目标IP负载平衡。它通常用于缓存集群。它与LBLC调度的不同之处如下：负载均衡器维护从目标到可以为目标服务的一组服务器节点的映射。对目标的请求将分配给目标服务器集中的最小连接节点。如果服务器集中的所有节点都过载，它将在集群中获取最少连接节点，并将其添加到目标服务器集中。如果服务器集未在指定时间内进行修改，则会从服务器集中删除负载最多的节点，以避免高度复制。

目的地哈希调度
目标哈希调度算法通过按目标IP地址查找静态分配的哈希表来为服务器分配网络连接。

源哈希调度
源哈希调度算法通过按源IP地址查找静态分配的哈希表来为服务器分配网络连接。

最短的预期延迟调度
最短的预期延迟调度算法以最短的预期延迟将网络连接分配给服务器。如果发送到第i个服务器，作业将经历的预期延迟是（Ci + 1）/ Ui，其中Ci是第i个服务器上的连接数，Ui是第i个服务器的固定服务速率（权重） 。

从不排队调度
永不排队调度算法采用双速模型。当有空闲服务器可用时，作业将被发送到空闲服务器，而不是等待快速服务器。当没有可用的空闲服务器时，作业将被发送到服务器，以最小化其预期延迟（最短预期延迟调度算法）。

LVS中IP或网络的名称

```txt

典型的LVS-NAT设置：LVS-NAT: no arp problem (the realservers don't have the VIP)
                        ________
                       |        |
                       | client | (local or on internet)
                       |________|
                          CIP
                           |
                        (router)
                          DGW--DIRECTOR-gateway
                           | outside network
                           |
                          VIP--Virtual IP
                       ____|_____
                      |          | (director can have 1 or 2 NICs)
                      | director |
                      |__________|
                      DIP (and PIP)--primary director IP = PIP (the director which will be the master on bootup)
                           |       --secondary director IP = SIP (the director which will be the backup on bootup)
                           | DRIP network
          ----------------------------------
          |                |               |
          |                |               |
         RIP1             RIP2            RIP3
     _____________   _____________   _____________
    |             | |             | |             |
    | realserver1 | | realserver2 | | realserver3 |
    |_____________| |_____________| |_____________|

典型的LVS-DR or LVS-Tun设置
                        ________
                       |        |
                       | client | (local or on internet)
                       |________|
                           |
                        (router)-----------
                           |    SERVER_GW  |
                           |               |
                          VIP              |
                       ____|_____          |
                      |          | (director can have 1 or 2 NICs)
                      | director |         |
                      |__________|         |
                          DIP              |
                           |               |
                           |               |
          -----------------+----------------
          |                |               |
          |                |               |
       RIP1,VIP         RIP2,VIP        RIP3,VIP
    ____________     ____________     ____________
   |            |   |            |   |            |
   | realserver |   | realserver |   | realserver |
   |____________|   |____________|   |____________|

client IP     = CIP
virtual IP    = VIP - the IP on the director that the client connects to)
director IP   = DIP - the IP on the director in the DIP/RIP (DRIP) network
   (this is the realserver gateway for LVS-NAT)
realserver IP = RIP (and RIP1, RIP2...) the IP on the realserver
director GW   = DGW - the director's gw (only needed for LVS-NAT)
   (this can be the realserver gateway for LVS-DR and LVS-Tun)

VIP和DIP设置为辅助IP（即该NIC(网卡)上有另一个主IP），因此可以在director故障转移后将它们移动到另一个复制的director。对于使用单个director的初始设置，将VIP和DIP设置为次要IP将使故障转移设置更加容易。
```

|       |                                      |
| :---: | :----------------------------------: |
|  DGW  |           公网IP的默认网关           |
|  VIP  | 客户端访问的公网IP，Director的虚拟IP |
|  DIP  |           Director的真实IP           |
|  RIP  |            realserver的IP            |
|  RIP  |               客户端IP               | - |

例子：LVS使用LVS-NAT转发

```txt
在direcotor使用一个网卡，也可使用两个，但要改变设置。
                      ________
                     |        |
                     | client |
                     |________|
                  CIP=DGW=192.168.2.254 (eth0)
                         |
                         |
           __________    |
          |          |   |   (VIP=192.168.2.110,eth0:110)
          | director |---|
          |__________|   |   DIP=SGW=192.168.1.9(eth0)
                         |
                         |
        -----------------------------------
        |                                 |
        |                                 |
RIP=192.168.1.11(eth0)              RIP=192.168.1.12(eth0)
   ____________                        ____________
  |            |                      |            |
  | realserver |                      | realserver |
  |____________|                      |____________|
```

例子：LVS使用LVS-DR转发

```txt
在具有单个NIC的计算机上使用单个网络（此处为192.168.1.0/24）设置LVS。以太网别名上的IP（例如eth0：110，lo：0）由configure脚本设置。没有默认路由，并且所有计算机都应该能够相互ping通。
                        ________
                       |        |
                       | client |
                       |________|
                   CIP=SGW=192.168.1.254 (eth0)
                           |
                           |
             __________    |
            |          |   |   (VIP=192.168.1.110, eth0:110)
            | director |---|
            |__________|   |   DIP=192.168.1.9 (eth0)
                           |
                           |
          -----------------------------------
          |                                 |
          |                                 |
 RIP=192.168.1.11(eth0)             RIP=192.168.1.12(eth0)
(VIP=192.168.1.110, lo:0)          (VIP=192.168.1.110, lo:0)
    ____________                       ____________
   |            |                     |            |
   | realserver |                     | realserver |
   |____________|                     |____________|
```

## LVS+Keepalived解决方案官方示例

使用keepalived构建一个具有两个负载平衡器和三个Web服务器的高可用性VS/NAT Web集群，拓扑图如下：
![topology](http://linux-vs.org/docs/ha/keepalived.jpg)
在本例中，VIP地址和DIP地址分别为10.23.8.80和172.18.1.254均为浮动IP，它们在两个负载均衡器（LD1和LD2）之间浮动，三个realserver的IP地址为172.18.1.11,172.18.1.12和172.18.1.13。
float IP工作原理如下图：
![yuanlli](http://assets.digitalocean.com/blog/static/floating-ips-start-architecting-your-applications-for-high-availability/ha-diagram-animated.gif)
LD1上的keepalived配置文件（/etc/keepalived/keepalived.conf）如下:

```conf
vrrp_sync_group VG1 {
    group {
        VI_1
        VI_2
    }
}

vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        10.23.8.80
    }
}

vrrp_instance VI_2 {
    state MASTER
    interface eth1
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        172.18.1.254
    }
}

virtual_server 10.23.8.80 80 {
    delay_loop 6
    lb_algo wlc
    lb_kind NAT
    persistence_timeout 600
    protocol TCP

    real_server 172.18.1.11 80 {
        weight 100
        TCP_CHECK {
            connect_timeout 3
        }
    }
    real_server 172.18.1.12 80 {
        weight 100
        TCP_CHECK {
            connect_timeout 3
        }
    }
    real_server 172.18.1.13 80 {
        weight 100
        TCP_CHECK {
            connect_timeout 3
        }
    }
}
LD2的和LD1相似，只需将VI_1和VI_2的状态从master改为backup
```

## Keepalived配置文件详解

```conf
# 全局定义模块
global_defs {
   notification_email {
     acassen@firewall.loc
     failover@firewall.loc
     sysadmin@firewall.loc #邮件报警
   }
   notification_email_from Alexandre.Cassen@firewall.loc 指定发件人
   smtp_server 192.168.200.1  #指定smtp服务器地址
   smtp_connect_timeout 30    指定smtp连接超时时间
   router_id LVS_DEVEL  #负载均衡标识，在局域网内应该是唯一的。
   vrrp_skip_check_adv_addr
   vrrp_strict
   vrrp_garp_interval 0
   vrrp_gna_interval 0
}

说明：
notification_email：指定当keepalived出现问题时，发送邮件给哪些用户。
notification_emai_from：发送邮件时，邮件的来源地址。
smtp_server <DOMAIN|IP> [<PORT>]：smtp服务器的地址或域名。默认端口为25.如：smtp_server smtp.felix.com 25
smtp_helo_name <HOST_NAME>：指定在HELO消息中所使用的名称。默认为本地主机名。
smtp_connect_timeout：指定smtp服务器连接的超时时间。单位是秒。

router_id：指定标识该机器的route_id. 如：route_id LVS_01
vrrp_mcast_group4 224.0.0.18：指定发送VRRP组播消息使用的IPV4组播地址。默认是224.0.0.18
vrrp_mcast_group6 ff02::12 指定发送VRRP组播消息所使用的IPV6组播地址。默认是ff02::12
default_interface eth0：设置静态地址默认绑定的端口。默认是eth0。
lvs_sync_daemon <INTERFACE> <VRRP_INSTANCE> [id <SYNC_ID>] [maxlen <LEN>] [port <PORT>] [ttl <TTL>] [group <IP ADDR>]
    设置LVS同步服务的相关内容。可以同步LVS的状态信息。
    INTERFACE：指定同步服务绑定的接口。
    VRRP_INSTANCE：指定同步服务绑定的VRRP实例。
    id <SYNC_ID>：指定同步服务所使用的SYNCID，只有相同的SYNCID才会同步。范围是0-255.
    maxlen：指定数据包的最大长度。范围是1-65507
    port：指定同步所使用的UDP端口。
    group：指定组播IP地址。

lvs_flush：在keepalived启动时，刷新所有已经存在的LVS配置。
vrrp_garp_master_delay 10：当转换为MASTER状态时，延迟多少秒发送第二组的免费ARP。默认为5s，0表示不发送第二组免的免费ARP。
vrrp_garp_master_repeat 1：当转换为MASTER状态时，在一组中一次发送的免费ARP数量。默认是5.
vrrp_garp_lower_prio_delay 10：当MASTER收到更低优先级的通告时，延迟多少秒发送第二组的免费ARP。
vrrp_garp_lower_prio_repeat 1：当MASTER收到更低优先级的通告时，在一组中一次发送的免费ARP数量。
vrrp_garp_master_refresh 60：当keepalived成为MASTER以后，刷新免费ARP的最小时间间隔(会再次发送免费ARP)。默认是0，表示不会刷新。
vrrp_garp_master_refresh_repeat 2： 当keepalived成为MASTER以后，每次刷新会发送多少个免费ARP。默认是1.
vrrp_garp_interval 0.001：在一个接口发送的两个免费ARP之间的延迟。可以精确到毫秒级。默认是0.
vrrp_lower_prio_no_advert true|false：默认是false。如果收到低优先级的通告，不发送任何通告。
vrrp_version 2|3：设置默认的VRRP版本。默认是2.
vrrp_check_unicast_src：在单播模式中，开启对VRRP数据包的源地址做检查，源地址必须是单播邻居之一。
vrrp_skip_check_adv_addr：默认是不跳过检查。检查收到的VRRP通告中的所有地址可能会比较耗时，设置此命令的意思是，如果通告与接收的上一个通告来自相同的master路由器，则不执行检查(跳过检查)。
vrrp_strict：严格遵守VRRP协议。下列情况将会阻止启动Keepalived：1. 没有VIP地址。2. 单播邻居。3. 在VRRP版本2中有IPv6地址。

vrrp_iptables：不添加任何iptables规则。默认是添加iptables规则的。

如果vrrp进程或check进程超时，可以用下面的4个选项。可以使处于BACKUP状态的VRRP实例变成MASTER状态，即使MASTER实例依然在运行。因为MASTER或BACKUP系统比较慢，不能及时处理VRRP数据包。
vrrp_priority <-20 -- 19>：设置VRRP进程的优先级。
checker_priority <-20 -- 19>：设置checker进程的优先级。
vrrp_no_swap：vrrp进程不能够被交换。
checker_no_swap：checker进程不能够被交换。

script_user <username> [groupname]：设置运行脚本默认用户和组。如果没有指定，则默认用户为keepalived_script(需要该用户存在)，否则为root用户。默认groupname同username。
enable_script_security：如果脚本路径的任一部分对于非root用户来说，都具有可写权限，则不会以root身份运行脚本。
nopreempt 默认是抢占模式 要是用非抢占式的就加上nopreempt
注意：上述为global_defs中的指令
```

## VRRPD配置包含如下子块

1. vrrp_script
2. vrrp_sync_group
3. vrrp_instance

```vrrp_script
作用：添加一个周期性执行的脚本。脚本的退出状态码会被调用它的所有的VRRP Instance记录。
注意：至少有一个VRRP实例调用它并且优先级不能为0.优先级范围是1-254.
vrrp_script <SCRIPT_NAME> {
          ...
    }

选项说明：
scrip "/path/to/somewhere"：指定要执行的脚本的路径。
interval <INTEGER>：指定脚本执行的间隔。单位是秒。默认为1s。
timeout <INTEGER>：指定在多少秒后，脚本被认为执行失败。
weight <-254 --- 254>：调整优先级。默认为2.
rise <INTEGER>：执行成功多少次才认为是成功。
fall <INTEGER>：执行失败多少次才认为失败。
user <USERNAME> [GROUPNAME]：运行脚本的用户和组。
init_fail：假设脚本初始状态是失败状态。

解释：
weight：
1. 如果脚本执行成功(退出状态码为0)，weight大于0，则priority增加。
2. 如果脚本执行失败(退出状态码为非0)，weight小于0，则priority减少。
3. 其他情况下，priority不变。
```

```vrrp_sync_group
作用：将所有相关的VRRP实例定义在一起，作为一个VRRP Group，如果组内的任意一个实例出现问题，都可以实现Failover。
 vrrp_sync_group VG_1 {
    group {
     inside_network     # vrrp instance name
     outside_network    # vrrp instance name
     ...
    }
    ...
}

说明：
如果username和groupname没有指定，则以默认的script_user所指定的用户和组。
1. notify_master /path/to_master.sh [username [groupname]]
    作用：当成为MASTER时，以指定的用户和组执行脚本。
2. notify_backup /path/to_backup.sh [username [groupname]]
    作用：当成为BACKUP时，以指定的用户和组执行脚本。
3. notify_fault "/path/fault.sh VG_1" [username [groupname]]
    作用：当该同步组Fault时，以指定的用户和组执行脚本。
4. notify /path/notify.sh [username [groupname]]
    作用：在任何状态都会以指定的用户和组执行脚本。
    说明：该脚本会在notify_*脚本后执行。
    notify可以使用3个参数，如下：
    $1：可以是GROUP或INTANCE，表明后面是组还是实例。
    $2：组名或实例名。
    $3：转换后的目标状态。有：MASTER、BACKUP、FAULT。

5. smtp_alert：当状态发生改变时，发送邮件。
6. global_tracking：所有的VRRP实例共享相同的tracking配置。

注意：脚本文件要加上x权限，同时指令最好写绝对路径。
```

```vrrp_instance
命令说明：
state MASTER|BACKUP：指定该keepalived节点的初始状态。
interface eth0：vrrp实例绑定的接口，用于发送VRRP包。
use_vmac [<VMAC_INTERFACE>]：在指定的接口产生一个子接口，如vrrp.51，该接口的MAC地址为组播地址，通过该接口向外发送和接收VRRP包。
vmac_xmit_base：通过基本接口向外发送和接收VRRP数据包，而不是通过VMAC接口。
native_ipv6：强制VRRP实例使用IPV6.(当同时配置了IPV4和IPV6的时候)
dont_track_primary：忽略VRRP接口的错误，默认是没有配置的。

track_interface {
  eth0
  eth1 weight <-254-254>
  ...
}：如果track的接口有任何一个出现故障，都会进入FAULT状态。

track_script {
  <SCRIPT_NAME>
  <SCRIPT_NAME> weight <-254-254>
}：添加一个track脚本(vrrp_script配置的脚本。)

mcast_src_ip <IPADDR>：指定发送组播数据包的源IP地址。默认是绑定VRRP实例的接口的主IP地址。
unicast_src_ip <IPADDR>：指定发送单薄数据包的源IP地址。默认是绑定VRRP实例的接口的主IP地址。
version 2|3：指定该实例所使用的VRRP版本。

unicast_peer {
   <IPADDR>
   ...
}：采用单播的方式发送VRRP通告，指定单播邻居的IP地址。

virtual_router_id 51：指定VRRP实例ID，范围是0-255.
priority 100：指定优先级，优先级高的将成为MASTER。
advert_int 1：指定发送VRRP通告的间隔。单位是秒。
authentication {
  auth_type PASS|AH：指定认证方式。PASS简单密码认证(推荐),AH:IPSEC认证(不推荐)。
  auth_pass 1234：指定认证所使用的密码。最多8位。
}

virtual_ipaddress {
   <IPADDR>/<MASK> brd <IPADDR> dev <STRING> scope <SCOPE> label <LABEL>
   192.168.200.17/24 dev eth1
   192.168.200.18/24 dev eth2 label eth2:1
}：指定VIP地址。

nopreempt：设置为不抢占。默认是抢占的，当高优先级的机器恢复后，会抢占低优先级的机器成为MASTER，而不抢占，则允许低优先级的机器继续成为MASTER，即使高优先级的机器已经上线。如果要使用这个功能，则初始化状态必须为BACKUP。
preempt_delay：设置抢占延迟。单位是秒，范围是0---1000，默认是0.发现低优先级的MASTER后多少秒开始抢占。


通知脚本：
notify_master <STRING>|<QUOTED-STRING> [username [groupname]]
notify_backup <STRING>|<QUOTED-STRING> [username [groupname]]
notify_fault <STRING>|<QUOTED-STRING> [username [groupname]]
notify <STRING>|<QUOTED-STRING> [username [groupname]]

# 当停止VRRP时执行的脚本。
notify_stop <STRING>|<QUOTED-STRING> [username [groupname]]
smtp_alert
```

## LVS配置

```virtual_server
virtual_server IP Port | virtual_server fwmark int | virtual_server group string {
  delay_loop <INT>：健康检查的时间间隔。
  lb_argo rr|wrr|lc|wlc|lblc|sh|dh：LVS调度算法。
  lb_kind NAT|DR|TUN：LVS模式。
  persistence_timeout 360：持久化超时时间，单位是秒。默认是6分钟。
  persistence_granularity：持久化连接的颗粒度。
  protocol TCP|UDP|SCTP：4层协议。
  ha_suspend：如果virtual server的IP地址没有设置，则不进行后端服务器的健康检查。
  virtualhost <STRING>：为HTTP_GET和SSL_GET执行要检查的虚拟主机。如virtualhost www.felix.com
  sorry_server <IPADDR> <PORT>：添加一个备用服务器。当所有的RS都故障时。
  sorry_server_inhibit：将inhibit_on_failure指令应用于sorry_server指令。

  alpha：在keepalived启动时，假设所有的RS都是down，以及健康检查是失败的。有助于防止启动时的误报。默认是禁用的。
  omega：在keepalived终止时，会执行quorum_down指令所定义的脚本。

  quorum <INT>：默认值1. 所有的存活的服务器的总的最小权重。
  quorum_up <STRING>：当quorum增长到满足quorum所定义的值时，执行该脚本。
  quorum_down <STRING>：当quorum减少到不满足quorum所定义的值时，执行该脚本。
}
```

```real_server
real_server IP Port {
  weight <INT>：给服务器指定权重。默认是1.
  inhibit_on_failure：当服务器健康检查失败时，将其weight设置为0，而不是从Virtual Server中移除。
  notify_up <STRING>：当服务器健康检查成功时，执行的脚本。
  notify_down <STRING>：当服务器健康检查失败时，执行的脚本。
  uthreshold <INT>：到这台服务器的最大连接数。
  lthreshold <INT>：到这台服务器的最小连接数。
}

# real_server中的健康检查
HTTP_GET | SSL_GET {
url {
  path <STRING>：指定要检查的URL的路径。如path / or path /mrtg2
  digest <STRING>：摘要。计算方式：genhash -s 172.17.100.1 -p 80 -u /index.html
  status_code <INT>：状态码。
}
nb_get_retry <INT>：get尝试次数。
delay_before_retry <INT>：在尝试之前延迟多长时间。

connect_ip <IP ADDRESS>：连接的IP地址。默认是real server的ip地址。
connect_port <PORT>：连接的端口。默认是real server的端口。
bindto <IP ADDRESS>：发起连接的接口的地址。
bind_port <PORT>：发起连接的源端口。
connect_timeout <INT>：连接超时时间。默认是5s。
fwmark <INTEGER>：使用fwmark对所有出去的检查数据包进行标记。
warmup <INT>：指定一个随机延迟，最大为N秒。可防止网络阻塞。如果为0，则关闭该功能。
}

TCP_CHECK {
connect_ip <IP ADDRESS>：连接的IP地址。默认是real server的ip地址。
connect_port <PORT>：连接的端口。默认是real server的端口。
bindto <IP ADDRESS>：发起连接的接口的地址。
bind_port <PORT>：发起连接的源端口。
connect_timeout <INT>：连接超时时间。默认是5s。
fwmark <INTEGER>：使用fwmark对所有出去的检查数据包进行标记。
warmup <INT>：指定一个随机延迟，最大为N秒。可防止网络阻塞。如果为0，则关闭该功能。
retry <INIT>：重试次数。默认是1次。
delay_before_retry <INT>：默认是1秒。在重试之前延迟多少秒。
}


SMTP_CHECK {
connect_ip <IP ADDRESS>：连接的IP地址。默认是real server的ip地址。
connect_port <PORT>：连接的端口。默认是real server的端口。 默认是25端口
bindto <IP ADDRESS>：发起连接的接口的地址。
bind_port <PORT>：发起连接的源端口。
connect_timeout <INT>：连接超时时间。默认是5s。
fwmark <INTEGER>：使用fwmark对所有出去的检查数据包进行标记。
warmup <INT>：指定一个随机延迟，最大为N秒。可防止网络阻塞。如果为0，则关闭该功能。

retry <INT>：重试次数。
delay_before_retry <INT>：在重试之前延迟多少秒。
helo_name <STRING>：用于SMTP HELO请求的字符串。
}


DNS_CHECK {
connect_ip <IP ADDRESS>：连接的IP地址。默认是real server的ip地址。
connect_port <PORT>：连接的端口。默认是real server的端口。 默认是25端口
bindto <IP ADDRESS>：发起连接的接口的地址。
bind_port <PORT>：发起连接的源端口。
connect_timeout <INT>：连接超时时间。默认是5s。
fwmark <INTEGER>：使用fwmark对所有出去的检查数据包进行标记。
warmup <INT>：指定一个随机延迟，最大为N秒。可防止网络阻塞。如果为0，则关闭该功能。

retry <INT>：重试次数。默认是3次。
type <STRING>：DNS query type。A/NS/CNAME/SOA/MX/TXT/AAAA
name <STRING>：DNS查询的域名。默认是(.)
}



MISC_CHECK {
 misc_path <STRING>：外部的脚本或程序路径。
 misc_timeout <INT>：脚本执行超时时间。
 user USERNAME [GROUPNAME]：指定运行该脚本的用户和组。如果没有指定GROUPNAME，则GROUPNAME同USERNAME。
 misc_dynamic：根据退出状态码动态调整权重。
  0，健康检查成功，权重不变。
  1，健康检查失败。
  2-255，健康检查成功。权重设置为退出状态码减去2.如退出状态码是250，则权重调整为248
   warmup <INT>：指定一个随机延迟，最大为N秒。可防止网络阻塞。如果为0，则关闭该功能。
}
```

## 修改Keepalived日志

```conf
# keepalived 默认将日志输出到系统日志/var/log/messages中
vi /etc/sysconfig/keepalived

# Options for keepalived. See `keepalived --help'output and keepalived(8) and
# keepalived.conf(5) man pages for a list of all options. Here are the most
# common ones :
#
# --vrrp               -P    Only run with VRRP subsystem.
# --check              -C    Only run with Health-checker subsystem.
# --dont-release-vrrp  -V    Dont remove VRRP VIPs & VROUTEs on daemon stop.
# --dont-release-ipvs  -I    Dont remove IPVS topology on daemon stop.
# --dump-conf          -d    Dump the configuration data.
# --log-detail         -D    Detailed log messages.
# --log-facility       -S    0-7 Set local syslog facility (default=LOG_DAEMON)(日志的级别)
#

# 把 KEEPALIVED_OPTIONS=”-D” 修改为 KEEPALIVED_OPTIONS=”-D -d -S 0”，其中 -S 指定 syslog 的 facility
KEEPALIVED_OPTIONS="-D -d -S 0"

# 修改 /etc/rsyslog.conf 末尾添加
vi /etc/rsyslog.conf
local0.*                                                /var/log/keepalived.log

# 重启日志记录服务
systemctl restart rsyslog

# 重启 keepalived
systemctl restart keepalived

日志级别：指定触发日志事件的级别
```

## LVS-NAT+Keepalived主从部署示例

LVS-DR修改virtual_server下lb_kind NAT参数为lb_kind DR(LVS-DR为常用方式)

|    IP地址     |   主机名   |    类型     |
| :-----------: | :--------: | :---------: |
|   1.1.1.10    | LVS-Master |     VIP     |
| 192.168.1.10  | LVS-Master |     DIP     |
|   1.1.1.20    | LVS-Backup |     VIP     |
| 192.168.1.20  | LVS-Backup |     DIP     |
| 192.168.1.100 | Web-node1  | realserver1 |
| 192.168.1.200 | Web-node2  | realserver2 |
|   1.1.1.131   |   client   |   client    |

公网浮动IP：1.1.1.1
内网浮动IP：192.168.1.1

eth0:1.1.1.10   ___________            ___________
VIP            |           |         \|           |  
               | LVS-Master|—— —— —— —| Web-node1 |192.168.1.100
               |___________|       __/|___________|
eth1:192.168.1.10  |         \      /|
DIP                |          \    /
 ________          |           \  /
|        |         |            \/
| client |—— —— —— —            /\
|________|         |           /  \
                   |          /    \
                   |         /      \
eth0:1.1.1.20   ___|_______ /       _\/___________
VIP            |           |         \|           |
               | LVS-Backup|—— —— —— —| Web-node2 |192.168.1.200
DIP            |___________|         /|___________|
eth1:192.168.1.20

## LVS服务器上配置IP

```sh
vim /etc/sysconfig/network-scripts/ifcfg-eth0
vim /etc/sysconfig/network-scripts/ifcfg-eth1

# 修改内核参数
vim /etc/sysctl.conf
net.ipv4.ip_forward = 1
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
net.ipv4.conf.eth0.send_redirects = 0
sysctl -p

# realserver分别执行以下命令
# 绑定vip(centos6)
ifconfig lo:0 1.1.1.1 netmask 255.255.255.255 broadcast 1.1.1.1
# (centos7)
ip a a 1.1.1.1/24 dev lo:0

# arp抑制
echo "1" >/proc/sys/net/ipv4/conf/lo/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/lo/arp_announce
echo "1" >/proc/sys/net/ipv4/conf/all/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/all/arp_announce
# arp_ignore:定义接收到ARP请求时的响应级别
0：只要本地配置的有对应地址，就给予回应；默认
1：仅在请求的目的地址配置在到达的接口上时，才给予回应

# arp_announce：定义将自己的地址向外通告时的通告级别
0：将本地任何接口上的任何地址向外通告；默认
1：试图仅向目标网络通告其网络匹配的地址
2：仅向与本地接口上地址匹配的网络进行通告

# 单个实例必须相同的配置项
vrrp_instance VI_1
virtual_router_id 51
auth_type PASS
auth_pass 1111
virtual_ipaddress

# 单个实例必须不相同的配置项
router_id id1
state MASTER  
priority 100

# 上述简化设置--在realserver创建脚本rs.sh
#!/bin/bash
#适用于centos6
vip1=1.1.1.10
vip2=1.1.1.20
dev1=lo:1
dev2=lo:2
case $1 in
start)
    echo 1 > /proc/sys/net/ipv4/conf/all/arp_ignore
    echo 1 > /proc/sys/net/ipv4/conf/lo/arp_ignore
    echo 2 > /proc/sys/net/ipv4/conf/all/arp_announce
    echo 2 > /proc/sys/net/ipv4/conf/lo/arp_announce
    ifconfig $dev1 $vip1 netmask 255.255.255.255 broadcast $vip up
    ifconfig $dev2 $vip2 netmask 255.255.255.255 broadcast $vip2 up
    sysctl -p >/dev/null 2>&1
    echo "VS Server is Ready!"
    ;;
stop)
    ifconfig $dev down
    echo 0 > /proc/sys/net/ipv4/conf/all/arp_ignore
    echo 0 > /proc/sys/net/ipv4/conf/lo/arp_ignore
    echo 0 > /proc/sys/net/ipv4/conf/all/arp_announce
    echo 0 > /proc/sys/net/ipv4/conf/lo/arp_announce
    echo "VS Server is Cancel!"
    ;;
*)
    echo "Usage `basename $0` start|stop"
    exit 1
    ;;
esac
```

## LVS服务器上分别安装并配置keepalived

### 主LVS配置keepalived.conf

```sh
global_defs {
    notification_email {
         your_email@qq.com   # 邮件发生目的人
       }
    notification_email_from keepalived@localhost
    smtp_server 127.0.0.1 #本机邮件服务器
    smtp_connect_timeout 30 #超时时间
    router_id LVS_R1  # 标识服务器ID
}

vrrp_sync_group VG1 {   # 设置实例组
    group {
        VI_1
        VI_2
    }
}

vrrp_instance VI_1 {    # 实例名称，实例可以一个，也可多个
state MASTER    # 主LVS服务器
interface eth0  # 网卡接口
virtual_router_id 1 # 服务器id
priority 100    # 优先级(0~255)
advert_int 1    # 心跳检测1秒每次
authentication {    # 设置验证，master和backup必须相同
    auth_type PASS  # 基于密码验证类型
    auth_pass 1111  # 密码
}
virtual_ipaddress {
    1.1.1.1 # 公网浮动IP
 }
}
vrrp_instance VI_2 {
state MASTER
interface eth1
virtual_router_id 1
priority 100
advert_int 1
authentication {
    auth_type PASS
    auth_pass 1111
}
virtual_ipaddress {
    192.168.1.1 # 内网浮动IP
 }
}
virtual_server 1.1.1.1 80 {
delay_loop 15   # 健康检查的时间
lb_algo rr      # 调度算法
lb_kind NAT      # 负载均衡群集的调度方式
protocol TCP
    real_server 192.168.1.100 80 {
        weight 1    # 权重值
        TCP_CHECK { # 健康检查方式
            connect_port 80     # 检查目标端口
            connect_timeout 3   # 链接超时时间
            nb_get_retry 3      # 重试次数
            delay_before_retry 4  # 重试间隔时间
        }
    }
    real_server 192.168.1.200 80 {
        weight 1
        TCP_CHECK {
            connect_port 80
            connect_timeout 3
            nb_get_retry 3
            delay_before_retry 4
        }
    }
}
```

### 从LVS配置

```sh
global_defs {
router_id LVS_R1
}

vrrp_sync_group VG1 {
    group {
        VI_1
        VI_2
    }
}

vrrp_instance VI_1 {
state BACKUP
interface eth0
virtual_router_id 1
priority 90
advert_int 1
authentication {
    auth_type PASS
    auth_pass 1111
}
virtual_ipaddress {
    1.1.1.1
 }
}
vrrp_instance VI_2 {
state BACKUP
interface eth1
virtual_router_id 1
priority 90
advert_int 1
authentication {
    auth_type PASS
    auth_pass 1111
}
virtual_ipaddress {
    192.168.1.1
 }
}
virtual_server 1.1.1.1 80 {
delay_loop 15   # 健康检查的时间
lb_algo rr      # 调度算法
lb_kind NAT      # 负载均衡群集的模式
protocol TCP
    real_server 192.168.1.100 80 {
        weight 1    # 权重值
        TCP_CHECK {
            connect_port 80     # 检查目标端口
            connect_timeout 3   # 链接超时时间
            nb_get_retry 3      # 重试次数
            delay_before_retry 4  # 重试间隔时间
        }
    }
    real_server 192.168.1.200 80 {
        weight 1
        TCP_CHECK {
            connect_port 80
            connect_timeout 3
            nb_get_retry 3
            delay_before_retry 4
        }
    }
}
```

## 创建通用单独的检查邮件提醒脚本lvs_notify.sh

```sh
#!/bin/bash
contact='your_email_address'

notify() {
    local mailsubject="$(hostname) to be $1, vip floating"
    local mailbody="$(date +'%F %T'): vrrp transition, $(hostname) changed to be $1"
    echo "$mailbody" | mail -s "$mailsubject" $contact
}

case $1 in
master)
    notify master
    ;;
backup)
    notify backup
    ;;
fault)
    notify fault
    ;;
*)
    echo "Usage: $(basename $0) {master|backup|fault}"
    exit 1
    ;;
esac
```
