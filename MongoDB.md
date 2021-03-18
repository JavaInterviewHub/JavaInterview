## MongoDB

* [1.什么是MongoDB？](#1什么是mongodb)
* [2.MongoDB的优势有哪些](#2mongodb的优势有哪些)
* [3.什么是集合(表)？](#3什么是集合表)
* [4.什么是文档(记录)](#4什么是文档记录)
* [5.为什么用MOngoDB？](#5为什么用mongodb)
* [6.在哪些场景使用MongoDB](#6在哪些场景使用mongodb)
* [7.MongoDB中的命名空间是什么意思?](#7mongodb中的命名空间是什么意思)
* [8.MongoDB中的分片什么意思](#8mongodb中的分片什么意思)
* [9.为什么要在MongoDB中使用分析器](#9为什么要在mongodb中使用分析器)
* [10.MongoDB支持主键外键关系吗](#10mongodb支持主键外键关系吗)
* [11.MongoDB支持哪些数据类型](#11mongodb支持哪些数据类型)
* [12.为什么要在MongoDB中用"Code"数据类型](#12为什么要在mongodb中用code数据类型)
* [13.为什么要在MongoDB中用"Regular Expression"数据类型](#13为什么要在mongodb中用regular-expression数据类型)
* [14.为什么在MongoDB中使用"Object ID"数据类型](#14为什么在mongodb中使用object-id数据类型)
* [15."ObjectID"有哪些部分组成](#15objectid有哪些部分组成)
* [16.在MongoDb中什么是索引](#16在mongodb中什么是索引)
* [17.在MongoDB中什么是副本集](#17在mongodb中什么是副本集)
* [18.MongoDB支持存储过程吗？如果支持的话，怎么用？](#18mongodb支持存储过程吗如果支持的话怎么用)
* [19.如何理解MongoDB中的GridFS机制，MongoDB为何使用GridFS来存储文件？](#19如何理解mongodb中的gridfs机制mongodb为何使用gridfs来存储文件)
* [20.为什么MongoDB的数据文件很大？](#20为什么mongodb的数据文件很大)

#### 1.什么是MongoDB？

MongoDB是一个文档数据库，提供好的性能，领先的非关系型数据库。采用BSON存储文档数据。
BSON（）是一种类json的一种二进制形式的存储格式，简称Binary JSON
相对于json多了date类型和二进制数组。

#### 2.MongoDB的优势有哪些

面向文档的存储：以 JSON 格式的文档保存数据。
任何属性都可以建立索引。
复制以及高可扩展性。
自动分片。
丰富的查询功能。
快速的即时更新。

#### 3.什么是集合(表)？

集合就是一组 MongoDB文档。它相当于关系型数据库（RDBMS）中的表这种概念。集合位于单独的一个数据库中。
一个集合内的多个文档可以有多个不同的字段。一般来说，集合中的文档都有着相同或相关的目的。

#### 4.什么是文档(记录)

文档由一组key value组成。文档是动态模式,这意味着同一集合里的文档不需要有相同的字段和结构。在关系型数据库中table中的每一条记录相当于MongoDB中的一个文档。

#### 5.为什么用MOngoDB？

架构简单
没有复杂的连接
深度查询能力,MongoDB支持动态查询。
容易调试
容易扩展
不需要转化/映射应用对象到数据库对象
使用内部内存作为存储工作区,以便更快的存取数据。

#### 6.在哪些场景使用MongoDB

大数据
内容管理系统
移动端Apps
数据管理

#### 7.MongoDB中的命名空间是什么意思?

mongodb存储bson对象在丛集(collection)中，数据库名字和丛集名字以句点连结起来叫做名字空间(namespace)。

一个集合命名空间又有多个数据域(extent)，集合命名空间里存储着集合的元数据，比如集合名称，集合的第一个数据域和最后一个数据域的位置等等。而一个数据域由若干条文档(document)组成，每个数据域都有一个头部，记录着第一条文档和最后一条文档的为知，以及该数据域的一些元数据。extent之间，document之间通过双向链表连接。

索引的存储数据结构是B树，索引命名空间存储着对B树的根节点的指针。

#### 8.MongoDB中的分片什么意思

分片是将数据水平切分到不同的物理节点。当应用数据越来越大的时候，数据量也会越来越大。当数据量增长时，单台机器有可能无法存储数据或可接受的读取写入吞吐量。利用分片技术可以添加更多的机器来应对数据量增加以及读写操作的要求。

#### 9.为什么要在MongoDB中使用分析器

mongodb中包括了一个可以显示数据库中每个操作性能特点的数据库分析器.通过这个分析器你可以找到比预期慢的查询(或写操作);利用这一信息,比如,可以确定是否需要添加索引。

#### 10.MongoDB支持主键外键关系吗

默认MongoDB不支持主键和外键关系。 用Mongodb本身的API需要硬编码才能实现外键关联，不够直观且难度较大。

#### 11.MongoDB支持哪些数据类型

String
Integer
Double
Boolean
Object
Object ID
Arrays
Min/Max Keys
Datetime
Code
Regular Expression等

#### 12.为什么要在MongoDB中用"Code"数据类型

"Code"类型用于在文档中存储 JavaScript 代码。

#### 13.为什么要在MongoDB中用"Regular Expression"数据类型

"Regular Expression"类型用于在文档中存储正则表达式

#### 14.为什么在MongoDB中使用"Object ID"数据类型

"ObjectID"数据类型用于存储文档id

#### 15."ObjectID"有哪些部分组成

一共有四部分组成:时间戳、客户端ID、客户进程ID、三个字节的增量计数器

#### 16.在MongoDb中什么是索引

索引用于高效的执行查询,没有索引的MongoDB将扫描整个集合中的所有文档,这种扫描效率很低,需要处理大量的数据。
索引是一种特殊的数据结构,将一小块数据集合保存为容易遍历的形式.索引能够存储某种特殊字段或字段集的
值,并按照索引指定的方式将字段值进行排序。

#### 17.在MongoDB中什么是副本集

在MongoDB中副本集由一组MongoDB实例组成，包括一个主节点多个次节点，MongoDB客户端的所有数据都写入主节点(Primary),副节点从主节点同步写入数据，以保持所有复制集内存储相同的数据，提高数据可用性。

#### 18.MongoDB支持存储过程吗？如果支持的话，怎么用？

MongoDB支持存储过程，它是javascript写的，保存在db.system.js表中。

#### 19.如何理解MongoDB中的GridFS机制，MongoDB为何使用GridFS来存储文件？

GridFS是一种将大型文件存储在MongoDB中的文件规范。使用GridFS可以将大文件分隔成多个小文档存放，这样我们能够有效的保存大文档，而且解决了BSON对象有限制的问题。

#### 20.为什么MongoDB的数据文件很大？

MongoDB采用的预分配空间的方式来防止文件碎片。

#### 参考资料

https://www.cnblogs.com/angle6-liu/p/10791875.html
