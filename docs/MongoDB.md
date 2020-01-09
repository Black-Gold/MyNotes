# MongoDB-4.0.10

## 慢查询分析

```javascript
show profile; # 显示最后几条慢查询

// sort by natural order (time in)
db.system.profile.find({}).sort({$natural:-1})

// sort by slow queries first
db.system.profile.find({}).sort({$millis:-1}).limit(10);

// anything > 20ms
db.system.profile.find({"millis":{$gt:20}})

// single coll order by response time
db.system.profile.find({"ns":"test.foo"}).sort({"millis":-1})

// regular expression on namespace
db.system.profile.find( { "ns": /test.foo/ } ).sort({millis:-1,$ts:-1})

// anything thats moved
db.system.profile.find({"moved":true})

// large scans
db.system.profile.find({"nscanned":{$gt:10000}})

// anything doing range or full scans
db.system.profile.find({"nreturned":{$gt:1}})


// using the agg framework
// response time by operation type
db.system.profile.aggregate(
{ $group : {
   _id :"$op",
   count:{$sum:1},
   "max response time":{$max:"$millis"},
   "avg response time":{$avg:"$millis"}
}});

// slowest by namespace
db.system.profile.aggregate(
{ $group : {
  _id :"$ns",
  count:{$sum:1},
  "max response time":{$max:"$millis"},
  "avg response time":{$avg:"$millis"}
}},
{$sort: {
 "max response time":-1}
});


// slowest by client
db.system.profile.aggregate(
{$group : {
  _id :"$client",
  count:{$sum:1},
  "max response time":{$max:"$millis"},
  "avg response time":{$avg:"$millis"}
}},
{$sort: {
  "max response time":-1}
});

// summary moved vs non-moved
db.system.profile.aggregate(
 { $group : {
   _id :"$moved",
   count:{$sum:1},
   "max response time":{$max:"$millis"},
   "avg response time":{$avg:"$millis"}
 }});

db.system.profile.aggregate([
  { $project : {
      "op" : "$op",
      "millis" : "$millis",
      "timeAcquiringMicrosrMS" : { $divide : [ "$lockStats.timeAcquiringMicros.r", 1000 ] },
      "timeAcquiringMicroswMS" : { $divide : [ "$lockStats.timeAcquiringMicros.w", 1000 ] },
      "timeLockedMicrosrMS" : { $divide : [ "$lockStats.timeLockedMicros.r", 1000 ] },
      "timeLockedMicroswMS" : { $divide : [ "$lockStats.timeLockedMicros.w", 1000 ] } }
  },
  { $project : {
      "op" : "$op",
      "millis" : "$millis",
      "total_time" : { $add : [ "$millis", "$timeAcquiringMicrosrMS", "$timeAcquiringMicroswMS" ] },
      "timeAcquiringMicrosrMS" : "$timeAcquiringMicrosrMS",
      "timeAcquiringMicroswMS" : "$timeAcquiringMicroswMS",
      "timeLockedMicrosrMS" : "$timeLockedMicrosrMS",
      "timeLockedMicroswMS" : "$timeLockedMicroswMS" }
  },
  { $group : {
      _id : "$op",
      "average response time" : { $avg : "$millis" },
      "average response time + acquire time": { $avg: "$total_time"},
      "average acquire time reads" : { $avg : "$timeAcquiringMicrosrMS" },
      "average acquire time writes" : { $avg : "$timeAcquiringMicroswMS" },
      "average lock time reads" : { $avg : "$timeLockedMicrosrMS" },
      "average lock time writes" : { $avg : "$timeLockedMicroswMS" } }
  }
]);
```

## 基础

views是只读的且无法重新命名，对视图执行写操作会报错，视图支持的读操作有：

```javascript
db.collection.find();
db.collection.findOne();
db.collection.aggregate();
db.collection.countDocuments();
db.collection.estimatedDocumentCount();
db.collection.count();
db.collection.distinct();
```

视图使用基础集合的索引。由于索引位于基础集合上，因此无法直接在视图上创建，删除或重新构建索引，也无法在视图上获取索引列表。无法在视图使用
$natural排序,如果视图是基于分片创建，则视图也被视为分片，无法为from字段$lookup和$graphlookup操作指定分片视图

创建视图是可以指定排序规则，默认规则是“简单”二进制比较排序规则，多个视图做聚合操作，排序规则必须相同

find()对视图的操作不支持以下Projection操作符：

* $
* $elemMatch
* $slice
* $meta

### Capped Collections

Capped Collections是固定大小的集合，支持基于插入顺序插入和检索文档的高吞吐量操作。以类似于循环缓冲区的方式工作：一旦集合填充其分配的空间，
它通过自动删除集合中最旧的文档为新文档腾出空间

无法从capped集合删除文档，要从集合删除文档的方法，使用drop()方法删除集合并重新创建，capped集合无法分片。创建capped集合，必须以字节为单位
指定集合的最大大小。如果size字段小于或等于4096，则集合的上限为4096字节。否则，MongoDB将提高提供的大小，使其成为256的整数倍；还可以使用
该max字段为集合指定最大文档数。该size参数总是必需的，即使你指定max的文件数量。如果集合在达到最大文档计数之前达到最大大小限制，MongoDB将
删除旧文档

将集合转换为capped集合:db.runCommand({"convertToCapped": "collectionName", size: 10000});在操作期间保存数据库独占锁。锁定同一
数据库的其他操作将被阻止，直到操作完成

capped替代方案：使用TTL(生存时间)索引从集合删除过期数据；这些索引允许您根据日期类型字段的值和索引的TTL值使正常集合中的数据到期和删除。
TTL索引与capped集合不兼容

每种BSON类型都有整数和字符串标识符，如下表所示
| Type | Number | Alias | Notes |
| :------: | :------: | :------: | :------: |
| Double | 1 | “double” |
| String | 2 | “string” |
| Object | 3 | “object” |
| Array | 4 | “array” |
| Binary data | 5 | “binData” |
| Undefined | 6 | “undefined” | 已废弃 |
| ObjectId | 7 | “objectId” |
| Boolean | 8 | “bool” |
| Date | 9 | “date” |
| Null | 10 | “null” |
| Regular Expression | 11 | “regex” |
| DBPointer | 12 | “dbPointer” | 已废弃 |
| JavaScript | 13 | “javascript” |
| Symbol | 14 | “symbol” | 已废弃 |
| JavaScript (with scope) | 15 | “javascriptWithScope” |
| 32-bit integer | 16 | “int” |
| Timestamp | 17 | “timestamp” |
| 64-bit integer | 18 | “long” |
| Decimal128 | 19 | “decimal” | New in version 3.4. |
| Min key | -1 | “minKey” |
| Max key | 127 | “maxKey” |

ObjectId值由12个字节组成：

* 一个4字节的值，表示Unix纪元以来的秒数
* 一个5字节的随机值
* 一个3字节的计数器，以随机值开始

比较会将不存在的字段视为空的BSON对象，比较不同类型的值时，MongoDB使用以下顺序；由低到高：

1. MinKey (internal type)
2. Null
3. Numbers (ints, longs, doubles, decimals)
4. Symbol, String
5. Object
6. Array
7. BinData
8. ObjectId
9. Boolean
10. Date
11. Timestamp
12. Regular Expression
13. MaxKey (internal type)

Object比较使用以下顺序：

1. 按照它们在BSON对象中出现的顺序递归地比较键值对
2. 比较关键字段名称
3. 如果键字段名称相等，则比较字段值
4. 如果字段值相等，则比较下一个键/值对（返回步骤1）。没有进一步对的对象小于具有更多对的对象

BinData排序顺序为：

1. 首先，数据的长度或大小
2. 然后，通过BSON一字节子类型
3. 最后，通过数据，执行逐字节比较

## 配置文件参考

