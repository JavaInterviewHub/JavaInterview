## Spring Cloud


* [1.什么是 Spring Cloud？](#1什么是-spring-cloud)
* [2.使用Spring Cloud有什么优势？](#2使用spring-cloud有什么优势)
* [3.服务注册和发现是什么意思？Spring Cloud如何实现？](#3服务注册和发现是什么意思spring-cloud如何实现)
* [4.Spring Cloud由哪些组件组成？](#4spring-cloud由哪些组件组成)
* [5.什么是Hystrix？它如何实现容错？](#5什么是hystrix它如何实现容错)
* [6.什么是Hystrix断路器？我们需要它吗？](#6什么是hystrix断路器我们需要它吗)
* [7.什么是Netflix Feign？它的优点是什么？](#7什么是netflix-feign它的优点是什么)
* [8.Eureka的工作原理？](#8eureka的工作原理)
* [9.说说Eureka的自我保护机制？](#9说说eureka的自我保护机制)
* [10.什么是zuul?](#10什么是zuul)
* [11.zuul的工作流程?](#11zuul的工作流程)
* [12.什么是服务熔断？什么是服务降级？](#12什么是服务熔断什么是服务降级)
* [13.什么是服务雪崩效应?](#13什么是服务雪崩效应)
* [14.ZuulFilter有哪些常用方法？](#14zuulfilter有哪些常用方法)
* [15.如何实现动态Zuul网关路由转发？](#15如何实现动态zuul网关路由转发)
* [16.什么是 Spring Cloud Bus?](#16什么是-spring-cloud-bus)
* [17.Spring Cloud Bus 原理？](#17spring-cloud-bus-原理)
* [18.SpringCloud Config可以实现实时刷新吗?](#18springcloud-config可以实现实时刷新吗)
* [19.Eureka和zookeeper都可以提供服务注册与发现的功能，两者的区别](#19eureka和zookeeper都可以提供服务注册与发现的功能两者的区别)
* [参考链接](#参考链接)


#### 1.什么是 Spring Cloud？

spring cloud 是一系列框架的有序集合。它利用 spring boot 的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 spring boot 的开发风格做到一键启动和部署。

#### 2.使用Spring Cloud有什么优势？

使用Spring Boot开发分布式微服务时，我们面临以下问题

- 与分布式系统相关的复杂性-这种开销包括网络问题，延迟开销，带宽问题，安全问题。
- 服务发现-服务发现工具管理群集中的流程和服务如何查找和互相交谈。它涉及一个服务目录，在该目录中注册服务，然后能够查找并连接到该目录中的服务。
- 冗余-分布式系统中的冗余问题。
- 负载平衡 --负载平衡改善跨多个计算资源的工作负荷，诸如计算机，计算机集群，网络链路，中央处理单元，或磁盘驱动器的分布。
- 性能-问题 由于各种运营开销导致的性能问题。
- 部署复杂性-Devops技能的要求。

#### 3.服务注册和发现是什么意思？Spring Cloud如何实现？

当我们开始一个项目时，我们通常在属性文件中进行所有的配置。随着越来越多的服务开发和部署，添加和修改这些属性变得更加复杂。有些服务可能会下降，而某些位置可能会发生变化。手动更改属性可能会产生问题。 Eureka服务注册和发现可以在这种情况下提供帮助。由于所有服务都在Eureka服务器上注册并通过调用Eureka服务器完成查找，因此无需处理服务地点的任何更改和处理。

#### 4.Spring Cloud由哪些组件组成？

`Eureka`：服务注册与发现

`Zuul`：服务网关

`Ribbon`：客户端负载均衡

`Feign`：声明性的Web服务客户端

`Hystrix`：断路器

`Config`：分布式统一配置管理

等20几个框架，开源一直在更新

![0e9c7b73ca58fcbd7ee8f5b7c3fe450.png](http://img1.sycdn.imooc.com/5f309a610001d87510410539.jpg)


#### 5.什么是Hystrix？它如何实现容错？ 

Hystrix是一个延迟和容错库，旨在隔离远程系统，服务和第三方库的访问点，当出现故障是不可避免的故障时，停止级联故障并在复杂的分布式系统中实现弹性。

通常对于使用微服务架构开发的系统，涉及到许多微服务。这些微服务彼此协作。 

思考以下微服务

![img](https://img-blog.csdn.net/20180922211907901?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21vYWt1bg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

假设如果上图中的微服务9失败了，那么使用传统方法我们将传播一个异常。但这仍然会导致整个系统崩溃。 

随着微服务数量的增加，这个问题变得更加复杂。微服务的数量可以高达1000.这是hystrix出现的地方 我们将使用Hystrix在这种情况下的Fallback方法功能。我们有两个服务employee-consumer使用由employee-consumer公开的服务。 

简化图如下所示 

![img](https://img-blog.csdn.net/20180922211919539?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21vYWt1bg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

现在假设由于某种原因，employee-producer公开的服务会抛出异常。我们在这种情况下使用Hystrix定义了一个回退方法。这种后备方法应该具有与公开服务相同的返回类型。如果暴露服务中出现异常，则回退方法将返回一些值。

#### 6.什么是Hystrix断路器？我们需要它吗？ 

由于某些原因，employee-consumer公开服务会引发异常。在这种情况下使用Hystrix我们定义了一个回退方法。如果在公开服务中发生异常，则回退方法返回一些默认值。

![img](https://img-blog.csdn.net/20180922211937187?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21vYWt1bg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果firstPage method() 中的异常继续发生，则Hystrix电路将中断，并且员工使用者将一起跳过firtsPage方法，并直接调用回退方法。 断路器的目的是给第一页方法或第一页方法可能调用的其他方法留出时间，并导致异常恢复。可能发生的情况是，在负载较小的情况下，导致异常的问题有更好的恢复机会 。

![img](https://img-blog.csdn.net/20180922211948793?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21vYWt1bg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 7.什么是Netflix Feign？它的优点是什么？

Feign是受到Retrofit，JAXRS-2.0和WebSocket启发的java客户端联编程序。Feign的第一个目标是将约束分母的复杂性统一到http apis，而不考虑其稳定性。在employee-consumer的例子中，我们使用了employee-producer使用REST模板公开的REST服务。

但是我们必须编写大量代码才能执行以下步骤

- 使用功能区进行负载平衡。
- 获取服务实例，然后获取基本URL。
- 利用REST模板来使用服务。 前面的代码如下

```java
@Controller
public class ConsumerControllerClient {
 
@Autowired
private LoadBalancerClient loadBalancer;
 
public void getEmployee() throws RestClientException, IOException {
 
    ServiceInstance serviceInstance=loadBalancer.choose("employee-producer");
 
    System.out.println(serviceInstance.getUri());
 
    String baseUrl=serviceInstance.getUri().toString();
 
    baseUrl=baseUrl+"/employee";
 
    RestTemplate restTemplate = new RestTemplate();
    ResponseEntity<String> response=null;
    try{
    response=restTemplate.exchange(baseUrl,
            HttpMethod.GET, getHeaders(),String.class);
    }catch (Exception ex)
    {
        System.out.println(ex);
    }
    System.out.println(response.getBody());
}
```

之前的代码，有像NullPointer这样的例外的机会，并不是最优的。我们将看到如何使用Netflix Feign使呼叫变得更加轻松和清洁。如果Netflix Ribbon依赖关系也在类路径中，那么Feign默认也会负责负载平衡。

#### 8.Eureka的工作原理？

`Eureka`：服务注册中心（可以是一个集群），对外暴露自己的地址
`提供者`：启动后向Eureka注册自己信息（地址，提供什么服务）
`消费者`：向Eureka订阅服务，Eureka会将对应服务的所有提供者地址列表发送给消费者，并且定期更新
`心跳(续约)`：提供者定期通过http方式向Eureka刷新自己的状态（每30s定时向EurekaServer发起请求）

#### 9.说说Eureka的自我保护机制？

当一个服务未按时进行心跳续约时，在生产环境下，因为网络延迟等原因，此时就把服务剔除列表并不妥当，因为服务可能没有宕机。 Eureka就会把当前实例的注册信息保护起来，不予剔除。生产环境下这很有效，保证了大多数服务依然可用。但是有可能会造成一些挂掉的服务被剔除有延迟。

#### 10.什么是zuul?

zuul是对SpringCloud提供的成熟对的`路由方案`，他会根据请求的路径不同，网关会定位到指定的微服务，并代理请求到不同的微服务接口，他对外隐蔽了微服务的真正接口地址。
三个重要概念：

- `动态路由表`：Zuul支持Eureka路由，手动配置路由，这俩种都支持自动更新

- `路由定位`：根据请求路径，Zuul有自己的一套定位服务规则以及路由表达式匹配

- `反向代理`：客户端请求到路由网关，网关受理之后，在对目标发送请求，拿到响应之后在 给客户端

Zuul的应用场景： 对外暴露，权限校验，服务聚合，日志审计等


#### 11.zuul的工作流程?

在Spring Cloud Netflix中，Zuul巧妙的整合了Eureka来实现面向服务的路由。
实际上，API网关将自己注册到Eureka服务注册中心上，也会从注册中心获取所有服务以及它们的实例清单。在Eureka的帮助下，API网关已经维护了系统中所有`serviceId与实例地址的映射关系`。当有外部请求到达API网关的时候，根据请求的URL找到最匹配的path，API网关就可以知道要将该请求"路由"到哪个具体的serviceId上去。 最终通过`Ribbon的负载均衡策略`实现请求的路由。


#### 12.什么是服务熔断？什么是服务降级？

服务熔断：
熔断机制是应对`雪崩效应`的一种微服务`链路保护机制`。
当某个微服务不可用或者响应时间太长时，会进行服务降级，进而熔断该节点微服务的调用，快速返回“错误”的响应信息。当检测到该节点微服务调用响应正常后恢复调用链路。
在SpringCloud框架里熔断机制通过`Hystrix`实现，Hystrix会监控微服务间调用的状况，当失败的调用到一定阈值，缺省是5秒内调用20次，如果失败，就会启动熔断机制。
服务降级：
服务降级，一般是从整体负荷考虑。就是当某个服务熔断之后，服务器将不再被调用，此时客户端可以自己准备一个本地的`fallback回调`，返回一个缺省值。

#### 13.什么是服务雪崩效应?

雪崩效应是在大型互联网项目中，当某个服务发生宕机时，调用这个服务的其他服务也会发生宕机，大型项目的微服务之间的调用是互通的，这样就会将服务的不可用逐步扩大到各个其他服务中，从而使整个项目的服务宕机崩溃。


#### 14.ZuulFilter有哪些常用方法？

`Run()`：过滤器的具体业务逻辑

`shouldFilter()`：判断过滤器是否有效

`filterOrder()`：过滤器执行顺序

`filterType()`：过滤器拦截位置

#### 15.如何实现动态Zuul网关路由转发？

通过`path`配置拦截请求，通过 `Serviceld`到配置中心获取转发的服务列表，zuul内部使用 `Ribbon`实现本地负载均衡和转发。

#### 16.什么是 Spring Cloud Bus?

Spring Cloud Bus就像个分布式执行器，用于扩展的 Spring Boot应用程序的配置文件，但也可以用作应用程序之间的通信通道。

Spring Cloud Bus不能单独完成通信，需要配合MQ支持

Spring Cloud Bus一般是配合Spring Cloud Config做配置中心的

Spring Cloud config实时刷新也必须采用 SpringCloud Bus消息总线

#### 17.Spring Cloud Bus 原理？

发送端（endpoint）构造事件event，将其publish到context上下文中（spring cloud bus有一个父上下文，bootstrap），然后将事件发送到channel中（json串message），接收端从channel中获取到message，将message转为事件event，然后将event事件publish到context上下文中，最后接收端（Listener）收到event，调用服务进行处理。
整个流程中，只有发送/接收端从context上下文中取事件和发送事件是需要我们在代码中明确写出来的，其它部分都由框架封装完成。

#### 18.SpringCloud Config可以实现实时刷新吗?

springcloud config实时刷新采用 `SpringCloud Bus`消息总线

#### 19.Eureka和zookeeper都可以提供服务注册与发现的功能，两者的区别

Zookeeper保证了CP(C：一致性，P：分区容错性)，Eureka保证了AP(A：高可用，P：分区容错)
1、Zookeeper-----当向注册中心查询服务列表时，我们可以容忍注册中心返回的是几分钟以前的信息，但不能容忍直接down掉不可用的。也就是说服务注册功能对高可用性要求比较高，但是zk会出现这样的一种情况，当master节点因为网络故障与其他节点失去联系时，剩余的节点会重新选leader。问题在于，选取leader的时间过长(30~120s)，且选取期间zk集群都不可用，这样就会导致选取期间注册服务瘫痪。在云部署的环境下，因网络问题使得zk集群失去master节点是较大概率会发生的事，虽然服务最终恢复，但是漫长的选择时间导致的注册长期不可用是不能容忍的
2、Eureka则看明白这一点，因此再设计的优先保证了高可用性。Eureka各个节点都是平等的，几个节点挂掉不会影响到正常节点的工作，剩余的节点依然可以提供注册和查询服务。而Eureka的客户端再向某个Eureka注册时如果发现连接失败，则会自动切换至其他节点，只要有一台Eureka还在，就能保证注册服务的可用(保证可用性)，只不过查到的信息可能不是最新的(不保证一致性)。除此之外Eureka还有一种自我保护机制，如果在15分钟内超过85%的节点都没有正常心跳，那么Eureka就认为客户端与注册中心出现了网络故障，此时就会出现以下几种情况：
1>、Eureka不再从注册列表移除因为长时间没收到心跳而应该过期的服务
2>、Eureka仍然能够接受新服务的注册和查询请求，但是不会被同步到其它节点上(保证当前节点可用)
3>、当网络稳定时，当前实例新的注册信息会被同步到其它节点中
Eureka还有客户端缓存功能(Eureka分为客户端程序和服务器端程序两个部分，客户端程序负责向外提供注册与发现服务接口)。所以即便Eureka集群中所有节点都失效，或者发生网络分隔故障导致客户端不能访问任何一台Eureka服务器；Eureka服务的消费者任然可以通过Eureka客户端缓存来获取所有的服务注册信息。甚至最极端的环境下，所有正常的Eureka节点都不对请求产生响应也没有更好的服务器解决方案来解决这种问题时；得益于Eureka的客户端缓存技术，消费者服务仍然可以通过Eureka客户端查询与获取注册服务信息，这点很重要，因此Eureka可以很好的应对网络故障导致部分节点失去联系的情况，而不像Zookeeper那样使整个注册服务瘫痪。

#### 参考链接

https://blog.csdn.net/qq_39827935/article/details/96282405

https://blog.csdn.net/moakun/article/details/82817757

https://www.imooc.com/article/309292
