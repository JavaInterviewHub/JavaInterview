## Linux


* [1.vim有几种工作模式？](#1vim有几种工作模式)
* [2.find 命令如何使用？](#2find-命令如何使用)
* [3.如何在 /usr 目录下找出大小超过 10MB 的文件?](#3如何在-usr-目录下找出大小超过-10mb-的文件)
* [4.如何在 /var 目录下找出 90 天之内未被访问过的文件？](#4如何在-var-目录下找出-90-天之内未被访问过的文件)
* [5.如何在 /home 目录下找出 120 天之前被修改过的文件？](#5如何在-home-目录下找出-120-天之前被修改过的文件)
* [6.在整个目录树下查找文件 “core” ，如发现则无需提示直接删除它们？](#6在整个目录树下查找文件-core-如发现则无需提示直接删除它们)
* [7.ls 命令](#7ls-命令)
* [8.df 命令](#8df-命令)
* [9.rm 命令](#9rm-命令)
* [10.mv 命令](#10mv-命令)
* [11.cp 命令](#11cp-命令)
* [12.tail 命令](#12tail-命令)
* [13.grep 命令](#13grep-命令)
* [14.sed 命令](#14sed-命令)
* [15.用 sed 命令将指定的路径 /usr/local/http 替换成为 /usr/src/local/http ？](#15用-sed-命令将指定的路径-usrlocalhttp-替换成为-usrsrclocalhttp-)
* [16.打印 /etc/ssh/sshd_config 的第一百行？](#16打印-etcsshsshd_config-的第一百行)
* [17.awk 命令](#17awk-命令)
* [18.打印 /etc/passwd 的 1 到 3 行？](#18打印-etcpasswd-的-1-到-3-行)
* [19.vim 命令](#19vim-命令)
* [20.diff 命令](#20diff-命令)
* [21.sort 命令](#21sort-命令)
* [22.xargs 命令](#22xargs-命令)
* [23.把当前目录下所有后缀名为 .txt 的文件的权限修改为 777 ？](#23把当前目录下所有后缀名为-txt-的文件的权限修改为-777-)
* [24.tar 命令](#24tar-命令)
* [25.gzip 命令](#25gzip-命令)
* [26.bzip2 命令](#26bzip2-命令)
* [27.unzip 命令](#27unzip-命令)
* [28.export 命令](#28export-命令)
* [29.yum 命令](#29yum-命令)
* [30.rpm 命令](#30rpm-命令)
* [31.shutdown 命令](#31shutdown-命令)
* [32.service 命令](#32service-命令)
* [33.whereis 命令](#33whereis-命令)
* [34.用一条命令显示本机 eth0 网卡的 IP 地址，不显示其它字符？](#34用一条命令显示本机-eth0-网卡的-ip-地址不显示其它字符)
* [35.如何禁止服务器被 ping ？](#35如何禁止服务器被-ping-)
* [36.设置 DNS 需要修改哪个配置文件？](#36设置-dns-需要修改哪个配置文件)
* [37.在 Linux 下如何指定dns服务器，来解析某个域名？](#37在-linux-下如何指定dns服务器来解析某个域名)
* [参考资料](#参考资料)



#### 1.vim有几种工作模式？

命令模式，行末模式，编辑模式

#### 2.find 命令如何使用？

查找指定文件名的文件(不区分大小写)：find -iname "MyProgram.c" 。
对找到的文件执行某个命令：find -iname "MyProgram.c" -exec md5sum {} \; 。
查找 home 目录下的所有空文件：find ~ -empty 。

#### 3.如何在 /usr 目录下找出大小超过 10MB 的文件?

find /usr -type f -size +10240k 

#### 4.如何在 /var 目录下找出 90 天之内未被访问过的文件？

find /var \! -atime -90 

#### 5.如何在 /home 目录下找出 120 天之前被修改过的文件？

find /home -mtime +120

#### 6.在整个目录树下查找文件 “core” ，如发现则无需提示直接删除它们？

find / -name core -exec rm {} \;

#### 7.ls 命令

以易读的方式显示文件大小(显示为 MB,GB…)：ls -lh 。
以最后修改时间升序列出文件：ls -ltr 。
在文件名后面显示文件类型：ls -F 。

#### 8.df 命令

显示文件系统的磁盘使用情况，默认情况下 df -k 将以字节为单位输出磁盘的使用量。
使用 df -h 选项可以以更符合阅读习惯的方式显示磁盘使用量。
使用 df -T 选项显示文件系统类型。

#### 9.rm 命令

删除文件前先确认：rm -i filename.txt 。
在文件名中使用 shell 的元字符会非常有用。删除文件前先打印文件名并进行确认：rm -i file* 。
递归删除文件夹下所有文件，并删除该文件夹：rm -r example 。

#### 10.mv 命令

将 file1 重命名为 file2 ，如果 file2 存在则提示是否覆盖：mv -i file1 file2 。
-v 会输出重命名的过程，当文件名中包含通配符时，这个选项会非常方便：mv -v file1 file2 。

#### 11.cp 命令

拷贝 file1 到 file2 ，并保持文件的权限、属主和时间戳：cp -p file1 file2 。
拷贝 file1 到 file2 ，如果 file2 存在会提示是否覆盖：cp -i file1 file2 。

#### 12.tail 命令

tail 命令默认显示文件最后的 10 行文本：tail filename.txt 。
你可以使用 -n 选项指定要显示的行数：tail -n N filename.txt 。
你也可以使用 -f 选项进行实时查看，这个命令执行后会等待，如果有新行添加到文件尾部，它会继续输出新的行，在查看日志时这个选项会非常有用。你可以通过 CTRL-C 终止命令的执行：tail -f log-file 。

#### 13.grep 命令

在文件中查找字符串(不区分大小写)：grep -i "the" demo_file 。
输出成功匹配的行，以及该行之后的三行：grep -A 3 -i "example" demo_text 。
在一个文件夹中递归查询包含指定字符串的文件：grep -r "ramesh" * 。

#### 14.sed 命令

当你将 Dos 系统中的文件复制到 Unix/Linux 后，这个文件每行都会以 \r\n 结尾，sed 可以轻易将其转换为 Unix 格式的文件，使用\n 结尾的文件：sed 's/.$//' filename 。
反转文件内容并输出：sed -n '1!G; h; p' filename 。
为非空行添加行号：sed '/./=' thegeekstuff.txt | sed 'N; s/\n/ /' 。

#### 15.用 sed 命令将指定的路径 /usr/local/http 替换成为 /usr/src/local/http ？

echo "/usr/local/http/" | sed 's#/usr/local/#/usr/src/local/#'

#### 16.打印 /etc/ssh/sshd_config 的第一百行？

sed -n '100p' /etc/ssh/sshd_config

#### 17.awk 命令

删除重复行：$ awk '!($0 in array) { array[$0]; print}' temp 。
打印 /etc/passwd 中所有包含同样的 uid 和 gid 的行：awk -F ':' '$3=$4' /etc/passwd 。
打印文件中的指定部分的字段：awk '{print $2,$5;}' employee.txt 。

#### 18.打印 /etc/passwd 的 1 到 3 行？

使用 sed 命令：sed -n '1,3p' /etc/passwd
使用 awk 命令：awk 'NR>=1&&NR<=3{print $0}' /etc/passwd

#### 19.vim 命令

打开文件并跳到第 10 行：vim +10 filename.txt 。
打开文件跳到第一个匹配的行：vim +/search-term filename.txt 。
以只读模式打开文件：vim -R /etc/passwd 。

#### 20.diff 命令

比较的时候忽略空白符：diff -w name_list.txt name_list_new.txt 。

#### 21.sort 命令

以升序对文件内容排序：sort names.txt 。
以降序对文件内容排序：sort -r names.txt 。
以第三个字段对 /etc/passwd 的内容排序：sort -t: -k 3n /etc/passwd | more 。

#### 22.xargs 命令

将所有图片文件拷贝到外部驱动器：ls *.jpg | xargs -n1 -i cp {} /external-hard-drive/directory 。
将系统中所有 jpg 文件压缩打包：find / -name *.jpg -type f -print | xargs tar -cvzf images.tar.gz 。
下载文件中列出的所有 url 对应的页面：cat url-list.txt | xargs wget –c 。

#### 23.把当前目录下所有后缀名为 .txt 的文件的权限修改为 777 ？

方式一，使用 xargs 命令：find ./ -type f -name "*.txt" |xargs chmod 777 。
方式二，使用 exec 命令：find ./ -type f -name "*.txt" -exec chmod 777 {} 。

#### 24.tar 命令

创建一个新的 tar 文件： tar cvf archive_name.tar dirname/ 。
解压 tar 文件：tar xvf archive_name.tar 。
查看 tar 文件：tar tvf archive_name.tar 。

#### 25.gzip 命令

创建一个 *.gz 的压缩文件：gzip test.txt 。
解压 *.gz 文件：gzip -d test.txt.gz 。
显示压缩的比率：gzip -l *.gz 。

#### 26.bzip2 命令

创建 *.bz2 压缩文件：bzip2 test.txt 。
解压 *.bz2 文件：bzip2 -d test.txt.bz2 。

#### 27.unzip 命令

解压 *.zip 文件：unzip test.zip 。
查看 *.zip 文件的内容：unzip -l jasper.zip 。

#### 28.export 命令

输出跟字符串 oracle 匹配的环境变量：export | grep ORCALE 。
设置全局环境变量：export ORACLE_HOME=/u01/app/oracle/product/10.2.0 。

#### 29.yum 命令

使用 yum 安装 apache ：yum install httpd 。
更新 apache ：yum update httpd 。
卸载/删除 apache ：yum remove httpd 。

#### 30.rpm 命令

使用 rpm 安装 apache ：rpm -ivh httpd-2.2.3-22.0.1.el5.i386.rpm 。
更新 apache ：rpm -uvh httpd-2.2.3-22.0.1.el5.i386.rpm 。
卸载/删除 apache ：rpm -ev httpd 。

#### 31.shutdown 命令

关闭系统并立即关机：shutdown -h now 。
10 分钟后关机：shutdown -h +10 。
重启：shutdown -r now 。
重启期间强制进行系统检查：shutdown -Fr now 。

#### 32.service 命令

service 命令用于运行 System V init 脚本，这些脚本一般位于 /etc/init.d 文件下，这个命令可以直接运行这个文件夹里面的脚本，而不用加上路径。
查看服务状态：service ssh status 。
查看所有服务状态：service --status-all 。
重启服务：service ssh restart 。

#### 33.whereis 命令

当你不知道某个命令的位置时可以使用 whereis 命令，下面使用 whereis 查找 ls 的位置：whereis ls 。
当你想查找某个可执行程序的位置，但这个程序又不在 whereis 的默认目录下，你可以使用 -B 选项，并指定目录作为这个选项的参数。下面的命令在 /tmp 目录下查找 lsmk 命令：whereis -u -B /tmp -f lsmk 。

#### 34.用一条命令显示本机 eth0 网卡的 IP 地址，不显示其它字符？

方法一：
ifconfig eth0|grep inet|awk -F ':' '{print $2}'|awk '{print $1}'
方法二
ifconfig eth0|grep "inet addr"|awk -F '[ :]+' '{print $4}' 
方法三：
ifconfig eth0|awk -F '[ :]+' 'NR==2 {print $4}' 
方法四：
ifconfig eth0|sed -n '2p'|sed 's#^.*addr:##g'|sed 's# Bc.*$##g'
方法五：
ifconfig eth0|sed -n '2p'|sed -r 's#^.*addr:(.*)  Bc.*$#\1#g'
方法六(CENTOS7 也适用)：
ip addr|grep eth0|grep inet|awk '{print $2}'|awk -F '/' '{print $1}'

#### 35.如何禁止服务器被 ping ？

echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all

#### 36.设置 DNS 需要修改哪个配置文件？

全局的配置，可以在 /etc/resolv.conf 文件中配置。
指定网卡的配置，可以在 /etc/sysconfig/network-scripts/ifcfg-eth0 文件中配置。

#### 37.在 Linux 下如何指定dns服务器，来解析某个域名？

使用谷歌 DNS 解析百度：dig @8.8.8.8 www.baidu.com



### 参考资料
https://blog.csdn.net/a303549861/article/details/93754526
