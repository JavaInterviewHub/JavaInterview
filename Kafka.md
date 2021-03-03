#### 1.为什么要使用 kafka？为什么要使用消息队列？

缓冲和削峰：上游数据时有突发流量，下游可能扛不住，或者下游没有足够多的机器来保证冗余，kafka在中间可以起到一个缓冲的作用，把消息暂存在kafka中，下游服务就可以按照自己的节奏进行慢慢处理。
解耦和扩展性：项目开始的时候，并不能确定具体需求。消息队列可以作为一个接口层，解耦重要的业务流程。只需要遵守约定，针对数据编程即可获取扩展能力。
冗余：可以采用一对多的方式，一个生产者发布消息，可以被多个订阅topic的服务消费到，供多个毫无关联的业务使用。
健壮性：消息队列可以堆积请求，所以消费端业务即使短时间死掉，也不会影响主要业务的正常进行。
异步通信：很多时候，用户不想也不需要立即处理消息。消息队列提供了异步处理机制，允许用户把一个消息放入队列，但并不立即处理它。想向队列中放入多少消息就放多少，然后在需要的时候再去处理它们。

#### 2.Kafka中的ISR、AR又代表什么？ISR的伸缩又指什么？

ISR:In-Sync Replicas 副本同步队列
AR:Assigned Replicas 所有副本
ISR是由leader维护，follower从leader同步数据有一些延迟（包括延迟时间replica.lag.time.max.ms和延迟条数replica.lag.max.messages两个维度, 当前最新的版本0.10.x中只支持replica.lag.time.max.ms这个维度），任意一个超过阈值都会把follower剔除出ISR, 存入OSR（Outof-Sync Replicas）列表，新加入的follower也会先存放在OSR中。AR=ISR+OSR。

#### 3.kafka中的broker 是干什么的？

broker 是消息的代理，Producers往Brokers里面的指定Topic中写消息，Consumers从Brokers里面拉取指定Topic的消息，然后进行业务处理，broker在中间起到一个代理保存消息的中转站。

#### 4.kafka中的 zookeeper 起到什么作用？可以不用zookeeper么？

zookeeper 是一个分布式的协调组件，早期版本的kafka用zk做meta信息存储，consumer的消费状态，group的管理以及 offset的值。考虑到zk本身的一些因素以及整个架构较大概率存在单点问题，新版本中逐渐弱化了zookeeper的作用。新的consumer使用了kafka内部的group coordination协议，也减少了对zookeeper的依赖，但是broker依然依赖于ZK，zookeeper 在kafka中还用来选举controller 和 检测broker是否存活等等。

#### 5.kafka follower如何与leader同步数据？

Kafka的复制机制既不是完全的同步复制，也不是单纯的异步复制。完全同步复制要求All Alive Follower都复制完，这条消息才会被认为commit，这种复制方式极大的影响了吞吐率。而异步复制方式下，Follower异步的从Leader复制数据，数据只要被Leader写入log就被认为已经commit，这种情况下，如果leader挂掉，会丢失数据，kafka使用ISR的方式很好的均衡了确保数据不丢失以及吞吐率。Follower可以批量的从Leader复制数据，而且Leader充分利用磁盘顺序读以及send file(zero copy)机制，这样极大的提高复制性能，内部批量写磁盘，大幅减少了Follower与Leader的消息量差。

#### 6.什么情况下一个 broker 会从 ISR 中被踢出去？

leader会维护一个与其基本保持同步的Replica列表，该列表称为ISR(in-sync Replica)，每个Partition都会有一个ISR，而且是由leader动态维护 ，如果一个follower比一个leader落后太多，或者超过一定时间未发起数据复制请求，则leader将其重ISR中移除 。

#### 7.kafka 为什么那么快？

