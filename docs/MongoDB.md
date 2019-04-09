# *MongoDB-4.X*

## 基础

```json
假设collection文档内容{ item: "test", qty: 25, status: "A", person: { age: 14, sex: "male", name: "cm" }, tags: [ "blue", "red" ] }

db.collection.find({});   // 查询所有documents
db.collection.find({person:{age:21,sex:"male"}});  //查询嵌入式文档
db.collection.find({"person.sex":"male"});  //匹配嵌入式文档某个字段
db.collection.find({tags:["blue", "red"]});   //匹配数组
db.collection.find({tags:"red"});  //匹配数组中的一个元素

views是只读的且无法重新命名，支持的操作有：
db.createCollection()
db.createView()

db.getCollection()
db.getCollectionInfos()
db.getCollectionNames()

db.collection.find()
db.collection.findOne()
db.collection.aggregate()
db.collection.countDocuments()
db.collection.estimatedDocumentCount()
db.collection.count()
db.collection.distinct()

```

### Capped Collections

Capped Collections是固定大小的集合，支持基于插入顺序插入和检索文档的高吞吐量操作。以类似于循环缓冲区的方式工作：一旦集合填充其分配的空间，它通过覆盖集合中最旧的文档为新文档腾出空间

每种BSON类型都有整数和字符串标识符，如下表所示
| Type | Number | Alias | Notes |
| :------: | :------: | :------: | :------: |
| Double | 1 | “double” |  |
| String | 2 | “string” |  |
| Object | 3 | “object” |  |
| Array | 4 | “array” |  |
| Binary data | 5 | “binData” |  |
| Undefined | 6 | “undefined” | Deprecated. |
| ObjectId | 7 | “objectId” |  |
| Boolean | 8 | “bool” |  |
| Date | 9 | “date” |  |
| Null | 10 | “null” |  |
| Regular Expression | 11 | “regex” |  |
| DBPointer | 12 | “dbPointer” | Deprecated. |
| JavaScript | 13 | “javascript” |  |
| Symbol | 14 | “symbol” | Deprecated. |
| JavaScript (with scope) | 15 | “javascriptWithScope” |  |
| 32-bit integer | 16 | “int” |  |
| Timestamp | 17 | “timestamp” |  |
| 64-bit integer | 18 | “long” |  |
| Decimal128 | 19 | “decimal” | New in version 3.4. |
| Min key | -1 | “minKey” |  |
| Max key | 127 | “maxKey” |  |

