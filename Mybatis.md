#### 1.什么是Mybatis？

**1.** mybatis是一个半ORM框架，它内部封装了JDBC，开发时只需要关乎sql语句本身，不需要花费精力去处理驱动，创建连接，创建1statement等繁复过程。

 **2.** mybatis可以使用xml或注解来配置和映射原生信息。将pijo映射成数据库中的记录，避免了几乎所有的JDBC 代码和手动设置参数以及获取结果集。

 **3.** 通过xm文件或注解的方式将要执行的各种statement配置起来,并通java对象和statement中sql的动态参数进行映射生成最终的sql语句,最后由mybatis框架执行sql并将结果映射java对象返回.

#### 2.Mybatis的优缺点?

Mybaits的优点：

（1）基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除sql与程序代码的耦合，便于统一管理；提供XML标签，支持编写动态SQL语句，并可重用。

（2）与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接；

（3）很好的与各种数据库兼容（因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）。

（4）能够与Spring很好的集成；

（5）提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护。

MyBatis框架的缺点：

（1）SQL语句的编写工作量较大，尤其当字段多、关联表多时，对开发人员编写SQL语句的功底有一定要求。

（2）SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。

MyBatis框架适用场合：

（1）MyBatis专注于SQL本身，是一个足够灵活的DAO层解决方案。

（2）对性能的要求很高，或者需求变化较多的项目，如互联网项目，MyBatis将是不错的选择。

#### 3.Mybatis使用场合?

专注于sql本身,是一个足够灵活的dao层解决方案.,对性能的要求很高,或者需求多变的项目,

#### 4.#{}和${}的区别是什么？

\#{}是预编译处理，${}是字符串替换。

Mybatis在处理#{}时，会将sql中的#{}替换为?号，调用PreparedStatement的set方法来赋值；

Mybatis在处理{}时，就是把时，就是把{}替换成变量的值。

使用#{}可以有效的防止SQL注入，提高系统安全性。

#### 5.当实体类的属性名和表种字段名不一致怎么办?

有两种解决方案:可以在sql语句给字段名取别名,别名于实体类属性名同名,也可以用resultMap来映射字段名和实体类属性名一一对应.


#### 6.Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？

第一种是使用resultMap标签，逐一定义数据库列名和对象属性名之间的映射关系。

第二种是使用sql列的别名功能，将列的别名书写为对象属性名。

有了列名与属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

#### 7.如何获取自动生成的(主)键值?

```
<insert id=”insertname” usegeneratedkeys=”true” keyproperty=”id”>
     insert into names (name) values (#{name})
</insert>
```

#### 8.Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？

不同的Xml映射文件，如果配置了namespace，那么id可以重复；如果没有配置namespace，那么id不能重复

#### 9. **Mybatis动态SQL？**

1) 传统的JDBC的方法，在组合SQL语句的时候需要去拼接，稍微不注意就会少少了一个空格，标点符号，都会导致系统错误。Mybatis的动态SQL就是为了解决这种问题而产生的；Mybatis的动态SQL语句值基于OGNL表达式的，方便在SQL语句中实现某些逻辑；可以使用标签组合成灵活的sql语句，提供开发的效率。

 2) Mybatis的动态SQL标签主要由以下几类： If语句（简单的条件判断） Choose(when/otherwise),相当于java语言中的switch，与jstl中choose类似 Trim(对包含的内容加上prefix，或者suffix) Where(主要是用来简化SQL语句中where条件判断，能智能的处理and/or 不用担心多余的语法导致的错误) Set(主要用于更新时候) Foreach(一般使用在mybatis in语句查询时特别有用)

#### 10.说一下resultMap和resultType？

resultmap是手动提交，人为提交，resulttype是自动提交
MyBatis中在查询进行select映射的时候，返回类型可以用resultType，也可以用resultMap，resultType是直接表示返回类型的，而resultMap则是对外部ResultMap的引用，但是resultType跟resultMap不能同时存在。
在MyBatis进行查询映射时，其实查询出来的每一个属性都是放在一个对应的Map里面的，其中键是属性名，值则是其对应的值。
1.当提供的返回类型属性是resultType时，MyBatis会将Map里面的键值对取出赋给resultType所指定的对象对应的属性。所以其实MyBatis的每一个查询映射的返回类型都是ResultMap，只是当提供的返回类型属性是resultType的时候，MyBatis对自动的给把对应的值赋给resultType所指定对象的属性。
2.当提供的返回类型是resultMap时，因为Map不能很好表示领域模型，就需要自己再进一步的把它转化为对应的对象，这常常在复杂查询中很有作用。

#### 11. Mybatis全局配置文件中有哪些标签?分别代表什么意思?

configuration 配置
properties 属性:可以加载properties配置文件的信息
settings 设置：可以设置mybatis的全局属性
typeAliases 类型命名
typeHandlers 类型处理器
objectFactory 对象工厂
plugins 插件
environments 环境
environment 环境变量
transactionManager 事务管理器
dataSource 数据源
mappers 映射器

#### 12.**Mybatis能执行一对一、一对多的关联查询吗？都有哪些实现方式，以及它们之间的区别。**

答：能，Mybatis不仅可以执行一对一、一对多的关联查询，还可以执行多对一，多对多的关联查询，多对一查询，其实就是一对一查询，只需要把selectOne()修改为selectList()即可；多对多查询，其实就是一对多查询，只需要把selectOne()修改为selectList()即可。

　　关联对象查询，有两种实现方式，一种是单独发送一个sql去查询关联对象，赋给主对象，然后返回主对象。另一种是使用嵌套查询，嵌套查询的含义为使用join查询，一部分列是A对象的属性值，另外一部分列是关联对象B的属性值，好处是只发一个sql查询，就可以把主对象和其关联对象查出来。

　　那么问题来了，join查询出来100条记录，如何确定主对象是5个，而不是100个？其去重复的原理是<resultMap>标签内的<id>子标签，指定了唯一确定一条记录的id列，Mybatis根据<id>列值来完成100条记录的去重复功能，<id>可以有多个，代表了联合主键的语意。

　　同样主对象的关联对象，也是根据这个原理去重复的，尽管一般情况下，只有主对象会有重复记录，关联对象一般不会重复。

#### 13.**Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？**

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

当然了，不光是Mybatis，几乎所有的包括Hibernate，支持延迟加载的原理都是一样的。

#### 14.**Mybatis都有哪些Executor执行器？它们之间的区别是什么？**

Mybatis有三种基本的Executor执行器，**SimpleExecutor、ReuseExecutor、BatchExecutor。**

**SimpleExecutor：**每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。

**ReuseExecutor：**执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map<String, Statement>内，供下一次使用。简言之，就是重复使用Statement对象。

**BatchExecutor：**执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。

作用范围：Executor的这些特点，都严格限制在SqlSession生命周期范围内。

#### 15.**Mybatis的一级、二级缓存**

（1）一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。

（2）二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap 存储，不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的映射文件中配置<cache/> ；

（3）对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear 掉并重新更新，如果开启了二级缓存，则只根据配置判断是否刷新。



#### 参考链接

https://my.oschina.net/u/4116655/blog/3055230

https://www.w3cschool.cn/kzsow/kzsow-4ovb2grl.html

https://www.cnblogs.com/lukelook/p/11099039.html

https://zhuanlan.zhihu.com/p/60257737

https://www.cnblogs.com/qmillet/p/12523636.html

https://blog.csdn.net/a745233700/article/details/80977133