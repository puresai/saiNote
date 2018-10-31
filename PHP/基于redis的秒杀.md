随着电商业务的发展，秒杀是个十分常见的场景，今天我们来利用redis实现一个简单的秒杀系统。

假定我们有一个商品id为1，秒杀数量是5。

一般场景：
```
// 引入数据库操作类medoo
include 'db.php';
$db = new db([
	    'database_type' => 'mysql',
	    'database_name' => 'test',
	    'server' => 'www.13sai.com',
	    'username' => '13sai',
	    'password' => '*',
	    'charset' => 'utf8'
	]);
$stock_num= $db->get('goods', 'stock_num', ['id' => 1]);
// 检测库存是否充足
if ($stock_num> 0) {
    sleep(1); //模拟真实环境
    $db->update('goods', ["stock_num[-]" => 1], ['id' => 1]);
    print_r('ok')
} else {
    print_r('sorry')
}
```

我们尝试模拟高并发场景，使用ab压测工具，

> ab -n 500 -c 500 http://www.13sai.com/test/miaosha.php

运行后发现库存stock_num很可能已经变成负数了，出现了超卖问题。


引入redis实现秒杀：

```
//实例化redis
$redis = new Redis();
//连接
$redis->connect('127.0.0.1', 6379);
$key = 'sale';
$redis->setnx($key, 0);
$redis->watch($key); //监测一个key的值是否被更改

$sale_num = $redis->get($key);
if ($sale_num > 4) {
	exit();
}

$redis->multi(); //标记事务

$redis->incr($key);  //销量+1
sleep(1); //模拟真实环境
$ret = $redis->exec(); // 事务块内所有命令的返回值，按命令执行的先后顺序排列。
if ($ret) {
	include 'db.php';
	$db = new db([
	    'database_type' => 'mysql',
	    'database_name' => 'test',
	    'server' => 'www.13sai.com',
	    'username' => '13sai',
	    'password' => '*',
	    'charset' => 'utf8'
	]);
	$db->update('goods', ["stock_num[-]" => 1], ['id' => 1]);
}
```

重新增加库存到5，多次测试，发现库存并无出现负数情况，测试通过。

> WATCH命令可以监控一个或多个键，一旦其中有一个键被修改（或删除），之后的事务就不会执行。监控一直持续到EXEC命令（事务中的命令是在EXEC之后才执行的，所以在MULTI命令后可以修改WATCH监控的键值）

原文链接：http://www.13sai.com/index.php/index/content/id/165