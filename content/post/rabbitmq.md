### RabbitMQ 基本概念以及使用

#### AMQP 中的一些概念 
RabbitMQ 原生实现了 AMQP 协议，下面讲的一些概念都是 AMQP 0-9-1 模型的概念

在 AMQP 中有以下几种角色

 - Publisher
   
   消息发布者，这个应该很好理解就是发送消息的一方
   
  - Broker
   
    中转站，接收消息并根据制定规则发给接收方
   
  - Exchange
   
    交换机，消息送到中转站后首先会被发给它，它再根据规则发给指定的队列
   
  - Queue
   
    队列，存储消息的地方
   
  - Consumer
   
    消费者，接收消息的一方，消费端会从指定的队列中取消息

给出一张 AMQP 协议的模型图

![AMQP](https://hurryking.github.io/img/AMQP.png)

Exchange 的类型分为 4 种

 1. Direct Exchange 
 2. Fanout Exchange 
 3. Topic Exchange 
 4. Headers Exchange

下面会按照类型给出相应的 PHP 示例
首先要下载安装 rabbitmq 的 server 端，我选择使用 docker
```shell
docker run -it --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management
```

安装 php 的 amqp 的扩展包，接下来要使用
```
composer require php-amqplib
```

1. Direct

应用场景: 把指定级别的日志，发送到指定的文件中，可以使用 direct 指定 routing-key，然后绑定 queue 的名称到这个交换机上，就可以实现了

![AMQP direct](https://hurryking.github.io/img/AMQP-direct.svg)
sender.php
```php
<?php
require_once __DIR__ . '/vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Message\AMQPMessage;

$host = 'localhost';
$port= 5672;

// 创建连接
$connection = new AMQPStreamConnection($host, $port, 'guest', 'guest');
// 定义通道
$channel = $connection->channel();
// 定义交换机
$channel->exchange_declare('direct_log', 'direct');

// 消息
$msg = new AMQPMessage('Hello World!');

// 发布消息到 direct_log 交换机 指定 routing_key 为 hello
$channel->basic_publish($msg, 'direct_log', 'hello');

echo " [x] Sent 'Hello World!'\n";

$channel->close();
$connection->close();
?>
```

receiver.php
```php
<?php
require_once __DIR__ . '/vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;

$host = 'localhost';
$port= 5672;

//　开启连接
$connection = new AMQPStreamConnection($host, $port, 'guest', 'guest');
// 创建通道
$channel = $connection->channel();

// 定义交换机
$channel->exchange_declare('direct_log', 'direct');
// 定义队列
list($queue_name, ,) = $channel->queue_declare('hello', false, false, false, false);

// 绑定交换机和队列
$channel->queue_bind($queue_name, 'direct_log', 'hello');
echo " [*] Waiting for messages. To exit press CTRL+C\n";

$callback = function ($msg) {
    echo ' [x] Received ', $msg->body, "\n";
};

// 开始消费
$channel->basic_consume('hello', 'direct_log', false, true, false, false, $callback);

while ($channel->is_consuming()) {
    $channel->wait();
}

$channel->close();
$connection->close();
```

实验过程中发现会有丢消息的情况，还需要再确认下原因

2. Fanout

应用场景: 聊天室的广播，游戏里的推送的全服通知，都可以使用 fanout 类型的交换机实现

![Fanout](https://hurryking.github.io/img/AMQP_Fanout.webp)

sender.php
```php
<?php

require_once __DIR__ . '/vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Message\AMQPMessage;

$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel = $connection->channel();

$channel->exchange_declare('logs', 'fanout', false, false, false);

$data = implode(' ', array_slice($argv, 1));
if (empty($data)) {
    $data = "info: Hello World!";
}
$msg = new AMQPMessage($data);

$channel->basic_publish($msg, 'logs');

echo ' [x] Sent ', $data, "\n";

$channel->close();
$connection->close();
```

receive.php
```php
require_once __DIR__ . '/vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;

$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel = $connection->channel();

$channel->exchange_declare('logs', 'fanout', false, false, false);

list($queue_name, ,) = $channel->queue_declare("", false, false, true, false);

$channel->queue_bind($queue_name, 'logs');

echo " [*] Waiting for logs. To exit press CTRL+C\n";

$callback = function ($msg) {
    echo ' [x] ', $msg->body, "\n";
};

$channel->basic_consume($queue_name, '', false, true, false, false, $callback);

while ($channel->is_consuming()) {
    $channel->wait();
}

$channel->close();
$connection->close();
```

3. Topic

应用场景: 游戏里的频道，给订阅某个频道的人群，推频道消息(比 Fanout 控制的更细，完全可以当做 fanout 使用)

![AMQP Topic](https://hurryking.github.io/img/AMQP_Topic.webp)
sender.php
```php
<?php

require_once __DIR__ . '/vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Message\AMQPMessage;

$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel = $connection->channel();

$channel->exchange_declare('topic_logs', 'topic', false, false, false);

$routing_key = isset($argv[1]) && !empty($argv[1]) ? $argv[1] : 'anonymous.info';
$data = implode(' ', array_slice($argv, 2));
if (empty($data)) {
    $data = "Hello World!";
}

$msg = new AMQPMessage($data);

$channel->basic_publish($msg, 'topic_logs', $routing_key);

echo ' [x] Sent ', $routing_key, ':', $data, "\n";

$channel->close();
$connection->close();
```

receiver.php
```php
<?php

require_once __DIR__ . '/vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;

$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel = $connection->channel();

$channel->exchange_declare('topic_logs', 'topic', false, false, false);

list($queue_name, ,) = $channel->queue_declare("", false, false, true, false);

$binding_keys = array_slice($argv, 1);
if (empty($binding_keys)) {
    file_put_contents('php://stderr', "Usage: $argv[0] [binding_key]\n");
    exit(1);
}

foreach ($binding_keys as $binding_key) {
    $channel->queue_bind($queue_name, 'topic_logs', $binding_key);
}

echo " [*] Waiting for logs. To exit press CTRL+C\n";

$callback = function ($msg) {
    echo ' [x] ', $msg->delivery_info['routing_key'], ':', $msg->body, "\n";
};

$channel->basic_consume($queue_name, '', false, true, false, false, $callback);

while ($channel->is_consuming()) {
    $channel->wait();
}

$channel->close();
$connection->close();
```

4. Header

应用场景: 类似 direct ，但是可以匹配更多属性更多字段，一般建议使用 direct 代替 header

sender.php
```php
<?php

require __DIR__ . '/vendor/autoload.php';

use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Exchange\AMQPExchangeType;
use PhpAmqpLib\Message\AMQPMessage;
use PhpAmqpLib\Wire;

$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel = $connection->channel();

$exchangeName = 'topic_headers_test';
$channel->exchange_declare($exchangeName, AMQPExchangeType::HEADERS);

$data = implode(' ', array_slice($argv, 2));
if (empty($data)) {
    $data = "Hello World!";
}

$message = new AMQPMessage($data);
$headers = new Wire\AMQPTable(array(
    'foo' => 'bar',
    'table' => array('figuf', 'ghf' => 5, 5 => 675),
    'num1' => -4294967295,
    'num2' => 5,
    'num3' => -2147483648,
    'true' => true,
    'false' => false,
    'void' => null,
    'date' => new DateTime('now', new DateTimeZone('UTC')),
    'array' => array(null, 'foo', 'bar', 5, 5674625, 'ttt', array(5, 8, 2)),
    'arr_with_tbl' => array(
        'bar',
        5,
        array(
            'foo',
            57,
            'ee',
            array(
                'foo' => 'bar',
                'baz' => 'boo',
                'arr' => array(1, 2, 3, true, new DateTime('now', new DateTimeZone('UTC'))),
            ),
        ),
        67,
        array(
            'foo' => 'bar',
            5 => 7,
            8 => 'boo',
            'baz' => 3,
        ),
    ),
    '64bitint' => 9223372036854775807,
    '64bit_uint' => '18446744073709600000',
    '64bitint_neg' => -pow(2, 40),
));
$headers->set('shortshort', -5, Wire\AMQPTable::T_INT_SHORTSHORT);
$headers->set('short', -1024, Wire\AMQPTable::T_INT_SHORT);

echo PHP_EOL . PHP_EOL . 'SENDING MESSAGE WITH HEADERS' . PHP_EOL . PHP_EOL;
var_dump($headers->getNativeData());
echo PHP_EOL;

$message->set('application_headers', $headers);
$channel->basic_publish($message, $exchangeName);

echo ' [x] Sent :', $data, PHP_EOL;

$channel->close();
$connection->close();
```

receive.php
```php
<?php

require __DIR__ . '/vendor/autoload.php';

use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Exception\AMQPTimeoutException;
use PhpAmqpLib\Exchange\AMQPExchangeType;
use PhpAmqpLib\Message\AMQPMessage;
use PhpAmqpLib\Wire\AMQPTable;

$headers = array_slice($argv, 1);
if (empty($headers)) {
    file_put_contents('php://stderr', "Usage: $argv[0] [header1=value1] [header2=value2]\n");
    exit(1);
}


$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel = $connection->channel();

$exchangeName = 'topic_headers_test';
$channel->exchange_declare($exchangeName, AMQPExchangeType::HEADERS);

list($queueName, ,) = $channel->queue_declare('', false, false, true);

$bindArguments = [];
foreach ($headers as $header) {
    list ($key, $value) = explode('=', $header, 2);
    $bindArguments[$key] = $value;
}

$channel->queue_bind($queueName, $exchangeName, '', false, new AMQPTable($bindArguments));

echo ' [*] Waiting for logs. To exit press CTRL+C', "\n";

$callback = function (AMQPMessage $message) {
    echo PHP_EOL . ' [x] ', $message->delivery_info['routing_key'], ':', $message->body, "\n";
    echo 'Message headers follows' . PHP_EOL;
    var_dump($message->get('application_headers')->getNativeData());
    echo PHP_EOL;
};

$channel->basic_consume($queueName, '', false, true, true, false, $callback);
while ($channel->is_consuming()) {
    try {
        $channel->wait(null, false, 2);
    } catch (AMQPTimeoutException $exception) {
    }
    echo '*' . PHP_EOL;
}

$channel->close();
$connection->close();
```

实验过程中发现如果 receive 解析出来的参数都转换成了字符串所以没办法匹配除了字符串以外的 header，如果是正常使用就没有这个问题了，直接在脚本中定义好数据以及类型就不用考虑参数类型的解析问题了

这篇中的实验代码都是搬运的 php-amqplib　里的 demo

> 做自己擅长的事谋生，做自己感兴趣的事来打发时间，如果是这两者是同一件事那真是很幸运了