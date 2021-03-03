## RabbitMQ


* [1.什么是RabbitMQ？为什么使用RabbitMQ？](#1什么是rabbitmq为什么使用rabbitmq)
* [2.RabbitMQ有什么优缺点？](#2rabbitmq有什么优缺点)
* [3.什么是元数据？元数据分为哪些类型？包括哪些内容？与cluster相关的元数据有哪些？元数据是如何保存的？元数据在cluster中是如何分布的？](#3什么是元数据元数据分为哪些类型包括哪些内容与cluster相关的元数据有哪些元数据是如何保存的元数据在cluster中是如何分布的)
* [4.在单node系统和多node构成的cluster系统中声明queue、exchange，以及进行binding会有什么不同？](#4在单node系统和多node构成的cluster系统中声明queueexchange以及进行binding会有什么不同)
* [5.客户端连接到cluster中的任意node上是否都能正常工作？](#5客户端连接到cluster中的任意node上是否都能正常工作)
* [6.若cluster中拥有某个queue的owner node失效了，且该queue 被声明具有durable属性，是否能够成功从其他node上重新声明该 queue ？](#6若cluster中拥有某个queue的owner-node失效了且该queue-被声明具有durable属性是否能够成功从其他node上重新声明该-queue-)
* [7.RabbitMQ 的消息是怎么发送的？](#7rabbitmq-的消息是怎么发送的)
* [8.RabbitMQ 怎么避免消息丢失？](#8rabbitmq-怎么避免消息丢失)
* [9.RabbitMQ的使用场景有哪些?](#9rabbitmq的使用场景有哪些)
* [10.RabbitMQ有哪些重要的角色?](#10rabbitmq有哪些重要的角色)
* [11.如何确保消息正确地发送至RabbitMQ?如何确保消息接收方消费了消息?](#11如何确保消息正确地发送至rabbitmq如何确保消息接收方消费了消息)
* [12.要保证消息持久化成功的条件有哪些?](#12要保证消息持久化成功的条件有哪些)
* [13.RabbitMQ 有几种广播类型?](#13rabbitmq-有几种广播类型)
* [14.vhost 是什么？起什么作用？](#14vhost-是什么起什么作用)
* [15.消息基于什么传输？](#15消息基于什么传输)
* [16.消息如何分发？](#16消息如何分发)
* [17.消息怎么路由？](#17消息怎么路由)
* [18.如何确保消息接收方消费了消息？](#18如何确保消息接收方消费了消息)
* [19.如何避免消息重复投递或重复消费？](#19如何避免消息重复投递或重复消费)
* [20.死信队列和延迟队列的使用](#20死信队列和延迟队列的使用)
* [参考链接：](#参考链接)



#### 1.什么是RabbitMQ？为什么使用RabbitMQ？

RabbitMQ是一款开源的，Erlang编写的，基于AMQP协议的，消息中间件；

可以用它来：解耦、异步、削峰。

#### 2.RabbitMQ有什么优缺点？

优点：解耦、异步、削峰；

缺点：降低了系统的稳定性：本来系统运行好好的，现在你非要加入个消息队列进去，那消息队列挂了，你的系统不是呵呵了。因此，系统可用性会降低；

增加了系统的复杂性：加入了消息队列，要多考虑很多方面的问题，比如：一致性问题、如何保证消息不被重复消费、如何保证消息可靠性传输等。因此，需要考虑的东西更多，复杂性增大。

#### 3.什么是元数据？元数据分为哪些类型？包括哪些内容？与cluster相关的元数据有哪些？元数据是如何保存的？元数据在cluster中是如何分布的？

在非cluster模式下，元数据主要分为Queue元数据（queue名字和属性 等）、Exchange元数据（exchange名字、类型和属性等）、Binding元数据 （存放路由关系的查找表）、Vhost元数据（vhost范围内针对前三者的名字空 间约束和安全属性设置）。

在cluster模式下，还包括cluster中node位置信息和node关系信息。元数据按照erlang node的类型确定是仅保存于RAM中，还是同时保存在RAM和disk上。元数据在cluster中是全node 分布的。

#### 4.在单node系统和多node构成的cluster系统中声明queue、exchange，以及进行binding会有什么不同？

当你在单node上声明queue时，只要该node上相关元数据进行了变 更，你就会得到Queue.Declare-ok回应；而在cluster上声明queue，则要 求cluster上的全部node都要进行元数据成功更新，才会得到 Queue.Declare-ok回应。另外，若node类型为RAM node则变更的数据 仅保存在内存中，若类型为disk node则还要变更保存在磁盘上的数据。

#### 5.客户端连接到cluster中的任意node上是否都能正常工作？

是的。客户端感觉不到有何不同。

#### 6.若cluster中拥有某个queue的owner node失效了，且该queue 被声明具有durable属性，是否能够成功从其他node上重新声明该 queue ？

不能，在这种情况下，将得到404 NOT_FOUND错误。只能等queue所 属的node恢复后才能使用该queue。但若该queue本身不具有durable 属性，则可在其他node上重新声明。

#### 7.RabbitMQ 的消息是怎么发送的？

首先客户端必须连接到 RabbitMQ 服务器才能发布和消费消息，客户端和 rabbit server 之间会创建一个 tcp 连接，一旦 tcp 打开并通过了认证（认证就是你发送给 rabbit 服务器的用户名和密码），你的客户端和 RabbitMQ 就创建了一条 amqp 信道（channel），信道是创建在“真实” tcp 上的虚拟连接，amqp 命令都是通过信道发送出去的，每个信道都会有一个唯一的 id，不论是发布消息，订阅队列都是通过这个信道完成的。

#### 8.RabbitMQ 怎么避免消息丢失？

①消息持久化;

②ACK确认机制;

③设置集群镜像模式;

④消息补偿机制。

#### 9.RabbitMQ的使用场景有哪些?

①跨系统的异步通信，所有需要异步交互的地方都可以使用消息队列。就像我们除了打电话(同步)以外，还需要发短信，发电子邮件(异步)的通讯方式。

②多个应用之间的耦合，由于消息是平台无关和语言无关的，而且语义上也不再是函数调用，因此更适合作为多个应用之间的松耦合的接口。基于消息队列的耦合，不需要发送方和接收方同时在线。在企业应用集成(EAI)中，文件传输，共享数据库，消息队列，远程过程调用都可以作为集成的方法。

③应用内的同步变异步，比如订单处理，就可以由前端应用将订单信息放到队列，后端应用从队列里依次获得消息处理，高峰时的大量订单可以积压在队列里慢慢处理掉。由于同步通常意味着阻塞，而大量线程的阻塞会降低计算机的性能。

④消息驱动的架构(EDA)，系统分解为消息队列，和消息制造者和消息消费者，一个处理流程可以根据需要拆成多个阶段(Stage)，阶段之间用队列连接起来，前一个阶段处理的结果放入队列，后一个阶段从队列中获取消息继续处理。

⑤应用需要更灵活的耦合方式，如发布订阅，比如可以指定路由规则。

⑥跨局域网，甚至跨城市的通讯(CDN行业)，比如北京机房与广州机房的应用程序的通信。

#### 10.RabbitMQ有哪些重要的角色?

RabbitMQ中重要的角色有：生产者、消费者和代理：

①生产者：消息的创建者，负责创建和推送数据到消息服务器;

②消费者：消息的接收方，用于处理数据和确认消息;

③代理：就是RabbitMQ本身，用于扮演“快递”的角色，本身不生产消息，只是扮演“快递”的角色。

#### 11.如何确保消息正确地发送至RabbitMQ?如何确保消息接收方消费了消息?

1、发送方确认模式

①将信道设置成confirm模式(发送方确认模式)，则所有在信道上发布的消息都会被指派一个唯一的ID。

②一旦消息被投递到目的队列后，或者消息被写入磁盘后(可持久化的消息)，信道会发送一个确认给生产者(包含消息唯一 ID)。

③如果RabbitMQ发生内部错误从而导致消息丢失，会发送一条 nack(notacknowledged，未确认)消息。

④发送方确认模式是异步的，生产者应用程序在等待确认的同时，可以继续发送消息。当确认消息到达生产者应用程序，生产者应用程序的回调方法就会被触发来处理确认消息。

2、接收方确认机制

①消费者接收每一条消息后都必须进行确认(消息接收和消息确认是两个不同操作)。只有消费者确认了消息，RabbitMQ 才能安全地把消息从队列中删除。

②这里并没有用到超时机制，RabbitMQ仅通过Consumer的连接中断来确认是否需要重新发送消息。也就是说，只要连接不中断，RabbitMQ给了Consumer足够长的时间来处理消息。保证数据的最终一致性。

3、下面罗列几种特殊情况

①如果消费者接收到消息，在确认之前断开了连接或取消订阅，RabbitMQ会认为消息没有被分发，然后重新分发给下一个订阅的消费者。(可能存在消息重复消费的隐患，需要去重)

②如果消费者接收到消息却没有确认消息，连接也未断开，则RabbitMQ认为该消费者繁忙，将不会给该消费者分发更多的消息。

#### 12.要保证消息持久化成功的条件有哪些?

①声明队列必须设置持久化durable设置为 true。

②消息推送投递模式必须设置持久化，deliveryMode设置为2(持久)。

③消息已经到达持久化交换器。

④消息已经到达持久化队列。

以上四个条件都满足才能保证消息持久化成功。

#### 13.RabbitMQ 有几种广播类型?

三种广播模式：

①fanout：所有bind到此exchange的queue都可以接收消息(纯广播，绑定到RabbitMQ的接受者都能收到消息);

②direct：通过routingKey和exchange决定的那个唯一的queue可以接收消息;

③topic：所有符合routingKey(此时可以是一个表达式)的routingKey所bind的queue可以接收消息;

#### 14.vhost 是什么？起什么作用？

vhost 可以理解为虚拟 broker ，即 mini-RabbitMQ  server。其内部均含有独立的 queue、exchange 和 binding 等，但最最重要的是，其拥有独立的权限系统，可以做到 vhost 范围的用户控制。当然，从 RabbitMQ 的全局角度，vhost 可以作为不同权限隔离的手段（一个典型的例子就是不同的应用可以跑在不同的 vhost 中）。

#### 15.消息基于什么传输？

由于TCP连接的创建和销毁开销较大，且并发数受系统资源限制，会造成性能瓶颈。RabbitMQ使用信道的方式来传输数据。信道是建立在真实的TCP连接内的虚拟连接，且每条TCP连接上的信道数量没有限制。

#### 16.消息如何分发？

若该队列至少有一个消费者订阅，消息将以循环（round-robin）的方式发送给消费者。每条消息只会分发给一个订阅的消费者（前提是消费者能够正常处理消息并进行确认）。

#### 17.消息怎么路由？

从概念上来说，消息路由必须有三部分：交换器、路由、绑定。生产者把消息发布到交换器上；绑定决定了消息如何从路由器路由到特定的队列；消息最终到达队列，并被消费者接收。

消息发布到交换器时，消息将拥有一个路由键（routing key），在消息创建时设定。
通过队列路由键，可以把队列绑定到交换器上。
消息到达交换器后，RabbitMQ会将消息的路由键与队列的路由键进行匹配（针对不同的交换器有不同的路由规则）。如果能够匹配到队列，则消息会投递到相应队列中；如果不能匹配到任何队列，消息将进入 “黑洞”。
常用的交换器主要分为一下三种：

direct：如果路由键完全匹配，消息就被投递到相应的队列
fanout：如果交换器收到消息，将会广播到所有绑定的队列上
topic：可以使来自不同源头的消息能够到达同一个队列。 使用topic交换器时，可以使用通配符，比如：“*” 匹配特定位置的任意文本， “.” 把路由键分为了几部分，“#” 匹配所有规则等。特别注意：发往topic交换器的消息不能随意的设置选择键（routing_key），必须是由"."隔开的一系列的标识符组成。

#### 18.如何确保消息接收方消费了消息？

接收方消息确认机制：消费者接收每一条消息后都必须进行确认（消息接收和消息确认是两个不同操作）。只有消费者确认了消息，RabbitMQ才能安全地把消息从队列中删除。这里并没有用到超时机制，RabbitMQ仅通过Consumer的连接中断来确认是否需要重新发送消息。也就是说，只要连接不中断，RabbitMQ给了Consumer足够长的时间来处理消息。

下面罗列几种特殊情况：

如果消费者接收到消息，在确认之前断开了连接或取消订阅，RabbitMQ会认为消息没有被分发，然后重新分发给下一个订阅的消费者。（可能存在消息重复消费的隐患，需要根据bizId去重）
如果消费者接收到消息却没有确认消息，连接也未断开，则RabbitMQ认为该消费者繁忙，将不会给该消费者分发更多的消息。

#### 19.如何避免消息重复投递或重复消费？

在消息生产时，MQ内部针对每条生产者发送的消息生成一个inner-msg-id，作为去重和幂等的依据（消息投递失败并重传），避免重复的消息进入队列；在消息消费时，要求消息体中必须要有一个bizId（对于同一业务全局唯一，如支付ID、订单ID、帖子ID等）作为去重和幂等的依据，避免同一条消息被重复消费。

这个问题针对业务场景来答分以下几点：

1.比如，你拿到这个消息做数据库的insert操作。那就容易了，给这个消息做一个唯一主键，那么就算出现重复消费的情况，就会导致主键冲突，避免数据库出现脏数据。

2.再比如，你拿到这个消息做redis的set的操作，那就容易了，不用解决，因为你无论set几次结果都是一样的，set操作本来就算幂等操作。

3.如果上面两种情况还不行，上大招。准备一个第三方介质,来做消费记录。以redis为例，给消息分配一个全局id，只要消费过该消息，将<id,message>以K-V形式写入redis。那消费者开始消费前，先去redis中查询有没消费记录即可。

#### 20.死信队列和延迟队列的使用

死信消息：

消息被拒绝（Basic.Reject或Basic.Nack）并且设置 requeue 参数的值为 false
消息过期了
队列达到最大的长度
过期消息：

在 rabbitmq 中存在2种方可设置消息的过期时间，第一种通过对队列进行设置，这种设置后，该队列中所有的消息都存在相同的过期时间，第二种通过对消息本身进行设置，那么每条消息的过期时间都不一样。如果同时使用这2种方法，那么以过期时间小的那个数值为准。当消息达到过期时间还没有被消费，那么那个消息就成为了一个 死信 消息。
    
队列设置：在队列申明的时候使用 x-message-ttl 参数，单位为 毫秒
    
单个消息设置：是设置消息属性的 expiration 参数的值，单位为 毫秒

延时队列：在rabbitmq中不存在延时队列，但是我们可以通过设置消息的过期时间和死信队列来模拟出延时队列。消费者监听死信交换器绑定的队列，而不要监听消息发送的队列。

有了以上的基础知识，我们完成以下需求：

需求：用户在系统中创建一个订单，如果超过时间用户没有进行支付，那么自动取消订单。

分析：

1、上面这个情况，我们就适合使用延时队列来实现，那么延时队列如何创建
    
2、延时队列可以由 过期消息+死信队列 来时间
    
3、过期消息通过队列中设置 x-message-ttl 参数实现
    
4、死信队列通过在队列申明时，给队列设置 x-dead-letter-exchange 参数，然后另外申明一个队列绑定x-dead-letter-exchange对应的交换器。
```java
ConnectionFactory factory = new ConnectionFactory(); 
factory.setHost("127.0.0.1"); 
factory.setPort(AMQP.PROTOCOL.PORT); 
factory.setUsername("guest"); 
factory.setPassword("guest"); 
Connection connection = factory.newConnection(); 
Channel channel = connection.createChannel();
 
// 声明一个接收被删除的消息的交换机和队列 
String EXCHANGE_DEAD_NAME = "exchange.dead"; 
String QUEUE_DEAD_NAME = "queue_dead"; 
channel.exchangeDeclare(EXCHANGE_DEAD_NAME, BuiltinExchangeType.DIRECT); 
channel.queueDeclare(QUEUE_DEAD_NAME, false, false, false, null); 
channel.queueBind(QUEUE_DEAD_NAME, EXCHANGE_DEAD_NAME, "routingkey.dead"); 
 
String EXCHANGE_NAME = "exchange.fanout"; 
String QUEUE_NAME = "queue_name"; 
channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.FANOUT); 
Map<String, Object> arguments = new HashMap<String, Object>(); 
// 统一设置队列中的所有消息的过期时间 
arguments.put("x-message-ttl", 30000); 
// 设置超过多少毫秒没有消费者来访问队列，就删除队列的时间 
arguments.put("x-expires", 20000); 
// 设置队列的最新的N条消息，如果超过N条，前面的消息将从队列中移除掉 
arguments.put("x-max-length", 4); 
// 设置队列的内容的最大空间，超过该阈值就删除之前的消息
arguments.put("x-max-length-bytes", 1024); 
// 将删除的消息推送到指定的交换机，一般x-dead-letter-exchange和x-dead-letter-routing-key需要同时设置
arguments.put("x-dead-letter-exchange", "exchange.dead"); 
// 将删除的消息推送到指定的交换机对应的路由键 
arguments.put("x-dead-letter-routing-key", "routingkey.dead"); 
// 设置消息的优先级，优先级大的优先被消费 
arguments.put("x-max-priority", 10); 
channel.queueDeclare(QUEUE_NAME, false, false, false, arguments); 
channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, ""); 
String message = "Hello RabbitMQ: "; 
 
for(int i = 1; i <= 5; i++) { 
	// expiration: 设置单条消息的过期时间 
	AMQP.BasicProperties.Builder properties = new AMQP.BasicProperties().builder()
			.priority(i).expiration( i * 1000 + ""); 
	channel.basicPublish(EXCHANGE_NAME, "", properties.build(), (message + i).getBytes("UTF-8")); 
} 
channel.close(); 
connection.close();
```


### 参考链接：

https://blog.csdn.net/jerryDzan/article/details/89183625

https://www.cnblogs.com/woadmin/p/10537174.html

https://blog.csdn.net/weixin_48272905/article/details/108988269

http://www.mianshigee.com/topic/1009ada/

http://blog.itpub.net/69902581/viewspace-2673724/

