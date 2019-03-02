# iptables

iptables一共有四张表和五条链,其中表是按照对数据包的操作区分的，链是按照不同的Hook点来区分的

- 表：
  - raw 负责连接跟踪
  - mangle 负责包处理
  - nat nat功能(端口映射，地址映射等）
  - filter 负责包过滤

- 链名规则：
  - PREROUTING 数据包作路由表之前(DNAT)
  - INPUT 接收数据包
  - FORWARD 在内核空间中，从一个网络接口进入，到另一个网络接口去。转发过滤
  - OUTPUT 输出数据包
  - POSTROUTING 数据包作路由选择之后(SNAT)

- 动作：
  - ACCEPT：接收数据包。
  - DROP：丢弃数据包。
  - REDIRECT：重定向、映射、透明代理。
  - SNAT：源地址转换。
  - DNAT：目标地址转换。
  - MASQUERADE：IP伪装（NAT），用于ADSL。
  - LOG：日志记录。

iptables架构流程图

-->PREROUTING-->[ROUTE]-->FORWARD-->POSTROUTING-->
     mangle        |       mangle        ^ mangle
     nat           |       filter        | nat
                   |                     |
                   |                     |
                   v                     |
                 INPUT                 OUTPUT
                   | mangle              ^ mangle
                   | filter              | nat
                   v -------->local----->| filter

补充：`-A` 代表追加，`-I` 代表插入，`-D` 代表删掉，`-R` 代表替换，`-L` 代表列表，`-p` 代表协议，`-s` 代表源地址，必须是 IP ，`-j` 代表动作，`-d` 代表目标地址，`-i` 网卡进入是数据，`-o` 代表网卡输出的数据。

iptables注意事项：
专表专用：filter、nat、mangle；注意数据包走向：PREROUTING、INPUT、FORWARD、OUTPUT、POSTROUTING
所有链名必须大写，所有表名必须小写，所有动作必须大写，所有匹配必须小写

```sh
iptables -I <链名> <规则号码> #插入规则

备注：
-t filter可以不写，默认即为filter表
-I 链名 规则号码，如果不写规则号码，则默认是1
确保规则号码≤(已有规则数+1)，否则报错

iptables -D <链名> <规则号码或具体规则内容> # 删除规则

备注：
若规则列表有多条相同规则，则按内容匹配只删除序号最小一条
按号码匹配删除时，确保规则号码≤已有规则数，否则报错
按内容匹配删除时，确保规则存在，否则报错

iptables -R <链名> <规则号码> <具体规则内容>  # 替换规则

备注：
确保规则号码≤已有规则数，否则报错

iptables -P <链名> <动作> # 设置链的默认规则

备注：
当数据包没有被规则列表的任何规则相匹配，则按默认规则处理，动作前不能加-j，也是唯一匹配动作前不加-j选择的情况

iptables -F <链名>  # (FLUSH)清空规则

备注：
-F仅仅清空链中规则，不影响-P设置的默认规则
-P设置了DROP后，使用-F要格外注意
若不写链名，默认清空表中所有链的规则

iptables -L   # 列出规则，v:显示每条规则的匹配包数量和匹配字节数；x:在v选项基础上禁止自动单位换算(K、M);n:只显示IP地址和端口，不显示域名和服务名称

匹配条件如下：

# 按照网络接口匹配
-i <匹配数据进入的网络接口>   # -i eth0
-o <匹配数据流出的网络接口>   # -o eth0

# 按来源目的地址匹配
-s <匹配来源地址>   # 可以是IP、NET、DOMAIN，也可为空
-d <匹配目的地址>   # 可以是IP、NET、DOMAIN，可以为空

# 按协议类型匹配
-p <匹配协议类型>   # 可以是TCP、UDP、ICMP等

# 按来源目的端口匹配，--sport和--dport必须配合-p参数使用，且必须指明协议类型
--sport <匹配源端口>  # 可以是个别端口或端口范围
--dport <匹配目的端口>  # 可以是个别端口或端口范围

iptables具体<动作>详解：ACCEPT、DROP、SNAT、DNAT、MASQUERADE
-j ACCEPT   # 允许数据包通过本链(类似Cisco中ACL里的permit)
-j DROP     # 阻止数据包通过本链而丢弃它(类似Cisco中ACL里的deny)
-j DNAT --to IP[-IP][:端口-端口](nat表的PREROUTING链)   # 目的地址转换，DNAT支持转换为单IP，也支持转换到IP地址池(一组连续的IP地址)
-j SNAT --to IP[-IP][:端口-端口]（nat表的POSTROUTING 链）# 源地址转换，SNAT 支持转换为单IP，也支持转换到IP 地址池(一组连续的IP 地址)
-j MASQUERADE   # 动态源地址转换(动态IP的情况下使用)

# 附加模块
按包状态匹配----(state)
按来源MAC 匹配--(mac)
按包速率匹配----(limit)
多端口匹配------(multiport)

# state
-m state --state 状态   # 状态：NEW、RELATED、ESTABLISHED、INVALID
NEW：有别于tcp的syn
ESTABLISHED：连接态
RELATED：衍生态，与conntrack 关联（FTP）
INVALID：不能被识别属于哪个连接或没有任何状态

# mac
-m mac --mac-source MAC   # 匹配某个MAC 地址
注意：报文经过路由后，数据包中原有的mac 信息会被替换，所以在路由后的iptables 中使用mac 模块是没有意义的

# limit
-m limit --limit 匹配速率[--burst 缓冲数量]   # 用一定速率去匹配数据包
注意：limit 英语上看是限制的意思，但实际上只是按一定速率去匹配而已，要想限制的话后面要再跟一条DROP

# multiport
-m multiport <--sports|--dports|--ports> 端口1[,端口2,..,端口n]    # 一次性匹配多个端口，可以区分源端口，目的端口或不指定端口
注意：必须与-p 参数一起使用
```

## 单服务器防护

```sh
# 思路：确定对外服务对象，对网络接口处理，状态检测处理，协议和端口处理。例如：普通WEB服务器
iptables -A INPUT -i lo -j ACCEPT   # 开放本地访问
iptables -A INPUT -p tcp -m multiport --dports 22,80,8080 -j ACCEPT   # 开放22,80,8080tcp端口
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT  # 开放状态为RELATED,ESTABLISHED的
iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -P INPUT DROP  # 关掉全部input链
# 注意：确保规则顺序正确，弄清逻辑关系，学会时刻使用-vnL
```

## 关于网关

```sh
# 思路：搞清网络拓扑，本机上网，设置nat启用路由转发，地址伪装SNAT/MASQUERADE
# ADSL拨号上网的拓扑实例如下
echo "1" > /proc/sys/net/ipv4/ip_forward    # 开启转发
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o eth0 -j MASQUERADE
```

## 限制内网用户

```sh
# 思路：过滤位置filter表FORWARD链，匹配条件-s -d -p --s/dport;处理动作ACCEPT DROP
iptables -A FORWARD -s 192.168.0.3 -j DROP
iptables -A FORWARD -m mac --mac-source 11:22:33:44:55:66 -j DROP
iptables -A FORWARD -d www.baidu.com -j DROP
```

## 内网做对外服务器

```sh
# 思路：服务协议(TCP/UDP)；对外服务端口，内部服务器私网IP；内部真正服务端口
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j DNAT --to 192.168.1.1
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 81 -j DNAT --to 192.168.1.2:80
```

```sh
iptables -I INPUT -s ip -j DROP   #屏蔽指定 ip 访问
iptables -A INPUT -p tcp --dport 2222 -j ACCEPT    #允许 2222 端口访问
iptables -A INPUT -p icmp --icmp-type 8 -s 0/0 -j DROP #屏蔽 icmp 协议，即关闭 ping 服务
iptables -L -n --line-numbers #查看已添加的规则并以序号显示
iptables -D INPUT 8 #删除序号为 8 的规则

# ip_conntrack: table full, dropping packet问题处理
iptables -t raw -A PREROUTING -d 1.2.3.4 -p tcp --dport 80 -j NOTRACK
iptables -A FORWARD -m state --state UNTRACKED -j ACCEPT
```
