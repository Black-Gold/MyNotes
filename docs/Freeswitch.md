<h1 style=text-align:center>Freeswitch</h1>

学习freeswitch需要掌握的内容：c/c++编程, socket编程 ，sip ,sdp，rtp ,tcp/ip 协议，XML，脚本语言JavaScript，lua，erlang,perl。数据库sqlite，mysql。系统编程知识：多进程线程同步（临界区，互斥量，信号灯，事件），APR，模块动态共享机制。

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

