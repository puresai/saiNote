# RabbitMQ

标签（空格分隔）： MQ

---

> RabbitMQ是轻量级的，很容易部署在前提和云中。它支持多种消息传递协议。RabbitMQ可以部署在分布式和联合配置中，以满足高规模、高可用性的需求。
RabbitMQ运行在许多操作系统和云环境上，并为大多数流行语言提供了广泛的开发工具。

### 1. 安装：

windows安装十分简单，印象中装的过程几乎无障碍，忽略。

centos7安装过程如下：

依赖安装
> yum -y install make gcc gcc-c++ kernel-devel m4 ncurses-devel openssl-devel unixODBC-devel socat

安装erlang，务必在下面的地址下载，之前在官网下载，安装总是有问题，浪费了大把时间。

```
https://dl.bintray.com/rabbitmq/rpm/erlang/20/el/7/
```

官网下载rabiitmq，rpm安装
> rpm -ivh (rpm包)

### 2. 配置

启动rabbitmq
> /bin/systemctl start rabbitmq-server.service

片刻之后，可以查看状态是否active
> bin/systemctl status rabbitmq-server.service


> cp rabbitmq.config.example /etc/rabbitmq/
cd /etc/rabbitmq/
mv rabbitmq.config.example rabbitmq.config


修改成你想要的配置，然后重启。

### 3. UI

启动

> rabbitmq-plugins enable rabbitmq_management

然后根据配置打开界面

安全性问题，可以禁止外部guest访问，新建账户

> rabbitmqctl add_user 13sai 12345678
rabbitmqctl set_user_tags 13sai administrator

并修改配置：
> {loopback_users, [<<"guest">>]}

重启rabbitmq服务。

### 4. 简单实例

生产者：
```
<?php
/**
 * Created by PhpStorm.
 * User: 13sai
 * Date: 2018/11/5
 * Time: 10:39
 */

// 配置信息
$config = [
    'host' => 'localhost',
    'port' => '5672',
    'login' => 'sai',
    'password' => '123456',
    'vhost'=>'/'
];

$exName = 'changes'; //交换机名
$queueMame = 'queue1'; //队列名
$route = 'key'; //路由key

// 创建连接和channel
$conn = new AMQPConnection($config);
if (!$conn->connect()) {
    die("连接失败");
}
$channel = new AMQPChannel($conn);

//消息内容
$message = "消息测试";

//创建交换机
$ex = new AMQPExchange($channel);
$ex->setName($exName);

//发送消息
for($i=0; $i<5; $i++){
    echo "Send Message:".$ex->publish($i.':'.$message, $route, AMQP_DURABLE)."\n";
}

$conn->disconnect();
```

消费者：
```
<?php
/**
 * Created by PhpStorm.
 * User: 13sai
 * Date: 2018/11/5
 * Time: 10:39
 */

$config = [
    'host' => 'localhost',
    'port' => '5672',
    'login' => 'sai',
    'password' => '123456',
    'vhost'=>'/'
];

$exName = 'changes'; //交换机名
$queueMame = 'queue1'; //队列名
$route = 'key'; //路由key

// 创建连接和channel
$conn = new AMQPConnection($config);
if (!$conn->connect()) {
    die("连接失败");
}
$channel = new AMQPChannel($conn);

// 创建交换机
$ex = new AMQPExchange($channel);
$ex->setName($exName);
$ex->setType(AMQP_EX_TYPE_DIRECT); //direct类型
$ex->setFlags(AMQP_DURABLE); //持久化，很重要

echo "Exchange Status:".$ex->declare()."\n";

// 创建队列
$queue = new AMQPQueue($channel);
$queue->setName($queueMame);
$queue->setFlags(AMQP_DURABLE); //持久化
echo "Message Total:".$queue->declare()."\n";

//绑定交换机与队列，并指定路由键
echo 'Queue Bind: '.$queue->bind($exName, $route)."\n";

//阻塞模式接收消息
echo "Message:\n";
while(True){
    $queue->consume('processMessage');
}
$conn->disconnect();

/**
 * 消费回调函数
 * 处理消息
 */
function processMessage($envelope, $queue) {
    $msg = $envelope->getBody();
    echo $msg."\n"; //处理消息
    $queue->ack($envelope->getDeliveryTag()); //手动发送ACK应答
}
```

花了些时间，不过弄出来还是感觉不错的。