```conf
  # 配置日志
    systemLog:
      verbosity: 5  #日志级别:范围是0-5,0是MongoDB的默认日志级别，5包含了debug信息
      quiet: false  #安静模式，是否输出日志
      traceAllExceptions: true  #是否打印详细的日志信息，开启
    #  syslogFacility: <string> #将日志输出到系统日志中，如果使用该功能，则systemLog.destination=syslog.
    #   path: <string>
    #   logAppend: <boolean>
    #   logRotate: <string>
    #   destination: <string>
    #   timeStampFormat: <string>
    #   component:
    #      accessControl:
    #         verbosity: <int>
    #      command:
    #         verbosity: <int>
      destination: file #mogodb日志的产生方式:file=指定的文件,syslog系统日志，如果不指定，则指向标准输出流
      logAppend: true   #在mongodb重启时,继续往已有的日志文件中追加内容，而不是新建一个文件.
      path: /data/mongodb/mongod.log    #日志文件的输出路径，mongo将在指定的位置创建日志文件.
      logRotate: reopen   #3.0.0,两个值:rename=重命名日志文件，reopen=关闭并重新打开日志文件.
                          #如果使用reopen则要使systemLog.logAppend=true
      timeStampFormat: iso8601-utc #日志中时间戳的格式:ctime=Wed Dec 31 18:17:54.811,iso8601-utc=1970-01-01T00:00:00.000Z,
                                   #iso8601-local=本地时间格式
    #日志组件的具体选项
      component:
       #索引操作相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
        index:
          verbosity: 5
        #查询操作相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
        query:
          verbosity: 5
        #复制操作相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
        replication:
          verbosity: 5
          #心跳相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.6
          #heartbeats:
            #verbosity: 5
          #回滚相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.6
          #heartbeats:
            #verbosity: 5
         #写操作相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
        write:
          verbosity: 5
        #访问控制相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
        accessControl:
          verbosity: 5
        #命令相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
        command:
          verbosity: 5
        #控制操作相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
        control:
          verbosity: 5
        #诊断消息相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.2
        ftdc:
          verbosity: 5
        #地理空间解析相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
        geo:
          verbosity: 5
        #网络操作相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
        network:
          verbosity: 5
        #分片相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
        sharding:
          verbosity: 5
        #存储相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
        storage:
          verbosity: 5
          #日志组件相关的日志消息级别，分为0-5，0表示info级别的日志，依次增加，5表示包含debug信息.version:3.0
          journal:
            verbosity: 5

    # 进程管理配置
    processManagement:
      fork: true  # 以守护线程模式在后台运行
      pidFilePath: /data/mongodb/mongod.pid  # 指定保存PID的文件
      #时区文件:https://downloads.mongodb.org/olson_tz_db/timezonedb-latest.zip
      #timeZoneInfo:    #加载数据库时区的完整路径，如果不指定，则采用默认时区

    # 网络配置
    net:
       port: 27017   #mongodb监听的TCP端口
       bindIp: 0.0.0.0  #允许访问的IP地址集合，不设置默认是127.0.0.1，如果需要设置多个路径可以通过[,]分割，例如:A,B,C
       #bindIpAll: true  #是否绑定所有的IP，允许访问. version:3.6
       maxIncomingConnections: 65536 #最大连接数，默认65536
       wireObjectCheck: true    #验证客户端的所有请求，防止客户端插入恶意或者无效的BSON数据，对于高度嵌套的自问当对象，性能影响很小.
       ipv6: false   #启用或禁用ipv6支持.
       #启用或者禁用在*UNIX套接字进行侦听.
       #unixDomainSocket:
          #enabled: true #启用或者禁用在*UNIX套接字进行侦听.
          #pathPrefix: <string>  #套接字的路径
          #filePermissions: <int>   #套接字文件权限
        #3.6版本删除http选项，改为了ssl.
    #   ssl:
          #sslOnNormalPorts: <boolean>  # 被2.6版本启用，改用mode: requireSSL.
          #启用TSL/SSL:
          #     disabled=不启用TLS/SSL,
          #     allowSSL=服务器之间不使用TLS/SSL,传入链接可以是TLS/SSL或者非TLS/SSl.
          #     preferSSL=服务器之间不使用TLS/SSL,传入链接可以是TLS/SSL或者非TLS/SSl.
          #     requireSSL=服务器仅使用并接受TLS/SSL加密链接.
          #mode: disabled
          #PEMKeyFile: <string> #.pem证书存放的绝对或相对路径.
          #PEMKeyPassword: <string> #用于解密PEM的密钥文件.
          #clusterFile: <string>    #集群或副本集内部身份验证的PEM文件.
          #clusterPassword: <string> #集群或副本集用于解密内部验证PEM文件的密钥.
          #CAFile: <string> #.pem包含证书颁发机构跟证书链的文件的相对或绝对路径.
          #CRLFile: <string>    #.pem包含证书吊销列表的文件的相对或绝对路径.
          #allowConnectionsWithoutCertificates: false   #弱证书验证
          #allowInvalidCertificates: <boolean>  #允许使用无效证书，关闭证书检查
          #allowInvalidHostnames: <boolean> #允许使用无效的主机名，禁用主机名校验
          #disabledProtocols: <string>  #禁用指定协议
          #FIPSMode: <boolean>  #启用或禁用使用安装OpenSSL库的的FIPS模式
       #zlib压缩器的支持，add to Version:3.4,version:3.6,默认使用snappy.
       #如果各方不共用至少一个通用压缩器，则双方之间的消息是未压缩的.
       #compression:
          #compressors: snappy
       #指定传输层通知方式,默认asio，version:3.6
       #transportLayer: asio
       #指定线程对客户端请求的执行模型：synchronous=使用同步网络并在每个链接的基础上管理其网络线程池.
       #adaptive:使用新的实验性的异步网络模式.
       #其中当值为adaptive，要求#transportLayer的值为asio.
       #serviceExecutor: <string>
    #安全管理
    #security:
       #keyFile: <string>   #存储共享密钥的密钥文件的路径
       #用于集群身份验证的身份验证模式
       #  -  keyFile:使用密钥文件进行身份验证.
       #  - sendKeyFile: 发送密钥文件用于身份验证，可以接受密钥文件和x.509证书.
       #  - sendX509:发送x.509文件进行身份验证，可以接受密钥文件和x.509证书.
       #  - x509:推荐使用，发送x.509证书进行身份验证，且只接受x.509证书.
       #clusterAuthMode: <string>
       #启用或禁用基于角色的控制访问(RBAC)来控制每个用户对数据库资源和操作的访问
       #  -  enabled:用户只能访问被授权的数据库资源和操作.
       #  -  disabled:用户可以访问任何资源和进行任何操作.
       #authorization: <string>
       #transitionToAuth: <boolean> #用户可以在没有任何访问控制检查的情况下访问机器，并进行读写和管理操作. version:3.4
       #javascriptEnabled:  <boolean>    #启用或禁用服务器端javascript的执行.
       #redactClientLogData: <boolean>   #记录日志的时候，将数据也记录下来. version:3.4 AND ONLY MongoDB Enterprise
       #配置SASL
       #sasl:
          #hostName: <string>   #用于配置SASL和Kerberos身份验证的完全限定服务器域名.
          #serviceName: <string>    #使用SASL的服务的注册名称.
          #saslauthdSocketPath: <string>    #UNIX域套接字文件的路径.
       #enableEncryption: <boolean> #为WiredTiger存储引擎启用机密.
       #encryptionCipherMode: <string>  #静态加密的密码模式:AES256-CBC=密码块链接模式下的256位高级加密标准,
       #AES256-GCM=Galois / Counter模式下的256位高级加密标准
       #encryptionKeyFile: <string> #通过KMIP 以外的进程管理密钥时，本地密钥文件的路径[MongoDB Enterprise]
       #
       #注意:security.kmip只能用于mongodb的企业版本,且版本需要高于3.2.
       #
       #kmip:
          #keyIdentifier: <string>   #KMIP服务器中现有秘钥的唯一KNIP标识符.version:3.2
          #rotateMasterKey: <boolean>   #旋转主秘钥并重新加密内部秘钥库.version:3.2[MongoDB Enterprise]
          #serverName: <string> #运行KMIP服务器的密钥管理解决方案的主机名或IP地址.[MongoDB Enterprise]
          #port: <string> #KMIP服务器正在监听的端口号
          #clientCertificateFile: <string>  #包含用于向KMIP服务器验证MongoDB的客户端证书路径的字符串
          #clientCertificatePassword: <string>  #用于解密客户端证书（即security.kmip.clientCertificateFile）的密码
          #serverCAFile: <string> #CA文件的路径。用于验证与KMIP服务器的安全客户端连接
          #
          #注意:security.ldap只能用于mongodb的企业版本,且版本需要高于3.4.
          #
    #   ldap:
    #      servers: <string>  #通过LDAP确定执行操作的用户是否被授权，多个服务器之间可以通过[,]分割.
    #      bind:
    #         method: <string>  #该方法mongod或mongos用于向LDAP服务器进行身份验证
    #                                - simple- mongod或mongos使用简单的身份验证
    #                                - sasl- mongod或mongos使用SASL协议进行身份验证
    #         saslMechanisms: <string>  #以逗号分隔的SASL机制列表.
    #         queryUser: <string> #绑定LADP查询用户.
    #         queryPassword: <string> #绑定LADP服务器的密码.
    #         useOSDefaults: <boolean>  #连接到LDAP服务器时，允许mongod或mongos使用Windows登录凭据进行身份验证或绑定
    #      transportSecurity: <string>  #默认情况下，mongod或mongos创建一个到LDAP服务器的TLS / SSL安全连接.
    #      timeoutMS: <int> #等待LDAP服务器响应请求时间(毫秒).
    #      userToDNMapping: <string>  #用于验证的用户名映射到LDAP专有名称（DN）
    #                                  -  match=ECMAScript格式的正则表达式（正则表达式）与提供的用户名匹配
    #                                          每个括号包围的部分表示由substitutionor 使用的正则表达式捕获组ldapQuery
    #                                  - substitution=LDAP专有名称（DN）格式化模板
    #                                  - ldapQuery=LDAP查询格式化模板.
    #      authz:
    #         queryTemplate: <string>   #一个相对的LDAP查询URL.
    #
    # 属性设置选项
    #
    #setParameter:
    #     ldapUserCacheInvalidationInterval: <int> #用于使用LDAP授权mongod或mongos使用LDAP授权的服务器(秒)
    #   <parameter1>: <value1>
    #   <parameter2>: <value2>

    # 存储设置
    storage:
       dbPath:  /data/mongodb #mongod实例存储数据的目录,默认值：/data/db.
       indexBuildRetry: true #指定mongodb在下次启动时是否重建不完整的索引,默认:true.
    #   repairPath: <string>
    #          MongoDB在--repair操作过程中将使用的工作目录
    #          当--repair完成后,storage.repairPath目录是空的,并且 dbPath包含了修复的文件
    #          该storage.repairPath设置仅适用于mongod
    #          仅适用于mongod使用MMAPv1存储引擎的实例
       journal:
          enabled: true #启用或禁用耐久性日志以确保数据文件保持有效并可恢复。64bit默认开启,32bit默认关闭.
    #                     不适用于mongod使用内存存储引擎的实例
          commitIntervalMs: 100 #mongod日志操作之间进程允许的最大时间量(毫秒),范围是1-500,默认值是100
    #                                  该值太低会增加日志的持久性，但是会牺牲磁盘性能.
    #                                  该storage.journal.commitIntervalMs设置仅适用于mongod
    #                                  不适用于mongod使用内存存储引擎的实例
    #   directoryPerDB: <boolean>    #MongoDB使用单独的目录来存储每个数据库的数据
    #                                  目录位于storage.dbPath目录下，并且每个子目录名称都与数据库名称相对应
    #                                  3.0版本有变更.
    #   syncPeriodSecs: <int>  #在MongoDB将数据通过sync操作刷新到数据文件之前可以传递的时间量,默认60s.
    #                              不要在生产系统上设置此值。在几乎所有情况下，您都应该使用默认设置
    #   engine: <string>  #mongod数据库的存储引擎
    #                         仅在MongoDB Enterprise中可用，3.0版本中的新功能,从MongoDB 3.2开始，wiredTiger是默认值
    #                         mmapv1=指定MMAPv1存储引擎
    #                         wiredTiger=指定WiredTiger存储引擎
    #                         inMemory=指定内存中存储引擎
       mmapv1:
          preallocDataFiles: true  #启用或禁用数据文件的预分配。默认情况下，MongoDB不预先分配数据文件
          nsSize: 16 #命名空间文件的默认大小，它是以文件结尾的文件.ns。每个集合和索引都被视为一个名称空间
    #                        使用此设置控制新创建的名称空间文件的大小。该选项对现有文件没有影响
    #                        命名空间文件的最大大小是2047兆字节。默认值16兆字节提供了大约24,000个名称空间
          quota:
             enforced: false  #启用或禁用强制每个数据库可能具有的数字数据文件的最大限制
    #                                 使用该storage.mmapv1.quota.enforced选项运行时，MongoDB每个数据库最多有8个数据文件
    #                                 通过调整storage.quota.maxFilesPerDB来调整这个限制
             maxFilesPerDB: 8 #每个数据库的数据文件数量限制，默认是8.该设置仅适用于设置仅适用于mongod.
          smallFiles: false #如果你有大量的数据库，每个保存少量数据。MongoDB使用较小的默认文件大小
    #                         该storage.mmapv1.smallFiles选项可减少数据文件的初始大小，并将最大大小限制为512兆字节
    #                         storage.mmapv1.smallFiles也可以将每个日志文件的大小从1千兆字节减少到128兆字节
    #      journal:
    #         debugFlags: <int>   #提供测试功能。不用于一般用途，并且会在系统异常关闭的情况下影响数据文件的完整性
    #         commitIntervalMs: <num> #MongoDB 3.2弃用该 torage.mmapv1.journal.commitIntervalMs设置
    #                                    改为使用storage.journal.commitIntervalMs
    #   wiredTiger:
    #      engineConfig:
    #         cacheSizeGB: <number>    #WiredTiger将用于所有数据的内部缓存的最大大小,默认采用RAB-1GB的50%和256MB的最大值.
    #                                      尽可能不要超过最大值.
    #         journalCompressor: <string>  #用于压缩WiredTiger日志数据的压缩类型。none,snappy,zlib.version:3.0
    #         directoryForIndexes: true #mongod将索引存储在名为的子目录中 index,
    #                                        并将集合数据存储在名为的子目录中 collection
    #      collectionConfig:
    #         blockCompressor: <string>  #用于压缩收集数据的默认压缩类型。none,snappy,zlib.version:3.0
          indexConfig:
             prefixCompression: true #启用或禁用索引数据的前缀压缩
    #   inMemory:    #仅在MongoDB Enterprise中可用
    #      engineConfig:
    #         inMemorySizeGB: <number> #为内存存储引擎数据分配的最大内存量
    #                                      包括索引，oplog（如果 mongod是副本集，副本集或分片群集元数据的一部分）等
    #                                       默认情况下，内存存储引擎使用50％的物理RAM减1 GB
    #运行分析配置
    operationProfiling:
       mode: slowOp   #指定应该分析哪些操作，分析操作可能会影响性能.
    #                        - off=分析器已关闭，并且不收集任何数据。这是默认的分析器级别
    #                        - slowOp=分析器收集比花费时间更长的操作的数据slowms
    #                        - all=分析器收集所有操作的数据
       slowOpThresholdMs: 100 #慢操作时间阈值，默认100.
    #   slowOpSampleRate: <double> #慢操作采样率，0-1之间，包含0,1.
    #
    #复制设置
    #
    #replication:
    #   oplogSizeMB: <int> #复制操作日志的最大大小（MB）
    #   replSetName: <string>  #mongod一部分的副本集的名称。副本集中的所有主机必须具有相同的集名称。仅适用于mongod.
    #   secondaryIndexPrefetch: <string>  #仅适用于mmapv1 存储引擎.
    #                                        在应用oplog中的操作之前，副本集的次要成员加载到内存中的索引
    #                                       - none=辅助程序不会将索引加载到内存中
    #                                       - all=辅助程序加载与操作相关的所有索引
    #                                       - _id_only=Secondaries不会将额外的索引加载到已有_id索引之外的内存中
    #   enableMajorityReadConcern: <boolean>  ## Deprecated in 3.6
    #                                            从MongoDB 3.6开始，"majority"始终启用读取关注，并且此设置不起作用
    #    localPingThresholdMs: <int>  #mongos用于确定哪些辅助副本集成员从客户端传递读取操作的ping时间（以毫秒为单位）
    #                                    默认值15对应于所有客户端驱动程序中的默认值
    #
    # 分片设置
    #
    #sharding:
    #   clusterRole: <string>    #mongod实例在分片群集中扮演的角色
    #                           - configsvr=将此实例作为配置服务器启动。默认情况下，该实例在27019端口上启动
    #                           - shardsvr=启动此实例为碎片。默认情况下，该实例在27018端口上启动
    #                            设置sharding.clusterRole要求mongod 实例与复制一起运行
    #                            要将实例部署为副本集成员，请使用该replSetName 设置并指定副本集的名称
    #   archiveMovedChunks: false  #从3.2开始，MongoDB false用作默认值。在块迁移期间，分片不会保存从分片迁移的文档
    #   configDB: <configReplSetName>/cfg1.example.net:27017, cfg2.example.net:27017,...   #该配置服务器用于 分片群集
    #
    # 审计日志设置,仅在MongoDB Enterprise中可用
    #
    #auditLog:
    #   destination: file  #设置后，auditLog.destination启用使用指定格式往指定目标发送所有审核事件
    ##                             - syslog=以JSON格式输出审计事件到系统日志.
    ##                             - console=以stdoutJSON格式输出审计事件
    ##                             - file=将审核事件输出到以中指定auditLog.path格式auditLog.format指定的文件
    #   format: JSON   #用于审计的输出文件的格式,当auditLog.destination=file时,可以指定该值(JSON/BSON).
    #   path: <string> #输出文件的绝对或相对路径,当auditLog.destination=file时,可以指定该值.
    #   filter: <string> #过滤指定类型的操作的记录
    #
    # 简单网络管理协议配置
    #
    #snmp:
    #   subagent: <boolean>  #该snmp.subagent设置仅适用于mongod
    #                        如果snmp.subagent是true，SNMP运行的子代理
    #   master: <boolean>    #当snmp.master是true，SNMP运行作为一个主站。·
    #
    # 文本搜索选项 ,仅在MongoDB Enterprise中可用
    #
    #basisTech:
    #   rootDirectory:<string> #指定Basis Technology Rosette语言学平台安装根目录的路径，以支持用于文本搜索操作的其他语言

```

