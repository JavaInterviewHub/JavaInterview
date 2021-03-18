### Docker

* [1.什么Docker](#1什么docker)
* [2.Docker与虚拟机有何不同](#2docker与虚拟机有何不同)
* [3.什么是Docker镜像](#3什么是docker镜像)
* [4.什么是Docker容器](#4什么是docker容器)
* [5.Docker容器有几种状态](#5docker容器有几种状态)
* [6.DockerFile中最常见的指定是什么?](#6dockerfile中最常见的指定是什么)
* [7.DockerFile中的命令COPY和ADD命令有什么区别？](#7dockerfile中的命令copy和add命令有什么区别)
* [8.Docker的常用命令？](#8docker的常用命令)
* [9.容器与主机之间的数据拷贝命令？](#9容器与主机之间的数据拷贝命令)
* [10.启动nginx容器（随机端口映射），并挂载本地文件目录到容器html的命令？](#10启动nginx容器随机端口映射并挂载本地文件目录到容器html的命令)
* [11.如何使用 Docker 技术创建与环境无关的容器系统？](#11如何使用-docker-技术创建与环境无关的容器系统)
* [12.有什么方法确定一个 Docker 容器运行状态](#12有什么方法确定一个-docker-容器运行状态)
* [13. Docker Image 和 Docker Layer (层) 有什么不同](#13-docker-image-和-docker-layer-层-有什么不同)
* [14.如何停止所有正在运行的容器？](#14如何停止所有正在运行的容器)
* [15.如何清理批量后台停止的容器？](#15如何清理批量后台停止的容器)
* [16.如何临时退出一个正在交互的容器的终端，而不终止它？](#16如何临时退出一个正在交互的容器的终端而不终止它)
* [17.Docker 群（Swarm）是什么](#17docker-群swarm是什么)
* [18.在使用 Docker 技术的产品中如何监控其运行](#18在使用-docker-技术的产品中如何监控其运行)
* [19.什么是孤儿卷及如何删除它？](#19什么是孤儿卷及如何删除它)
* [20.在 Windows 系统上可以运行原生的 Docker 容器吗？](#20在-windows-系统上可以运行原生的-docker-容器吗)
* [21.在 非 Linux 操作系统平台上如何运行 Docker ?](#21在-非-linux-操作系统平台上如何运行-docker-)
* [参考链接](#参考链接)

#### 1.什么Docker

Docker是一个容器化平台，它以容器的形式将您的应用程序及其所有依赖项打包在一起，以确保您的应用程序在任何环境中无缝运行。

#### 2.Docker与虚拟机有何不同

Docker不是虚拟化方法。它依赖于实际实现基于容器的虚拟化或操作系统级虚拟化的其他工具。为此，Docker最初使用LXC驱动程序，然后移动到libcontainer现在重命名为runc。Docker主要专注于在应用程序容器内自动部署应用程序。应用程序容器旨在打包和运行单个服务，而系统容器则设计为运行多个进程，如虚拟机。因此，Docker被视为容器化系统上的容器管理或应用程序部署工具。
A 容器不需要引导操作系统内核，因此可以在不到一秒的时间内创建容器。此功能使基于容器的虚拟化比其他虚拟化方法更加独特和可取。
B 由于基于容器的虚拟化为主机增加了很少或没有开销，因此基于容器的虚拟化具有接近本机的性能。
C 对于基于容器的虚拟化，与其他虚拟化不同，不需要其他软件。
D 主机上的所有容器共享主机的调度程序，从而节省了额外资源的需求。
E 与虚拟机映像相比，容器状态（Docker或LXC映像）的大小很小，因此容器映像很容易分发。
F 容器中的资源管理是通过cgroup实现的。Cgroups不允许容器消耗比分配给它们更多的资源。虽然主机的所有资源都在虚拟机中可见，但无法使用。这可以通过在容器和主机上同时运行top或htop来实现。所有环境的输出看起来都很相似。

#### 3.什么是Docker镜像

Docker镜像是Docker容器的源代码，Docker镜像用于创建容器。使用build命令创建镜像。

#### 4.什么是Docker容器

Docker容器包括应用程序及其所有依赖项，作为操作系统的独立进程运行。

#### 5.Docker容器有几种状态

四种状态：运行、已暂停、重新启动、已退出。

#### 6.DockerFile中最常见的指定是什么?

| 指令  | 备注                   |
| ----- | ---------------------- |
| FROM  | 指定基础镜像           |
| LABEL | 功能为镜像指定标签     |
| RUN   | 运行指定命令           |
| CMD   | 容器启动时要运行的命令 |

#### 7、DockerFile中的命令COPY和ADD命令有什么区别？

COPY和ADD的区别时COPY的SRC只能是本地文件，其他用法一致。

#### 8.Docker的常用命令？

| 命令          | 备注                 |
| ------------- | -------------------- |
| docker pull   | 拉去或更新指定的镜像 |
| docker push   | 将镜像推送到远程仓库 |
| docker rm     | 删除容器             |
| docker rmi    | 删除镜像             |
| docker images | 列出所有镜像         |
| docker ps     | 列出所有容器         |

#### 9.容器与主机之间的数据拷贝命令？

Docker cp命令用于穷奇与主机之间的数据拷贝

- 主机到哦容器：docker cp /www 96f7f14e99ab:/www/
- 容器到主机：docker cp 96f7f14e99ab:/www /tmp

#### 10.启动nginx容器（随机端口映射），并挂载本地文件目录到容器html的命令？

```
Docker run -d -p --name nginx2 -v /home/nginx:/usr/share/nginx/html nginx
```

#### 11.如何使用 Docker 技术创建与环境无关的容器系统？

Docker 技术有三中主要的技术途径辅助完成此需求：

- 存储卷（Volumes）
- 环境变量（Environment variable）注入
- 只读（Read-only）文件系统

#### 12.有什么方法确定一个 Docker 容器运行状态

使用如下命令行命令确定一个 Docker 容器的运行状态

```
$ docker ps –a
```

这将列表形式输出运行在主机上的所有 Docker 容器及其运行状态。从这个列表中很容易找到想要的容器及其运行状态。

#### 13. Docker Image 和 Docker Layer (层) 有什么不同

Image ：一个 Docker Image 是由一系列 Docker 只读层（read-only Layer） 创建出来的。
Layer： 在 Dockerfile 配置文件中完成的一条配置指令，即表示一个 Docker 层（Layer）。
如下 Dockerfile 文件包含 4 条指令，每条指令创建一个层（Layer）。

```
FROM ubuntu:15.04
COPY . /app
RUN make /app
CMD python /app/app.py
```

重点，每层只对其前一层进行一（某）些进化。

#### 14.如何停止所有正在运行的容器？

使用docker kill $(sudo docker ps -q)

#### 15.如何清理批量后台停止的容器？

使用docker rm $（sudo docker ps -a -q）

#### 16.如何临时退出一个正在交互的容器的终端，而不终止它？

按Ctrl+p，后按Ctrl+q，如果按Ctrl+c会使容器内的应用进程终止，进而会使容器终止。

#### 17.Docker 群（Swarm）是什么

Docker Swarm -- Docker 群 -- 是原生的 Docker 集群服务工具。它将一群 Docker 主机集成为单一一个虚拟 Docker 主机。利用一个 Docker 守护进程，通过标准的 Docker API 和任何完善的通讯工具，Docker Swarm 提供透明地将 Docker 主机扩散到多台主机上的服务。

#### 18.在使用 Docker 技术的产品中如何监控其运行

Docker 在产品中提供如 运行统计和 Docker 事件的工具。可以通过这些工具命令获取 Docker 运行状况的统计信息或报告。

Docker stats ： 通过指定的容器 id 获取其运行统计信息，可获得容器对 CPU，内存使用情况等的统计信息，类似 Linux 系统中的 top 命令。
Docker events ：Docker 事件是一个命令，用于观察显示运行中的 Docker 一系列的行为活动。
一般的 Docker 事件有：attach（关联），commit（提交），die（僵死），detach（取消关联），rename（改名），destory（销毁）等。也可使用多个选项对事件记录筛选找到想要的事件信息。

#### 19.什么是孤儿卷及如何删除它？

孤儿卷是未与任何容器关联的卷。在 Docker v。1.9 之前的版本中，删除这些孤儿卷存在很大问题。

#### 20.在 Windows 系统上可以运行原生的 Docker 容器吗？

在 'Windows Server 2016' 系统上， 你可以运行 Windows 的原生容器， 微软推出其映像是 'Windows Nano Server' ， 一个轻量级的运行在容器中的 Windows 原生系统。 您可以在其中布署基于 .NET 的应用。

译注： 结合 Docker 的基本技术原理，参考后面的 问题 26 和 问题 27， 可推测， 微软在系统内核上开发了对 Docker 的支持， 支持其闭源系统的容器化虚拟技术。但译者认为， Windows 系统本就是闭源紧耦合的系统， 好像你在本机上不装 .NET 组件，各应用能很好运行似的。何必再弄个容器，浪费资源。这只是译者自己之孔见，想喷就喷！ 另： Windows Server 2016 版本之后的都可支持这种原生 Docker 技术，如 Windows Server 2018 版。

#### 21.在 非 Linux 操作系统平台上如何运行 Docker ?

容器化虚拟技术概念可能来源于，在 Linux 内核版本 2.6.24 上加入的对 命名空间（ namespace） 的技术支持特性。 容器化进程加入其进程 ID 到其创建的每个进程上并且对每个进程中的系统级调用进行访问控制及审查。 其本身是由系统级调用 clone () 克隆出来的进程， 允许其创建属于自己命名空间的进程实例，而区别于之前的，归属与整个本机系统的进程实例。

如果上述在 Linux 系统内核上的技术实现成为可能， 那么明显的问题是如何在 非 Linux 系统上运行容器化的 Docker 。过去， Mac 和 Windows 系统上运行 Docker 容器都使用 Linux 虚拟机（VMs） 技术， Docker 工具箱使用的容器运行在 Virtual Box 虚拟机上。 现在，最新的情况是， Windows 平台上使用的是 Hyper-V 产品技术，Mac 平台上使用的是 Hypervisor.framework （框架）产品技术。

#### 参考链接

https://www.cnblogs.com/xiaoyangjia/p/11388806.html

https://www.wkcto.com/article/detail/662

https://blog.csdn.net/qq_43286578/article/details/105160725

https://learnku.com/server/t/42179

https://www.jianshu.com/p/f11077d7b301