Cache Filesystem Cache PageCache缓存
顺序。 由于现代的操作系统提供了预读和写技术，磁盘的顺序写大多数情况下比随机写内存还要快。
Zero-copy。零拷技术减少拷贝次数
Batching of Messages 批量量处理。合并小的请求，然后以流的方式进行交互，直顶网络上限。
Pull 拉模式。使用拉模式进行消息的获取消费，与消费端处理能力相符。

#### 8.kafka producer如何优化打入速度？

增加线程
提高 batch.size
增加更多 producer 实例
增加 partition 数
设置 acks=-1 时，如果延迟增大：可以增大 num.replica.fetchers（follower 同步数据的线程数）来调解；
跨数据中心的传输：增加 socket 缓冲区设置以及 OS tcp 缓冲区设置。

#### 9.kafka producer 打数据，ack  为 0， 1， -1 的时候代表啥， 设置 -1 的时候，什么情况下，leader 会认为一条消息 commit 了

1（默认）：数据发送到Kafka后，经过leader成功接收消息的的确认，就算是发送成功了。在这种情况下，如果leader宕机了，则会丢失数据。
0：生产者将数据发送出去就不管了，不去等待任何返回。这种情况下数据传输效率最高，但是数据可靠性确是最低的。
-1：producer需要等待ISR中的所有follower都确认接收到数据后才算一次发送完成，可靠性最高。当ISR中所有Replica都向Leader发送ACK时，leader才commit，这时候producer才能认为一个请求中的消息都commit了。

#### 10.kafka  unclean 配置代表啥？会对 spark streaming 消费有什么影响？

unclean.leader.election.enable 为true的话，意味着非ISR集合的broker 也可以参与选举，这样有可能就会丢数据，spark streaming在消费过程中拿到的 end offset 会突然变小，导致 spark streaming job挂掉。如果unclean.leader.election.enable参数设置为true，就有可能发生数据丢失和数据不一致的情况，Kafka的可靠性就会降低；而如果unclean.leader.election.enable参数设置为false，Kafka的可用性就会降低。

#### 11.如果leader crash时，ISR为空怎么办？

kafka在Broker端提供了一个配置参数：unclean.leader.election,这个参数有两个值：
true（默认）：允许不同步副本成为leader，由于不同步副本的消息较为滞后，此时成为leader，可能会出现消息不一致的情况。
false：不允许不同步副本成为leader，此时如果发生ISR列表为空，会一直等待旧leader恢复，降低了可用性。

#### 12.kafka的message格式是什么样的？

一个Kafka的Message由一个固定长度的header和一个变长的消息体body组成。
header部分由一个字节的magic(文件格式)和四个字节的CRC32(用于判断body消息体是否正常)构成。
当magic的值为1的时候，会在magic和crc32之间多一个字节的数据：attributes(保存一些相关属性，比如是否压缩、压缩格式等等)；如果magic的值为0，那么不存在attributes属性。
body是由N个字节构成的一个消息体，包含了具体的key/value消息。

#### 13.kafka中consumer group 是什么概念？

同样是逻辑上的概念，是Kafka实现单播和广播两种消息模型的手段。同一个topic的数据，会广播给不同的group；同一个group中的worker，只有一个worker能拿到这个数据。换句话说，对于同一个topic，每个group都可以拿到同样的所有数据，但是数据进入group后只能被其中的一个worker消费。group内的worker可以使用多线程或多进程来实现，也可以将进程分散在多台机器上，worker的数量通常不超过partition的数量，且二者最好保持整数倍关系，因为Kafka在设计时假定了一个partition只能被一个worker消费（同一group内）。

#### 14.Kafka中的消息是否会丢失和重复消费？

要确定Kafka的消息是否丢失或重复，从两个方面分析入手：消息发送和消息消费。

1）消息发送
Kafka消息发送有两种方式：同步（sync）和异步（async），默认是同步方式，可通过producer.type属性进行配置。Kafka通过配置request.required.acks属性来确认消息的生产：

