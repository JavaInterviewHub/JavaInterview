## Spring


* [1.什么是spring?](#1什么是spring)
* [2.使用Spring框架的好处是什么？](#2使用spring框架的好处是什么)
* [3.Spring由哪些模块组成?](#3spring由哪些模块组成)
* [4.Spring是怎么解决循环依赖的？](#4spring是怎么解决循环依赖的)
* [5.Spring Boot手动装配有哪几种方式？](#5spring-boot手动装配有哪几种方式)
* [6.Spring Boot自动配置原理](#6spring-boot自动配置原理)
* [7.谈谈自己对于Spring IOC的理解](#7谈谈自己对于spring-ioc的理解)
* [8.谈谈自己对于Spring AOP的理解](#8谈谈自己对于spring-aop的理解)
* [9.Spring AOP和AspectJ AOP有什么区别？](#9spring-aop和aspectj-aop有什么区别)
* [10.Spring中的bean的作用域有哪些？](#10spring中的bean的作用域有哪些)
* [11.Spring中的单例bean的线程安全问题了解吗？](#11spring中的单例bean的线程安全问题了解吗)
* [12.Spring中的bean生命周期了解过吗？](#12spring中的bean生命周期了解过吗)
* [13.Spring MVC的工作原理了解嘛？](#13spring-mvc的工作原理了解嘛)
* [14.Spring框架中用到了哪些设计模式？](#14spring框架中用到了哪些设计模式)
* [15.@Component和@Bean的区别是什么？](#15component和bean的区别是什么)
* [16.将一个类声明为Spring的bean的注解有哪些？](#16将一个类声明为spring的bean的注解有哪些)
* [17.Spring事务管理的方式有几种？](#17spring事务管理的方式有几种)
* [18.Spring事务中的隔离级别有哪几种？](#18spring事务中的隔离级别有哪几种)
* [19.Spring事务中有哪几种事务传播行为？](#19spring事务中有哪几种事务传播行为)
* [20.Spring 事务底层原理](#20spring-事务底层原理)
* [21.BeanFactory和ApplicationContext有什么区别？](#21beanfactory和applicationcontext有什么区别)
* [22.Resource 是如何被查找、加载的？](#22resource-是如何被查找加载的)
* [23.解释自动装配的各种模式？](#23解释自动装配的各种模式)
* [24.有哪些不同类型的IOC(依赖注入)？](#24有哪些不同类型的ioc依赖注入)
* [25.Spring AOP 实现原理](#25spring-aop-实现原理)
* [26.ApplicationContext通常的实现是什么?](#26applicationcontext通常的实现是什么)
* [27. Bean 工厂和 Application contexts 有什么区别？](#27-bean-工厂和-application-contexts-有什么区别)
* [参考资料](#参考资料)



#### 1.什么是spring?

Spring 是个java企业级应用的开源开发框架。Spring主要用来开发Java应用，但是有些扩展是针对构建J2EE平台的web应用。Spring 框架目标是简化Java企业级应用开发，并通过POJO为基础的编程模型促进良好的编程习惯。

#### 2.使用Spring框架的好处是什么？

- 轻量：Spring 是轻量的，基本的版本大约2MB。
- 控制反转：Spring通过控制反转实现了松散耦合，对象们给出它们的依赖，而不是创建或查找依赖的对象们。
- 面向切面的编程(AOP)：Spring支持面向切面的编程，并且把应用业务逻辑和系统服务分开。
- 容器：Spring 包含并管理应用中对象的生命周期和配置。
- MVC框架：Spring的WEB框架是个精心设计的框架，是Web框架的一个很好的替代品。
- 事务管理：Spring 提供一个持续的事务管理接口，可以扩展到上至本地事务下至全局事务（JTA）。
- 异常处理：Spring 提供方便的API把具体技术相关的异常（比如由JDBC，Hibernate or JDO抛出的）转化为一致的unchecked 异常。

#### 3.Spring由哪些模块组成?

以下是Spring 框架的基本模块：

- Core module
- Bean module
- Context module
- Expression Language module
- JDBC module
- ORM module
- OXM module
- Java Messaging Service(JMS) module
- Transaction module
- Web module
- Web-Servlet module
- Web-Struts module
- Web-Portlet module

#### 4.Spring是怎么解决循环依赖的？

整个IOC容器解决循环依赖，用到的几个重要成员：
singletonObjects：一级缓存，存放完全初始化好的Bean的集合，从这个集合中取出来的Bean可以立马返回
earlySingletonObjects：二级缓存，存放创建好但没有初始化属性的Bean的集合，它用来解决循环依赖
singletonFactories：三级缓存，存放单实例Bean工厂的集合
singletonsCurrentlyInCreation：存放正在被创建的Bean的集合

IOC容器解决循环依赖的思路：

1. 初始化Bean之前，将这个BeanName放入三级缓存
2. 创建Bean将准备创建的Bean放入 singletonsCurrentlyInCreation （正在创建的Bean）
3. createNewInstance 方法执行完后执行 addSingletonFactory，将这个实例化但没有属性赋值的Bean放入二级缓存，并从三级缓存中移除
4. 属性赋值&自动注入时，引发关联创建
5. 关联创建时，检查“正在被创建的Bean”中是否有即将注入的Bean。如果有，检查二级缓存中是否有当前创建好但没有赋值初始化的Bean。如果没有，检查三级缓存中是否有正在创建中的Bean。至此一般会有，将这个Bean放入二级缓存，并从三级缓存中移除
6. 之后Bean被成功注入，最后执行 addSingleton，将这个完全创建好的Bean放入一级缓存，从二级缓存和三级缓存移除，并记录已经创建了的单实例Bean

#### 5.Spring Boot手动装配有哪几种方式？

1. 使用模式注解 @Component 等（Spring2.5+）
2. 使用配置类 @Configuration 与 @Bean （Spring3.0+）
3. 使用模块装配 @EnableXXX 与 @Import （Spring3.1+）

其中使用 @Component 及衍生注解很常见，咱开发中常用的套路，不再赘述。
但模式注解只能在自己编写的代码中标注，无法装配jar包中的组件。为此可以使用 @Configuration 与 @Bean，手动装配组件（如上一篇的 @Configuration 示例）。但这种方式一旦注册过多，会导致编码成本高，维护不灵活等问题。
SpringFramework 提供了模块装配功能，通过给配置类标注 @EnableXXX 注解，再在注解上标注 @Import 注解，即可完成组件装配的效果。

#### 6.Spring Boot自动配置原理

详见：https://blog.csdn.net/Dongguabai/article/details/80865599


#### 7.谈谈自己对于Spring IOC的理解

IOC（Inversion Of Controll，控制反转）是一种设计思想，就是将原本在程序中手动创建对象的控制权，交由给Spring框架来管理。IOC在其他语言中也有应用，并非Spring特有。IOC容器是Spring用来实现IOC的载体，IOC容器实际上就是一个Map(key, value)，Map中存放的是各种对象。

将对象之间的相互依赖关系交给IOC容器来管理，并由IOC容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。IOC容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的。在实际项目中一个Service类可能由几百甚至上千个类作为它的底层，假如我们需要实例化这个Service，可能要每次都搞清楚这个Service所有底层类的构造函数，这可能会把人逼疯。如果利用IOC的话，你只需要配置好，然后在需要的地方引用就行了，大大增加了项目的可维护性且降低了开发难度。

Spring时代我们一般通过XML文件来配置Bean，后来开发人员觉得用XML文件来配置不太好，于是Sprng Boot注解配置就慢慢开始流行起来。

#### 8.谈谈自己对于Spring AOP的理解

AOP（Aspect-Oriented Programming，面向切面编程）能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可扩展性和可维护性。

Spring AOP是基于动态代理的，如果要代理的对象实现了某个接口，那么Spring AOP就会使用JDK动态代理去创建代理对象；而对于没有实现接口的对象，就无法使用JDK动态代理，转而使用CGlib动态代理生成一个被代理对象的子类来作为代理。
当然也可以使用AspectJ，Spring AOP中已经集成了AspectJ，AspectJ应该算得上是Java生态系统中最完整的AOP框架了。使用AOP之后我们可以把一些通用功能抽象出来，在需要用到的地方直接使用即可，这样可以大大简化代码量。我们需要增加新功能也方便，提高了系统的扩展性。日志功能、事务管理和权限管理等场景都用到了AOP。

#### 9.Spring AOP和AspectJ AOP有什么区别？

Spring AOP是属于运行时增强，而AspectJ是编译时增强。Spring AOP基于代理（Proxying），而AspectJ基于字节码操作（Bytecode Manipulation）。

Spring AOP已经集成了AspectJ，AspectJ应该算得上是Java生态系统中最完整的AOP框架了。AspectJ相比于Spring AOP功能更加强大，但是Spring AOP相对来说更简单。

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择AspectJ，它比SpringAOP快很多。

#### 10.Spring中的bean的作用域有哪些？

1.singleton：唯一bean实例，Spring中的bean默认都是单例的。
2.prototype：每次请求都会创建一个新的bean实例。
3.request：每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP request内有效。
4.session：每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP session内有效。
5.global-session：全局session作用域，仅仅在基于Portlet的Web应用中才有意义，Spring5中已经没有了。Portlet是能够生成语义代码（例如HTML）片段的小型Java Web插件。它们基于Portlet容器，可以像Servlet一样处理HTTP请求。但是与Servlet不同，每个Portlet都有不同的会话。

#### 11.Spring中的单例bean的线程安全问题了解吗？

大部分时候我们并没有在系统中使用多线程，所以很少有人会关注这个问题。单例bean存在线程问题，主要是因为当多个线程操作同一个对象的时候，对这个对象的非静态成员变量的写操作会存在线程安全问题。
有两种常见的解决方案：
1.在bean对象中尽量避免定义可变的成员变量（不太现实）。
2.在类中定义一个ThreadLocal成员变量，将需要的可变成员变量保存在ThreadLocal中（推荐的一种方式）。

#### 12.Spring中的bean生命周期了解过吗？

1.Bean容器找到配置文件中Spring Bean的定义。
2.Bean容器利用Java Reflection API创建一个Bean的实例。
3.如果涉及到一些属性值，利用set()方法设置一些属性值。
4.如果Bean实现了BeanNameAware接口，调用setBeanName()方法，传入Bean的名字。
5.如果Bean实现了BeanClassLoaderAware接口，调用setBeanClassLoader()方法，传入ClassLoader对象的实例。
6.如果Bean实现了BeanFactoryAware接口，调用setBeanClassFacotory()方法，传入ClassLoader对象的实例。
7.与上面的类似，如果实现了其他*Aware接口，就调用相应的方法。
8.如果有和加载这个Bean的Spring容器相关的BeanPostProcessor对象，执行postProcessBeforeInitialization()方法。
9.如果Bean实现了InitializingBean接口，执行afeterPropertiesSet()方法。
10.如果Bean在配置文件中的定义包含init-method属性，执行指定的方法。
11.如果有和加载这个Bean的Spring容器相关的BeanPostProcess对象，执行postProcessAfterInitialization()方法。
12.当要销毁Bean的时候，如果Bean实现了DisposableBean接口，执行destroy()方法。
13.当要销毁Bean的时候，如果Bean在配置文件中的定义包含destroy-method属性，执行指定的方法。

#### 13.Spring MVC的工作原理了解嘛？

1.客户端（浏览器）发送请求，直接请求到DispatcherServlet。
2.DispatcherServlet根据请求信息调用HandlerMapping，解析请求对应的Handler。
3.解析到对应的Handler（也就是我们平常说的Controller控制器）。
4.HandlerAdapter会根据Handler来调用真正的处理器来处理请求和执行相对应的业务逻辑。
5.处理器处理完业务后，会返回一个ModelAndView对象，Model是返回的数据对象，View是逻辑上的View。
6.ViewResolver会根据逻辑View去查找实际的View。
7.DispatcherServlet把返回的Model传给View（视图渲染）。
8.把View返回给请求者（浏览器）。

#### 14.Spring框架中用到了哪些设计模式？

1.工厂设计模式：Spring使用工厂模式通过BeanFactory和ApplicationContext创建bean对象。
2.代理设计模式：Spring AOP功能的实现。
3.单例设计模式：Spring中的bean默认都是单例的。
4.模板方法模式：Spring中的jdbcTemplate、hibernateTemplate等以Template结尾的对数据库操作的类，它们就使用到了模板模式。
5.包装器设计模式：我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。
6.观察者模式：Spring事件驱动模型就是观察者模式很经典的一个应用。
7.适配器模式：Spring AOP的增强或通知（Advice）使用到了适配器模式、Spring MVC中也是用到了适配器模式适配Controller。

#### 15.@Component和@Bean的区别是什么？

1.作用对象不同。@Component注解作用于类，而@Bean注解作用于方法。
2.@Component注解通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中（我们可以使用@ComponentScan注解定义要扫描的路径）。@Bean注解通常是在标有该注解的方法中定义产生这个bean，告诉Spring这是某个类的实例，当我需要用它的时候还给我。
3.@Bean注解比@Component注解的自定义性更强，而且很多地方只能通过@Bean注解来注册bean。比如当引用第三方库的类需要装配到Spring容器的时候，就只能通过@Bean注解来实现。

#### 16.将一个类声明为Spring的bean的注解有哪些？

我们一般使用@Autowired注解去自动装配bean。而想要把一个类标识为可以用@Autowired注解自动装配的bean，可以采用以下的注解实现：
1.@Component注解。通用的注解，可标注任意类为Spring组件。如果一个Bean不知道属于哪一个层，可以使用@Component注解标注。
2.@Repository注解。对应持久层，即Dao层，主要用于数据库相关操作。
3.@Service注解。对应服务层，即Service层，主要涉及一些复杂的逻辑，需要用到Dao层（注入）。
4.@Controller注解。对应Spring MVC的控制层，即Controller层，主要用于接受用户请求并调用Service层的方法返回数据给前端页面。

#### 17.Spring事务管理的方式有几种？

1.编程式事务：在代码中硬编码（不推荐使用）。
2.声明式事务：在配置文件中配置（推荐使用），分为基于XML的声明式事务和基于注解的声明式事务。

#### 18.Spring事务中的隔离级别有哪几种？

在TransactionDefinition接口中定义了五个表示隔离级别的常量：

ISOLATION_DEFAULT：使用后端数据库默认的隔离级别，Mysql默认采用的REPEATABLE_READ隔离级别；Oracle默认采用的READ_COMMITTED隔离级别。

ISOLATION_READ_UNCOMMITTED：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。

ISOLATION_READ_COMMITTED：允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生

ISOLATION_REPEATABLE_READ：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。

ISOLATION_SERIALIZABLE：最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

#### 19.Spring事务中有哪几种事务传播行为？

在TransactionDefinition接口中定义了八个表示事务传播行为的常量。

支持当前事务的情况：
PROPAGATION_REQUIRED：如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
PROPAGATION_SUPPORTS： 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
PROPAGATION_MANDATORY： 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性）。

不支持当前事务的情况：
PROPAGATION_REQUIRES_NEW： 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
PROPAGATION_NOT_SUPPORTED： 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
PROPAGATION_NEVER： 以非事务方式运行，如果当前存在事务，则抛出异常。

其他情况：
PROPAGATION_NESTED： 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于PROPAGATION_REQUIRED。

#### 20.Spring 事务底层原理

划分处理单元——IoC
由于spring解决的问题是对单个数据库进行局部事务处理的，具体的实现首先用spring中的IoC划分了事务处理单元。并且将对事务的各种配置放到了ioc容器中（设置事务管理器，设置事务的传播特性及隔离机制）。

AOP拦截需要进行事务处理的类
Spring事务处理模块是通过AOP功能来实现声明式事务处理的，具体操作（比如事务实行的配置和读取，事务对象的抽象），用TransactionProxyFactoryBean接口来使用AOP功能，生成proxy代理对象，通过TransactionInterceptor完成对代理方法的拦截，将事务处理的功能编织到拦截的方法中。读取ioc容器事务配置属性，转化为spring事务处理需要的内部数据结构（TransactionAttributeSourceAdvisor），转化为TransactionAttribute表示的数据对象。

对事务处理实现（事务的生成、提交、回滚、挂起）
spring委托给具体的事务处理器实现。实现了一个抽象和适配。适配的具体事务处理器：DataSource数据源支持、hibernate数据源事务处理支持、JDO数据源事务处理支持，JPA、JTA数据源事务处理支持。这些支持都是通过设计PlatformTransactionManager、AbstractPlatforTransaction一系列事务处理的支持。 为常用数据源支持提供了一系列的TransactionManager。

总结
PlatformTransactionManager实现了TransactionInterception接口，让其与TransactionProxyFactoryBean结合起来，形成一个Spring声明式事务处理的设计体系。

#### 21.BeanFactory和ApplicationContext有什么区别？

ApplicationContext提供了一种解决文档信息的方法，一种加载文件资源的方式(如图片)，他们可以向监听他们的beans发送消息。另外，容器或者容器中beans的操作，这些必须以bean工厂的编程方式处理的操作可以在应用上下文中以声明的方式处理。应用上下文实现了MessageSource，该接口用于获取本地消息，实际的实现是可选的。

相同点：两者都是通过xml配置文件加载bean，ApplicationContext和BeanFacotry相比，提供了更多的扩展功能。

不同点：BeanFactory是延迟加载，如果Bean的某一个属性没有注入，BeanFacotry加载后，直至第一次使用调用getBean方法才会抛出异常；而ApplicationContext则在初始化自身是检验，这样有利于检查所依赖属性是否注入；所以通常情况下我们选择使用ApplicationContext。


#### 22.Resource 是如何被查找、加载的？

Resource 接口是 Spring 资源访问策略的抽象，它本身并不提供任何资源访问实现，具体的资源访问由该接口的实现类完成——每个实现类代表一种资源访问策略。 Spring 为 Resource 接口提供了如下实现类：

UrlResource：访问网络资源的实现类。ClassPathResource：访问类加载路径里资源的实现类。FileSystemResource：访问文件系统里资源的实现类。ServletContextResource：访问相对于 ServletContext 路径里的资源的实现类：InputStreamResource：访问输入流资源的实现类。ByteArrayResource：访问字节数组资源的实现类。 这些 Resource 实现类，针对不同的的底层资源，提供了相应的资源访问逻辑，并提供便捷的包装，以利于客户端程序的资源访问。

#### 23.解释自动装配的各种模式？

自动装配提供五种不同的模式供Spring容器用来自动装配beans之间的依赖注入:

no：默认的方式是不进行自动装配，通过手工设置ref 属性来进行装配bean。

byName：通过参数名自动装配，Spring容器查找beans的属性，这些beans在XML配置文件中被设置为byName。之后容器试图匹配、装配和该bean的属性具有相同名字的bean。

byType：通过参数的数据类型自动自动装配，Spring容器查找beans的属性，这些beans在XML配置文件中被设置为byType。之后容器试图匹配和装配和该bean的属性类型一样的bean。如果有多个bean符合条件，则抛出错误。

constructor：这个同byType类似，不过是应用于构造函数的参数。如果在BeanFactory中不是恰好有一个bean与构造函数参数相同类型，则抛出一个严重的错误。

autodetect：如果有默认的构造方法，通过 construct的方式自动装配，否则使用 byType的方式自动装配。

#### 24.有哪些不同类型的IOC(依赖注入)？

构造器依赖注入：构造器依赖注入在容器触发构造器的时候完成，该构造器有一系列的参数，每个参数代表注入的对象。

Setter方法依赖注入：首先容器会触发一个无参构造函数或无参静态工厂方法实例化对象，之后容器调用bean中的setter方法完成Setter方法依赖注入。

#### 25.Spring AOP 实现原理

实现AOP的技术，主要分为两大类：

一是采用动态代理技术，利用截取消息的方式，对该消息进行装饰，以取代原有对象行为的执行；二是采用静态织入的方式，引入特定的语法创建“方面”，从而使得编译器可以在编译期间织入有关“方面”的代码。
Spring AOP 的实现原理其实很简单：AOP 框架负责动态地生成 AOP 代理类，这个代理类的方法则由 Advice和回调目标对象的方法所组成, 并将该对象可作为目标对象使用。AOP 代理包含了目标对象的全部方法，但AOP代理中的方法与目标对象的方法存在差异，AOP方法在特定切入点添加了增强处理，并回调了目标对象的方法。

Spring AOP使用动态代理技术在运行期织入增强代码。使用两种代理机制：基于JDK的动态代理（JDK本身只提供接口的代理）和基于CGlib的动态代理。

(1) JDK的动态代理
JDK的动态代理主要涉及java.lang.reflect包中的两个类：Proxy和InvocationHandler。其中InvocationHandler只是一个接口，可以通过实现该接口定义横切逻辑，并通过反射机制调用目标类的代码，动态的将横切逻辑与业务逻辑织在一起。而Proxy利用InvocationHandler动态创建一个符合某一接口的实例，生成目标类的代理对象。
其代理对象必须是某个接口的实现, 它是通过在运行期间创建一个接口的实现类来完成对目标对象的代理.只能实现接口的类生成代理，而不能针对类。
(2)CGLib
CGLib采用底层的字节码技术，为一个类创建子类，并在子类中采用方法拦截的技术拦截所有父类的调用方法，并顺势织入横切逻辑.它运行期间生成的代理对象是目标类的扩展子类.所以无法通知final、private的方法,因为它们不能被覆写.是针对类实现代理,主要是为指定的类生成一个子类，覆盖其中方法。
在spring中默认。况下使用JDK动态代理实现AOP,如果proxy-target-class设置为true或者使用了优化策略那么会使用CGLIB来创建动态代理.Spring　AOP在这两种方式的实现上基本一样．以JDK代理为例，会使用JdkDynamicAopProxy来创建代理，在invoke()方法首先需要织入到当前类的增强器封装到拦截器链中，然后递归的调用这些拦截器完成功能的织入，最终返回代理对象。

#### 26.ApplicationContext通常的实现是什么?

- FileSystemXmlApplicationContext ：此容器从一个XML文件中加载beans的定义，XML Bean 配置文件的全路径名必须提供给它的构造函数。
- ClassPathXmlApplicationContext：此容器也从一个XML文件中加载beans的定义，这里，你需要正确设置classpath因为这个容器将在classpath里找bean配置。
- WebXmlApplicationContext：此容器加载一个XML文件，此文件定义了一个WEB应用的所有bean。

#### 27. Bean 工厂和 Application contexts 有什么区别？

Application contexts提供一种方法处理文本消息，一个通常的做法是加载文件资源（比如镜像），它们可以向注册为监听器的bean发布事件。另外，在容器或容器内的对象上执行的那些不得不由bean工厂以程序化方式处理的操作，可以在Application contexts中以声明的方式处理。Application contexts实现了MessageSource接口，该接口的实现以可插拔的方式提供获取本地化消息的方法。


### 参考资料
https://www.cnblogs.com/yanggb/p/11004887.html
https://www.jianshu.com/p/a5d960c6f6dd
https://ifeve.com/spring-interview-questions-and-answers/
