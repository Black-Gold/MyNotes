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
| 对比方面 | VS/NAT | VS/TUN | VS/DR |
| :------: | :------: | :------: | :------: |
| realserver | 任何 | 隧道 | 非ARP设备 |
| realserver network | 私有 | lan/wan | lan |
| realserver numbers | low(10-20) | high | high |
| realserver gateway | director | ownrouter | own router |
| client connnects to | VIP | VIP | VIP |

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
IP隧道（IP封装）是一种将IP数据报封装在IP数据报中的技术，它允许将发往一个IP地址的数据报包装并重定向到另一个IP地址。IP封装现在通常用于外联网，移动IP，IP多播，隧道主机或网络。
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

|  | |
| :------: | :------: |
| DGW | 公网IP的默认网关  |
| VIP | 客户端访问的公网IP，Director的虚拟IP |
| DIP | Director的真实IP |
| RIP | realserver的IP |
| RIP | 客户端IP |-

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

LVS+Keepalived解决方案示例

使用keepalived构建一个具有两个负载平衡器和三个Web服务器的高可用性VS/NAT Web集群，拓扑图如下：
![topology](http://linux-vs.org/docs/ha/keepalived.jpg)
在本例中，虚拟IP地址和网关IP地址分别为10.23.8.80和172.18.1.254，它们在两个负载均衡器（LD1和LD2）之间浮动，三个realserver的IP地址为172.18.1.11,172.18.1.12和172.18.1.13。

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