0---表示不进行消息接收是否成功的确认；
1---表示当Leader接收成功时确认；
-1---表示Leader和Follower都接收成功时确认；
综上所述，有6种消息生产的情况，下面分情况来分析消息丢失的场景：

（1）acks=0，不和Kafka集群进行消息接收确认，则当网络异常、缓冲区满了等情况时，消息可能丢失；

（2）acks=1、同步模式下，只有Leader确认接收成功后但挂掉了，副本没有同步，数据可能丢失；

2）消息消费

Kafka消息消费有两个consumer接口，Low-level API和High-level API：

Low-level API：消费者自己维护offset等值，可以实现对Kafka的完全控制；

High-level API：封装了对parition和offset的管理，使用简单；

如果使用高级接口High-level API，可能存在一个问题就是当消息消费者从集群中把消息取出来、并提交了新的消息offset值后，还没来得及消费就挂掉了，那么下次再消费时之前没消费成功的消息就“诡异”的消失了；

3）解决办法

针对消息丢失：同步模式下，确认机制设置为-1，即让消息写入Leader和Follower之后再确认消息发送成功；异步模式下，为防止缓冲区满，可以在配置文件设置不限制阻塞超时时间，当缓冲区满时让生产者一直处于阻塞状态；
针对消息重复：将消息的唯一标识保存到外部介质中，每次消费时判断是否处理过即可。


#### 15.为什么Kafka不支持读写分离？

在 Kafka 中，生产者写入消息、消费者读取消息的操作都是与 leader 副本进行交互的，从 而实现的是一种主写主读的生产消费模型。

Kafka 并不支持主写从读，因为主写从读有 2 个很明 显的缺点:

(1)数据一致性问题。数据从主节点转到从节点必然会有一个延时的时间窗口，这个时间 窗口会导致主从节点之间的数据不一致。某一时刻，在主节点和从节点中 A 数据的值都为 X， 之后将主节点中 A 的值修改为 Y，那么在这个变更通知到从节点之前，应用读取从节点中的 A 数据的值并不为最新的 Y，由此便产生了数据不一致的问题。

(2)延时问题。类似 Redis 这种组件，数据从写入主节点到同步至从节点中的过程需要经 历网络→主节点内存→网络→从节点内存这几个阶段，整个过程会耗费一定的时间。而在 Kafka 中，主从同步会比 Redis 更加耗时，它需要经历网络→主节点内存→主节点磁盘→网络→从节 点内存→从节点磁盘这几个阶段。对延时敏感的应用而言，主写从读的功能并不太适用。

#### 16.Kafka中是怎么体现消息顺序性的？

kafka每个partition中的消息在写入时都是有序的，消费时，每个partition只能被每一个group中的一个消费者消费，保证了消费时也是有序的。
整个topic不保证有序。如果为了保证topic整个有序，那么将partition调整为1。

#### 17.kafka如何实现延迟队列？

Kafka并没有使用JDK自带的Timer或者DelayQueue来实现延迟的功能，而是基于时间轮自定义了一个用于实现延迟功能的定时器（SystemTimer）。JDK的Timer和DelayQueue插入和删除操作的平均时间复杂度为O(nlog(n))，并不能满足Kafka的高性能要求，而基于时间轮可以将插入和删除操作的时间复杂度都降为O(1)。时间轮的应用并非Kafka独有，其应用场景还有很多，在Netty、Akka、Quartz、Zookeeper等组件中都存在时间轮的踪影。

底层使用数组实现，数组中的每个元素可以存放一个TimerTaskList对象。TimerTaskList是一个环形双向链表，在其中的链表项TimerTaskEntry中封装了真正的定时任务TimerTask.

