## Kafka安装配置

```
下载地址: http://kafka.apache.org/downloads
```

### linux环境

```
1. 启动Zookeeper server
sh bin/zookeeper-server-start.sh config/zookeeper.properties &
2. 启动Kafka server
sh bin/kafka-server-start.sh config/server.properties &
3. 运行生产者producer
sh bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
4. 运行消费者consumer
sh bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning
```

```
Github: https://github.com/nmred/kafka-php
```

### Windows环境

```
1. 启动Zookeeper server
  bin\windows\zookeeper-server-start.bat config\zookeeper.properties
2. 启动Kafka server
 bin\windows\kafka-server-start.bat config\server.properties 
3. 运行生产者producer
 bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test
4. 运行消费者consumer
 bin\windows\kafka-console-consumer.bat --zookeeper localhost:2181 --topic test --from-beginning
```

### producer-demo

```
注意:Windows环境选装rdkafka扩展
<?php
$rk = new RdKafka\Producer();
$rk->setLogLevel(LOG_DEBUG);
$rk->addBrokers("127.0.0.1");

$topic = $rk->newTopic("test");

for ($i = 0; $i < 10; $i++) {
    $topic->produce(RD_KAFKA_PARTITION_UA, 0, "Message $i");
}
```

```
<?php
 require_once('./vendor/autoload.php');
 $produce  =  \Kafka\Produce::getInstance('localhost:2181',  3000);
 $produce->setRequireAck(-1);
$topicName  =  'testtopic';
//获取到topic下可用的partitions
$partitions  =  $produce->getAvailablePartitions($topicName);
$partitionCount  =  count($partitions);
$count  =  1;
while(true){
    $message  =  json_encode(array('uid'  =>  $count,  'age'  =>  $count%100,  'datetime'  =>  date('Y-m-d H:i:s')));
 
    //发送消息到不同的partition
 
    $partitionId  =  $count%$partitionCount;
 
    $produce->setMessages('testtopic',  $partitionId,  array($message));
 
    $result  =  $produce->send();
 
    var_dump($result);
 
    $count++;
 
    echo  "producer sleeping\n";
 
    sleep(1);
}
```

### consumer-demo 

```
<?php
$rk = new RdKafka\Consumer();
$rk->setLogLevel(LOG_DEBUG);
$rk->addBrokers("127.0.0.1");

$topic = $rk->newTopic("test");

$topic->consumeStart(0, RD_KAFKA_OFFSET_BEGINNING);

while (true) {
    $msg = $topic->consume(0, 1000);
    if ($msg->err) {
        echo $msg->errstr(), "\n";
        break;
    } else {
        echo $msg->payload, "\n";
    }
}
```

```
<?php
 
require_once('./vendor/autoload.php');
 
//获取需要处理的partitionId
 
$partitionId  =  isset($argv[1])  ?  intval($argv[1])  :  0;
 
$consumer  =  \Kafka\Consumer::getInstance('localhost:2181');
 
$consumer->setGroup('test-consumer-group');
 
$consumer->setPartition('testtopic',  $partitionId);
 
$consumer->setFromOffset(true);
 
$consumer->setMaxBytes(102400);
 
while(true){
 
    $topic  =  $consumer->fetch();
 
foreach  ($topic  as  $topicName  =>  $partition)  {
 
    foreach  ($partition  as  $partId  =>  $messageSet)  {
 
        foreach  ($messageSet  as  $message)  {
 
            var_dump($message);
 
        }
 
    }
 
    }
 
echo  "consumer sleeping\n";
 
sleep(1);
 
}
```

