# ActiveMQ 

<http://activemq.apache.org/download.html>

​     MQ是消息中间件，是一种在分布式系统中应用程序借以传递消息的媒介，常用的有ActiveMQ，ZeroMQ,RabbitMQ，kafka。ActiveMQ是Apache下的开源项目，完全支持JMS1.1和J2EE1.4规范的JMS Provider实现。

    特点:
    1、支持多种语言编写客户端 
    2、对spring的支持，很容易和spring整合 
    3、支持多种传输协议：TCP,SSL,NIO,UDP等 
    4、支持AJAX 

```
消息形式:
1、点对点（queue） 
2、一对多（topic） 
```

用PHP来操作ActiveMQ，我们可以借助一个第三方扩展. stmp协议连接

```
Composer: composer require fusesource/stomp-php:2.0.*
```

producer-demo 

```
<?php
try {
  // 1.建立连接
  $stomp = new Stomp('tcp://47.52.119.21:61613');
  // 2.实例化类
  $obj = new Stdclass();
  // 3.获取数据
  for($i=0; $i<3; $i++){
    $obj->username = 'test';
    $obj->password = '123456';
    $queneName   = "/queue/userReg";
    // 4.发送一个注册消息到队列
    $stomp->send($queneName, json_encode($obj));
  }
} catch (StompException $e) {
  die('Connection failed: ' . $e->getMessage());
}
```

consumer1-demo

```
<?php
$stomp = new Stomp('tcp://localhost:61613');
$stomp->subscribe('/queue/userReg');
while (true) {
  //判断是否有读取的信息
  if ($stomp->hasFrame()) {
    $frame = $stomp->readFrame();
    $data = json_decode($frame->body, true);
    var_dump($data);
    $stomp->ack($frame);
  }
}
```

sonsumer2-demo

```
<?php 
$stomp = new Stomp('tcp://localhost:61613');
$stomp->subscribe('/queue/userReg');
while (true) {
  //判断是否有读取的信息
  if ($stomp->hasFrame()) {
    $frame = $stomp->readFrame();
    $data = json_decode($frame->body, true);
    var_dump($data);
    $stomp->ack($frame);
  }
}
```



#  RabbitMQ 3.6.1+PHP-amqp 

下载:  http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.1/rabbitmq-server-generic-unix-3.6.1.tar.xz

```
1. 安装依赖: yum install gcc glibc-devel make ncurses-devel openssl-devel xmlto
2.Erlang安装配置:
 下载安装包 http://www.erlang.org/downloads    otp_src_18.3.tar.gz
  解压:tar -xzvf otp_src_18.3.tar.gz
  进入: cd otp_src_18.3/
  配置: ./configure --prefix=/opt/erlang
  编译安装: make && make install
  配置环境变量: vi /etc/profile文件，增加下面的环境变量
           export PATH=$PATH:/opt/erlang/bin
           source  /etc/profile
3. 下载rabbitmq:
  wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.1/rabbitmq-server-generic-unix-3.6.1.tar.xz
   xz -d rabbitmq-server-generic-unix-3.6.1.tar.xz
   tar -xvf rabbitmq-server-generic-unix-3.6.1.tar
4. 启动服务:  ./sbin/rabbitmq-server -detached
5. 查看服务状态: ./sbin/rabbitmqctl status
6. 启用插件: ./sbin/rabbitmq-plugins enable rabbitmq_management
7. 访问:  http://localhost:15672
8. 配置远程访问:
   
   添加用户:./sbin/rabbitmqctl add_user assasin assasin
　 添加权限:./sbin/rabbitmqctl set_permissions -p "/" assasin ".*" ".*" ".*"
   修改用户角色:./sbin/rabbitmqctl set_user_tags assasin administrator
   
9. rabbitmq常用命令:

    add_user        <UserName> <Password>

　　delete_user    <UserName>

　　change_password <UserName> <NewPassword>

　　list_users

　　add_vhost    <VHostPath>

　　delete_vhost <VHostPath>

　　list_vhostsset_permissions  [-p <VHostPath>] <UserName> <Regexp> <Regexp> <Regexp>

　　clear_permissions [-p <VHostPath>] <UserName>

　　list_permissions  [-p <VHostPath>]

　　list_user_permissions <UserName>

　　list_queues    [-p <VHostPath>] [<QueueInfoItem> ...]

　　list_exchanges [-p <VHostPath>] [<ExchangeInfoItem> ...]

　　list_bindings  [-p <VHostPath>]

　　list_connections [<ConnectionInfoItem> ...]
```