Kafka中到底是怎么推进时间的呢？Kafka中的定时器借助了JDK中的DelayQueue来协助推进时间轮。具体做法是对于每个使用到的TimerTaskList都会加入到DelayQueue中。Kafka中的TimingWheel专门用来执行插入和删除TimerTaskEntry的操作，而DelayQueue专门负责时间推进的任务。再试想一下，DelayQueue中的第一个超时任务列表的expiration为200ms，第二个超时任务为840ms，这里获取DelayQueue的队头只需要O(1)的时间复杂度。如果采用每秒定时推进，那么获取到第一个超时的任务列表时执行的200次推进中有199次属于“空推进”，而获取到第二个超时任务时有需要执行639次“空推进”，这样会无故空耗机器的性能资源，这里采用DelayQueue来辅助以少量空间换时间，从而做到了“精准推进”。Kafka中的定时器真可谓是“知人善用”，用TimingWheel做最擅长的任务添加和删除操作，而用DelayQueue做最擅长的时间推进工作，相辅相成。

#### 18.什么是消费者组?

消费者组是 Kafka 独有的概念，如果面试官问这 个，就说明他对此是有一定了解的。我先给出标准答案：
1、定义：即消费者组是 Kafka 提供的可扩展且具有容错性的消费者机制。
2、原理：在 Kafka 中，消费者组是一个由多个消费者实例 构成的组。多个实例共同订阅若干个主题，实现共同消费。同一个组下的每个实例都配置有 相同的组 ID，被分配不同的订阅分区。当某个实例挂掉的时候，其他实例会自动地承担起 它负责消费的分区。

此时，又有一个小技巧给到你:消费者组的题目，能够帮你在某种程度上掌控下面的面试方
向。

如果你擅长位移值原理，就不妨再提一下消费者组的位移提交机制;
如果你擅长 Kafka Broker，可以提一下消费者组与 Broker 之间的交互;
如果你擅长与消费者组完全不相关的 Producer，那么就可以这么说:“消费者组要消 费的数据完全来自于 Producer 端生产的消息，我对 Producer 还是比较熟悉的。”

#### 19.解释下 Kafka 中位移(offset)的作用。

在 Kafka 中，每个 主题分区下的每条消息都被赋予了一个唯一的 ID 数值，用于标识它在分区中的位置。这个 ID 数值，就被称为位移，或者叫偏移量。一旦消息被写入到分区日志，它的位移值将不能 被修改。
答完这些之后，你还可以把整个面试方向转移到你希望的地方。常见方法有以下 3 种:
如果你深谙 Broker 底层日志写入的逻辑，可以强调下消息在日志中的存放格式;
如果你明白位移值一旦被确定不能修改，可以强调下“Log Cleaner 组件都不能影响位 移值”这件事情;
如果你对消费者的概念还算熟悉，可以再详细说说位移值和消费者位移值之间的区别。

#### 20.阐述下 Kafka 中的领导者副本(Leader Replica)和追随者副本 (Follower Replica)的区别。

这道题表面上是考核你对 Leader 和 Follower 区别的理解，但很容易引申到 Kafka 的同步 机制上。因此，我建议你主动出击，一次性地把隐含的考点也答出来，也许能够暂时把面试 官“唬住”，并体现你的专业性。

你可以这么回答:Kafka 副本当前分为领导者副本和追随者副本。只有 Leader 副本才能 对外提供读写服务，响应 Clients 端的请求。Follower 副本只是采用拉(PULL)的方 式，被动地同步 Leader 副本中的数据，并且在 Leader 副本所在的 Broker 宕机后，随时 准备应聘 Leader 副本。

通常来说，回答到这个程度，其实才只说了 60%，因此，我建议你再回答两个额外的加分 项。

