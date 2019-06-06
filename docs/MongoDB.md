# *MongoDB-4.X*

## 基础

views是只读的且无法重新命名，对视图执行写操作会报错，视图支持的读操作有：

```js
db.collection.find()
db.collection.findOne()
db.collection.aggregate()
db.collection.countDocuments()
db.collection.estimatedDocumentCount()
db.collection.count()
db.collection.distinct()
```

视图使用基础集合的索引。由于索引位于基础集合上，因此无法直接在视图上创建，删除或重新构建索引，也无法在视图上获取索引列表。无法在视图使用$natural排序,如果视图是基于分片创建，则视图也被视为分片，无法为from字段$lookup和$graphlookup操作指定分片视图。
创建视图是可以指定排序规则，默认规则是“简单”二进制比较排序规则，多个视图做聚合操作，排序规则必须相同

find()对视图的操作不支持以下Projection操作符：

* $
* $elemMatch
* $slice
* $meta

### Capped Collections

Capped Collections是固定大小的集合，支持基于插入顺序插入和检索文档的高吞吐量操作。以类似于循环缓冲区的方式工作：一旦集合填充其分配的空间，它通过自动删除集合中最旧的文档为新文档腾出空间

无法从capped集合删除文档，要从集合删除文档的方法，使用drop()方法删除集合并重新创建，capped集合无法分片。创建capped集合，必须以字节为单位指定集合的最大大小。如果size字段小于或等于4096，则集合的上限为4096字节。否则，MongoDB将提高提供的大小，使其成为256的整数倍；还可以使用该max字段为集合指定最大文档数。该size参数总是必需的，即使你指定max的文件数量。如果集合在达到最大文档计数之前达到最大大小限制，MongoDB将删除旧文档

将集合转换为capped集合:db.runCommand({"convertToCapped": "collectionName", size: 10000});在操作期间保存数据库独占锁。锁定同一数据库的其他操作将被阻止，直到操作完成

capped替代方案：使用TTL(生存时间)索引从集合删除过期数据；这些索引允许您根据日期类型字段的值和索引的TTL值使正常集合中的数据到期和删除。TTL索引与capped集合不兼容

每种BSON类型都有整数和字符串标识符，如下表所示
| Type | Number | Alias | Notes |
| :------: | :------: | :------: | :------: |
| Double | 1 | “double” |  |
| String | 2 | “string” |  |
| Object | 3 | “object” |  |
| Array | 4 | “array” |  |
| Binary data | 5 | “binData” |  |
| Undefined | 6 | “undefined” | 已废弃 |
| ObjectId | 7 | “objectId” |  |
| Boolean | 8 | “bool” |  |
| Date | 9 | “date” |  |
| Null | 10 | “null” |  |
| Regular Expression | 11 | “regex” |  |
| DBPointer | 12 | “dbPointer” | 已废弃 |
| JavaScript | 13 | “javascript” |  |
| Symbol | 14 | “symbol” | 已废弃 |
| JavaScript (with scope) | 15 | “javascriptWithScope” |  |
| 32-bit integer | 16 | “int” |  |
| Timestamp | 17 | “timestamp” |  |
| 64-bit integer | 18 | “long” |  |
| Decimal128 | 19 | “decimal” | New in version 3.4. |
| Min key | -1 | “minKey” |  |
| Max key | 127 | “maxKey” |  |

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

1. 按照它们在BSON对象中出现的顺序递归地比较键值对。
2. 比较关键字段名称。
3. 如果键字段名称相等，则比较字段值。
4. 如果字段值相等，则比较下一个键/值对（返回步骤1）。没有进一步对的对象小于具有更多对的对象。

BinData排序顺序为：

