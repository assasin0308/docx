# nodejs 压力测试(mongodb,redis最大连接数)

[TOC]

##简单测试:

`siege`  https://blog.csdn.net/lshemail/article/details/79298357

siege.js是一个面向程序员友好的测试工具，主要以测试http接口(或页面), 需要手动写测试代码. 没有可视化的客户端. 适用于初级测试.



`websocket-bench`:https://github.com/M6Web/websocket-bench

nodejs tool to benchmark socket.io and faye websocket server.

websocket-benck是基于nodejs的工具. 主要用来测试websocket, 此工具三年前已暂停更新.



`JMeter`:https://jmeter.apache.org

是Apache基金会开源的100%纯JAVA桌面应用程序.被设计为用于测试客户端/服务端结构的软件(例如web应用程序)。它可以用来测试静态和动态资源的性能，例如：静态文件，Java Servlet,CGI Scripts,Java Object,数据库和FTP服务器等等。JMeter可用于模拟大量负载来测试一台服务器，网络或者对象的健壮性或者分析不同负载下的整体性能.

jmeter需要有java环境支持.

[Jmeter（一）工具的简单介绍](https://www.jianshu.com/p/de29e4ff71d0)



### 修改系统最大文件数和最大线程数(centos7.2)

```
有时候进程数太小会导致卡死服务状态 而使用ulimit -u增加的线程不是永久添加的  这时候需要改配置文件使其永久添加。
1，切换root权限
2，vim /etc/security/limits.conf   

# End of file
#root soft nofile 65535
#root hard nofile 65535
#* soft nofile 65535
#* hard nofile 65535
* soft noproc 127093
* hard noproc 127093
* soft nofile 127093
* hard nofile 127093

注释掉本来的配置 直接加新的就行  。
3，vim /etc/security/limits.d/20-nproc.conf

#*         soft    nproc     4096
#root      soft    nproc     unlimited
*          soft    nproc     127098
*          hard    nproc     204800

还是注掉原本存在的  直接加，然后重启  就会发现root和普通用户都是127098（自己设置）的线程。
最大文件数同理  nofile代表最大文件数 

--------------------- 
作者：BiuBiuBiu___ 
来源：CSDN 
原文：https://blog.csdn.net/BiuBiuBiu___/article/details/80169358 
版权声明：本文为博主原创文章，转载请附上博文链接！
```



### mongodb调整最大连接数(3.6)

mongodb新版本中默认最大连接数是65536( [`maxIncomingConnections`](https://docs.mongodb.com/manual/reference/configuration-options/#net.maxIncomingConnections) )

控制台登录mongodb使用以下命令查看

```
> db.serverStatus().connections;
{ "current" : 1, "available" : 203, "totalCreated" : 1 }
最大连接数=current数值+available数值
```



### redis最大连接数

redis最大连接数默认是10000, 调整至30000

```
//查看redis最大连接数, 需要先登录redis控制台
> config get maxclients
1) "maxclients"
2) "10000"

//查看redis当前连接数
>info Clients
# Clients
connected_clients:16
client_longest_output_list:0
client_biggest_input_buf:0
blocked_clients:0

//修改redis最大连接数
临时修改:
config set maxclients 10208

//永久修改:
vim /etc/redis.conf
修改maxclients = 30000
修改并保存配置文件后重启redis
```

**遇到问题**

修改ulimit -n和config文件后并不管用,需要修改systemctl目录中的`redis.server.d`下的配置文件

```
cd /etc/systemd/system/redis.service.d
修改limit.conf文件中参数LimitNOFILE的值, 保存
执行如下命令
# systemctl daemon-reload
//重启redis
# systemctl restart redis

```

 或者启动时手动指定配置文件或者指定最大连接数

```
redis-server /etc/redis.conf
或者 redis-server --maxclients=20000
```



### mysql最大连接数

查看mariadb最大连接数, 默认为151

```
MariaDB [(none)]> show variables like 'max_connections';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 151   |
+-----------------+-------+
1 row in set (0.00 sec)
```

配置/etc/my.conf, 

[mysqld]新添加一行如下参数:

```
max_connections=10000
```

重启maradb服务后, 再次查看mariadb最大连接数, 并非是我们设置的10000

```
MariaDB [(none)]> show variables like 'max_connections';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 214   |
+-----------------+-------+
1 row in set (0.00 sec)
```

这是由于mariadb有默认打开文件数限制。可以通过配置/usr/lib/systemd/system/mariadb.service来调大打开文件数目。

配置/usr/lib/systemd/system/mariadb.service

[Service]新添加两行如下参数：

```
LimitNOFILE=20000
LimitNPROC=20000
```

重新加载服务器并重启mariadb

```
# systemctl daemon-reload
# systemctl restart mariadb
```



###可能影响

Linux一般默认一个进程只有1024个文件描述符，需要通过ulimit修改

https://cnodejs.org/topic/5445caed9657d9ab12567e88