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

脚本说明
```
主要有以下三部分
[unit] :定义与Unit类型无关的通用选项；用于提供unit的描述信息、 unit行为及依赖关系等

[Service]：与特定类型相关的专用选项；此处为Service类型

[Install]：定义由“ systemctl enable”以及"systemctl disable“命令在实现服务启用或禁用时用到的一些选项


----------
Service参数

EnvironmentFile：环境配置文件
ExecStart：指明启动unit要运行命令或脚本的绝对路径
ExecStartPre： ExecStart前运行
ExecStartPost： ExecStart后运行
ExecRsload: 重启当前服务时执行的命令
ExecStopPost：停止当前服务之后执行的命令
ExecStartSec：自动重启当前服务间隔的秒数
ExecStop：指明停止unit要运行的命令或脚本
Restart：当设定Restart=1时，则当次daemon服务意外终止后，会再次自动启动此服务。
TimeoutSec：定义 Systemd 停止当前服务之前等待的秒数。
Environment：指定环境变量。


----------


Install参数：

Alias：别名，可使用systemctl command Alias.service
RequiredBy：被哪些units所依赖，强依赖
WantedBy：被哪些units所依赖，弱依赖
Also：安装本服务的时候还要安装别的相关服务

```


更多请看：
[在linux下创建自定义服务][1]
[linux中的unit的配置文件][2]


  [1]: https://www.jianshu.com/p/92208194d700
  [2]: http://blog.51cto.com/gavin0/2156626