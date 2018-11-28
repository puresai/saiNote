# LNMP开机自启动

标签（空格分隔）： devops

---

有时候服务器需要重启，但重启各种程序需要启动是个麻烦的事情，我们尝试配置开机自启动。

环境：centos 7.5

我的版本跟低版本有多不同，不能通过chkconfig add命令加入，我们可以进入目录

> /lib/systemd/system

依次新建

```
// mysqld.service

[Unit]
Description=mysql
After=network.target
[Service]
Type=forking
ExecStart=/usr/bin/mysql.server
ExecReload=/bin/systemctl restart mysqld.service
ExecStop=/bin/systemctl stop mysqld.service
PrivateTmp=true
[Install]
WantedBy=multi-user.target


----------
//nginx.service

[Unit]
Description=nginx
After=network.target
[Service]
Type=forking
ExecStart=/usr/bin/nginx
ExecReload=/usr/bin/nginx -s reload
ExecStop=/usr/bin/nginx -s quit
PrivateTmp=true
[Install]
WantedBy=multi-user.target


----------
// php-fpm.service

[Unit]
Description=php-fpm
After=network.target
[Service]
Type=forking
ExecStart=/usr/bin/php-fpm
ExecReload=ps -ef|grep php-fpm|awk -F ' ' '{print $2}'|xargs kill -USR2
ExecStop=ps -ef|grep php-fpm|awk -F ' ' '{print $2}'|xargs kill -INT
PrivateTmp=true
[Install]
WantedBy=multi-user.target


----------

// redis.service
[Unit]
Description=redis
After=network.target
[Service]
Type=forking
ExecStart=/usr/bin/redis-server /usr/local/redis-5.0.0/redis.conf
ExecStop=/usr/local/redis-5.0.0/redis-cli shutdown
PrivateTmp=true
[Install]
WantedBy=multi-user.target
```

> ExecStart,ExecStop,ExecReload各有不同，自行修改

并且systemctl enable启动。

>reboot

测试一下。