1. 首先，数据的长度或大小。
2. 然后，通过BSON一字节子类型。
3. 最后，通过数据，执行逐字节比较。

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
      destination: file #mogodb日志的产生方式:file=指定的文件,syslog系统日志，如果不指定，则指向标准输出流。
      logAppend: true   #在mongodb重启时,继续往已有的日志文件中追加内容，而不是新建一个文件.
      path: /data/mongodb/mongod.log    #日志文件的输出路径，mongo将在指定的位置创建日志文件.
      logRotate: reopen   #3.0.0,两个值:rename=重命名日志文件，reopen=关闭并重新打开日志文件.如果使用reopen则要使systemLog.logAppend=true.
      timeStampFormat: iso8601-utc #日志中时间戳的格式:ctime=Wed Dec 31 18:17:54.811,iso8601-utc=1970-01-01T00:00:00.000Z,#iso8601-local=本地时间格式
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
      pidFilePath: /data/mongodb/mongod.pid  # 指定保存PID的文件。
      #时区文件:https://downloads.mongodb.org/olson_tz_db/timezonedb-latest.zip
      #timeZoneInfo:    #加载数据库时区的完整路径，如果不指定，则采用默认时区。

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
       #指定线程对客户端请求的执行模型：synchronous=使用同步网络并在每个链接的基础上管理其网络线程池.adaptive:使用新的实验性的异步网络模式.
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
       #启用或禁用基于角色的控制访问(RBAC)来控制每个用户对数据库资源和操作的访问。
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
          #port: <string> #KMIP服务器正在监听的端口号。
          #clientCertificateFile: <string>  #包含用于向KMIP服务器验证MongoDB的客户端证书路径的字符串。
          #clientCertificatePassword: <string>  #用于解密客户端证书（即security.kmip.clientCertificateFile）的密码
          #serverCAFile: <string> #CA文件的路径。用于验证与KMIP服务器的安全客户端连接。
          #
          #注意:security.ldap只能用于mongodb的企业版本,且版本需要高于3.4.
          #
    #   ldap:
    #      servers: <string>  #通过LDAP确定执行操作的用户是否被授权，多个服务器之间可以通过[,]分割.
    #      bind:
    #         method: <string>  #该方法mongod或mongos用于向LDAP服务器进行身份验证。
    #                                - simple- mongod或mongos使用简单的身份验证。
    #                                - sasl- mongod或mongos使用SASL协议进行身份验证
    #         saslMechanisms: <string>  #以逗号分隔的SASL机制列表.
    #         queryUser: <string> #绑定LADP查询用户.
    #         queryPassword: <string> #绑定LADP服务器的密码.
    #         useOSDefaults: <boolean>  #连接到LDAP服务器时，允许mongod或mongos使用Windows登录凭据进行身份验证或绑定。
    #      transportSecurity: <string>  #默认情况下，mongod或mongos创建一个到LDAP服务器的TLS / SSL安全连接.
    #      timeoutMS: <int> #等待LDAP服务器响应请求时间(毫秒).
    #      userToDNMapping: <string>  #用于验证的用户名映射到LDAP专有名称（DN）。
    #                                  -  match=ECMAScript格式的正则表达式（正则表达式）与提供的用户名匹配。
    #                                          每个括号包围的部分表示由substitutionor 使用的正则表达式捕获组ldapQuery。
    #                                  - substitution=LDAP专有名称（DN）格式化模板。
    #                                  - ldapQuery=LDAP查询格式化模板.
    #      authz:
    #         queryTemplate: <string>   #一个相对的LDAP查询URL.
    #
    # 属性设置选项
    #
    #setParameter:
    #     ldapUserCacheInvalidationInterval: <int> #用于使用LDAP授权mongod或mongos使用LDAP授权的服务器(秒)。
    #   <parameter1>: <value1>
    #   <parameter2>: <value2>

    # 存储设置
    storage:
       dbPath:  /data/mongodb #mongod实例存储数据的目录,默认值：/data/db.
       indexBuildRetry: true #指定mongodb在下次启动时是否重建不完整的索引,默认:true.
    #   repairPath: <string>
    #          MongoDB在--repair操作过程中将使用的工作目录 。
    #          当--repair完成后,storage.repairPath目录是空的,并且 dbPath包含了修复的文件。
    #          该storage.repairPath设置仅适用于mongod。
    #          仅适用于mongod使用MMAPv1存储引擎的实例。
       journal:
          enabled: true #启用或禁用耐久性日志以确保数据文件保持有效并可恢复。64bit默认开启,32bit默认关闭.
    #                     不适用于mongod使用内存存储引擎的实例 。
          commitIntervalMs: 100 #mongod日志操作之间进程允许的最大时间量(毫秒),范围是1-500,默认值是100，
    #                                  该值太低会增加日志的持久性，但是会牺牲磁盘性能.
    #                                  该storage.journal.commitIntervalMs设置仅适用于mongod。
    #                                  不适用于mongod使用内存存储引擎的实例 。
    #   directoryPerDB: <boolean>    #MongoDB使用单独的目录来存储每个数据库的数据。
    #                                  目录位于storage.dbPath目录下，并且每个子目录名称都与数据库名称相对应。
    #                                  3.0版本有变更.
    #   syncPeriodSecs: <int>  #在MongoDB将数据通过sync操作刷新到数据文件之前可以传递的时间量,默认60s.
    #                              不要在生产系统上设置此值。在几乎所有情况下，您都应该使用默认设置。
    #   engine: <string>  #mongod数据库的存储引擎。
    #                         仅在MongoDB Enterprise中可用，3.0版本中的新功能,从MongoDB 3.2开始，wiredTiger是默认值。
    #                         mmapv1=指定MMAPv1存储引擎。
    #                         wiredTiger=指定WiredTiger存储引擎。
    #                         inMemory=指定内存中存储引擎。
       mmapv1:
          preallocDataFiles: true  #启用或禁用数据文件的预分配。默认情况下，MongoDB不预先分配数据文件。
          nsSize: 16 #命名空间文件的默认大小，它是以文件结尾的文件.ns。每个集合和索引都被视为一个名称空间。
    #                        使用此设置控制新创建的名称空间文件的大小。该选项对现有文件没有影响。
    #                        命名空间文件的最大大小是2047兆字节。默认值16兆字节提供了大约24,000个名称空间。
          quota:
             enforced: false  #启用或禁用强制每个数据库可能具有的数字数据文件的最大限制。
    #                                 使用该storage.mmapv1.quota.enforced选项运行时，MongoDB每个数据库最多有8个数据文件。
    #                                 通过调整storage.quota.maxFilesPerDB来调整这个限制。
             maxFilesPerDB: 8 #每个数据库的数据文件数量限制，默认是8.该设置仅适用于设置仅适用于mongod.
          smallFiles: false #如果你有大量的数据库，每个保存少量数据。MongoDB使用较小的默认文件大小。
    #                         该storage.mmapv1.smallFiles选项可减少数据文件的初始大小，并将最大大小限制为512兆字节。
    #                         storage.mmapv1.smallFiles也可以将每个日志文件的大小从1千兆字节减少到128兆字节。
    #      journal:
    #         debugFlags: <int>   #提供测试功能。不用于一般用途，并且会在系统异常关闭的情况下影响数据文件的完整性。
    #         commitIntervalMs: <num> #MongoDB 3.2弃用该 torage.mmapv1.journal.commitIntervalMs设置。
    #                                    改为使用storage.journal.commitIntervalMs。
    #   wiredTiger:
    #      engineConfig:
    #         cacheSizeGB: <number>    #WiredTiger将用于所有数据的内部缓存的最大大小,默认采用RAB-1GB的50%和256MB的最大值.
    #                                      尽可能不要超过最大值.
    #         journalCompressor: <string>  #用于压缩WiredTiger日志数据的压缩类型。none,snappy,zlib.version:3.0
    #         directoryForIndexes: true #mongod将索引存储在名为的子目录中 index,
    #                                        并将集合数据存储在名为的子目录中 collection。
    #      collectionConfig:
    #         blockCompressor: <string>  #用于压缩收集数据的默认压缩类型。none,snappy,zlib.version:3.0
          indexConfig:
             prefixCompression: true #启用或禁用索引数据的前缀压缩。
    #   inMemory:    #仅在MongoDB Enterprise中可用。
    #      engineConfig:
    #         inMemorySizeGB: <number> #为内存存储引擎数据分配的最大内存量，
    #                                      包括索引，oplog（如果 mongod是副本集，副本集或分片群集元数据的一部分）等。
    #                                       默认情况下，内存存储引擎使用50％的物理RAM减1 GB。
    #运行分析配置
    operationProfiling:
       mode: slowOp   #指定应该分析哪些操作，分析操作可能会影响性能.
    #                        - off=分析器已关闭，并且不收集任何数据。这是默认的分析器级别。
    #                        - slowOp=分析器收集比花费时间更长的操作的数据slowms。
    #                        - all=分析器收集所有操作的数据。
       slowOpThresholdMs: 100 #慢操作时间阈值，默认100.
    #   slowOpSampleRate: <double> #慢操作采样率，0-1之间，包含0,1.
    #
    #复制设置
    #
    #replication:
    #   oplogSizeMB: <int> #复制操作日志的最大大小（MB）
    #   replSetName: <string>  #mongod一部分的副本集的名称。副本集中的所有主机必须具有相同的集名称。仅适用于mongod.
    #   secondaryIndexPrefetch: <string>  #仅适用于mmapv1 存储引擎.
    #                                        在应用oplog中的操作之前，副本集的次要成员加载到内存中的索引。
    #                                       - none=辅助程序不会将索引加载到内存中。
    #                                       - all=辅助程序加载与操作相关的所有索引。
    #                                       - _id_only=Secondaries不会将额外的索引加载到已有_id索引之外的内存中。
    #   enableMajorityReadConcern: <boolean>  ## Deprecated in 3.6
    #                                            从MongoDB 3.6开始，"majority"始终启用读取关注，并且此设置不起作用。
    #    localPingThresholdMs: <int>  #mongos用于确定哪些辅助副本集成员从客户端传递读取操作的ping时间（以毫秒为单位）。
    #                                    默认值15对应于所有客户端驱动程序中的默认值。
    #
    # 分片设置
    #
    #sharding:
    #   clusterRole: <string>    #mongod实例在分片群集中扮演的角色
    #                           - configsvr=将此实例作为配置服务器启动。默认情况下，该实例在27019端口上启动。
    #                           - shardsvr=启动此实例为碎片。默认情况下，该实例在27018端口上启动。
    #                            设置sharding.clusterRole要求mongod 实例与复制一起运行。
    #                            要将实例部署为副本集成员，请使用该replSetName 设置并指定副本集的名称。
    #   archiveMovedChunks: false  #从3.2开始，MongoDB false用作默认值。在块迁移期间，分片不会保存从分片迁移的文档。
    #   configDB: <configReplSetName>/cfg1.example.net:27017, cfg2.example.net:27017,...   #该配置服务器用于 分片群集。
    #
    # 审计日志设置,仅在MongoDB Enterprise中可用。
    #
    #auditLog:
    #   destination: file  #设置后，auditLog.destination启用使用指定格式往指定目标发送所有审核事件。
    ##                             - syslog=以JSON格式输出审计事件到系统日志.
    ##                             - console=以stdoutJSON格式输出审计事件。
    ##                             - file=将审核事件输出到以中指定auditLog.path格式auditLog.format指定的文件 。
    #   format: JSON   #用于审计的输出文件的格式,当auditLog.destination=file时,可以指定该值(JSON/BSON).
    #   path: <string> #输出文件的绝对或相对路径,当auditLog.destination=file时,可以指定该值.
    #   filter: <string> #过滤指定类型的操作的记录。
    #
    # 简单网络管理协议配置
    #
    #snmp:
    #   subagent: <boolean>  #该snmp.subagent设置仅适用于mongod。
    #                        如果snmp.subagent是true，SNMP运行的子代理。
    #   master: <boolean>    #当snmp.master是true，SNMP运行作为一个主站。·
    #
    # 文本搜索选项 ,仅在MongoDB Enterprise中可用。
    #
    #basisTech:
    #   rootDirectory:<string> #指定Basis Technology Rosette语言学平台安装根目录的路径，以支持用于文本搜索操作的其他语言。

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
| show log <[logname]> | db.adminCommand({ 'getLog' : '<[logname]>' }) |
| show logs | db.adminCommand({ 'getLog' : '*' }) |
| it | cursor = db.collection.find() if ( cursor.hasNext() ){ cursor.next(); } |