## mongo shell

常见的mongoshell帮助与JavaScript等效项表

| Shell Helpers | JavaScript Equivalents |
| :------: | :------: |
| show dbs, show databases | db.adminCommand('listDatabases') |
| use <[db]> | db = db.getSiblingDB('<[db]>') |
| show collections | db.getCollectionNames() |
| show users | db.getUsers() |
| show roles | db.getRoles({showBuiltinRoles: true}) |
| show log <[logname]> | db.adminCommand({ 'getLog' : '<[logname]>' }); |
| show logs | db.adminCommand({ 'getLog' : '*' }); |
| it | cursor = db.collection.find(); if ( cursor.hasNext(); );{ cursor.next();; } |

## MongoDB CRUD 操作

### 插入文件

```javascript
db.collection.insertOne();           // 插入一个文档
db.collection.insertMany();          // 插入多个文档
db.collection.insert();              // 插入一个或多个文档
db.collection.update();              // when used with the **upsert: true** option
db.collection.updateOne();           // when used with the **upsert: true** option
db.collection.updateMany();          // when used with the **upsert: true** option
db.collection.findAndModify();       // when used with the **upsert: true** option
db.collection.findOneAndUpdate();    // when used with the **upsert: true** option
db.collection.findOneAndReplace();   // when used with the **upsert: true** option
db.collection.save();
db.collection.bulkWrite();
```

### 查询文档

```javascript
db.collection.find({person:{age:21,sex:"male"}});  // 查询嵌入/嵌套式文档
db.collection.find({"person.sex":"male"});  // 匹配嵌入式文档某个字段
db.collection.find({tags: ["blue", "red"]});   // 按顺序匹配数组中的两个元素
db.collection.find({tags: {$all:["red","blank"]}});  // 不按顺序匹配数组中两个元素(查询只要包含这两个元素的所有结果)
db.collection.find({age: {$gt: 1, $lt: 2}});   // 集合中age数组中一个元素满足大于1，另一个元素满足小于2，或者单个元素满足两个条件
db.collection.find({age: {$elemMatch: {$gt: 1, $lt: 2}}});   // 使用$elemMatch运算符在数组的元素上指定多个条件，以便至少一个数组元素满足所有指定的条件。此查询条件表示age数组包含至少一个大于1且小于2的元素
db.collection.find( { "tags": { $size: 3 } } );  // 查询条件为tags数组中元素长度为3
db.collection.find( { "tags.2": { "$exists": 1} } ); // 查询条件为tags数组中元素数量大于2(实际利用数组索引来实现的)

// 查询嵌套在数组中的文档示例：
db.inventory.insertMany( [
   { item: "journal", instock: [ { warehouse: "A", qty: 5 }, { warehouse: "C", qty: 15 } ] },
   { item: "notebook", instock: [ { warehouse: "C", qty: 5 } ] },
   { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 15 } ] },
   { item: "planner", instock: [ { warehouse: "A", qty: 40 }, { warehouse: "B", qty: 5 } ] },
   { item: "postcard", instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
]);

db.inventory.find( { "instock": { warehouse: "A", qty: 5 } } );  // 匹配在invertory集合中instock数组元素与指定文档匹配的文档，可以理解为一个文档作为数组的元素；注意：整个嵌入/嵌套文档上的等式匹配需要指定文档的完全匹配
db.inventory.find( { "instock": { $elemMatch: { qty: 5, warehouse: "A" } } } );  // 查询嵌入文档数组instock，嵌入的到此数组的文档至少一个qty值为5，warehost值为A
db.inventory.find( { "instock": { $elemMatch: { qty: { $gt: 10, $lte: 20 } } } } );  // 查询嵌入文档数组instock，嵌入的到此数组的文档qty值大于10且小于等于20
db.inventory.find( { "instock.qty": { $gt: 10,  $lte: 20 } } );  // 查询嵌套在instock数组中的任何文档的qty字段大于10并且数组中的任何文档（但不一定是相同的嵌入文档）的qty字段小于或等于20
db.inventory.find( { "instock.qty": 5, "instock.warehouse": "A" } ); // 查询嵌套在instock数组中的至少一个嵌套文档包含字段qty值为5，且至少一个嵌套文档warehouse字段值为A(但不一定是相同的嵌套文档);
db.inventory.find({"instock.qty":{$lte:20}});  // 查询嵌入到instock数组的文档qty字段值小于20的文档
db.inventory.find({"instock.0.qty":{$lte:20}});  // 查询嵌入到instock数组的文档，索引为0且qty字段值小于20的文档

db.inventory.find( { status: "A" }, { item: 1, status: 1 } );  // 查询集合中status值为A，只返回item、status及_id字段
db.inventory.find( { status: "A" }, { status: 0, instock: 0 } ); // 查询集合中status值为A，结果排除status和instock字段
db.inventory.find( { status: "A" }, { item: 1, status: 1, "size.uom": 1 } ); // 查询集合中status值为A，结果只包括item、status字段及嵌入文档size的uom字段
db.inventory.find( { status: "A" }, { item: 1, status: 1, instock: { $slice: -1 } } );   // 查询集合中status为A，结果只包含item、status字段instock，$slice运算符返回instock数组最后一个元素

db.inventory.find({item: null});  // 查询item字段为null，或不包含item字段的文档
db.inventory.find({item: {$type: 10}});  // 只查询item字段值类型是10(即null类型);的文档
db.inventory.find({item: {$exists: false}}) // 只查询不包含item字段的文档
```

### 更新文档

