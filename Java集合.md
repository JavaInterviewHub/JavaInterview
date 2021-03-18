## Java集合

* [1.常见的集合有哪些？](#1常见的集合有哪些)
* [2.常见的集合底层实现](#2常见的集合底层实现)
* [3.HashMap与HashTable的区别？](#3hashmap与hashtable的区别)
* [4.ConcurrentHashMap和Hashtable的区别？](#4concurrenthashmap和hashtable的区别)
* [5.ConcurrentHashMap实现原理](#5concurrenthashmap实现原理)
* [6.ArrayList 和 Vector 的区别？](#6arraylist-和-vector-的区别)
* [7.ArrayList和LinkedList的区别？](#7arraylist和linkedlist的区别)
* [8.HashMap 默认的初始化长度是多少？](#8hashmap-默认的初始化长度是多少)
* [9.谈谈对HashMap 构造方法中初始容量、加载因子的理解](#9谈谈对hashmap-构造方法中初始容量加载因子的理解)
* [10.Java集合框架是什么？说出一些集合框架的优点？](#10java集合框架是什么说出一些集合框架的优点)
* [11.集合框架中的泛型有什么优点？](#11集合框架中的泛型有什么优点)
* [12.为何Collection不从Cloneable和Serializable接口继承？](#12为何collection不从cloneable和serializable接口继承)
* [13.为何Map接口不继承Collection接口？](#13为何map接口不继承collection接口)
* [14.Iterator是什么？](#14iterator是什么)
* [15.Enumeration和Iterator接口的区别？](#15enumeration和iterator接口的区别)
* [16.Iterater和ListIterator之间有什么区别？](#16iterater和listiterator之间有什么区别)
* [17.fail-fast与fail-safe有什么区别？](#17fail-fast与fail-safe有什么区别)
* [18.hashCode()和equals()方法有何重要性？](#18hashcode和equals方法有何重要性)
* [19.我们能否使用任何类作为Map的key？](#19我们能否使用任何类作为map的key)
* [20.如何决定选用HashMap还是TreeMap？](#20如何决定选用hashmap还是treemap)
* [21.哪些集合类提供对元素的随机访问？](#21哪些集合类提供对元素的随机访问)
* [22.BlockingQueue是什么？](#22blockingqueue是什么)
* [23.队列和栈是什么，列出它们的区别？](#23队列和栈是什么列出它们的区别)
* [24.Collections类是什么？](#24collections类是什么)
* [25.Comparable和Comparator接口有何区别？](#25comparable和comparator接口有何区别)

#### 1.常见的集合有哪些？

Collection接口的子接口包括：Set接口和List接口
Map接口的实现类主要有：HashMap、TreeMap、Hashtable、ConcurrentHashMap以及Properties等
Set接口的实现类主要有：HashSet、TreeSet、LinkedHashSet等
List接口的实现类主要有：ArrayList、LinkedList、Stack以及Vector等

#### 2.常见的集合底层实现

ArrayList底层是数组。
LinkedList底层是双向链表。
HashMap底层与HashTable原理相同，Java 8版本以后如果同一位置哈希冲突大于8则链表变成红黑树。
HashTable底层是链地址法组成的哈希表（即数组+单项链表组成）。
HashSet底层是HashMap。
LinkedHashMap底层修改自HashMap，包含一个维护插入顺序的双向链表。
TreeMap底层是红黑树。
LinkedHashSet底层是LinkedHashMap。
TreeSet底层是TreeMap。

#### 3.HashMap与HashTable的区别？

HashMap没有考虑同步，是线程不安全的；Hashtable使用了synchronized关键字，是线程安全的；
HashMap允许K/V都为null；后者K/V都不允许为null；

#### 4.ConcurrentHashMap和Hashtable的区别？

ConcurrentHashMap 结合了 HashMap 和 HashTable 二者的优势。HashMap 没有考虑同步，HashTable 考虑了同步的问题。但是 HashTable 在每次同步执行时都要锁住整个结构。 ConcurrentHashMap 锁的方式是稍微细粒度的。

#### 5.ConcurrentHashMap实现原理

JDK1.7 : 【数组（Segment） + 数组（HashEntry） + 链表（HashEntry节点）】
ConcurrentHashMap（分段锁） 对整个桶数组进行了分割分段(Segment)，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。
Segment是一种可重入锁ReentrantLock，在ConcurrentHashMap里扮演锁的角色，HashEntry则用于存储键值对数据。

JDK1.8 : Node数组+链表 / 红黑树
利用CAS+Synchronized来保证并发更新的安全，底层依然采用数组+链表+红黑树的存储结构。

#### 6.ArrayList 和 Vector 的区别？

Vector 是线程安全的，ArrayList 是线程不安全的。
Vector在数据满时增长为原来的两倍，而 ArrayList在数据量达到容量的一半时,增长为原容量的1.5倍。

#### 7.ArrayList和LinkedList的区别？

LinkedList基于链表的数据结构；ArrayList基于动态数组的数据结构
LinkedList 在插入和删除数据时效率更高，ArrayList 查询效率更高；

#### 8.HashMap 默认的初始化长度是多少？

在JDK中默认长度是16，并且默认长度和扩容后的长度都必须是 2 的幂。

#### 9.谈谈对HashMap 构造方法中初始容量、加载因子的理解

初始容量代表了哈希表中桶的初始数量，即 Entry< K,V>[] table 数组的初始长度。
加载因子是哈希表在其容量自动增加之前可以达到多满的一种饱和度百分比，其衡量了一个散列表的空间的使用程度，负载因子越大表示散列表的装填程度越高，反之愈小。

#### 10.Java集合框架是什么？说出一些集合框架的优点？

每种编程语言中都有集合，最初的Java版本包含几种集合类：Vector、Stack、HashTable和Array。随着集合的广泛使用，Java1.2提出了囊括所有集合接口、实现和算法的集合框架。在保证线程安全的情况下使用泛型和并发集合类，Java已经经历了很久。它还包括在Java并发包中，阻塞接口以及它们的实现。集合框架的部分优点如下：

（1）使用核心集合类降低开发成本，而非实现我们自己的集合类。

（2）随着使用经过严格测试的集合框架类，代码质量会得到提高。

（3）通过使用JDK附带的集合类，可以降低代码维护成本。

（4）复用性和可操作性。

#### 11.集合框架中的泛型有什么优点？

Java1.5引入了泛型，所有的集合接口和实现都大量地使用它。泛型允许我们为集合提供一个可以容纳的对象类型，因此，如果你添加其它类型的任何元素，它会在编译时报错。这避免了在运行时出现ClassCastException，因为你将会在编译时得到报错信息。泛型也使得代码整洁，我们不需要使用显式转换和instanceOf操作符。它也给运行时带来好处，因为不会产生类型检查的字节码指令。

#### 12.为何Collection不从Cloneable和Serializable接口继承？

Collection接口指定一组对象，对象即为它的元素。如何维护这些元素由Collection的具体实现决定。例如，一些如List的Collection实现允许重复的元素，而其它的如Set就不允许。很多Collection实现有一个公有的clone方法。然而，把它放到集合的所有实现中也是没有意义的。这是因为Collection是一个抽象表现。重要的是实现。

当与具体实现打交道的时候，克隆或序列化的语义和含义才发挥作用。所以，具体实现应该决定如何对它进行克隆或序列化，或它是否可以被克隆或序列化。

在所有的实现中授权克隆和序列化，最终导致更少的灵活性和更多的限制。特定的实现应该决定它是否可以被克隆和序列化。

#### 13.为何Map接口不继承Collection接口？

尽管Map接口和它的实现也是集合框架的一部分，但Map不是集合，集合也不是Map。因此，Map继承Collection毫无意义，反之亦然。

如果Map继承Collection接口，那么元素去哪儿？Map包含key-value对，它提供抽取key或value列表集合的方法，但是它不适合“一组对象”规范。

#### 14.Iterator是什么？

Iterator接口提供遍历任何Collection的接口。我们可以从一个Collection中使用迭代器方法来获取迭代器实例。迭代器取代了Java集合框架中的Enumeration。迭代器允许调用者在迭代过程中移除元素。

#### 15.Enumeration和Iterator接口的区别？

Enumeration的速度是Iterator的两倍，也使用更少的内存。Enumeration是非常基础的，也满足了基础的需要。但是，与Enumeration相比，Iterator更加安全，因为当一个集合正在被遍历的时候，它会阻止其它线程去修改集合。

迭代器取代了Java集合框架中的Enumeration。迭代器允许调用者从集合中移除元素，而Enumeration不能做到。为了使它的功能更加清晰，迭代器方法名已经经过改善。

#### 16.Iterater和ListIterator之间有什么区别？

（1）我们可以使用Iterator来遍历Set和List集合，而ListIterator只能遍历List。

（2）Iterator只可以向前遍历，而LIstIterator可以双向遍历。

（3）ListIterator从Iterator接口继承，然后添加了一些额外的功能，比如添加一个元素、替换一个元素、获取前面或后面元素的索引位置。

#### 17.fail-fast与fail-safe有什么区别？

Iterator的fail-fast属性与当前的集合共同起作用，因此它不会受到集合中任何改动的影响。Java.util包中的所有集合类都被设计为fail-fast的，而java.util.concurrent中的集合类都为fail-safe的。Fail-fast迭代器抛出ConcurrentModificationException，而fail-safe迭代器从不抛出ConcurrentModificationException。

#### 18.hashCode()和equals()方法有何重要性？

HashMap使用Key对象的hashCode()和equals()方法去决定key-value对的索引。当我们试着从HashMap中获取值的时候，这些方法也会被用到。如果这些方法没有被正确地实现，在这种情况下，两个不同Key也许会产生相同的hashCode()和equals()输出，HashMap将会认为它们是相同的，然后覆盖它们，而非把它们存储到不同的地方。同样的，所有不允许存储重复数据的集合类都使用hashCode()和equals()去查找重复，所以正确实现它们非常重要。equals()和hashCode()的实现应该遵循以下规则：

（1）如果o1.equals(o2)，那么o1.hashCode() == o2.hashCode()总是为true的。

（2）如果o1.hashCode() == o2.hashCode()，并不意味着o1.equals(o2)会为true。


#### 19.我们能否使用任何类作为Map的key？

我们可以使用任何类作为Map的key，然而在使用它们之前，需要考虑以下几点：

（1）如果类重写了equals()方法，它也应该重写hashCode()方法。

（2）类的所有实例需要遵循与equals()和hashCode()相关的规则。请参考之前提到的这些规则。

（3）如果一个类没有使用equals()，你不应该在hashCode()中使用它。

（4）用户自定义key类的最佳实践是使之为不可变的，这样，hashCode()值可以被缓存起来，拥有更好的性能。不可变的类也可以确保hashCode()和equals()在未来不会改变，这样就会解决与可变相关的问题了。

#### 20.如何决定选用HashMap还是TreeMap？

对于在Map中插入、删除和定位元素这类操作，HashMap是最好的选择。然而，假如你需要对一个有序的key集合进行遍历，TreeMap是更好的选择。基于你的collection的大小，也许向HashMap中添加元素会更快，将map换为TreeMap进行有序key的遍历。

#### 21.哪些集合类提供对元素的随机访问？

 ArrayList、HashMap、TreeMap和HashTable类提供对元素的随机访问。

#### 22.BlockingQueue是什么？

Java.util.concurrent.BlockingQueue是一个队列，在进行检索或移除一个元素的时候，它会等待队列变为非空；当在添加一个元素时，它会等待队列中的可用空间。BlockingQueue接口是Java集合框架的一部分，主要用于实现生产者-消费者模式。我们不需要担心等待生产者有可用的空间，或消费者有可用的对象，因为它都在BlockingQueue的实现类中被处理了。Java提供了集中BlockingQueue的实现，比如ArrayBlockingQueue、LinkedBlockingQueue、PriorityBlockingQueue,、SynchronousQueue等。

#### 23.队列和栈是什么，列出它们的区别？

栈和队列两者都被用来预存储数据。java.util.Queue是一个接口，它的实现类在Java并发包中。队列允许先进先出（FIFO）检索元素，但并非总是这样。Deque接口允许从两端检索元素。

栈与队列很相似，但它允许对元素进行后进先出（LIFO）进行检索。

Stack是一个扩展自Vector的类，而Queue是一个接口。

#### 24.Collections类是什么？

Java.util.Collections是一个工具类仅包含静态方法，它们操作或返回集合。它包含操作集合的多态算法，返回一个由指定集合支持的新集合和其它一些内容。这个类包含集合框架算法的方法，比如折半搜索、排序、混编和逆序等。

#### 25.Comparable和Comparator接口有何区别？

Comparable和Comparator接口被用来对对象集合或者数组进行排序。Comparable接口被用来提供对象的自然排序，我们可以使用它来提供基于单个逻辑的排序。

Comparator接口被用来提供不同的排序算法，我们可以选择需要使用的Comparator来对给定的对象集合进行排序。


#### 参考资料

https://blog.csdn.net/qq_36387471/article/details/105479208

https://blog.csdn.net/u010775025/article/details/79315361