## MongoDB CRUD 操作

### 插入文件

```js
db.collection.insertOne()           // 插入一个文档
db.collection.insertMany()          // 插入多个文档
db.collection.insert()              // 插入一个或多个文档
db.collection.update()              // when used with the **upsert: true** option
db.collection.updateOne()           // when used with the **upsert: true** option
db.collection.updateMany()          // when used with the **upsert: true** option
db.collection.findAndModify()       // when used with the **upsert: true** option
db.collection.findOneAndUpdate()    // when used with the **upsert: true** option
db.collection.findOneAndReplace()   // when used with the **upsert: true** option
db.collection.save()
db.collection.bulkWrite()
```

### 查询文档

```js
db.collection.find({person:{age:21,sex:"male"}});  // 查询嵌入/嵌套式文档
db.collection.find({"person.sex":"male"});  // 匹配嵌入式文档某个字段

db.collection.find({tags:["blue", "red"]});   // 匹配数组
db.collection.find({tags:"red", "blank"});  // 按顺序匹配数组中的两个元素
db.collection.find({tags: {$all:["red","blank"]}})  // 不按顺序匹配数组中两个元素(查询只要包含这两个元素的所有结果)
db.collection.find({age: {$gt: 1, $lt: 2}})   // 集合中age数组中一个元素满足大于1，另一个元素满足小于2，或者单个元素满足两个条件
db.collection.find({age: {$elemMatch: {$gt: 1, $lt: 2}}})   // 使用$elemMatch运算符在数组的元素上指定多个条件，以便至少一个数组元素满足所有指定的条件。此查询条件表示age数组包含至少一个大于1且小于2的元素
db.collection.find( { "tags": { $size: 3 } } )  // 查询条件为tags数组中元素长度为3
db.collection.find( { "tags.2": { "$exists": 1} } ) // 查询条件为tags数组中元素数量大于2(实际利用数组索引来实现的)

查询嵌套在数组中的文档示例：
db.inventory.insertMany( [
   { item: "journal", instock: [ { warehouse: "A", qty: 5 }, { warehouse: "C", qty: 15 } ] },
   { item: "notebook", instock: [ { warehouse: "C", qty: 5 } ] },
   { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 15 } ] },
   { item: "planner", instock: [ { warehouse: "A", qty: 40 }, { warehouse: "B", qty: 5 } ] },
   { item: "postcard", instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
]);

db.inventory.find( { "instock": { warehouse: "A", qty: 5 } } )  // 匹配在invertory集合中instock数组元素与指定文档匹配的文档，可以理解为一个文档作为数组的元素；注意：整个嵌入/嵌套文档上的等式匹配需要指定文档的完全匹配
db.inventory.find( { "instock": { $elemMatch: { qty: 5, warehouse: "A" } } } )  // 查询嵌入文档数组instock，嵌入的到此数组的文档至少一个qty值为5，warehost值为A
db.inventory.find( { "instock": { $elemMatch: { qty: { $gt: 10, $lte: 20 } } } } )  // 查询嵌入文档数组instock，嵌入的到此数组的文档qty值大于10且小于等于20
db.inventory.find( { "instock.qty": { $gt: 10,  $lte: 20 } } )  // 查询嵌套在instock数组中的任何文档的qty字段大于10并且数组中的任何文档（但不一定是相同的嵌入文档）的qty字段小于或等于20
db.inventory.find( { "instock.qty": 5, "instock.warehouse": "A" } ) // 查询嵌套在instock数组中的至少一个嵌套文档包含字段qty值为5，且至少一个嵌套文档warehouse字段值为A(但不一定是相同的嵌套文档)
db.inventory.find({"instock.qty":{$lte:20}})  // 查询嵌入到instock数组的文档qty字段值小于20的文档
db.inventory.find({"instock.0.qty":{$lte:20}})  // 查询嵌入到instock数组的文档，索引为0且qty字段值小于20的文档

db.inventory.find( { status: "A" }, { item: 1, status: 1 } )  // 查询集合中status值为A，只返回item、status及_id字段
db.inventory.find( { status: "A" }, { status: 0, instock: 0 } ) // 查询集合中status值为A，结果排除status和instock字段
db.inventory.find( { status: "A" }, { item: 1, status: 1, "size.uom": 1 } ) // 查询集合中status值为A，结果只包括item、status字段及嵌入文档size的uom字段
db.inventory.find( { status: "A" }, { item: 1, status: 1, instock: { $slice: -1 } } )   // 查询集合中status为A，结果只包含item、status字段instock，$slice运算符返回instock数组最后一个元素

db.inventory.find({item: null})  // 查询item字段为null，或不包含item字段的文档
db.inventory.find({item: {$type: 10}})  // 只查询item字段值类型是10(即null类型)的文档
db.inventory.find({item: {$exists: false}}) // 只查询不包含item字段的文档
```