# ZeroMQ  

```
1.介绍:Zeromp是一个C++编写的高性能分布式消息，是一个非常简单好用的传输层，使得Socket编程更加简单、简洁和性能更高效。 http://download.zeromq.org/
2.特点:  支持高并发的异步Socket框架
        协议比TCP更快，适用于大型集群和分布式计算
        提供多种消息传递机制，如inproc/IPC/TCP/multicast等
        内置丰富的组合模式，可用于简化大型分布式计算框架
        提供异步I/O模式，适用于可扩展的多核应用开发
        拥有活跃的开发者社区提供技术支持，发展相当迅速
        支持超过30种的编程语言，如C/C++/Java/.Net/Python/PHP等
        良好的跨平台性，支持多种OS，如Linux/Windows/OS X等
        拥有iMatix公司的商业级别支持，却使完全免费的
 3. 下载 git clone git://github.com/zeromq/libzmq.git
 4. cd  zeromq
 5. ./autogen.sh
 6. ./configure --prefix=/usr/local/zeromq
 7. make && make install
 8. 安装PHP+zmq扩展 http://www.zeromq.org/bindings:php
 9. 参考:  ZMQ API参考手册：http://api.zeromq.org/
           PHP使用手册可参考：http://zguide.zeromq.org/php:all
           ZeroMQ的学习和研究：http://www.searchtb.com/2012/08/zeromq-primer.html
           ZMQ PHP编程参考手册：http://php.zero.mq
           https://github.com/imatix/zguide
 
```

```
   ZeroMQ是个类似于Socket的一系列接口，他跟Socket的区别是：普通的socket是端到端的（1:1的关系），而ZMQ却是可以N：M 的关系，人们对BSD套接字的了解较多的是点对点的连接，点对点连接需要显式地建立连接、销毁连接、选择协议（TCP/UDP）和处理错误等，而ZMQ屏蔽了这些细节，让你的网络编程更为简单。ZMQ用于node与node间的通信，node可以是主机或者是进程。
    ZeroMQ是网络栈中新的一层，它是个可伸缩层，分散在分布式系统间。因此，它可支持任意大的应用程序。ØMQ不是简单的点对点交互，相反，它定义了分布式系统的全局拓扑。ØMQ应用程序没有锁，可并行运行。此外，它可在多个线程、内核和主机盒之间弹性伸缩。
   与其他的消息队列相比，ZeroMQ有以下一些特点:
   1.点对点无中间节点
   传统的消息队列都需要一个消息服务器来存储转发消息。而ZeroMQ则放弃了这个模式，把侧重点放在了点对点的消息传输上，并且（试图）做到极致。以为消息服务器最终还是转化为服务器对其他节点的点对点消息传输上。ZeroMQ能缓存消息，但是是在发送端缓存。ZeroMQ里有水位设置的相关接口来控制缓存量。当然，ZeroMQ也支持传统的消息队列（通过zmq_device来实现）。
   2.强调消息收发模式
   在点对点的消息传输上ZeroMQ将通信的模式做了归纳，比如常见的订阅模式（一个消息发多个客户），分发模式（N个消息平均分给X个客户）等等。下面是目前支持的消息模式配对，任何一方都可以做为服务端。
    • PUB and SUB
    • REQ and REP
    • REQ and XREP
    • XREQ and REP
    • XREQ and XREP
    • XREQ and XREQ
    • XREP and XREP
    • PUSH and PULL
    • PAIR and PAIR
   3.以统一接口支持多种底层通信方式（线程间通信，进程间通信，跨主机通信）
   如果想把本机多进程的软件放到跨主机的环境里去执行，通常要将IPC接口用套接字重写一遍。非常麻烦。而有了ZeroMQ就方便多了，只要把通信协议从"ipc:///xxx"改为"tcp://*.*.*.*:****"就可以了，其他代码通通不需要改，如果这个是从配置文件里读的话，那么程序就完全不要动了，直接复制到其他机器上就可以了。以为ZeroMQ为我们做了很多。
   4.异步，强调性能
   ZeroMQ设计之初就是为了高性能的消息发送而服务的，所以其设计追求简洁高效。它发送消息是异步模式，通过单独出一个IO线程来实现，所以消息发送调用之后不要立刻释放相关资源哦，会出错的（以为还没发送完），要把资源释放函数交给ZeroMQ让ZeroMQ发完消息自己释放。
```

