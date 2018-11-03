# windows搭建php+mysql+nginx

标签（空格分隔）： wnmp

---

### 1. 下载nginx，php7，mysql，并放置到你需要的文件夹
### 2. nginx
进入nginx目录，执行nginx.exe

浏览器进入localhost，会看到welcome to nginx！

### 3. 配置php
php.ini

    php.ini-development复制为php.ini

修改
```
;这里根据自己的实际情况而定
extension_dir = "D:/wnmp/php-7.0.26/ext"
;加入扩展：选择需要运行哪些扩展，只需将extension前面的注释去掉，例如：
extension=php_mbstring.dll
extension=php_exif.dll      ; Must be after mbstring as it depends on it
extension=php_mysqli.dll

CGI 设置

enable_dl = On
cgi.force_redirect = 0
cgi.fix_pathinfo = 1
fastcgi.impersonate = 1
cgi.rfc2616_headers = 1
```

修改nginx配置
打开D:\wnmp\nginx-1.12.2\conf下nginx.conf

更改root目录，添加index.php
```
location / {  
    root   D:/wnmp/www;  
    index  index.html index.htm index.php;  
}  


更改root目录，更改/scripts为$document_root# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000  #  
location ~ \.php$ {  
    root           D:/wnmp/www;  
    fastcgi_pass   127.0.0.1:9000;  
    fastcgi_index  index.php;  
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;  
    include        fastcgi_params;  
}
```
重启nginx

> nginx.exe -s reload

可以nginx -t测试下配置有没有写错，如果提示ok就表明配置写法正确。
启动

> php-cgi.exe -b 127.0.0.1:9000

提示 计算机中丢失 fbclient.dll

    解决：会看到是扩展load的错误，注释php.ini里相应的扩展就好了。

再次启动php-cgi,ok!
测试
创建并编辑一个php文件放在D:/wnmp/www目录下，例如：新建一个phpinfo.php文件，内容：

```
<?php 
phpinfo();
```

访问http://localhost/phpinfo.php（若不是使用80端口记得要加上端口号访问），会出现我们熟悉的phpinfo界面。
Congratulations！

### 4. 安装Mysql
打开msi安装提示需要安装.NET Framework 4，前往microsoft官网下载.NET Framework 4，总是下载失败，转而下载.NET Framework 4.5，安装成功！

    https://www.microsoft.com/zh-cn/download/details.aspx?id=30653

继续安装mysql
一步步安装需要的服务就好。
如果安装mysql那一步总是failed，请安装vc++2013，链接如下：

    https://www.microsoft.com/zh-cn/download/details.aspx?id=40784

启动mysql，尝试连接就算完成了。

(当然也可以建议安装，可参照windows安装mysql文章)