```javascript
db.inventory.updateOne({ item: "paper" },{$set: { "size.uom": "cm", status: "P" },$currentDate: { lastModified: true }});    // 更新一个文档item字段值为paper，将size数组中uom更新为cm，status更新为P
db.inventory.updateMany({ "qty": { $lt: 50 }},{$set: { "size.uom": "in", status: "Z" },$currentDate: { lastModified: true }});   // 更新qty字段值小于50，更新size数组uom字段值为in，status字段值为Z，用$currentDate操作更新lastModified字段为当前时间，没有此字段
db.collection.update()  // 更新或替换与指定过滤器匹配的单个文档，或更新与指定过滤器匹配的所有文档
// 以下几种方法也可以用来更新文档
db.collection.findOneAndReplace()
db.collection.findOneAndUpdate()
db.collection.findAndModify()
db.collection.save()
db.collection.bulkWrite()
```

### 删除文档

```markdown
db.collection.deleteMany({})  // 删除集合所有文档
db.collection.deleteOne( { status: "D" } )   // 删除status为D的第一个文档
db.collection.remove()  // 删除单个文档或与指定过滤器匹配的所有文档
删除操作不会删除索引，即使从集合中删除所有文档！MongoDB中的所有写入操作都是单个文档级别的原子操作
以下方法也可以从集合中删除文档：
db.collection.findOneAndDelete()  // findOneAndDelete()提供一个排序选项.该选项允许删除按指定顺序排序的第一个文档
db.collection.findAndModify()   // findAndModify()提供一个排序选项.该选项允许删除按指定顺序排序的第一个文档
db.collection.bulkWrite()
```

### 批量写入操作

```markdown
db.collection.bulkWrite() // 此方法可以批量插入、更新、删除操作。批量写操作可以有序也可无序
// 通过有序的操作列表，MongoDB以串行方式执行操作。如果在处理其中一个写操作期间发生错误，MongoDB将返回而不处理列表中的任何剩余写操作
// 使用无序的操作列表，MongoDB可以并行执行操作，但不保证这种行为。如果在处理其中一个写操作期间发生错误，MongoDB将继续处理列表中的剩余写操作
// 分片集合上，有序操作通常比无序要慢，bulk.Write()默认是有序操作
bulkWrite()支持以下写操作：
insertOne
updateOne
updateMany
replaceOne
deleteOne
deleteMany

批量插入到分片集合的策略：
1. 预分割集合
2. 无序写入mongos
3. 避免单调节流--Avoid Monotonic Throttling(此处存疑)
```

### 可重试的写入

```markdown
可重试写入要具备以下条件：
1. 需要副本集或分片集群
2. 支持文档锁定的存储引擎，例如：WiredTiger或inmemory内存存储引擎
3. MongoDB 3.6+ 版本驱动程序
4. 集群中MongoDB版本大于等于3.6
5. 写操作出错，Write Concern为0的不重试

transaction commit和abort operations  如果提交操作或中止操作遇到错误，MongoDB驱动程序会重试该操作一次，无论是否retryWrites设置为true
事务内的写操作是不能单独重试写入的，无论retryWrites是否设置为true；默认情况不启动重试写入


以下为可重试写入操作(Write Concern不能是{w: 0})：

// 插入操作
db.collection.insertOne()
db.collection.insert()
db.collection.insertMany()

// 单文档更新操作
db.collection.updateOne()
db.collection.replaceOne()
db.collection.save()
db.collection.update()这里multi是false

// 单个文档删除操作
db.collection.deleteOne()
db.collection.remove()这里justOne是true

// findAndModify操作。所有findAndModify操作都是单文档操作
db.collection.findAndModify()
db.collection.findOneAndDelete()
db.collection.findOneAndReplace()
db.collection.findOneAndUpdate()

// 批量写入操作仅包含单文档写入操作。可重试的批量操作可以包括指定的写入操作的任何组合，但不能包括任何多文档写入操作，例如updateMany
db.collection.bulkWrite() 使用以下写操作:
insertOne
updateOne
replaceOne
deleteOne

// 仅包含单文档的批量写入操作，一个可重试批量写入操作可以包含任何指定写入操作组合，但不能包括任何多文档的写入操作，例如update指定multi选项为true
Bulk 运作：批量写入操作仅包含单文档写入操作。可重试的批量操作可以包括指定的写入操作的任何组合，但不能包括任何多文档写入操作，例如为该选项update指定的操作。truemulti
Bulk.find.removeOne()
Bulk.find.replaceOne()
Bulk.find.replaceOne()

可重试写入有助于解决瞬时网络错误和副本集选举，并不能解决持久性网络错误，因为只进行一次重试尝试
如果驱动程序在目标副本集或分片集群分片中找不到正常的主节点，则驱动程序会在serverSelectionTimeoutMS在重试之前等待以确定新的主节点，可重试写入不能解决故障转移期间，时间超过serverSelectionTimeoutMS的实例

注意：如果客户端发出写入操作在超过serverSelectionTimeoutMS没有响应，有可能在客户端重新响应时(没有重新启动)，则可能会再重试写入操作

db.serverStatus()包含有关该transactions部分中可重试写入的统计信息
```

### 文本搜索

示例文档如下所示：
| _id | description | name |
| :------: | :------: | :------: |
| _id | description | name |
| 1 | Coffee and cakes | Java Hut |
| 2 | Gourmet hamburgers | Burger Buns |
| 3 | Just coffee | Coffee Shop |
| 4 | Discount clothing | Clothes Clothes Clothes |
| 5 | Indonesian goods | Java Shopping |

```markdown
// 视图不支持文本搜索；文本索引以支持对字符串内容的文本搜索查询。text索引可以包括其值为字符串或字符串元素数组的任何字段
// 要执行文本搜索查询，您必须text在集合上有索引。集合只能有一个文本搜索索引，但该索引可以涵盖多个字段
// 使用$text查询运算符对具有文本索引的集合执行文本搜索。$text将使用空格和大多数标点符号作为分隔符来标记搜索字符串，并OR在搜索字符串中执行所有此类标记的逻辑
db.stores.find( { $text: { $search: "java shop" } } )  //查找stores集合包含java shop的任何文档
db.stores.find( { $text: { $search: "java -shop" } } )  //查找stores集合包含java排除shop的任何文档
db.stores.find( { $text: { $search: "java coffee shop" } }, { score: { $meta: "textScore" } } ).sort( { score: { $meta: "textScore" } } )   // 要按相关性得分的顺序对结果进行排序

aggregation pipeline也可以进行text搜索
db.stores.createIndex( { name: "text", description: "text" } )  // 创建索引,允许在name和description字段上进行文本搜索
在聚合管道中通过$text在$match阶段使用查询运算符进行文本搜索；聚合管道中的文本搜索具有以下限制：
1. $match阶段包含的$text必须在流水线的第一阶段
2. 一个text操作只能在流水阶段出现一次
3. text操作不能出现在$or或$not表达式
4. 默认情况下，文本搜索不会按匹配分数的顺序返回匹配的文档，在$sort阶段使用$meta聚合表达式
```

### 地理空间查询

```markdown
// 指定GeoJSON数据,使用嵌入式文档：
1. 一个名为字段名为type，指定GeoJSON对象类型
2. 一个名为字段coordinates，用于指定对象的坐标，指定经纬度坐标，有效经度-180至180(包括两者)，有效纬度-90到90(包括两者)
location: {
      type: "Point",
      coordinates: [-73.856077, 40.848447]
}

MongoDB提供以下地理空间索引类型以支持地理空间查询：
2dsphere索引支持在类似地球的球体上计算几何的查询
db.collection.createIndex({location field: "2dsphere"}) // 创建2dsphere索引

2d索引支持在二维平面上计算几何的查询,索引支持$nearSphere在球体上计算的查询，如果可能的话，还是使用2dsphere索引进行球形查询，2d对球形查询使用索引可能会导致错误的结果，如：使用2d包围极点的球形查询索引

分片集合时，不能将地理空间索引作为分片键，但可以使用其他字段作为片键在分片集合上创建地址空间索引

分片集合支持以下地理空间操作：
1. $geoNear 聚合阶段
2. $near和$nearSphere查询运算符
3. geoNear命令

在分片集群，可以使用$geoWithin和$geoIntersect来查询地理空间数据

以下为地理空间查询运算符：
$geoIntersects    // 选择和GeoJSON几何体相交的几何，该2dsphere索引支持$geoIntersects
$geoWithin        // 选择边界GeoJSON几何体内的几何,该2dsphpere和2d索引支持$geoWithin
$near             // 返回点附近的地理空间对象。需要地理空间索引。该2dsphere和2D索引支持 $near
$nearSphere       // 返回球体上某点附近的地理空间对象。需要地理空间索引。该2dsphere和2D索引支持 $nearSphere

地理空间命令：
geoNear（在MongoDB 4.0中不推荐使用）  // 执行地理空间查询，返回最接近给定点的文档。不推荐使用的geoNear命令需要地理空间索引

地理空间聚合阶段:
$geoNear          // 基于与地理空间点的接近度返回有序的文档流。集成的功能 $match，$sort以及$limit地理空间数据。输出文档包括附加距离字段，并且可以包括位置标识符字段。$geoNear需要地理空间索引

地理空间模型:
MongoDB地理空间查询可以解释平面或球面上的几何体
2dsphere 索引仅支持球形查询（即解释球面上几何的查询）
2d索引支持平面查询（即解释平面上几何的查询）和一些球形查询。虽然2d 索引支持某些球形查询，但使用2d这些球形查询的索引可能会导致错误。如果可能，请使用 2dsphere索引进行球形查询

下表列出了每个地理空间操作使用的地理空间查询运算符支持的查询：[http://docs.mongodb.com/manual/geospatial-queries/]
| 操作 | 球形/平面查询 | 备注 |
| :------: | :------: | :------: |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |


```

```markdown
// write-concern
{ w: <value>, j: <boolean>, wtimeot: <number> }
w选项：确认写入请求传递给mongod实例的数量，1表示传递写入操作给一个standalone或副本的一个主节点；0写入操作没有确认，w大于1是因为需要来自其他辅助数据节点的确认
j选项：确认写操作已写入磁盘日志(on-disk journal)
wtimeout:写入的时间限制(毫秒为单位)，wtimeout仅限于w大于1的情况

```

### MongoDBCRUD概念

```markdown
// 原子性和事务
当单个写操作(如：db.collection.updateMany())修改多个文件时，每个文件的修改都是原子性的，但是整个操作不是！

// 并发控制
并发控制允许多个程序同时运行，而不会导致数据不一致或冲突
一种方法是在字段上创建唯一索引，该索引只有唯一值。可以防止插入、更新创建重复数据。在多个字段创建唯一索引，以强制该字段组合的唯一性
另一种方法是在为写操作的查询谓词中指定字段预期的当前值

// 读取隔离,一致性和Recency


```

### 分布式查询、查询优化、查询性能、优化查询