强调 Follower 副本也能对外提供读服务。自 Kafka 2.4 版本开始，社区通过引入新的 Broker 端参数，允许 Follower 副本有限度地提供读服务。
强调 Leader 和 Follower 的消息序列在实际场景中不一致。很多原因都可能造成 Leader 和 Follower 保存的消息序列不一致，比如程序 Bug、网络问题等。这是很严重 的错误，必须要完全规避。你可以补充下，之前确保一致性的主要手段是高水位机制， 但高水位值无法保证 Leader 连续变更场景下的数据一致性，因此，社区引入了 Leader Epoch 机制，来修复高水位值的弊端。关于“Leader Epoch 机制”，国内的资料不是 很多，它的普及度远不如高水位，不妨大胆地把这个概念秀出来，力求惊艳一把。

#### 21.如何设置 Kafka 能接收的最大消息的大小?

这道题除了要回答消费者端的参数设置之外，一定要加上 Broker 端的设置，这样才算完整。毕竟，如果 Producer 都不能向 Broker 端发送数据很大的消息，又何来消费一说呢? 因此，你需要同时设置 Broker 端参数和 Consumer 端参数。

Broker 端参数:message.max.bytes、max.message.bytes(主题级别)和 replica.fetch.max.bytes。
Consumer 端参数:fetch.message.max.bytes。
Broker 端的最后一个参数比较容易遗漏。我们必须调整 Follower 副本能够接收的最大消 息的大小，否则，副本同步就会失败。因此，把这个答出来的话，就是一个加分项。

#### 22.监控 Kafka 的框架都有哪些?

面试官其实是在 考察你对监控框架的了解广度，或者说，你是否知道很多能监控 Kafka 的框架或方法。下面这些就是 Kafka 发展历程上比较有名气的监控系统。

Kafka Manager:应该算是最有名的专属 Kafka 监控框架了，是独立的监控系统。
Kafka Monitor:LinkedIn 开源的免费框架，支持对集群进行系统测试，并实时监控测
试结果。
CruiseControl:也是 LinkedIn 公司开源的监控框架，用于实时监测资源使用率，以及 提供常用运维操作等。无 UI 界面，只提供 REST API。
JMX 监控:由于 Kafka 提供的监控指标都是基于 JMX 的，因此，市面上任何能够集成 JMX 的框架都可以使用，比如 Zabbix 和 Prometheus。
已有大数据平台自己的监控体系:像 Cloudera 提供的 CDH 这类大数据平台，天然就提 供 Kafka 监控方案。
JMXTool:社区提供的命令行工具，能够实时监控 JMX 指标。答上这一条，属于绝对 的加分项，因为知道的人很少，而且会给人一种你对 Kafka 工具非常熟悉的感觉。如果 你暂时不了解它的用法，可以在命令行以无参数方式执行一下kafka-run-class.sh kafka.tools.JmxTool，学习下它的用法。

#### 23.Broker 的 Heap Size 如何设置？

如何设置 Heap Size 的问题，其实和 Kafka 关系不大，它是一类非常通用的面试题目。一 旦你应对不当，面试方向很有可能被引到 JVM 和 GC 上去，那样的话，你被问住的几率就 会增大。因此，我建议你简单地介绍一下 Heap Size 的设置方法，并把重点放在 Kafka Broker 堆大小设置的最佳实践上。

比如，你可以这样回复:任何 Java 进程 JVM 堆大小的设置都需要仔细地进行考量和测 试。一个常见的做法是，以默认的初始 JVM 堆大小运行程序，当系统达到稳定状态后，手动触发一次 Full GC，然后通过 JVM 工具查看 GC 后的存活对象大小。之后，将堆大小设 置成存活对象总大小的 1.5~2 倍。对于 Kafka 而言，这个方法也是适用的。不过，业界有 个最佳实践，那就是将 Broker 的 Heap Size 固定为 6GB。经过很多公司的验证，这个大 小是足够且良好的。

#### 24.如何估算 Kafka 集群的机器数量?

这道题目考查的是机器数量和所用资源之间的关联关系。所谓资源，也就是 CPU、内存、磁盘和带宽。

通常来说，CPU 和内存资源的充足是比较容易保证的，因此，你需要从磁盘空间和带宽占用两个维度去评估机器数量。

