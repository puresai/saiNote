# Linux装composer

标签（空格分隔）： composer

---

> 不要yum安装，因为composer依赖php，它会将你的php也覆盖掉


步骤简单：

下载
> curl -sS https://getcomposer.org/installer | php

注意： 如果上述方法由于某些原因失败了，你还可以通过 php 下载安装器：
> php -r "readfile('https://getcomposer.org/installer');" | php

将composer.phar文件移动到bin目录以便全局使用composer命令
> mv composer.phar /usr/local/bin/composer

当然，由于在国内，可以切换国内镜像源

> composer config -g repo.packagist composer https://packagist.phpcomposer.com