```markdown
oplog是对数据集操作的可重现序列,索引可以涵盖嵌入式文档中字段的查询；如果索引跟踪哪个或哪些字段导致索引为多建索引，则多建索引可以覆盖非数组字段上的查询。(多建索引不能覆盖数组字段上的查询)；地理空间索引无法覆盖索引。要确定一个查询是否为覆盖查询，使用db.collection.explain()或explain()

使用Database Profiler评估对数据库的操作

// 优化查询
1. 创建支持查询的索引
2. 如经常对timestamp字段进行排序性查询，则可以通过在timestamp字段创建索引，查询时使用db.collection.find().sort({ timestamp: -1})更快得到结果，因为MongoDB可以按照升序或降序读取索引
3. 索引支持查询、更新操作和聚合管道的某些阶段；如符合以下条件，则BinData类型的索引建可以更有效的存储在索引中
  * 二进制子类型值得范围为0-7或128-135
  * 字节数组的长度为：0,1,2,3,4,5,6,7,8,10,12,14,16,20,24或32
4. 限制查询结果数，通过limit()方法减少查询对网络资源的消耗。通常和sort()结合使用
5. 使用预测返回必要的数据；只需要文档的部分字段时，只返回所需的字段可以获取更好的性能
6. 使用$hint选择一个特定的指数，大多数情况下，查询优化器会为特定操作选择最佳的索引，但是也可以使用$hint()方法强制使用特定索引。hint()支持性能测试或在某些查询，在一些查询中，必须选择一个字段或字段必须包含在多个索引中
7. 使用增量运算符执行操作服务端，MongoDB的$inc运算符来增加或减少文档中的值，该操作增加服务端字段的值，作为选择文档的替代方法，在客户端进行简单修改，然后将整个文档写入服务器。$inc操作还可以帮助避免竞争条件，这将导致两个应用实例对同一个文件查询时，手动增加一个字段，并保存整个文件回到同一时间

// 写操作性能

集合中每个索引都会增加性能的开销；因集合中每个insert和delete写入操作。MongoDB都会从目标集合的每个索引中插入或删除对应的文档键。update操作可能会导致更新在这个集合中索引的子集，取决于更新时这个键的影响
// 注意：如果写入操作中涉及的文档包含在索引中，MongoDB则仅更新sparse或partial索引(sparse和partial为MongoDB的索引类型)

某些更新操作可能增加文档的大小。如:向文档增加字段。对于MMAPv1引擎，如果更新操作导致文档超过当前分配的记录大小。MongoDB会将文档重新定位到磁盘上。并留有足够的连续空间来保存文档。需要重定位的更新比没有重定位的更新花费的时间更长，特别是在集合具有索引的情况下。如果集合具有索引，MongoDB必须更新所有索引条目。因此，对于具有许多索引的集合，此移动将影响写入吞吐量

// explain结果
Stages描述了操作--如：
COLLSCAN          用于集合扫描
IXSCAN            用于扫描索引键
FETCH             用于检索文档
SHARD_MERGE       用于合并分片的结果
SHARDING_FILTER   用于从分片中过滤掉孤立文档

// explain result
explain.queryPlanner    // 包含查询优化器选择查询计划的信息
explain.queryPlanner.namespace  //

// 分析查询性能
利用cursor.explain("executionStats")和db.collection.explain("executionStats")方法查看有关查询性能的统计信息

1. queryPlanner.winningPlan.stage // COLLSCAN表示集合扫描，也就表明mongod必须扫描整个集合文档来识别结果
2. executionStats.nReturned // 显示3，表明查询匹配到三个文档
3. executionStats.totalKeysExamined   // 显示0，表明此查询未用到索引
4. executionStats.totalDocsExamined   // 显示10，表明MongoDB必须扫描的文档数量(此处为10个)才能查找到和条件匹配相符的文档
5. queryPlanner.winningPlan.inputStage.stage  // 显示IXSCAN表示此查询使用索引

// 比较索引性能
手动比较使用多个索引查询的性能，可以hint()和explain()方法结合使用，添加复合索引时，字段的顺序非常重要；顺序不同可能会对查询造成的影响不同，性能产生差异；例如：
db.collection.createIndex( { quantity: 1, type: 1 } ) // 索引先按quantity字段排序，然后按照type字段排序
db.collection.createIndex( { type: 1, quantity: 1 } ) // 索引先type字段排序，再按quantity字段排序

// Tailable Cursors
默认情况下，当查询操作结束后，会关闭返回的数据cursor，Tailable Cursors类似于tail -f命令。作用在capped集合上，当客户端有新的文档插入到capped集合时，tailable游标将继续检索文档。MongoDB副本集就使用tailable游标来跟踪主节点的oplog

注意：如果查询字段位于索引字段，不要使用tailable游标，使用常规游标即可，跟踪查询返回的索引字段的最后一个值。要检索新添加的文档，请使用查询条件中索引字段的最后一个值再次查询集合。例如：db.collection.find( { indexedField: { $gt: lastvalue } } )

考虑以下与tailable游标相关的行为：
1. Tailable游标不使用索引并按自然顺序返回文档
2. 由于tailable游标不使用索引，因此查询的初始扫描开销很大，但在后续的新添加文档检索消耗很小
3. 以下情况Tailable游标可能会死亡或者无效：
  * 查询返回不匹配
  * 游标返回集合"end"处的文档，然后应用程序删除了该文档
  一个死亡的cursor有一个id为0
```

## 聚合(Aggregation)

聚合操作将来自多个文档的值组合一起，并且对分组数据执行各种操作后返回单个结果。MongoDB三种执行聚合的方式：聚合管道、map-reduce函数和单用途(single purpose)聚合方法

### 聚合管道

最基本的管道阶段是过滤器，操作类似于查询和文档转换。其他管道操作提供了按特定字段或字段对文档进行分组和排序的工具以及用于聚合数组内容的工具，包括文档中的数组。此方式是MongoDB数据聚合的首选方法