在预估磁盘的占用时，你一定不要忘记计算副本同步的开销。如果一条消息占用 1KB 的磁 盘空间，那么，在有 3 个副本的主题中，你就需要 3KB 的总空间来保存这条消息。显式地 将这些考虑因素答出来，能够彰显你考虑问题的全面性，是一个难得的加分项。

对于评估带宽来说，常见的带宽有 1Gbps 和 10Gbps，但你要切记，这两个数字仅仅是最大值。因此，你最好和面试官确认一下给定的带宽是多少。然后，明确阐述出当带宽占用接 近总带宽的 90% 时，丢包情形就会发生。这样能显示出你的网络基本功。

#### 25.Leader 总是 -1，怎么破?

在生产环境中，你一定碰到过“某个主题分区不能工作了”的情形。使用命令行查看状态的 话，会发现 Leader 是 -1，于是，你使用各种命令都无济于事，最后只能用“重启大 法”。

但是，有没有什么办法，可以不重启集群，就能解决此事呢?这就是此题的由来。

我直接给答案:删除 ZooKeeper 节点 /controller，触发 Controller 重选举。 Controller 重选举能够为所有主题分区重刷分区状态，可以有效解决因不一致导致的 Leader 不可用问题。我几乎可以断定，当面试官问出此题时，要么就是他真的不知道怎么 解决在向你寻求答案，要么他就是在等你说出这个答案。所以，千万别一上来就说“来个重 启”之类的话。

#### 26.LEO、LSO、AR、ISR、HW 都表示什么含义?

LEO:Log End Offset。日志末端位移值或末端偏移量，表示日志下一条待插入消息的 位移值。举个例子，如果日志有 10 条消息，位移值从 0 开始，那么，第 10 条消息的位 移值就是 9。此时，LEO = 10。
LSO:Log Stable Offset。这是 Kafka 事务的概念。如果你没有使用到事务，那么这个 值不存在(其实也不是不存在，只是设置成一个无意义的值)。该值控制了事务型消费 者能够看到的消息范围。它经常与 Log Start Offset，即日志起始位移值相混淆，因为 有些人将后者缩写成 LSO，这是不对的。在 Kafka 中，LSO 就是指代 Log Stable Offset。
AR:Assigned Replicas。AR 是主题被创建后，分区创建时被分配的副本集合，副本个 数由副本因子决定。
ISR:In-Sync Replicas。Kafka 中特别重要的概念，指代的是 AR 中那些与 Leader 保 持同步的副本集合。在 AR 中的副本可能不在 ISR 中，但 Leader 副本天然就包含在 ISR 中。关于 ISR，还有一个常见的面试题目是如何判断副本是否应该属于 ISR。目前的判断 依据是:Follower 副本的 LEO 落后 Leader LEO 的时间，是否超过了 Broker 端参数 replica.lag.time.max.ms 值。如果超过了，副本就会被从 ISR 中移除。
HW:高水位值(High watermark)。这是控制消费者可读取消息范围的重要字段。一 个普通消费者只能“看到”Leader 副本上介于 Log Start Offset 和 HW(不含)之间的 所有消息。水位以上的消息是对消费者不可见的。关于 HW，问法有很多，我能想到的 最高级的问法，就是让你完整地梳理下 Follower 副本拉取 Leader 副本、执行同步机制 的详细步骤。这就是我们的第 20 道题的题目，一会儿我会给出答案和解析。

#### 27.Kafka 能手动删除消息吗?

其实，Kafka 不需要用户手动删除消息。它本身提供了留存策略，能够自动删除过期消息。 当然，它是支持手动删除消息的。因此，你最好从这两个维度去回答。