### 更新文档

```js
db.inventory.updateOne({ item: "paper" },{$set: { "size.uom": "cm", status: "P" },$currentDate: { lastModified: true }})  // 更新一个文档item字段值为paper，将size数组中uom更新为cm，status更新为P
db.inventory.updateMany({ "qty": { $lt: 50 }},{$set: { "size.uom": "in", status: "Z" },$currentDate: { lastModified: true }})   // 更新qty字段值小于50，更新size数组uom字段值为in，status字段值为Z，用$currentDate操作更新lastModified字段为当前时间，没有此字段
db.collection.update()  // 更新或替换与指定过滤器匹配的单个文档，或更新与指定过滤器匹配的所有文档
以下几种方法也可以用来更新文档
db.collection.findOneAndReplace()
db.collection.findOneAndUpdate()
db.collection.findAndModify()
db.collection.save()
db.collection.bulkWrite()
```

### 删除文档

```js
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

```js
db.collection.bulkWrite() // 此方法可以批量插入、更新、删除操作。批量写操作可以有序也可无序。
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

```js
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

注意：如果客户端发出写入操作在超过serverSelectionTimeoutMS没有响应，有可能在客户端重新响应时(没有重新启动)，则可能会再重试写入操作。

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

```js
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

```js
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
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |


```

