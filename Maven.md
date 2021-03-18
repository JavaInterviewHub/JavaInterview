## Maven

* [1.maven是什么？](#1maven是什么)
* [2.使用Maven好处](#2使用maven好处)
* [3.Maven的坐标和依赖](#3maven的坐标和依赖)
* [4.Maven的⽣命周期](#4maven的命周期)
* [5.你们项目为什么选用 Maven 进行构建？](#5你们项目为什么选用-maven-进行构建)
* [6.Maven 规约是什么？](#6maven-规约是什么)
* [7.Maven 常用命令](#7maven-常用命令)
* [8.Maven 有哪些优点和缺点](#8maven-有哪些优点和缺点)
* [9.Maven 版本规则？](#9maven-版本规则)
* [10.对于一个多模块项目，如果管理项目依赖的版本？](#10对于一个多模块项目如果管理项目依赖的版本)
* [11.Maven 依赖原则？](#11maven-依赖原则)
* [12.如何解决 jar 冲突？](#12如何解决-jar-冲突)
* [13.什么是 Maven 插件？](#13什么是-maven-插件)
* [14.Maven依赖冲突](#14maven依赖冲突)
* [15.依赖的解析机制](#15依赖的解析机制)


#### 1.maven是什么？

Apache Maven是一个软件项目管理和理解工具。基于项目对象模型（POM）的概念，Maven可以从一个中心信息管理项目的构建，报告和文档。
项目构建
在eclipse中新建一个WEB工程。
进行编码及编写配置文件
对源代码进行编译运行，生成class文件
打成war包，部署至tomcat

#### 2.使用Maven好处

Maven能提供一种项目的配置，配置好的项目，只需要运行一条简单的命令，就能完成重复的，繁琐的构建动作.
Maven能提供一种项目的依赖配置.可以自动的导入项目依赖的jar,并且自动导入这些jar包依赖的第三方的jar包.
Maven提供了一种标准的项目目录结构，测试命名规则等项目的最佳实践方案，统一了不同项目的学习成本.
maven的常用命令
mvn clean : 清理
mvn compile：编译
mvn package：打包
mvn test : 测试，⾃动运⾏所有的测试⽤例
mvn install : 安装，将项⽬打的包安装到本地仓库，其他项⽬就可以依赖了
mvn jetty:run : 运⾏jetty插件

#### 3.Maven的坐标和依赖

maven的坐标组成部分
groupId: 组织机构id，org.aptech.hdax， org.springframework
artifactId: ⼦项⽬编号，springmvc, spring-test, spring-core
version: 版本号，可以⼀直迭代，平时项⽬开发⽤的是快照版本 0.0.1-SNAPSHOT
Package： jar war pom
项⽬依赖的核⼼概念：
框架整合最害怕jar包冲突，之前不使⽤maven，经常出现这个⽂件。
依赖范围 scope标签进⾏配置
Compile: 默认值，项⽬打包的时候会把该依赖包打进去
Test : 测试依赖，只是在运⾏测试⽤例的时候会⽤到，打包是不打进去的
Provided: 提供依赖，类似于test
传递依赖
a -> b, b->c ,如果在a中导⼊到b的依赖，c会⾃动过来
依赖调解
如果不同的包传递依赖了⼀个相同的jar，但是版本不⼀致
原则：
最短路径
第⼀声明优先原则
排除依赖
归类依赖: ⽅便后期的依赖版本升级、降级

#### 4.Maven的⽣命周期

一个项目的构建过成通常包括清理、编译、测试、打包、集成测试、验证、部署等。Maven从中抽取了一套完善的、易扩展的生命周期。Maven的生命周期是抽象的，其中的具体任务都交由插件来完成。Maven为大多数构建任务编写并绑定了默认的插件。
Maven定义了三套生命周期：clean、default、site，每个生命周期都包含了一些阶段（phase）。三套生命周期相互独立，但各个生命周期中的阶段却是有顺序的，且后面的夹断依赖于前面的阶段。执行某个阶段时，其前面的阶段会依顺序执行，但不会触发另外两套生命周期中的任何阶段。

#### 5.你们项目为什么选用 Maven 进行构建？

- 首先，Maven 是一个优秀的项目构建工具。使用maven，可以很方便的对项目进行分模块构建，这样在开发和测试打包部署时，效率会提高很多。
- 其次，Maven 可以进行依赖的管理。使用 Maven ，可以将不同系统的依赖进行统一管理，并且可以进行依赖之间的传递和继承。

#### 6.Maven 规约是什么？

- `/src/main/java/` ：Java 源码。
- `/src/main/resource` ：Java 配置文件，资源文件。
- `/src/test/java/` ：Java 测试代码。
- `/src/test/resource` ：Java 测试配置文件，资源文件。
- `/target` ：文件编译过程中生成的 `.class` 文件、jar、war 等等。
- `pom.xml` ：配置文件

Maven 要负责项目的自动化构建，以编译为例，Maven 要想自动进行编译，那么它必须知道 Java 的源文件保存在哪里，这样约定之后，不用我们手动指定位置，Maven 能知道位置，从而帮我们完成自动编译。

遵循**“约定>>>配置>>>编码”**。即能进行配置的不要去编码指定，能事先约定规则的不要去进行配置。这样既减轻了劳动力，也能防止出错。

#### 7.Maven 常用命令

- `mvn archetype：create` ：创建 Maven 项目。
- `mvn compile` ：编译源代码。
- `mvn deploy` ：发布项目。
- `mvn test-compile` ：编译测试源代码。
- `mvn test` ：运行应用程序中的单元测试。
- `mvn site` ：生成项目相关信息的网站。
- `mvn clean` ：清除项目目录中的生成结果。
- `mvn package` ：根据项目生成的 jar/war 等。
- `mvn install` ：在本地 Repository 中安装 jar 。
- `mvn eclipse:eclipse` ：生成 Eclipse 项目文件。
- `mvn jetty:run` 启动 Jetty 服务。
- `mvn tomcat:run` ：启动 Tomcat 服务。
- `mvn clean package -Dmaven.test.skip=true` ：清除以前的包后重新打包，跳过测试类。
- 用到最多的命令
  - `mvn eclipse:clean` ：清除 Project 中以前的编译的东西，重新再来。
  - `mvn eclipse:eclipse` ：开始编译 Maven 的 Project 。
  - `mvn clean package` ：清除以前的包后重新打包。

#### 8.Maven 有哪些优点和缺点

1）优点

- 简化了项目依赖管理。

  当年，多少人被 SSH 整合搞死搞活，很多时候，是因为依赖不完整，或者版本不正确。自从 Maven 出来后，终于可以无痛了~当然，也有一部分功劳是 Spring Boot ，这是后话。

- 易于上手，对于新手可能一个 `mvn clean package` 命令就可能满足我们的工作。

- 便于与持续集成工具(Jenkins)整合。

- 便于项目升级，无论是项目本身升级还是项目使用的依赖升级。

- 有助于多模块项目的开发，一个模块开发好后，发布到仓库，依赖该模块时可以直接从仓库更新，而不用自己去编译。

- Maven 有很多插件，便于功能扩展，比如生产站点，自动发布版本等。

 2）缺点

- Maven 是一个庞大的构建系统，学习难度大。

  这里的学习，更多指的完整学习。如果基本使用，并不会存在该问题。

- Maven 采用约定优于配置的策略(convention over configuration)，虽然上手容易，但是一旦出了问题，难于调试。

  这个确实，略微痛苦。

- 当依赖很多时，m2eclipse 老是搞得 Eclipse 很卡。

  使用 IDEA ，而不是 Eclipse ，完美解决。

- 中国的网络环境差，很多 repository 无法访问，比如 Google Code、 JBoss 仓库无法访问等。

  这个也好解决，在 `<mirrors>` 中增加阿里巴巴的 Maven 私服，具体可以参见 [《提高 Maven 速度 —— Maven 仓库修改成国内阿里巴巴地址》](https://my.oschina.net/af8991/blog/833513) 文章。

#### 9.Maven 版本规则？

Maven 主要是这样定义版本规则的：`<主版本>.<次版本>.<增量版本>` 。比如说 `1.2.3` ，主版本是 1 ，次版本是 2 ，增量版本是 3 。

- 主版本，一般来说代表了项目的重大的架构变更，比如说 Maven 1 和 Maven 2 ，在架构上已经两样了，将来的 Maven 3 和 Maven 2 也会有很大的变化。
- 次版本，一般代表了一些功能的增加或变化，但没有架构的变化，比如说Nexus 1.3 较之于 Nexus 1.2 来说，增加了一系列新的或者改进的功能（仓库镜像支持，改进的仓库管理界面等等），但从大的架构上来说，1.3 和 1.2 没什么区别。
- 增量版本，一般是一些小的 bug fix ，不会有重大的功能变化。

一般来说，在我们发布一次重要的版本之后，随之会开发新的版本。比如说，`myapp-1.1` 发布之后，就着手开发 `myapp-1.2` 了。由于`myapp-1.2` 有新的主要功能的添加和变化，在发布测试前，它会变得不稳定，而 `myapp-1.1` 是一个比较稳定的版本，现在的问题是，我们在`myapp-1.1中` 发现了一些 BUG（当然在 1.2 中也存在），为了能够在一段时间内修复 BUG 并仍然发布稳定的版本，我们就会用到分支(branch)，我们基于 1.1 开启一个分支 1.1.1 ，在这个分支中修复 BUG ，并快速发布。这既保证了版本的稳定，也能够使bug得到快速修复，也不同停止 1.2 的开发。只是，每次修复分支 1.1.1 中的 BUG 后，需要 merge 代码到 1.2 中。

#### 10.对于一个多模块项目，如果管理项目依赖的版本？

- 方式一，通过在父模块中声明 `<dependencyManagement />` 和`<pluginManagement />`， 然后让子模块通过元素指定父模块，这样子模块在定义依赖是就可以只定义 `groupId` 和 `artifactId`，自动使用父模块的 `version` ，这样统一整个项目的依赖的版本。

  继承的方式。

- 方式二，使用 `<dependencie />` 声明 `<scope />` 为 `import` 的依赖，从而引入一个 pom 的`<dependencyManagement />` 的。具体的，可以看看 [《Maven Spring BOM (bill of materials)》](https://www.cnblogs.com/YLsY/p/5711103.html)文章。

  组合的方式。

#### 11.Maven 依赖原则？

- 1、赖路径最短优先原则。

  一个项目 Demo 依赖了两个 jar 包，其中 `A-B-C-X(1.0)` ， `A-D-X(2.0)` 。由于 `X(2.0)` 路径最短，所以项目使用的是 `X(2.0)` 。

- 2、pom文 件中申明顺序优先。

  如果 `A-B-X(1.0)` ，`A-C-X(2.0)` 这样的路径长度一样怎么办呢？这样的情况下，Maven 会根据 pom 文件声明的顺序加载，如果先声明了 B ，后声明了 C ，那就最后的依赖就会是 `X(1.0)` 。

- 3、覆写优先

  子 pom 内声明的优先于父 pom 中的依赖。

#### 12.如何解决 jar 冲突？

遇到冲突的时候第一步，要找到 Maven 加载的到时是什么版本的 jar 包，通过们 `mvn dependency:tree` 查看依赖树，或者使用 [IDEA Maven Helper](https://plugins.jetbrains.com/plugin/7179-maven-helper) 插件。

然后，通过 Maven 的依赖原则来调整坐标在 pom 文件的申明顺序是最好的办法，或者使用将冲突中不想要的 jar 引入的 jar 进行 `<exclusions>` 掉。

#### 13.什么是 Maven 插件？

Maven 生命周期的每一个阶段的具体实现都是由 Maven 插件实现的。插件通常提供了一个目标的集合，并且可以使用下面的语法执行：`mvn [plugin-name]:[goal-name]`

Maven 提供了下面两种类型的插件：

- Build plugins ：在构建时执行，并在 `pom.xml` 的 元素中配置。
- Reporting plugins ：在网站生成过程中执行，并在 `pom.xml` 的元素中配置。

下面是一些常用插件的列表：

- clean ：构建之后清理目标文件。删除目标目录。
- compiler ：编译 Java 源文件。
- surefile ：运行 JUnit 单元测试。创建测试报告。
- jar ：从当前工程中构建 JAR 文件。
- war ：从当前工程中构建 WAR 文件。
- javadoc ：为工程生成 Javadoc 。
- antrun ：从构建过程的任意一个阶段中运行一个 ant 任务的集合。

#### 14.Maven依赖冲突

每个显式声明的类包都会依赖于一些其它的隐式类包，这些隐式的类包会被maven间接引入进来，因而可能造成一个我们不想要的类包的载入，严重的甚至会引起类包之间的冲突。

要解决这个问题，首先就是要查看pom.xml显式和隐式的依赖类包，然后通过这个类包树找出我们不想要的依赖类包，手工将其排除在外就可以了。 例如：

```xml
<exclusions>  
    <exclusion>  
        <artifactId>unitils-database</artifactId>  
        <groupId>org.unitils</groupId>  
    </exclusion>  
</exclusions>  
```

#### 15.依赖的解析机制

当依赖的范围是 system 的时候，Maven 直接从本地文件系统中解析构件。

根据依赖坐标计算仓库路径，尝试直接从本地仓库寻找构件，如果发现对应的构件，就解析成功。

如果在本地仓库不存在相应的构件，就遍历所有的远程仓库，发现后，下载并解析使用。

如果依赖的版本是 RELEASE 或 LATEST，就基于更新策略读取所有远程仓库的元数据文件（groupId/artifactId/maven-metadata.xml），将其与本地仓库的对应元合并后，计算出 RELEASE 或者 LATEST 真实的值，然后基于该值检查本地仓库，或者从远程仓库下载。

如果依赖的版本是 SNAPSHOT，就基于更新策略读取所有远程仓库的元数据文件，将它与本地仓库对应的元数据合并，得到最新快照版本的值，然后根据该值检查本地仓库，或从远程仓库下载。

如果最后解析得到的构件版本包含有时间戳，先将该文件下载下来，再将文件名中时间戳信息删除，剩下 SNAPSHOT 并使用（以非时间戳的形式使用）。

#### 参考链接

https://blog.csdn.net/weixin_44450768/article/details/104218776

https://blog.csdn.net/a303549861/article/details/93752178

https://www.cnblogs.com/lin0/p/14153982.html
