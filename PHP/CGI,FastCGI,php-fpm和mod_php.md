# CGI,FastCGI,php-fpm和mod_php

标签（空格分隔）： 基础知识

---

### CGI（Common Gateway Interface） - 通用网关接口

> Common Gateway Interface(CGI)为web servers执行程序，控制台应用程序运行在服务上自动产生web网页（命令行接口程序）提供一种标准协议。这样的程序被称作CGI脚本或简称为CGIs. 脚本被服务怎样执行的描述被服务决定。一般情况下，一个CGI脚本执行一次请求产生HTML. 简言之， CGI程序通过Unix/Linux标准输入接受一个HTTP格式数据，和其他数据（如URL paths, URL arguments, HTTP header data）通过众所周知的unix/linux处理环境变量。


### FastCGI(FastCommonGatewayInterface) - 快速通用网关接口

> FastCGI全称快速通用网关接口（FastCommonGatewayInterface）。
FastCGI像是一个常驻(long-live)型的CGI，它可以一直执行着，只要激活后，不会每次都要花费时间去fork一次(这是CGI最为人诟病的fork-and-execute 模式)。它还支持分布式的运算, 即 FastCGI 程序可以在网站服务器以外的主机上执行并且接受来自其它网站服务器来的请求。
FastCGI是语言无关的、可伸缩架构的CGI开放扩展，其主要行为是将CGI解释器进程保持在内存中并因此获得较高的性能。众所周知，CGI解释器的反复加载是CGI性能低下的主要原因，如果CGI解释器保持在内存中并接受FastCGI进程管理器调度，则可以提供良好的性能、伸缩性、Fail- Over特性等等。


### php-fpm(FastCGI Process Manager) - FastCGI进程管理器

> php-fpm是 FastCGI 的实现，并提供了进程管理的功能。进程包含 master 进程和 worker 进程两种进程。master 进程只有一个，负责监听端口，接收来自 Web Server 的请求，而 worker 进程则一般有多个(具体数量根据实际需要配置)，每个进程内部都嵌入了一个 PHP 解释器，是 PHP 代码真正执行的地方。

### mod_php

> apache的php模块，将PHP做为web-server的子进程控制,两者之间有从属关系.最明显的例子就是在CGI模式下,如果修改了PHP.INI的配置文件,不用重启web服务便可生效,而模块模式下则需要重启web服务。以mod_php模式运行PHP，意味着php是作为apache的一个模块来启动的，因此只有在apache启动的时候会读取php.ini配置文件并加载扩展模块，在apache运行期间是不会再去读取和加载扩展模块的


更多可了解：
[CGI、FastCGI和php-fpm的概念和区别][1]
[php的几种运行模式CLI、CGI、FastCGI、mod_php][2]


  [1]: https://www.cnblogs.com/zuochuang/p/6509773.html
  [2]: https://www.cnblogs.com/orlion/p/5282753.html