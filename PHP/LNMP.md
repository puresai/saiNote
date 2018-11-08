# LNMP

标签（空格分隔）： LNMP

---

### PHP安装

扩展安装 
> yum -y install openssl openssl-devel curl curl-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel pcre pcre-devel libxslt libxslt-devel bzip2 bzip2-devel libicu libicu-devel libmcrypt-devel  libxml2-devel   gcc gcc-c++ m4  autoconf numactl

下载php源码包
>wget http://hk2.php.net/get/php-7.1.23.tar.gz/from/this/mirror
tar -zxvf mirror
cd mirror

配置

> ./configure  --prefix=/usr/local/php7 --with-config-file-path=/usr/local/php7/etc --enable-fpm --with-fpm-user=www --with-fpm-group=www --enable-mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --with-iconv-dir=/usr/local --with-freetype-dir=/usr/local/freetype --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml --disable-rpath --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --enable-mbregex --enable-mbstring --enable-intl --with-mcrypt --enable-ftp --with-gd --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --enable-sockets --with-xmlrpc --enable-zip --enable-soap --with-gettext --enable-opcache

编译
> make && make install





### nginx
> wget http://nginx.org/download/nginx-1.14.0.tar.gz
 tar zxvf nginx-1.14.0.tar.gz
cd nginx-1.14.0

配置
> ./configure --user=www --group=www --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_gzip_static_module --with-http_sub_module --with-http_v2_module

编译
> make && make install





### mysql

卸载冲突数据库
> rpm -qa | grep mariadb
rpm -e --nodeps mariadb-libs-5.5.56-2.el7.x86_64

下载
> wget http://mirrors.163.com/mysql/Downloads/MySQL-5.7/mysql-5.7.23-linux-glibc2.12-x86_64.tar.gz   

> tar zxvf mysql-5.7.23-linux-glibc2.12-x86_64.tar.gz
mv mysql-5.7.23-linux-glibc2.12-x86_64 /usr/local/mysql    

                                                                   
// 添加用户
> groupadd mysql
useradd mysql -g mysql -p mysql
mkdir /data/mysql    
chown mysql:mysql /data/mysql    

编辑/etc/my.cnf，以下参考
```
[mysqld]
explicit_defaults_for_timestamp
user    = mysql
port    = 3306 //端口
socket  = /tmp/mysql.sock
basedir = /usr/local/mysql
datadir = /data/mysql  //数据库文件地址
open_files_limit    = 3072
back_log = 103
max_connections = 512
max_connect_errors = 100000
table_open_cache = 2048
table_definition_cache = 2048
external-locking = FALSE
max_allowed_packet = 512M
sort_buffer_size = 2M
join_buffer_size = 2M
thread_cache_size = 51
query_cache_size = 0
query_cache_type = 0
tmp_table_size = 96M
max_heap_table_size = 96M
slow_query_log = 1
slow_query_log_file = /data/mysql/slow.log
log-error = /data/mysql/error.log
long_query_time = 5
server-id = 2
#log-bin = mysql-bin
secure_file_priv=/tmp/

wait_timeout = 3600
interactive_timeout = 3600

sync_binlog = 1000
binlog_cache_size = 4M
max_binlog_cache_size = 160M
max_binlog_size = 1024M
expire_logs_days = 3
key_buffer_size = 32M
read_buffer_size = 1M
read_rnd_buffer_size = 16M
bulk_insert_buffer_size = 64M
myisam_sort_buffer_size = 128M
myisam_max_sort_file_size = 10G
myisam_repair_threads = 1
character-set-server=utf8

log-bin=mysql-bin
server_id=1

group_concat_max_len=102400

transaction_isolation = REPEATABLE-READ

innodb_buffer_pool_size = 256M
innodb_flush_log_at_trx_commit = 1
innodb_log_buffer_size = 8M
innodb_log_file_size = 128M
innodb_log_files_in_group = 2
innodb_max_dirty_pages_pct = 50
innodb_file_per_table = 1
innodb_locks_unsafe_for_binlog = 0
innodb_print_all_deadlocks = 1 
#innodb_undo_directory = /data/mysql
#innodb_undo_logs = 128
#innodb_undo_tablespaces = 3
innodb_autoinc_lock_mode=2
innodb_lock_wait_timeout=5
innodb_flush_log_at_trx_commit=2
innodb_buffer_pool_load_at_startup = 1
innodb_buffer_pool_dump_at_shutdown = 1
innodb_io_capacity=1000
innodb_io_capacity_max=2000


sql_mode=NO_ENGINE_SUBSTITUTION


[client]
socket = /tmp/mysql.sock
port    = 3306
user=root
[mysqldump]
quick
max_allowed_packet = 32M
user=root
```

初始化：

> mysqld --initialize-insecure --user=mysql 初始化mysql 空密码 

初始化之后/data/mysql目录会自动建立很多文件

启动即可
> mysqld