```js
// write-concern
{ w: <value>, j: <boolean>, wtimeot: <number> }
w选项：确认写入请求传递给mongod实例的数量，1表示传递写入操作给一个standalone或副本的一个主节点；0写入操作没有确认，w大于1是因为需要来自其他辅助数据节点的确认
j选项：确认写操作已写入磁盘日志(on-disk journal)
wtimeout:写入的时间限制(毫秒为单位)，wtimeout仅限于w大于1的情况

```

### MongoDBCRUD概念

```js
// 原子性和事务
当单个写操作(如：db.collection.updateMany())修改多个文件时，每个文件的修改都是原子性的，但是整个操作不是！

// 并发控制
并发控制允许多个程序同时运行，而不会导致数据不一致或冲突
一种方法是在字段上创建唯一索引，该索引只有唯一值。可以防止插入、更新创建重复数据。在多个字段创建唯一索引，以强制该字段组合的唯一性。
另一种方法是在为写操作的查询谓词中指定字段预期的当前值

// 读取隔离,一致性和Recency


```

### 分布式查询、查询优化、查询性能、优化查询

```js
oplog是对数据集操作的可重现序列,索引可以涵盖嵌入式文档中字段的查询；如果索引跟踪哪个或哪些字段导致索引为多建索引，则多建索引可以覆盖非数组字段上的查询。(多建索引不能覆盖数组字段上的查询)；地理空间索引无法覆盖索引。要确定一个查询是否为覆盖查询，使用db.collection.explain()或explain()

使用Database Profiler评估对数据库的操作，

// 优化查询
1. 创建支持查询的索引
2. 如经常对timestamp字段进行排序性查询，则可以通过在timestamp字段创建索引，查询时使用db.collection.find().sort({ timestamp: -1})更快得到结果，因为MongoDB可以按照升序或降序读取索引。
3. 索引支持查询、更新操作和聚合管道的某些阶段；如符合以下条件，则BinData类型的索引建可以更有效的存储在索引中。
  * 二进制子类型值得范围为0-7或128-135
  * 字节数组的长度为：0,1,2,3,4,5,6,7,8,10,12,14,16,20,24或32
4. 限制查询结果数，通过limit()方法减少查询对网络资源的消耗。通常和sort()结合使用
5. 使用预测返回必要的数据；只需要文档的部分字段时，只返回所需的字段可以获取更好的性能。
6. 使用$hint选择一个特定的指数，大多数情况下，查询优化器会为特定操作选择最佳的索引，但是也可以使用$hint()方法强制使用特定索引。hint()支持性能测试或在某些查询，在一些查询中，必须选择一个字段或字段必须包含在多个索引中。
7. 使用增量运算符执行操作服务端，MongoDB的$inc运算符来增加或减少文档中的值，该操作增加服务端字段的值，作为选择文档的替代方法，在客户端进行简单修改，然后将整个文档写入服务器。$inc操作还可以帮助避免竞争条件，这将导致两个应用实例对同一个文件查询时，手动增加一个字段，并保存整个文件回到同一时间。

// 写操作性能

集合中每个索引都会增加性能的开销；因集合中每个insert和delete写入操作。MongoDB都会从目标集合的每个索引中插入或删除对应的文档键。update操作可能会导致更新在这个集合中索引的子集，取决于更新时这个键的影响。
// 注意：如果写入操作中涉及的文档包含在索引中，MongoDB则仅更新sparse或partial索引(sparse和partial为MongoDB的索引类型)

某些更新操作可能增加文档的大小。如:向文档增加字段。对于MMAPv1引擎，如果更新操作导致文档超过当前分配的记录大小。MongoDB会将文档重新定位到磁盘上。并留有足够的连续空间来保存文档。需要重定位的更新比没有重定位的更新花费的时间更长，特别是在集合具有索引的情况下。如果集合具有索引，MongoDB必须更新所有索引条目。因此，对于具有许多索引的集合，此移动将影响写入吞吐量

// explain结果
Stages描述了操作--如：
COLLSCAN          用于集合扫描
IXSCAN            用于扫描索引键
FETCH             用于检索文档
SHARD_MERGE       用于合并分片的结果
SHARDING_FILTER   用于从分片中过滤掉孤立文档

// queryPlanner
explain.queryPlanner    // 包含查询优化器选择查询计划的信息
explain.queryPlanner.namespace


```

