## RocketMQ

* [1.RocketMQ中的Topic和JMS的queue有什么区别？](#1rocketmq中的topic和jms的queue有什么区别)
* [2.RocketMQ Broker中的消息被消费后会立即删除吗？](#2rocketmq-broker中的消息被消费后会立即删除吗)
* [3.RocketMQ消费模式有几种？](#3rocketmq消费模式有几种)
* [4.消费消息是push还是pull？](#4消费消息是push还是pull)
* [5.broker如何处理拉取请求的？](#5broker如何处理拉取请求的)
* [6.RocketMQ如何做负载均衡？](#6rocketmq如何做负载均衡)
* [7.消息重复消费如何解决？](#7消息重复消费如何解决)
* [8.如何让RocketMQ保证消息的顺序消费？](#8如何让rocketmq保证消息的顺序消费)
* [9.RocketMQ如何保证消息不丢失？](#9rocketmq如何保证消息不丢失)
* [10.rocketMQ的消息堆积如何处理](#10rocketmq的消息堆积如何处理)
* [11.RocketMQ在分布式事务支持这块机制的底层原理?](#11rocketmq在分布式事务支持这块机制的底层原理)
* [12.如果让你来动手实现一个分布式消息中间件，整体架构你会如何设计实现?](#12如果让你来动手实现一个分布式消息中间件整体架构你会如何设计实现)
* [13.高吞吐量下如何优化生产者和消费者的性能?](#13高吞吐量下如何优化生产者和消费者的性能)
* [14.再说说RocketMQ 是如何保证数据的高容错性的?](#14再说说rocketmq-是如何保证数据的高容错性的)
* [15.任何一台Broker突然宕机了怎么办？](#15任何一台broker突然宕机了怎么办)
* [参考资料](#参考资料)


#### 1.RocketMQ中的Topic和JMS的queue有什么区别？

queue就是来源于数据结构的FIFO队列。而Topic是个抽象的概念，每个Topic底层对应N个queue，而数据也真实存在queue上的。

#### 2.RocketMQ Broker中的消息被消费后会立即删除吗？

不会，每条消息都会持久化到CommitLog中，每个Consumer连接到Broker后会维持消费进度信息，当有消息消费后只是当前Consumer的消费进度（CommitLog的offset）更新了。

#### 3.RocketMQ消费模式有几种？

集群消费
一条消息只会被同Group中的一个Consumer消费
多个Group同时消费一个Topic时，每个Group都会有一个Consumer消费到数据

广播消费
消息将对一 个Consumer Group 下的各个 Consumer 实例都消费一遍。即即使这些 Consumer 属于同一个Consumer Group ，消息也会被 Consumer Group 中的每个 Consumer 都消费一次。

#### 4.消费消息是push还是pull？

RocketMQ没有真正意义的push，都是pull，虽然有push类，但实际底层实现采用的是长轮询机制，即拉取方式。

broker端属性 longPollingEnable 标记是否开启长轮询。默认开启。

#### 5.broker如何处理拉取请求的？

Consumer首次请求Broker

Broker中是否有符合条件的消息
如果有
响应Consumer
等待下次Consumer的请求

如果没有
DefaultMessageStore#ReputMessageService#run方法
PullRequestHoldService 来Hold连接，每个5s执行一次检查pullRequestTable有没有消息，有的话立即推送
每隔1ms检查commitLog中是否有新消息，有的话写入到pullRequestTable
当有新消息的时候返回请求
挂起consumer的请求，即不断开连接，也不返回数据
使用consumer的offset

#### 6.RocketMQ如何做负载均衡？

通过Topic在多Broker中分布式存储实现。

1）producer端
发送端指定message queue发送消息到相应的broker，来达到写入时的负载均衡：
提升写入吞吐量，当多个producer同时向一个broker写入数据的时候，性能会下降
消息分布在多broker中，为负载消费做准备

默认策略是随机选择：
producer维护一个index
每次取节点会自增
index向所有broker个数取余
自带容错策略

其他实现：
SelectMessageQueueByHash
hash的是传入的args
SelectMessageQueueByRandom
SelectMessageQueueByMachineRoom 没有实现
也可以自定义实现MessageQueueSelector接口中的select方法


2）consumer端

采用的是平均分配算法来进行负载均衡。

其他负载均衡算法
平均分配策略(默认)(AllocateMessageQueueAveragely) 环形分配策略(AllocateMessageQueueAveragelyByCircle) 手动配置分配策略(AllocateMessageQueueByConfig) 机房分配策略(AllocateMessageQueueByMachineRoom) 一致性哈希分配策略(AllocateMessageQueueConsistentHash) 靠近机房策略(AllocateMachineRoomNearby)

#### 7.消息重复消费如何解决？

影响消息正常发送和消费的重要原因是网络的不确定性。

引起重复消费的原因
1）ACK
正常情况下在consumer真正消费完消息后应该发送ack，通知broker该消息已正常消费，从queue中剔除
当ack因为网络原因无法发送到broker，broker会认为词条消息没有被消费，此后会开启消息重投机制把消息再次投递到consumer
2）消费模式
在CLUSTERING模式下，消息在broker中会保证相同group的consumer消费一次，但是针对不同group的consumer会推送多次

解决方案
1）数据库表
处理消息前，使用消息主键在表中带有约束的字段中insert
2）Map
单机时可以使用map ConcurrentHashMap -> putIfAbsent   guava cache
3）Redis
分布式锁搞起来。

#### 8.如何让RocketMQ保证消息的顺序消费？

首先多个queue只能保证单个queue里的顺序，queue是典型的FIFO，天然顺序。多个queue同时消费是无法绝对保证消息的有序性的。所以总结如下：

同一topic，同一个QUEUE，发消息的时候一个线程去发送消息，消费的时候 一个线程去消费一个queue里的消息。

#### 9.RocketMQ如何保证消息不丢失？

首先在如下三个部分都可能会出现丢失消息的情况：
Producer端
Broker端
Consumer端

1）Producer端如何保证消息不丢失
采取send()同步发消息，发送结果是同步感知的。
发送失败后可以重试，设置重试次数。默认3次。
producer.setRetryTimesWhenSendFailed(10);

集群部署，比如发送失败了的原因可能是当前Broker宕机了，重试的时候会发送到其他Broker上。

2）Broker端如何保证消息不丢失
修改刷盘策略为同步刷盘。默认情况下是异步刷盘的。
flushDiskType = SYNC_FLUSH

集群部署，主从模式，高可用。

3）Consumer端如何保证消息不丢失
完全消费正常后在进行手动ack确认。

#### 10.rocketMQ的消息堆积如何处理

首先要找到是什么原因导致的消息堆积，是Producer太多了，Consumer太少了导致的还是说其他情况，总之先定位问题。

然后看下消息消费速度是否正常，正常的话，可以通过上线更多consumer临时解决消息堆积问题。

#### 11.RocketMQ在分布式事务支持这块机制的底层原理?

分布式系统中的事务可以使用TCC（Try、Confirm、Cancel）、2pc来解决分布式系统中的消息原子性
RocketMQ 4.3+提供分布事务功能，通过 RocketMQ 事务消息能达到分布式事务的最终一致

RocketMQ实现方式：
Half Message：预处理消息，当broker收到此类消息后，会存储到RMQ_SYS_TRANS_HALF_TOPIC的消息消费队列中

检查事务状态：Broker会开启一个定时任务，消费RMQ_SYS_TRANS_HALF_TOPIC队列中的消息，每次执行任务会向消息发送者确认事务执行状态（提交、回滚、未知），如果是未知，Broker会定时去回调在重新检查。

超时：如果超过回查次数，默认回滚消息。

也就是他并未真正进入Topic的queue，而是用了临时queue来放所谓的half message，等提交事务后才会真正的将half message转移到topic下的queue。

#### 12.如果让你来动手实现一个分布式消息中间件，整体架构你会如何设计实现?

需要考虑能快速扩容、天然支持集群
持久化的姿势
高可用性
数据0丢失的考虑
服务端部署简单、client端使用简单

#### 13.高吞吐量下如何优化生产者和消费者的性能?

1）开发
同一group下，多机部署，并行消费
单个Consumer提高消费线程个数
批量消费。消息批量拉取，业务逻辑批量处理。

2）运维
网卡调优
jvm调优
多线程与cpu调优
Page Cache

#### 14.再说说RocketMQ 是如何保证数据的高容错性的?

在不开启容错的情况下，轮询队列进行发送，如果失败了，重试的时候过滤失败的Broker
如果开启了容错策略，会通过RocketMQ的预测机制来预测一个Broker是否可用
如果上次失败的Broker可用那么还是会选择该Broker的队列
如果上述情况失败，则随机选择一个进行发送
在发送消息的时候会记录一下调用的时间与是否报错，根据该时间去预测broker的可用时间

#### 15.任何一台Broker突然宕机了怎么办？

Broker主从架构以及多副本策略。Master收到消息后会同步给Slave，这样一条消息就不止一份了，Master宕机了还有slave中的消息可用，保证了MQ的可靠性和高可用性。而且Rocket MQ4.5.0开始就支持了Dlegder模式，基于raft的，做到了真正意义的HA。

#### 参考资料

https://www.cnblogs.com/javazhiyin/p/13327925.html
