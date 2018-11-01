# windows7 安装mysql5.7

标签（空格分隔）： mysql

---

环境：
windows7，mysql5.7

### 1. 下载（mysql）[下载地址][1]并解压到所需目录。

### 2. 新建my.ini

```
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8 

[mysqld]
port = 3308
basedir=D:/mysql-5.7.24
datadir=D:/mysql-5.7.24/data

# 允许最大连接数
max_connections=200

# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8

# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```
3. 初始化安装

进入bin目录

> mysqld –-initialize //初始化
mysqld install

4. 启动重置
启动mysql
> net start mysql

进入data，查看.err文件，查看生成的随机密码。

> mysql -u root -p 

然后输入密码，进入mysql

> 此处，一直报10061错误，然后反复装了几次，失败，回来把端口改为3306，成功进入。

最后重置密码
> ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码' PASSWORD EXPIRE NEVER;	// 密码永不过期






  [1]: https://dev.mysql.com/downloads/mysql/5.7.html#downloads "mysql下载"