## 聚合(Aggregation)

## 数据模型

## Transactions

## 索引

## 安全

## Change Streams

## 复制(Replication)

## 分片(Sharding)

## 管理(Administration)

## 存储引擎(Storage)

## MongoDB-->kafka

```intro
Debezium的MongoDB Connector可以监视MongoDB副本集或MongoDB分片集群；MongoDB复制的工作原理是让Master记录其oplog（或操作日志）中的更改，然后每个Slave节点读取主要的oplog并按顺序应用所有操作到他们自己的文档。将新服务器添加到副本集时，该服务器首先执行初始同步主数据库上的所有数据库和集合，然后读取主数据库的oplog以应用自开始同步以来可能已经进行的所有更改。这个新服务器在赶上Master节点oplog的尾部时成为Slave服务器（并且能够处理查询）

Debezium MongoDB Connector使用相同的复制机制，但它实际上并不成为副本集的成员，Connector始终连接副本集的主节点来跟踪oplog，MongoDB重新选举主节点后，Connector会自动切换到新的主节点。Connector会记录oplog的位置，来实现增量同步。

Debezium MongoDB Connector和副本集一起使用时，只需将副本集的地址作为Connector的seed地址mongodb.hosts即可。Connector使用seed连接副本集，获取到完整的成员集及确定主节点后。Connector连接到主节点并从其oplog捕获更改。

Connector和分片集群一起使用时，使用config服务器的地址配置Connector连接。针对分片群集运行连接器时，请使用tasks.max大于副本集数量的值,这将允许连接器为每个副本集创建一个任务，并让Kafka Connect在所有可用的工作进程中协调，分发和管理任务

mongodb.name，它充当MongoDB副本集或分片集群的逻辑名称,连接器以多种方式使用逻辑名称：作为所有主题名称的前缀，以及记录每个副本集的oplog位置时的唯一标识符。
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
MongoDB连接器不会明确确定事件的topic分区,相反，它允许Kafka根据密钥确定分区。您可以通过在Kafka Connect工作程序配置中定义Partitioner实现的名称来更改Kafka的分区逻辑。
```

