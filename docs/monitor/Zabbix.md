# Zabbix

```markdown
zabbix几个主要功能组件如下：
zabbix server：是其核心组件，agent 向其报告可用性、系统完整性信息和统计信息。server也是存储所有配置信息、统计信息和操作信息的核心存储库
数据库：zabbix采集到的数据都存储在数据库
web界面：该界面属于zabbix server的一部分，通常(不一定)和zabbix server在一台机器上部署
zabbix proxy：可以代理zabbix server采集性能和可用性数据，proxy可以分担zabbix server的负载
zabbix agent：部署在被监控目标中，用于主动监控本地资源和应用程序，并将收集的数据发送给zabbix server或proxy

了解下 Zabbix 内部的数据流对Zabbix的使用也很重要。首先，为了创建一个采集数据的监控项，您就必须先创建主机。其次，在任务的另外一端，必须要有监控项才能创建触发器（trigger），必须要有触发器来创建动作（action）。因此，如果您想要收到类似“X个server上CPU负载过高”这样的告警，您必须首先为 Server X 创建一个主机条目，其次创建一个用于监控其 CPU的监控项，最后创建一个触发器，用来触发 CPU负载过高这个动作，并将其发送到您的邮箱里。虽然这些步骤看起来很繁琐，但是使用模板的话，实际操作非常简单。也正是由于这种设计，使得 Zabbix 的配置变得更加灵活易用


为了避免耗尽内存， 当Zabbix server使用 Zabbix protocol 协议时一次连接只接受128M
被动检查是一个简单的数据请求。Zabbix服务器或proxy请求一些数据(例如，CPU负载)，Zabbix agent将结果发送回服务器

主动检查需要更复杂的处理，agent 必须首先从server端检索独立处理监控项的列表
```
