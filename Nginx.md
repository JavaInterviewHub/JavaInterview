## Nginx


* [1.请解释一下什么是 Nginx ？](#1请解释一下什么是-nginx-)
* [2.为什么要用Nginx？](#2为什么要用nginx)
* [3.Nginx怎么处理请求的？](#3nginx怎么处理请求的)
* [4.Nginx的优缺点？](#4nginx的优缺点)
* [5.Nginx应用场景？](#5nginx应用场景)
* [6.使用“反向代理服务器”的优点是什么?](#6使用反向代理服务器的优点是什么)
* [7.列举Nginx服务器的最佳用途。](#7列举nginx服务器的最佳用途)
* [8.请解释Nginx如何处理HTTP请求。](#8请解释nginx如何处理http请求)
* [9.在Nginx中，如何使用未定义的服务器名称来阻止处理请求?](#9在nginx中如何使用未定义的服务器名称来阻止处理请求)
* [10.在Nginx中如何在URL中保留双斜线?](#10在nginx中如何在url中保留双斜线)
* [11.ngx_http_upstream_module的作用是什么?](#11ngx_http_upstream_module的作用是什么)
* [12. fastcgi 与 cgi 的区别？](#12-fastcgi-与-cgi-的区别)
* [13. Nginx 常用命令？](#13-nginx-常用命令)
* [14.Nginx 常用配置？](#14nginx-常用配置)
* [15.请陈述stub_status和sub_filter指令的作用是什么?](#15请陈述stub_status和sub_filter指令的作用是什么)
* [参考链接](#参考链接)


#### 1.请解释一下什么是 Nginx ？

Nginx ，是一个 Web 服务器和反向代理服务器，用于 HTTP、HTTPS、SMTP、POP3 和 IMAP 协议。

目前使用的最多的 Web 服务器或者代理服务器，像淘宝、新浪、网易、迅雷等都在使用。

Nginx 的主要功能如下：
作为 http server (代替 Apache ，对 PHP 需要 FastCGI 处理器支持)
FastCGI：Nginx 本身不支持 PHP 等语言，但是它可以通过 FastCGI 来将请求扔给某些语言或框架处理。
反向代理服务器
实现负载均衡
虚拟主机

#### 2.为什么要用Nginx？

跨平台、配置简单、方向代理、高并发连接：处理2-3万并发连接数，官方监测能支持5万并发，内存消耗小：开启10个nginx才占150M内存 ，nginx处理静态文件好，耗费内存少，

而且Nginx内置的健康检查功能：如果有一个服务器宕机，会做一个健康检查，再发送的请求就不会发送到宕机的服务器了。重新将请求提交到其他的节点上。

使用Nginx的话还能：

节省宽带：支持GZIP压缩，可以添加浏览器本地缓存
稳定性高：宕机的概率非常小
接收用户请求是异步的

#### 3.Nginx怎么处理请求的？

- nginx接收一个请求后，首先由listen和server_name指令匹配server模块，再匹配server模块里的location，location就是实际地址

```
 server {            						# 第一个Server区块开始，表示一个独立的虚拟主机站点
        listen       80；      					# 提供服务的端口，默认80
        server_name  localhost；       			# 提供服务的域名主机名
        location / {            				# 第一个location区块开始
            root   html；       				# 站点的根目录，相当于Nginx的安装目录
            index  index.html index.htm；      	# 默认的首页文件，多个用空格分开
        }          			

```


#### 4.Nginx的优缺点？

优点：
占内存小，可实现高并发连接，处理响应快
可实现http服务器、虚拟主机、方向代理、负载均衡
Nginx配置简单
可以不暴露正式的服务器IP地址

缺点：
动态处理差：nginx处理静态文件好,耗费内存少，但是处理动态页面则很鸡肋，现在一般前端用nginx作为反向代理抗住压力，

#### 5.Nginx应用场景？

http服务器。Nginx是一个http服务可以独立提供http服务。可以做网页静态服务器。
虚拟主机。可以实现在一台服务器虚拟出多个网站，例如个人网站使用的虚拟机。
反向代理，负载均衡。当网站的访问量达到一定程度后，单台服务器不能满足用户的请求时，需要用多台服务器集群可以使用nginx做反向代理。并且多台服务器可以平均分担负载，不会应为某台服务器负载高宕机而某台服务器闲置的情况。
nginz 中也可以配置安全管理、比如可以使用Nginx搭建API接口网关,对每个接口服务进行拦截。

#### 6.使用“反向代理服务器”的优点是什么?

反向代理服务器可以隐藏源服务器的存在和特征。它充当互联网云和web服务器之间的中间层。这对于安全方面来说是很好的，特别是当您使用web托管服务时。

#### 7.列举Nginx服务器的最佳用途。

Nginx服务器的最佳用法是在网络上部署动态HTTP内容，使用SCGI、WSGI应用程序服务器、用于脚本的FastCGI处理程序。它还可以作为负载均衡器。

#### 8.请解释Nginx如何处理HTTP请求。

Nginx使用反应器模式。主事件循环等待操作系统发出准备事件的信号，这样数据就可以从套接字读取，在该实例中读取到缓冲区并进行处理。单个线程可以提供数万个并发连接。

#### 9.在Nginx中，如何使用未定义的服务器名称来阻止处理请求?

只需将请求删除的服务器就可以定义为：

```
Server {

listen 80;

server_name “ “ ;

return 444;

}
```

这里，服务器名被保留为一个空字符串，它将在没有“主机”头字段的情况下匹配请求，而一个特殊的`Nginx`的非标准代码`444`被返回，从而终止连接。

#### 10.在Nginx中如何在URL中保留双斜线?

要在URL中保留双斜线，就必须使用merge_slashes_off；语法:merge_slashes [on/off] ； 默认值: merge_slashes on ；环境: http，server

#### 11.ngx_http_upstream_module的作用是什么?

ngx_http_upstream_module用于定义可通过fastcgi传递、proxy传递、uwsgi传递、memcached传递和scgi传递指令来引用的服务器组。

#### 12. fastcgi 与 cgi 的区别？

1）cgi

web 服务器会根据请求的内容，然后会 fork 一个新进程来运行外部 c 程序（或 perl 脚本…）， 这个进程会把处理完的数据返回给 web 服务器，最后 web 服务器把内容发送给用户，刚才 fork 的进程也随之退出。

如果下次用户还请求改动态脚本，那么 web 服务器又再次 fork 一个新进程，周而复始的进行。

2）fastcgi

web 服务器收到一个请求时，他不会重新 fork 一个进程（因为这个进程在 web 服务器启动时就开启了，而且不会退出），web 服务器直接把内容传递给这个进程（进程间通信，但 fastcgi 使用了别的方式，tcp 方式通信），这个进程收到请求后进行处理，把结果返回给 web 服务器，最后自己接着等待下一个请求的到来，而不是退出。

综上，差别在于是否重复 fork 进程，处理请求。

#### 13. Nginx 常用命令？

启动 nginx 。
停止 nginx -s stop 或 nginx -s quit 。
重载配置 ./sbin/nginx -s reload(平滑重启) 或 service nginx reload 。
重载指定配置文件 .nginx -c /usr/local/nginx/conf/nginx.conf 。
查看 nginx 版本 nginx -v 。
检查配置文件是否正确 nginx -t 。
显示帮助信息 nginx -h 。

#### 14.Nginx 常用配置？

```nginx
worker_processes  8; # 工作进程个数
worker_connections  65535; # 每个工作进程能并发处理（发起）的最大连接数（包含所有连接数）
error_log         /data/logs/nginx/error.log; # 错误日志打印地址
access_log      /data/logs/nginx/access.log; # 进入日志打印地址
log_format  main  '$remote_addr"$request" ''$status $upstream_addr "$request_time"'; # 进入日志格式

## 如果未使用 fastcgi 功能的，可以无视
fastcgi_connect_timeout=300; # 连接到后端 fastcgi 超时时间
fastcgi_send_timeout=300; # 向 fastcgi 请求超时时间(这个指定值已经完成两次握手后向fastcgi传送请求的超时时间)
fastcgi_rend_timeout=300; # 接收 fastcgi 应答超时时间，同理也是2次握手后
fastcgi_buffer_size=64k; # 读取 fastcgi 应答第一部分需要多大缓冲区，该值表示使用1个64kb的缓冲区读取应答第一部分(应答头),可以设置为fastcgi_buffers选项缓冲区大小
fastcgi_buffers 4 64k; # 指定本地需要多少和多大的缓冲区来缓冲fastcgi应答请求，假设一个php或java脚本所产生页面大小为256kb,那么会为其分配4个64kb的缓冲来缓存
fastcgi_cache TEST; # 开启fastcgi缓存并为其指定为TEST名称，降低cpu负载,防止502错误发生

listen       80; # 监听端口
server_name  rrc.test.jiedaibao.com; # 允许域名
root  /data/release/rrc/web; # 项目根目录
index  index.php index.html index.htm; # 访问根文件
```

#### 15.请陈述stub_status和sub_filter指令的作用是什么?

（1）Stub_status指令：该指令用于了解Nginx当前状态的当前状态，如当前的活动连接，接受和处理当前读/写/等待连接的总数 ；

（2）Sub_filter指令：它用于搜索和替换响应中的内容，并快速修复陈旧的数据

#### 参考链接

https://blog.csdn.net/a303549861/article/details/88672901

https://blog.csdn.net/weixin_43122090/article/details/105461971

https://www.wkcto.com/article/detail/233