map-reduce函数：通常，map-reduce操作有两个阶段：一个map阶段，它处理每个文档并为每个输入文档发出一个或多个对象，并减少组合map操作输出的阶段。可选地，map-reduce可以具有 最终化阶段以对结果进行最终修改。与其他聚合操作一样，map-reduce可以指定查询条件以选择输入文档以及排序和限制结果。Map-reduce使用自定义JavaScript函数来执行映射和减少操作，以及可选的finalize操作。虽然自定义JavaScript与聚合管道相比提供了极大的灵活性，但通常，map-reduce比聚合管道效率更低，更复杂
[map-reduce](http://docs.mongodb.com/manual/_images/map-reduce.bakedsvg.svg)

单用途聚合操作：MongoDB有db.collection.estimatedDocumentCount()，db.collection.count()和db.collection.distinct(),这些操作聚合都只能聚合来自单个集合的文档

```markdown
聚合管道流水线表达式：某些阶段将流水线表达式作为操作数，流水线表达式指定要应用于输入文档的转换。表达式具有文档结构且包含其他表达式
流水线表达式只能操作在当前流水线的文档并且不能引用其他文档中的数据。表达式提供文档的内存中转换操作。通常，当聚合过程带有一个例外：accumulator表达式，表达式是无状态的。仅进行评估
这个累加器用在$group阶段，保持其状态(例如：totals,maximums,minimums和related data)通过流水线作为文档处理进程
在$project阶段使用一些累加器，但在此阶段使用时，累加器不会在文档中保持其状态

使用以下操作可以避免扫描整个集合：
流水线操作符和索引：当$match和$sort出现在管道流水线起始位置可以利用索引的优势。$GeoNear管道则可以有一个地理空间索引的优势，当使用$geoNear时，其必须在聚合管道操作中的第一个阶段

限制：聚合管道对值类型和结果大小有一定的限制

 聚合管道优化：
Projection优化：聚合管道可以确定它是否仅需要文档中字段的子集来获得结果。如果是这样，管道将只使用那些必需的字段，减少通过管道的数据量
流水线序列化优化：$project或$addFields + $match序列优化；对于包含projection阶段($project或$addFields)后面跟着一个$match阶段的聚合流水线，在projection之前MongoDB移动$match阶段中任何过滤器不需要在projection阶段计算的值给一个新的$match阶段

聚合管道限制：
使用aggregate命令的聚合操作具有以下限制：
1. 结果大小限制：此aggregate命令可以返回游标或将结果存储在集合中。目前16M
2. 内存限制：管道阶段的RAM限制为100M，如果某个阶段超过此限制，则会出现错误，允许处理大型数据集，使用allowDishUse选项将数据写入到临时文件

$groupLookkup阶段必须保持在100M的内存限制中，如果allowDishUse: true指定在aggregate()操作，$graphLookup阶段会忽略此选项。如果有其他阶段在aggregate()操作中，allowDiskUse: true选项将影响这些阶段

聚合管道和分片集合：
如果管道的开始在分片键有一个确切的$match，则整个管道仅在匹配的分片上运行。对于必须在多个分片上运行的聚合操作。如果操作不需要在数据库主分片运行，则这些操作会将结果路由到随机的分片上来合并结果，避免使数据库的主分片超载。$out和$lookup阶段需要在数据库的主分片上运行

在将聚合管道分成两部分时，管道被拆分以确保分片在考虑优化的情况下执行尽可能多的阶段；优化可能会在不同版本之间发生变化
```

### Map-Reduce

### 聚合参考

聚合管道快速参考：Stages(db.collection.aggregate)除了$out和$geoNear阶段都可以在管道中多次出现

#### Stages(db.collection.aggregate)

| 阶段 | 描述 |
| :------: | :------: |
| $addFields | 向文档添加新字段。类似于 $project，$addFields重塑流中的每个文档; 具体而言，通过向输出文档添加新字段，该文档包含输入文档的现有字段和新添加字段 |
| $bucket | 根据指定的表达式和存储区边界，将传入的文档分组，称为存储桶 |
| $bucketAuto | 根据指定的表达式将传入的文档分类为特定数量的组（称为存储桶）。自动确定存储桶边界，以尝试将文档均匀地分配到指定数量的存储桶中 |
| $collStats | 返回有关集合或视图的统计信息 |
| $count | 返回聚合管道此阶段的文档数量计数 |
| $facet | 在同一组输入文档的单个阶段内处理多个聚合管道。支持创建能够在单个阶段中跨多个维度或方面表征数据的多面聚合 |
| $geoNear | 基于与地理空间点的接近度返回有序的文档流。集成的功能 $match，$sort以及$limit地理空间数据。输出文档包括附加距离字段，并且可以包括位置标识符字段。 |
| $graphLookup | 对集合执行递归搜索。对于每个输出文档，添加一个新的数组字段，其中包含该文档的递归搜索的遍历结果。 |
| $group | 按指定的标识符表达式对文档进行分组，并将累加器表达式（如果已指定）应用于每个组。消耗所有输入文档，并为每个不同的组输出一个文档。输出文档仅包含标识符字段，如果指定，则包含累积字段。 |
| $indexStats | 返回有关集合的每个索引的使用的统计信息。 |
| $limit | 将未修改的前n个文档传递给管道，其中n是指定的限制。对于每个输入文档，输出一个文档（对于前n个文档）或零文档（在前n个文档之后）。 |
| $listSessions | 列出活动时间足以传播到system.sessions集合的所有会话。 |
| $lookup | 对同一数据库中的另一个集合执行左外连接，以 从“已连接”集合中过滤文档以进行处理。 |
| $match | 过滤文档流以仅允许匹配的文档未经修改地传递到下一个管道阶段。 $match使用标准的MongoDB查询。对于每个输入文档，输出一个文档（匹配）或零文档（不匹配）。 |
| $out | 将聚合管道的结果文档写入集合。要使用$out舞台，它必须是管道中的最后一个阶段。 |
| $project | 重新整形流中的每个文档，例如添加新字段或删除现有字段。对于每个输入文档，输出一个文档。 |
| $redact | 通过基于文档本身中存储的信息限制每个文档的内容来重塑流中的每个文档。包含$project和的功能 $match。可用于实现字段级别的编辑。对于每个输入文档，输出一个或零个文档。 |
| $replaceRoot | 用指定的嵌入文档替换文档。该操作将替换输入文档中的所有现有字段，包括_id字段。指定嵌入在输入文档中的文档以将嵌入文档提升到顶层。 |
| $sample | 从输入中随机选择指定数量的文档。 |
| $skip | 跳过前n个文档，其中n是指定的跳过编号，并将未修改的其余文档传递给管道。对于每个输入文档，输出零文档（对于前n个文档）或一个文档（如果在前n个文档之后）。 |
| $sort | 按指定的排序键重新排序文档流。只有订单改变; 文件保持不变。对于每个输入文档，输出一个文档。 |
| $sortByCount | 根据指定表达式的值对传入文档进行分组，然后计算每个不同组中的文档计数。 |
| $unwind | 从输入文档解构数组字段以输出每个元素的文档。每个输出文档都使用元素值替换数组。对于每个输入文档，输出n个文档，其中n是数组元素的数量，对于空数组，可以为零。 |

#### Stages(db.aggregate)

MongoDB还提供db.aggregate方法：

| 名称 | 描述 |
| :------: | :------: |
| $currentOp | 返回有关MongoDB部署的活动 和/或 休眠操作的信息 |
| $listLocalSessions | 列出最近在当前连接mongos或mongod实例上的所有活动回话，这些会话不一定都传到system.sessions集合中 |

表达式可以包括字段路径和系统变量，文字， 表达式对象和 表达式运算符。表达式可以嵌套

#### 布尔表达式运算符

布尔表达式将其参数表达式计算为布尔值，并返回布尔值作为结果。除了false布尔值，布尔表达式为false如下：null，0，和undefined 的值。布尔表达式将所有其他值计算为true，包括非零数值和数组

| 名称 | 描述 |
| :------: | :------: |
| $and | 仅在其所有表达式求值为true时，返回true,接受任意数量的参数表达式 |
| $not | 返回与其参数表达式相反的布尔值，接受单个参数表达式 |
| $or | 当任何表达式求值为true时，返回true，允许任意数量的参数表达式 |

#### 比较表达式运算符

比较表达式返回一个布尔值，除了$cmp返回一个数字。比较表达式采用两个参数表达式并比较值和类型，使用指定的BSON比较顺序来表示不同类型的值

| 名称 | 描述 |
| :------: | :------: |
| $cmp | 返回0如果这两个值是相等的，1如果第一个值大于所述第二值，以及-1比所述第二如果第一个值是以下。 |
| $eq | 返回true值是否相等。 |
| $gt | true如果第一个值大于第二个值，则返回。 |
| $gte | true如果第一个值大于或等于第二个值，则返回。 |
| $lt | true如果第一个值小于第二个值，则返回。 |
| $lte | true如果第一个值小于或等于第二个值，则返回。 |
| $ne | true如果值不相等则返回。 |

#### 条件表达式运算符

| 名称 | 描述 |
| :------: | :------: |
| $cond | 一个三元运算符，用于计算一个表达式，并根据结果返回其他两个表达式之一的值。接受有序列表中的三个表达式或三个命名参数。 |
| $ifNull | 如果第一个表达式导致null结果，则返回第一个表达式的非null结果或第二个表达式的结果。空结果包含未定义值或缺少字段的实例。接受两个表达式作为参数。第二个表达式的结果可以为null。 |
| $switch | 评估一系列案例表达。当它找到一个求值的表达式时true，$switch执行一个指定的表达式并中断控制流。 |

#### 日期表达式运算符

以下运算符返回日期对象或日期对象的组件

| 名称 | 描述 |
| :------: | :------: |
| $dateFromParts | 给定日期的组成部分构造BSON Date对象。 |
| $dateFromString | 将日期/时间字符串转换为日期对象。 |
| $dateToParts | 返回包含日期组成部分的文档。 |
| $dateToString | 以格式化字符串形式返回日期。 |
| $dayOfMonth | 以1到31之间的数字返回日期的月中某天。 |
| $dayOfWeek | 返回日期的星期几，作为1（星期日）和7（星期六）之间的数字。 |
| $dayOfYear | 以1到366（闰年）之间的数字返回日期的日期。 |
| $hour | 以0到23之间的数字返回日期的小时。 |
| $isoDayOfWeek | 返回ISO 8601格式的工作日编号，范围从 1（星期一）到7（星期日）。 |
| $isoWeek | 返回ISO 8601格式的周数，从 1到53。周数从1包含年份第一个星期四的一周（星期一到星期日）开始。 |
| $isoWeekYear | 以ISO 8601格式返回年份编号。年份从第1周的星期一（ISO 8601）开始，到上周的星期日（ISO 8601）结束。 |
| $millisecond | 以0到999之间的数字返回日期的毫秒数。 |
| $minute | 以0到59之间的数字返回日期的分钟。 |
| $month | 以1（1月）到12（12月）之间的数字返回日期的月份。 |
| $second | 以0到60（闰秒）之间的数字返回日期的秒数。 |
| $toDate | 将值转换为日期。(4.0版中的新功能) |
| $week | 返回日期的周数，作为0（在一年的第一个星期日之前的部分周）和53（闰年）之间的数字。 |
| $year | 以数字形式返回日期年份（例如2014年）。 |

以下算术运算符可以采用日期操作数

| 名称 | 描述 |
| :------: | :------: |
| $add | 添加数字和日期以返回新日期。如果添加数字和日期，请将数字视为毫秒。接受任意数量的参数表达式，但最多只能有一个表达式解析为日期。 |
| $subtract | 返回从第一个值中减去第二个值的结果。如果这两个值是日期，则返回以毫秒为单位的差异。如果这两个值是日期和以毫秒为单位的数字，则返回结果日期。接受两个参数表达式。如果这两个值是日期和数字，请首先指定日期参数，因为从数字中减去日期没有意义。 |

#### 文字表达运算符

| 名称 | 描述 |
| :------: | :------: |
| $literal | 返回一个没有解析的值。用于聚合管道可以将其解释为表达式的值。例如，将$literal表达式用于以a开头的字符串，$以避免将其解析为字段路径 |

#### 对象表达式运算符

| 名称 | 描述 |
| :------: | :------: |
| $mergeObjects | 将多个文档合并为一个文档。(版本3.6中的新功能) |
| $objectToArray | 将文档转换为表示键值对的文档数组。(版本3.6中的新功能) |

#### Set表达式运算符

Set表达式对数组执行set操作，将数组视为集合。Set表达式忽略每个输入数组中的重复条目以及元素的顺序。如果set操作返回一个set，则该操作会过滤掉结果中的重复项，以输出仅包含唯一条目的数组。未指定输出数组中元素的顺序

| 名称 | 描述 |
| :------: | :------: |
| $allElementsTrue | 如果集合的没有元素的计算结果为false，则返回true，否则返回false。接受单个参数表达式 |
| $anyElementTrue | 如果集合的任何元素的计算结果为true， 则返回true; 否则返回false。接受单个参数表达式 |
| $setDifference | 返回一个集合，其中的元素出现在第一个集合中但不在第二个集合中; 即执行第二组相对于第一组的相对补充。接受两个参数表达式 |
| $setEquals | 如果输入集合具有相同的不同元素返回true。接受两个或多个参数表达式 |
| $setIntersection | 返回包含出现在所有输入集中的元素的集合。接受任意数量的参数表达式 |
| $setIsSubset | 第一组中的所有元素出现在第二组中返回true，包括第一组中的所有元素等于第二组时; 即不是严格的子集。接受两个参数表达式 |
| $setUnion | 返回包含出现在任何输入集中的元素的集合 |

#### String表达式运算符

无论使用什么字符，$concat行为都是明确定义的

| 名称 | 描述 |
| :------: | :------: |
| $concat | 连接任意数量的字符串。 |
| $dateFromString | 将日期/时间字符串转换为日期对象。 |
| $dateToString | 以格式化字符串形式返回日期。 |
| $indexOfBytes | 搜索字符串以查找子字符串的出现并返回第一次出现的UTF-8字节索引。如果未找到子字符串，则返回-1。 |
| $indexOfCP | 搜索字符串以查找子字符串的出现并返回第一次出现的UTF-8代码点索引。如果未找到子字符串，则返回-1 |
| $ltrim | 从字符串的开头删除空格或指定的字符。(4.0版中的新功能) |
| $rtrim | 从字符串末尾删除空格或指定的字符。(4.0版中的新功能) |
| $split | 根据分隔符将字符串拆分为子字符串。返回一个子字符串数组。如果在字符串中找不到分隔符，则返回包含原始字符串的数组。 |
| $strLenBytes | 返回字符串中UTF-8编码字节的数量。 |
| $strLenCP | 返回字符串中UTF-8 代码点的数量。 |
| $strcasecmp | 执行不区分大小写的字符串比较并返回： 0如果两个字符串相同，1如果第一个字符串大于第二个-1字符串，并且第一个字符串小于第二个字符串。 |
| $substr | 已过时。使用$substrBytes或 $substrCP。 |
| $substrBytes | 返回字符串的子字符串。从字符串中指定的UTF-8字节索引（从零开始）开始，并继续指定的字节数。 |
| $substrCP | 返回字符串的子字符串。以字符串中指定的UTF-8 代码点（CP）索引（从零开始）处的字符开始，并继续指定的代码点数。 |
| $toLower | 将字符串转换为小写。接受单个参数表达式。 |
| $toString | 将值转换为字符串。(4.0版中的新功能) |

#### 文本表达式运算符

| 名称 | 描述 |
| :------: | :------: |
| $meta | 访问文本搜索元数据 |

#### Type表达式运算符

| 名称 | 描述 |
| :------: | :------: |
| $convert | 将值转换为指定的类型。(4.0版中的新功能) |
| $toBool | 将值转换为布尔值。(4.0版中的新功能) |
| $toDate | 将值转换为日期。(4.0版中的新功能) |
| $toDecimal | 将值转换为Decimal128。(4.0版中的新功能) |
| $toDouble | 将值转换为double。(4.0版中的新功能) |
| $toInt | 将值转换为整数。(4.0版中的新功能) |
| $toLong | 将值转换为long。(4.0版中的新功能) |
| $toObjectId | 将值转换为ObjectId。(4.0版中的新功能) |
| $toString | 将值转换为字符串。(4.0版中的新功能) |
| $type | 返回该字段的BSON数据类型(4.0版中的新功能) |

#### Accumulators(累加器)($group)

可以在$group阶段使用，其是在文档通过管道时保持其状态(如：总数、最大值、最小值和相关数据)的运算符。当在$group阶段中作为累加器使用时，这些运算符将单个表达式作为输入，为每个输入文档计算一次表达式，并为共享相同组密钥的文档组保持其阶段

| 名称 | 描述 |
| :------: | :------: |
| $addToSet | 返回每个组的唯一表达式值数组。数组元素的顺序未定义 |
| $avg | 返回数值的平均值。忽略非数字值 |
| $first | 返回每个组的第一个文档中的值。仅在文档按定义的顺序定义时才定义订单 |
| $last | 返回每个组的最后一个文档的值。仅在文档按定义的顺序定义时才定义订单 |
| $max | 返回每个组的最高表达式值 |
| $mergeObjects | 返回通过组合每个组的输入文档创建的文档 |
| $min | 返回每个组的最低表达式值 |
| $push | 返回每个组的表达式值数组 |
| $stdDevPop | 返回输入值的总体标准偏差 |
| $stdDevSamp | 返回输入值的样本标准偏差 |
| $sum | 返回数值的总和。忽略非数字值 |

#### Accumulators(累加器)($project和$addFields)

一些可用用作$group阶段的累加器操作也可以用于$project和$addFields阶段，但不能用作累加器。在$project和$addFields阶段使用时，这些运算符不会保持其状态，并且可以将单个参数或多个参数作为输入

| 名称 | 描述 |
| :------: | :------: |
| $avg | 返回每个文档的指定表达式或表达式列表的平均值。忽略非数字值 |
| $max | 返回每个文档的指定表达式或表达式列表的最大值 |
| $min | 返回每个文档的指定表达式或表达式列表的最小值 |
| $stdDevPop | 返回输入值的总体标准偏差 |
| $stdDevSamp | 返回输入值的样本标准偏差 |
| $sum | 返回数值的总和。忽略非数字值 |

#### 变量表达式运算符

| 名称 | 描述 |
| :------: | :------: |
| $let | 定义在子表达式范围内使用的变量，并返回子表达式的结果。接受命名参数。接受任意数量的参数表达式 |

[表达式运算符索引](http://docs.mongodb.com/manual/meta/aggregation-quick-reference/#index-of-expression-operators)

### aggregation-commands(聚合命令)

#### 聚合命令

| 名称 | 描述 |
| :------: | :------: |
| aggregate | 使用聚合框架执行聚合任务，例如组 |
| count | 计算集合或视图中的文档数 |
| distinct | 显示为集合或视图中的指定键找到的不同值 |
| group | 不推荐。按指定键对集合中的文档进行分组，并执行简单聚合 |
| mapReduce | 对大型数据集执行map-reduce聚合 |

#### 聚合方法

| 名称 | 描述 |
| :------: | :------: |
| 名称 | 描述 |
| db.collection.aggregate() | 提供对聚合管道的访问 |
| db.collection.group() | 已过时。按指定键对集合中的文档进行分组，并执行简单聚合 |
| db.collection.mapReduce() | 对大型数据集执行map-reduce聚合 |

### 聚合表达式中的变量

聚合表达式可以使用用户自定义和系统变量，变量可以保存任何BSON类型的数据。要访问变量的值，使用带有前缀双dollar符($$)的变量名称的字符串。如果变量引用了一个对象，要访问该对象中特定字段，要使用"$$variable.field"表示法

#### 用户变量

用户变量名称可以包含ASCII字符[_a-zA-Z0-9]和任何非ASCII字符，用户变量名必须以小写的ASCII字符[a-z]或非ASCII字符开头

#### 系统变量

| 变量 | 描述 |
| :------: | :------: |
| ROOT | 引用当前正在聚合管道阶段处理的根文档，即顶级文档 |
| CURRENT | 引用聚合管道阶段中正在处理的字段路径的开始。除非另有说明，否则所有阶段都以CURRENT相同的 方式开头ROOT |
|  | CURRENT是可以修改的。但是，由于$field等同于$$CURRENT.field，重新绑定会CURRENT改变$访问的含义 |
| REMOVE | 一个计算缺失值的变量。允许条件排除字段。在a中$projection，REMOVE从输出中排除设置为变量的字段 |
|  | 有关其用法的示例，请参阅有条件排除字段 |
|  | 版本3.6中的新功能 |
| DESCEND | $redact表达式的允许结果之一 |
| PRUNE | $redact表达式的允许结果之一 |
| KEEP | $redact表达式的允许结果之一 |

### SQL和aggregation(聚合)的比较

[sql-aggregation](http://docs.mongodb.com/manual/reference/sql-comparison/)

## 数据模型

创建索引应考虑以下：

* 每个索引至少占用8kb的数据空间
* 添加索引会对写入操作产生负面影响，因每个插入也必须更新索引
* 具有高读写比率的集合通常受益于其他索引，索引不影响未创建索引的操作
* 处于活动状态时，每个索引都会占用磁盘空间和内存

## Transactions

可以跨多个操作、集合、数据库和文档使用多文档事务

多文档事务是原子性的：

* 当事务提交时，事务中所有的数据更改都被保存并在事务外部可见，事务提交之前，事务中发生的数据更改事务外部不可见
* 事务中止时，事务中所有的数据更改都会被丢弃但不会不可见

## 索引

索引的排序支持有效的等式匹配和基于范围的查询操作，mongodb还可以使用索引中的顺序返回排序结果

分片集群中，如果不将_id字段作为片键，则应用程序必须确保_id字段值的唯一性以防出错

### 索引类型

单字段索引：对于单字段索引和排序操作，索引键的排序顺序无关紧要

复合索引：MongoDB支持多字段的用户定义索引，复合索引中字段的顺序具有重要意义

多键索引：MongoDB使用多键索引来索引存储在数组中的内容，如果索引包含数组的字段，则会为数组的每个元素创建单独的索引条目

地理空间索引：mongodb提供两个特殊索引以支持对地理空间坐标数据的查询，2d索引使用平面几何返回结果；2dsphere索引使用球形几何返回结果

文本索引：mongodb提供text索引类型以支持字符串查询

散列索引：MongoDB提供散列索引类型以支持基于散列的分片，该类型索引是一个字段值的hash，索引在其范围内有相当多随机值分配，仅支持相等匹配不支持范围查询

### 索引属性

索引具有唯一性，MongoDB拒绝索引字段的重复值，唯一索引可与其他MongoDB索引互换

部分索引仅索引符合指定过滤器表达式的集合中的文档

索引的稀疏属性可确保索引仅包含具有索引字段的文档的条目。索引会跳过没有索引字段的文档

TTL索引是MongoDB可用于在一定时间后自动从集合中删除文档的特殊索引。这对于某些类型的信息非常理想，例如机器生成的事件数据，日志和会话信息，这些信息只需要在数据库中持续有限的时间

### 文本索引

一个集合对多只能有一个text索引,对于text索引，索引字段的权重表示字段相对于其他索引字段在文本搜索分数方面的重要性，对于文档中的每个索引字段，MongoDB将匹配数乘以权重并将结果相加。使用此总和，MongoDB然后计算文档的分数。索引字段的默认权重为1。要调整索引字段的权重，请weights在db.collection.createIndex()方法中包含该选项

db.collection.createIndex( { field: "text" } )

text在多个字段上创建索引时，还可以使用通配符说明符（$**）。使用通配符文本索引，MongoDB会为包含集合中每个文档的字符串数据的每个字段编制索引。以下示例使用通配符说明符创建文本索引
db.collection.createIndex( { "$**": "text" } )

指定text索引名称：索引的默认名称由每个索引名称加_text组成，可自定义索引名称;db.collection.createIndex( { content: "text", "user.comments": "text"}, { name: "myTextName"} )
用权重控制搜索结果：以下为设置content和keywords索引权重分别为10和5，about默认为1
db.blog.createIndex(  content: "text", keywords: "text", about: "text" }, { weights: { content: 10, keywords: 5 }, name: "TextIndex" } )
限制文档扫描数量：文档结构如下

```markdown
{ _id: 1, dept: "tech", description: "lime green computer" }
{ _id: 2, dept: "tech", description: "wireless red mouse" }
{ _id: 3, dept: "kitchen", description: "green placemat" }
{ _id: 4, dept: "kitchen", description: "red peeler" }
{ _id: 5, dept: "food", description: "green apple" }
{ _id: 6, dept: "food", description: "red potato" }
```

## 安全

## Change Streams

## 复制(Replication)

## 分片(Sharding)

## 管理(Administration)

## 存储引擎(Storage)

## MongoDB-->kafka

```intro
Debezium的MongoDB Connector可以监视MongoDB副本集或MongoDB分片集群；MongoDB复制的工作原理是让Master记录其oplog（或操作日志）中的更改，然后每个Slave节点读取主要的oplog并按顺序应用所有操作到他们自己的文档。将新服务器添加到副本集时，该服务器首先执行初始同步主数据库上的所有数据库和集合，然后读取主数据库的oplog以应用自开始同步以来可能已经进行的所有更改。这个新服务器在赶上Master节点oplog的尾部时成为Slave服务器（并且能够处理查询）

Debezium MongoDB Connector使用相同的复制机制，但它实际上并不成为副本集的成员，Connector始终连接副本集的主节点来跟踪oplog，MongoDB重新选举主节点后，Connector会自动切换到新的主节点。Connector会记录oplog的位置，来实现增量同步

Debezium MongoDB Connector和副本集一起使用时，只需将副本集的地址作为Connector的seed地址mongodb.hosts即可。Connector使用seed连接副本集，获取到完整的成员集及确定主节点后。Connector连接到主节点并从其oplog捕获更改

Connector和分片集群一起使用时，使用config服务器的地址配置Connector连接。针对分片群集运行连接器时，请使用tasks.max大于副本集数量的值,这将允许连接器为每个副本集创建一个任务，并让Kafka Connect在所有可用的工作进程中协调，分发和管理任务

mongodb.name，它充当MongoDB副本集或分片集群的逻辑名称,连接器以多种方式使用逻辑名称：作为所有主题名称的前缀，以及记录每个副本集的oplog位置时的唯一标识符
```

## topics name

```info
Connector对于每个集合的insert、update、delete文档操作写入事件到每个kafka topic。kafka topic的名字规则logicalName.databaseName.collectionName，logicalName是配置属性mongodb.name指定的连接器的名称,databaseName是发生操作数据库的名称,collectionName是受影响文档所在的MongoDB集合的名称。例如：

MongoDB数据库inventory包含四个集合：products,product_on_hand,customers,orders。假设监视此数据库的连接器逻辑名称是fulfillment，则连接器生成的四个kafka topic事件为：
fulfillment.inventory.products
fulfillment.inventory.products_on_hand
fulfillment.inventory.customers
fulfillment.inventory.orders
```

## Partitions

```info
MongoDB连接器不会明确确定事件的topic分区,相反，它允许Kafka根据密钥确定分区。您可以通过在Kafka Connect工作程序配置中定义Partitioner实现的名称来更改Kafka的分区逻辑
```

## Events

```info
MongoDB连接器生成的数据更改事件都有一个key和value。下面概述了这些key和value的结构
从kafka0.10开始，可以使用消息密钥进行记录并创建消息的时间戳(由生产者记录)或由kafka写入日志的值进行评估
如果事件的来源在结构上发生变化或连接器改变，事件的结构可能会随着时间推移而发生变化。对于消费者可能很难处理。因此kafka每个事件都自成一体，每个消息键值都有两部分:a schema和payload。当payload包含数据时，schema描述payload的结构

更改事件的key：对于给定集合，更改事件的key将是包含单个id字段的结构。它的值是文档的标识符表示为字符串

```

MongoDB不可用时，重新尝试连接由三个属性控制：

* connect.backoff.initial.delay.ms -- 第一次尝试重新连接之前的延迟，默认值为1秒（1000毫秒）
* connect.backoff.max.delay.ms --- 尝试重新连接之前的最大延迟，默认值为120秒（120,000毫秒）
* connect.max.attempts ------------ 产生错误之前的最大尝试次数，默认值为16

## Connector属性

| 属性 | 默认 | 描述 |
| :------: | :------: | :------: |
| name |  | 连接器的唯一名称。尝试再次使用相同名称注册将失败。（所有Kafka Connect连接器都需要此属性。） |
| connector.class |  | 连接器的Java类的名称。始终使用io.debezium.connector.mongodb.MongoDbConnectorMongoDB连接器的值。 |
| mongodb.hosts |  | 副本集中MongoDB服务器的主机名和端口对（格式为“host”或“host：port”）的逗号分隔列表。该列表可以包含单个主机名和端口对。如果mongodb.members.auto.discover设置为false，则主机和端口对应以副本集名称为前缀（例如，rs0/localhost:27017）。 |
| mongodb.name |  | 一个唯一名称，用于标识此连接器监视的连接器和/或MongoDB副本集或分片群集。每个服务器最多应由一个Debezium连接器监控，因为此服务器名称前缀是MongoDB副本集或集群发出的所有持久性Kafka主题。 |
| mongodb.user |  | 连接到MongoDB时要使用的数据库用户的名称。仅当MongoDB配置为使用身份验证时才需要这样做。 |
| mongodb.password |  | 连接MongoDB时使用的密码。仅当MongoDB配置为使用身份验证时才需要这样做。 |
| mongodb.ssl.enabled | false | Connector将使用SSL连接到MongoDB实例。 |
| mongodb.ssl.invalid.hostname.allowed | false | 启用S​​SL时，此设置控制在连接阶段是否禁用严格主机名检查。如果true连接不会阻止中间人攻击。 |
| database.whitelist | 空字符串 | 可选的以逗号分隔的正则表达式列表，用于匹配要监视的数据库名称; 未列入白名单的任何数据库名称都将从监视中排除。默认情况下，将监视所有数据库。不得与之配合使用database.blacklist。 |
| database.blacklist | 空字符串 | 可选的以逗号分隔的正则表达式列表，用于匹配要从监视中排除的数据库名称; 将监控黑名单中未包含的任何数据库名称。不得与之配合使用database.whitelist。 |
| collection.whitelist | 空字符串 | 可选的以逗号分隔的正则表达式列表，它们匹配要监视的MongoDB集合的完全限定名称空间; 任何未包含在白名单中的集合都将被排除在监控之外。每个标识符的格式为databaseName。collectionName。默认情况下，连接器将监视除local和admin数据库中的集合之外的所有集合。不得与之配合使用collection.blacklist。 |
| collection.blacklist | 空字符串 | 可选的以逗号分隔的正则表达式列表，它们匹配MongoDB集合的完全限定名称空间，以便从监视中排除; 任何未包含在黑名单中的集合都将受到监控。每个标识符的格式为databaseName。collectionName。不得与之配合使用collection.whitelist。 |
"| snapshot.mode
0.9.2及更高版本 | initial | 指定在启动连接器时运行快照的条件（例如，初始同步）。默认值为initial，并指定连接器在未找到偏移量或oplog不再包含先前偏移量时读取快照。在从不选项指定连接器不应该使用快照，而不是连接器应着手尾日志。 |"
"| field.blacklist
0.9.0及更高版本 | 空字符串 | 应该从更改事件消息值中排除的字段的完全限定名称的可选逗号分隔列表。字段的完全限定名称的格式为databaseName。collectionName。fieldName。nestedFieldName，其中databaseName和collectionName可以包含匹配任何字符的通配符（*）。 |"
"| field.renames
0.9.0及更高版本 | 空字符串 | 可选的以逗号分隔的完整限定字段替换列表，用于重命名更改事件消息值中的字段。字段的完全限定替换的格式为databaseName。collectionName。fieldName。nestedFieldName：newNestedFieldName，其中databaseName和collectionName可以包含匹配任何字符的通配符（*），冒号字符（:)用于确定字段的重命名映射。下一个字段替换将应用于列表中上一个字段替换的结果，因此在重命名同一路径中的多个字段时请记住这一点。 |"
| tasks.max | 1 | 应为此连接器创建的最大任务数。MongoDB连接器将尝试为每个副本集使用单独的任务，因此在将连接器与单个MongoDB副本集一起使用时，默认值是可接受的。将连接器与MongoDB分片群集一起使用时，建议指定一个等于或大于群集中分片数的值，以便Kafka Connect可以分配每个副本集的工作。 |
| initial.sync.max.threads | 1 | 正整数值，指定用于在副本集中执行集合的初始同步的最大线程数。默认为1。 |
"| tombstones.on.delete
0.7.3及以后 | true | 控制是否应在删除事件后生成逻辑删除事件
当true删除操作是通过删除事件和随后的墓碑事件表示。如果false只发送删除事件
发送逻辑删除事件（默认行为）允许Kafka在删除源记录后完全删除与给定密钥有关的所有事件。 |"
| snapshot.delay.ms0.9.0及更高版本 |  | 连接器在启动后拍摄快照之前应等待的间隔（以毫秒为单位）;可用于在群集中启动多个连接器时避免快照中断，这可能会导致连接器重新平衡 |

以下高级配置属性具有良好的默认值，可在大多数情况下使用，因此很少需要在连接器的配置中指定
| 属性 | 默认 | 描述 |
| :------: | :------: | :------: |
| max.queue.size | 8192 | 正整数值，指定在将数据库日志写入Kafka之前将更改事件从数据库日志中读取的阻塞队列的最大大小。例如，当写入Kafka较慢或者Kafka不可用时，此队列可以向oplog读取器提供反压。队列中出现的事件不包含在此连接器定期记录的偏移中。默认为8192，并且应始终大于max.batch.size属性中指定的最大批量大小。 |
| max.batch.size | 2048 | 正整数值，指定在此连接器的每次迭代期间应处理的每批事件的最大大小。默认为2048。 |
| poll.interval.ms | 1000 | 正整数值，指定连接器在每次迭代期间应出现的毫秒数，以显示新的更改事件。默认为1000毫秒或1秒。 |
| connect.backoff.initial.delay.ms | 1000 | 正整数值，指定在第一次尝试失败的连接尝试后或没有主节点可用时尝试重新连接到主节点时的初始延迟。默认为1秒（1000毫秒）。 |
| connect.backoff.max.delay.ms | 1000 | 正整数值，指定在重复失败的连接尝试后或没有主节点可用时尝试重新连接到主节点时的最大延迟。默认为120秒（120,000毫秒）。 |
| connect.max.attempts | 16 | 正整数值，指定在发生异常并且任务中止之前，对副本集主要连接尝试失败的最大次数。缺省值为16，其与默认值connect.backoff.initial.delay.ms和connect.backoff.max.delay.ms失效之前导致仅有20多分钟的尝试。 |
| mongodb.members.auto.discover | true | 布尔值，指定'mongodb.hosts'中的地址是否应该用于发现集群或副本集（true）的所有成员的种子，或者是否mongodb.hosts应该按原样使用地址（false）。默认值是true并且应该在所有情况下使用，除非MongoDB 由代理提供 |

```markdown
安装debezium-connector-mongodb
启动zookeeper   zookeeper-server-start zookeeper.properties

启动kafka       kafka-server-start    server.properties

启动schema-registry-start schema-registry.properties

export CLASSPATH=/path/to/my/connectors/*
启动connect-standalone  /schema-registry/
connect-avro-standalone.properties kafka/connect-mongo-source.properties

启动kafka consumer  kafka-console-consumer --zookeeper localhost:2181 --topic test.test.test --from-beginning

schema-registry/connect-avro-distributed.properties添加debezium-connector-mongodb的plugin.path=

配置Debezium MongoDB connector
connect.json
{
  "name": "inventory-connector",  (1)
  "config": {
    "connector.class": "io.debezium.connector.mongodb.MongoDbConnector", (2)
    "mongodb.hosts": "rs0/192.168.99.100:27017", (3)
    "mongodb.name": "fullfillment", (4)
    "collection.whitelist": "inventory[.]*", (5)
  }
}
1 我们使用Kafka Connect服务注册时的连接器名称
2 MongoDB连接器类的名称
3 用于连接MongoDB副本集的主机地址
4 该MongoDB的副本集逻辑名称，形成一个命名空间产生的事件，并在kafka的topics，kafka连接架构名称中的所有名称使用，以及相应的Avro架构的命名空间时的Avro使用
5 与要监视的所有集合的集合名称空间（例如，dbName collectionName）匹配的正则表达式列表。这是可选的

加载连接就是配置connect-mongo-source.properties
curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:8084/connectors/ -d @/etc/kafka/mongodb.json

查看kafka的topics
kafka-topics --zookeeper localhost:2181 --list

db.getCollection("rent_houses").find({
    "price": { "$lte": 2000 },
    "title": { "$in": [ /转租/,/整租/ ] }
     })
```
