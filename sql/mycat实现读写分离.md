# mycat实现读写分离


> mysql中间件，分库分表，读写分离。

环境：
centos7.4
mysql5.7

1. 下载安装

下载
[下载地址][1]

解压到你喜欢的目录。

启动，
> ./bin/mycat start

> tips:建议修改配置文件时bak下原配置文件。

秒启动，尝试修改配置(conf/wrapper.conf)，保存后发现mycat进程没有了。

猜测是OOM kill了，查看系统日志，貌似无mycat异常。
> dmesg | tail -20

查看logs目录下日志，应该是内存不足。（心疼ecs1cpu1g的配置）

修改conf/wrapper.conf
```
# Java Additional Parameters
#wrapper.java.additional.1=
wrapper.java.additional.1=-DMYCAT_HOME=.
wrapper.java.additional.2=-server
wrapper.java.additional.3=-XX:MaxPermSize=64M
wrapper.java.additional.4=-XX:+AggressiveOpts
wrapper.java.additional.5=-XX:MaxDirectMemorySize=256M
wrapper.java.additional.6=-Dcom.sun.management.jmxremote
wrapper.java.additional.7=-Dcom.sun.management.jmxremote.port=1984
wrapper.java.additional.8=-Dcom.sun.management.jmxremote.authenticate=false
wrapper.java.additional.9=-Dcom.sun.management.jmxremote.ssl=false
wrapper.java.additional.10=-Xmx512M
wrapper.java.additional.11=-Xms512M
```

重新启动，成功，进程也没有被kill，但内存已经80%左右了。

关闭ecs云监控缩减内存占用，调低mysql占用内存缓冲区。

开启3306,3307端口的mysql实例，增加mycat数据库，并配置master-slave，设置好所需账号密码权限。

关注下内存占用，约85%，还能撑住。

关闭mycat。

server.xml主要修改，数据库，账号密码。
```
<user name="root">
		<property name="password">123456</property>
		<property name="schemas">sai</property>
</user>
```
sai为逻辑库，可自行配置。

schema.xml主要修改
```
// sai对应server.xml配置
<schema name="sai" checkSQLschema="false" sqlMaxLimit="100">
	<table name="company" primaryKey="ID" type="global" dataNode="dn1" />
</schema>
// localhost3306对应dataHost的name，database是真实的数据库
<dataNode name="dn1" dataHost="localhost3306" database="mycat" />

<dataHost name="localhost3306" maxCon="100" minCon="10" balance="1"
		  writeType="0" dbType="mysql" dbDriver="native" switchType="2"  slaveThreshold="30">
	<heartbeat>show slave status</heartbeat>
	// 读（3307）写（3306）实例
	<writeHost host="hostM1" url="127.0.1:3306" user="mycat"
			   password="111111">
		<!-- can have multi read hosts -->
		<readHost host="hostS2" url="127.0.1:3307" user="mycat" password="111111" />
	</writeHost>
</dataHost>
```

```
balance指的负载均衡类型，目前的取值有4种：

0, 不开启读写分离机制，所有读操作都发送到当前可用的writeHost上。
1，全部的readHost与stand by writeHost参与select语句的负载均衡，简单的说，当双主双从模式(M1->S1，M2->S2，并且M1与 M2互为主备)，正常情况下，M2,S1,S2都参与select语句的负载均衡。
2，所有读操作都随机的在writeHost、readhost上分发。
3，所有读请求随机的分发到wiriterHost对应的readhost执行，writerHost不负担读压力

switchType指的是切换的模式，目前的取值也有4种：

-1， 表示不自动切换
1， 默认值，表示自动切换，1.5版本后丢弃
2 ，基于MySQL主从同步的状态决定是否切换,心跳语句为 show slave status
```

启动。

>  mysql -h127.0.0.1 -uroot -P8066 -p

输入密码进入，切换sai

默认端口8066，mysql执行下语句，
> INSERT INTO `company` VALUES ('1', '13sai');

> select * from company;

mysql实例已经开启了查询日志，查看日志，读写分离了。


> 吐槽
1. 假如表很多，配置不是得晕死
2. 最近发现很多好用的中间件底层都是java，考虑要不要入坑。




  [1]: http://dl.mycat.io/