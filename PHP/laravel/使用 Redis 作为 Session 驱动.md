# 使用 Redis 作为 Session 驱动

标签（空格分隔）： session redis laravel

---
先看这篇：

[[ Laravel 5.5 文档 ] 处理用户请求 —— Session 实现、配置与使用详解][1]

Tips：
- laravel的session机制是自行实现，不可以使用 ```$_SESSION```获取
- 务必开启中间件StartSession
- 修改.env的SESSION_DRIVER为redis
- 如果不是正常在控制器使用了die/exit等，需要存储session务必手动存储，如
> $request->session()->save();//因为laravel在调用玩controller才会去存储session


  [1]: https://laravelacademy.org/post/7954.html