对于设置了 Key 且参数 cleanup.policy=compact 的主题而言，我们可以构造一条 <Key，null> 的消息发送给 Broker，依靠 Log Cleaner 组件提供的功能删除掉该 Key 的消息。
对于普通主题而言，我们可以使用 kafka-delete-records 命令，或编写程序调用 Admin.deleteRecords 方法来删除消息。这两种方法殊途同归，底层都是调用 Admin 的 deleteRecords 方法，通过将分区 Log Start Offset 值抬高的方式间接删除消息。

#### 28.consumer_offsets 是做什么用的?

这是一个内部主题，公开的官网资料很少涉及到。因此，我认为，此题属于面试官炫技一类 的题目。你要小心这里的考点:该主题有 3 个重要的知识点，你一定要全部答出来，才会显得对这块知识非常熟悉。

它是一个内部主题，无需手动干预，由 Kafka 自行管理。当然，我们可以创建该主题。

它的主要作用是负责注册消费者以及保存位移值。可能你对保存位移值的功能很熟悉， 但其实该主题也是保存消费者元数据的地方。千万记得把这一点也回答上。另外，这里 的消费者泛指消费者组和独立消费者，而不仅仅是消费者组。

Kafka 的 GroupCoordinator 组件提供对该主题完整的管理功能，包括该主题的创建、 写入、读取和 Leader 维护等。

#### 29.分区 Leader 选举策略有几种?

分区的 Leader 副本选举对用户是完全透明的，它是由 Controller 独立完成的。你需要回答的是，在哪些场景下，需要执行分区 Leader 选举。每一种场景对应于一种选举策略。当前，Kafka 有 4 种分区 Leader 选举策略。

OfflinePartition Leader 选举：每当有分区上线时，就需要执行 Leader 选举。所谓的分区上线，可能是创建了新分区，也可能是之前的下线分区重新上线。这是最常见的分区 Leader 选举场景。

ReassignPartition Leader 选举：当你手动运行 kafka-reassign-partitions 命令，或者是调用 Admin 的 alterPartitionReassignments 方法执行分区副本重分配时，可能触发此类选举。假设原来的 AR 是[1，2，3]，Leader 是 1，当执行副本重分配后，副本集 合 AR 被设置成[4，5，6]，显然，Leader 必须要变更，此时会发生 Reassign Partition Leader 选举。

PreferredReplicaPartition Leader 选举：当你手动运行 kafka-preferred-replica- election 命令，或自动触发了 Preferred Leader 选举时，该类策略被激活。所谓的 Preferred Leader，指的是 AR 中的第一个副本。比如 AR 是[3，2，1]，那么， Preferred Leader 就是 3。

ControlledShutdownPartition Leader 选举：当 Broker 正常关闭时，该 Broker 上 的所有 Leader 副本都会下线，因此，需要为受影响的分区执行相应的 Leader 选举。

这 4 类选举策略的大致思想是类似的，即从 AR 中挑选首个在 ISR 中的副本，作为新 Leader。当然，个别策略有些微小差异。不过，回答到这种程度，应该足以应付面试官 了。毕竟，微小差别对选举 Leader 这件事的影响很小。

#### 30.Kafka 的哪些场景中使用了零拷贝(Zero Copy)?

Zero Copy 是特别容易被问到的高阶题目。在 Kafka 中，体现 Zero Copy 使用场景的地方有两处:基于 mmap 的索引和日志文件读写所用的 TransportLayer。

先说第一个。索引都是基于 MappedByteBuffer 的，也就是让用户态和内核态共享内核态 的数据缓冲区，此时，数据不需要复制到用户态空间。不过，mmap 虽然避免了不必要的 拷贝，但不一定就能保证很高的性能。在不同的操作系统下，mmap 的创建和销毁成本可 能是不一样的。很高的创建和销毁开销会抵消 Zero Copy 带来的性能优势。由于这种不确 定性，在 Kafka 中，只有索引应用了 mmap，最核心的日志并未使用 mmap 机制。