## Events

```info
MongoDB连接器生成的数据更改事件都有一个key和value。下面概述了这些key和value的结构
从kafka0.10开始，可以使用消息密钥进行记录并创建消息的时间戳(由生产者记录)或由kafka写入日志的值进行评估
如果事件的来源在结构上发生变化或连接器改变，事件的结构可能会随着时间推移而发生变化。对于消费者可能很难处理。因此kafka每个事件都自成一体，每个消息键值都有两部分:a schema和payload。当payload包含数据时，schema描述payload的结构。

更改事件的key：对于给定集合，更改事件的key将是包含单个id字段的结构。它的值是文档的标识符表示为字符串。

```

MongoDB不可用时，重新尝试连接由三个属性控制：

* connect.backoff.initial.delay.ms -- 第一次尝试重新连接之前的延迟，默认值为1秒（1000毫秒）
* connect.backoff.max.delay.ms --- 尝试重新连接之前的最大延迟，默认值为120秒（120,000毫秒）
* connect.max.attempts ------------ 产生错误之前的最大尝试次数，默认值为16

## Connector属性

| 属性 | 默认 | 描述 |
| :--: | :--: | :--: |
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
0.7.3及以后 | true | 控制是否应在删除事件后生成逻辑删除事件。
当true删除操作是通过删除事件和随后的墓碑事件表示。如果false只发送删除事件。
发送逻辑删除事件（默认行为）允许Kafka在删除源记录后完全删除与给定密钥有关的所有事件。 |"
| snapshot.delay.ms0.9.0及更高版本 |  | 连接器在启动后拍摄快照之前应等待的间隔（以毫秒为单位）;可用于在群集中启动多个连接器时避免快照中断，这可能会导致连接器重新平衡 |

