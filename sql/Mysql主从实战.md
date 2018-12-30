# Mysql主从实战

标签（空格分隔）： 未分类

---

主从分别建立库test


### master

> GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO repl@'127.0.0.1' IDENTIFIED BY '111111'; //分配账号
grant all on *.* to repl@'127.0.0.1'; //授权


查看状态
> SHOW MASTER STATUS;

记住这里的file和position。

修改配置
[mysqld]
log-bin=mysql-bin
server-id=1

重启


### slave

修改配置，注意server_id要与主库，其他从库不一样，可以设置成ip
[mysqld]
log_bin           = mysql-bin
server_id         = 2
relay_log         = mysql-relay-bin
log_slave_updates = 1
read_only         = 1

建议先
> reset slave

连接master
> CHANGE MASTER TO MASTER_HOST='127.0.0.1',MASTER_USER='repl', MASTER_PASSWORD='111111',  MASTER_LOG_FILE='mysql-bin.000051',master_log_pos=8694; 

MASTER_LOG_FILE和master_log_pos就是上面记住的file和position。


开启并查看状态
> start slave;
> show slave status

如果Slave_IO_Running和Slave_SQL_Running都显示Yes，一般就成功了。

可以再master插入数据，测试。

> 如果master已有数据，可以锁定master（flush tables with read lock），然后导出同步到slave，配置完成后释放master（unlock tables）


