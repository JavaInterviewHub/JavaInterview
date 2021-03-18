## Mycat

* [1.Mycat是什么？](#1mycat是什么)
* [2.什么叫混合切分](#2什么叫混合切分)
* [3.在项目组中，切分后的库从哪里而来？](#3在项目组中切分后的库从哪里而来)
* [4.搭建mycat的核心配置文件有哪些？](#4搭建mycat的核心配置文件有哪些)
* [5.mycat分库可以分成100个库吗？](#5mycat分库可以分成100个库吗)
* [6.进行库表拆分时，拆分规则怎么取舍？](#6进行库表拆分时拆分规则怎么取舍)
* [7.Mycat中全局ID方案有哪些？程序自定义全局ID的方案有哪些？](#7mycat中全局id方案有哪些程序自定义全局id的方案有哪些)
* [8.Mycat的在分库分表之后，它是怎么支持联表查询的？](#8mycat的在分库分表之后它是怎么支持联表查询的)
* [9.配置文件不会变多，配置的节点主机会变多？](#9配置文件不会变多配置的节点主机会变多)
* [10.你们项目中分片的实现方式是什么？](#10你们项目中分片的实现方式是什么)
* [参考链接](#参考链接)


#### 1.Mycat是什么？

Mycat是基于MySQL的数据库中间件，目的是为了降低数据库的压力。

#### 2.什么叫混合切分

项目组中如果有水平切分，那项目组里的开发方式就叫混合切分。或者项目组里就是单纯的垂直切分。

#### 3.在项目组中，切分后的库从哪里而来？

在开发中是基于原有库创建出来，并且原有库和切分后的库是数据表的设计是保持一致的。dm_order1,dm_order2,dm_order3这些库是需要和dm_order的设计保持一致的！！！！

附注：所以，切分后的库例如dm_order1,dm_order2,dm_order3这些都是有数据库维护团队创建出来的。

#### 4.搭建mycat的核心配置文件有哪些？

schem.xml 配置参数：逻辑库，逻辑表，数据节点。节点主机

rule.xml：分片规则

server.xml：连接mycat的用户信息（账号和密码）

这里是使用中间件做数据切分，感兴趣的小伙伴还可以了解一下mysql的分库分表高可用方案

#### 5.mycat分库可以分成100个库吗？

我们目前项目组分的是3个库，我们说一般数据量大的话我们使用的是mycat中间件进行分片处理，如果更大的话，我们可以使用oracle数据库，如果更大的话可以使用hadoop或是云存储数据，不需要mycat作为工具手段。衡量的标准是项目有没有对应的硬件设备。 如果没有，基本就是使用mysql 因为搭建一套云环境或者大数据的环境基本都是超大型的公司。比如大数据中的所有的技术，例如hbase 或者是一大堆的服务器 一大堆的网络路由设备 或是私有云。或者是一大堆的数据库运维实施人员都是成本

#### 6.进行库表拆分时，拆分规则怎么取舍？

1.不存在热点数据时，则使用连续分片

2.存在热点数据时，使用离散分片或者是综合分片

3.离散分片暂时迁移比较麻烦（但是mycat给出了数据迁移的脚本，虽然现在还是不是很完美），综合分片占用总机器数量多

#### 7.Mycat中全局ID方案有哪些？程序自定义全局ID的方案有哪些？

1.mycat的全局id方案

（1）本地文件方式
sequnceHandlerType = 0

配置sequence_conf.properties

使用next value for MYCATSEQ_XXX

（2）数据库方式

sequnceHandlerType = 1

配置sequence_db_conf.properties

使用next value for MYCATSEQ_XXX或者指定autoIncrement

（3）本地时间戳方式

ID= 64 位二进制 (42(毫秒)+5(机器 ID)+5(业务编码)+12(重复累加)

sequnceHandlerType = 2

配置sequence_time_conf.properties

指定autoIncrement

\2. 程序方式

（1）Snowflake

（2）UUID

（3）Redis

#### 8.Mycat的在分库分表之后，它是怎么支持联表查询的？

- 使用好ER表
- 善用全局表
- 在sql上添加注解

```
/*!mycat:catlet=io.mycat.catlets.ShareJoin */
```

#### 9.配置文件不会变多，配置的节点主机会变多？

不会

#### 10.你们项目中分片的实现方式是什么？

在rule.xml中配置PartitionByMod

#### 参考链接

https://www.cnblogs.com/zhaozhitong/p/12450130.html

https://zhuanlan.zhihu.com/p/281636679

https://blog.csdn.net/jiongsui7605/article/details/94410680

https://blog.csdn.net/weixin_30682415/article/details/94993907