以下高级配置属性具有良好的默认值，可在大多数情况下使用，因此很少需要在连接器的配置中指定。
| 属性 | 默认 | 描述 |
| :--: | :--: | :--: |
| max.queue.size | 8192 | 正整数值，指定在将数据库日志写入Kafka之前将更改事件从数据库日志中读取的阻塞队列的最大大小。例如，当写入Kafka较慢或者Kafka不可用时，此队列可以向oplog读取器提供反压。队列中出现的事件不包含在此连接器定期记录的偏移中。默认为8192，并且应始终大于max.batch.size属性中指定的最大批量大小。 |
| max.batch.size | 2048 | 正整数值，指定在此连接器的每次迭代期间应处理的每批事件的最大大小。默认为2048。 |
| poll.interval.ms | 1000 | 正整数值，指定连接器在每次迭代期间应出现的毫秒数，以显示新的更改事件。默认为1000毫秒或1秒。 |
| connect.backoff.initial.delay.ms | 1000 | 正整数值，指定在第一次尝试失败的连接尝试后或没有主节点可用时尝试重新连接到主节点时的初始延迟。默认为1秒（1000毫秒）。 |
| connect.backoff.max.delay.ms | 1000 | 正整数值，指定在重复失败的连接尝试后或没有主节点可用时尝试重新连接到主节点时的最大延迟。默认为120秒（120,000毫秒）。 |
| connect.max.attempts | 16 | 正整数值，指定在发生异常并且任务中止之前，对副本集主要连接尝试失败的最大次数。缺省值为16，其与默认值connect.backoff.initial.delay.ms和connect.backoff.max.delay.ms失效之前导致仅有20多分钟的尝试。 |
| mongodb.members.auto.discover | true | 布尔值，指定'mongodb.hosts'中的地址是否应该用于发现集群或副本集（true）的所有成员的种子，或者是否mongodb.hosts应该按原样使用地址（false）。默认值是true并且应该在所有情况下使用，除非MongoDB 由代理提供 |

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
1 我们使用Kafka Connect服务注册时的连接器名称。
2 MongoDB连接器类的名称。
3 用于连接MongoDB副本集的主机地址
4 该MongoDB的副本集逻辑名称，形成一个命名空间产生的事件，并在kafka的topics，kafka连接架构名称中的所有名称使用，以及相应的Avro架构的命名空间时的Avro使用。
5 与要监视的所有集合的集合名称空间（例如，dbName collectionName）匹配的正则表达式列表。这是可选的。

加载连接就是配置connect-mongo-source.properties
curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:8084/connectors/ -d @/etc/kafka/mongodb.json

查看kafka的topics
kafka-topics --zookeeper localhost:2181 --list