再说第二个。TransportLayer 是 Kafka 传输层的接口。它的某个实现类使用了 FileChannel 的 transferTo 方法。该方法底层使用 sendfile 实现了 Zero Copy。对 Kafka 而言，如果 I/O 通道使用普通的 PLAINTEXT，那么，Kafka 就可以利用 Zero Copy 特 性，直接将页缓存中的数据发送到网卡的 Buffer 中，避免中间的多次拷贝。相反，如果 I/O 通道启用了 SSL，那么，Kafka 便无法利用 Zero Copy 特性了。

#### 31.如何调优 Kafka?

回答任何调优问题的第一步，就是确定优化目标，并且定量给出目标!这点特别重要。对于 Kafka 而言，常见的优化目标是吞吐量、延时、持久性和可用性。每一个方向的优化思路都 是不同的，甚至是相反的。

确定了目标之后，还要明确优化的维度。有些调优属于通用的优化思路，比如对操作系统、 JVM 等的优化;有些则是有针对性的，比如要优化 Kafka 的 TPS。我们需要从 3 个方向去考虑

Producer 端:增加 batch.size、linger.ms，启用压缩，关闭重试等。
Broker 端:增加 num.replica.fetchers，提升 Follower 同步 TPS，避免 Broker Full GC 等。
Consumer:增加 fetch.min.bytes 等

#### 32.Controller 发生网络分区(Network Partitioning)时，Kafka 会怎么样?

这道题目能够诱发我们对分布式系统设计、CAP 理论、一致性等多方面的思考。不过，针 对故障定位和分析的这类问题，我建议你首先言明“实用至上”的观点，即不论怎么进行理论分析，永远都要以实际结果为准。一旦发生 Controller 网络分区，那么，第一要务就是 查看集群是否出现“脑裂”，即同时出现两个甚至是多个 Controller 组件。这可以根据 Broker 端监控指标 ActiveControllerCount 来判断。

现在，我们分析下，一旦出现这种情况，Kafka 会怎么样。

由于 Controller 会给 Broker 发送 3 类请求，即LeaderAndIsrRequest、 StopReplicaRequest 和 UpdateMetadataRequest，因此，一旦出现网络分区，这些请求将不能顺利到达 Broker 端。这将影响主题的创建、修改、删除操作的信息同步，表现为 集群仿佛僵住了一样，无法感知到后面的所有操作。因此，网络分区通常都是非常严重的问 题，要赶快修复。

#### 33.Java Consumer 为什么采用单线程来获取消息?

在回答之前，如果先把这句话说出来，一定会加分:Java Consumer 是双线程的设计。一 个线程是用户主线程，负责获取消息;另一个线程是心跳线程，负责向 Kafka 汇报消费者 存活情况。将心跳单独放入专属的线程，能够有效地规避因消息处理速度慢而被视为下线 的“假死”情况。

单线程获取消息的设计能够避免阻塞式的消息获取方式。单线程轮询方式容易实现异步非阻塞式，这样便于将消费者扩展成支持实时流处理的操作算子。因为很多实时流处理操作算子都不能是阻塞式的。另外一个可能的好处是，可以简化代码的开发。多线程交互的代码是非常容易出错的。

#### 34.简述 Follower 副本消息同步的完整流程。

首先，Follower 发送 FETCH 请求给 Leader。接着，Leader 会读取底层日志文件中的消 息数据，再更新它内存中的 Follower 副本的 LEO 值，更新为 FETCH 请求中的 fetchOffset 值。最后，尝试更新分区高水位值。Follower 接收到 FETCH 响应之后，会把 消息写入到底层日志，接着更新 LEO 和 HW 值。

Leader 和 Follower 的 HW 值更新时机是不同的，Follower 的 HW 更新永远落后于 Leader 的 HW。这种时间上的错配是造成各种不一致的原因。



### 参考资料
https://blog.csdn.net/qq_28900249/article/details/90346599
https://www.jianshu.com/p/511962462e58