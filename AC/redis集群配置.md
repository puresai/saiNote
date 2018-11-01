# redis集群配置

标签（空格分隔）： redis

---

> 环境：centos7.5，redis5.0.0

下载安装在合适的目录，我是安装在/usr/local/redis-5.0.0下，过程比较简单，不赘述了。

同级下新建cluster，拷贝redis.conf文件进来，生成4个文件redis_2001.conf、redis_2002.conf、redis_2003.conf、redis_2004.conf，修改对应配置。

主要配置如下
```
# 端口号
port 2001
# 后台启动
daemonize yes
# 开启集群
cluster-enabled yes
# 集群节点配置文件
cluster-config-file nodes-2001.conf
# 进程pid的文件位置
pidfile /var/run/redis-2001.pid

# 持久化配置
# 开启aof
appendonly yes
# aof文件路径
appendfilename "appendonly-2001.aof"
# rdb文件路径
dbfilename dump-2001.rdb
```

启动redis，
```
redis-server cluster/redis-2001.conf
redis-server cluster/redis-2002.conf
redis-server cluster/redis-2003.conf
redis-server cluster/redis-2004.conf
```


连接构建redis集群

> ./redis-trib.rb create --replicas 1 127.0.0.1:2001 127.0.0.1:2002  127.0.0.1:2003 127.0.0.1:2004

抛出警告：
> WARNING: redis-trib.rb is not longer available!

5.0版本抛弃了redis-trib。

按照提示执行：
> redis-cli --cluster create 127.0.0.1:2001 127.0.0.1:2002 127.0.0.1:2003 127.0.0.1:2004 --cluster-replicas 1

错误输出如下：
```
*** ERROR: Invalid configuration for cluster creation.
*** Redis Cluster requires at least 3 master nodes.
*** This is not possible with 4 nodes and 1 replicas per node.
*** At least 6 nodes are required.
```

因为是单机测试而已，去掉复制
> redis-cli --cluster create 127.0.0.1:2001 127.0.0.1:2002 127.0.0.1:2003 127.0.0.1:2004

然后有个保存配置的确认，yes之后成功！

```
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

进入客户端
> redis-cli -p 2001

查看节点
> cluster nodes

```
90a734c7716a00060584acb82cb3dd7e437a5459 127.0.0.1:2003@12003 master - 0 1541042930063 3 connected 8192-12287
7ac1fab72c275ba9549a9da71245fdd29a3920a6 127.0.0.1:2002@12002 master - 0 1541042930563 2 connected 4096-8191
490035c372472798a224b6d00c249d8b41508b78 127.0.0.1:2001@12001 myself,master - 0 1541042928000 1 connected 0-4095
9101d285b991c6dcb086cf3da4cd0eae7777ece0 127.0.0.1:2004@12004 master - 0 1541042929563 4 connected 12288-16383
```

可以看出集群已搭建成功！

对应的php可以使用predis操作redis。








