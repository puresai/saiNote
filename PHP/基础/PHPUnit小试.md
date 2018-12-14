# PHPUnit小试

---

> 单元测试主要是作为一种良好实践来编写的，它能帮助开发人员识别并修复 bug、重构代码，还可以看作被测软件单元的文档。要实现这些好处，理想的单元测试应当覆盖程序中所有可能的路径。一个单元测试通常覆盖一个函数或方法中的一个特定路径。但是，测试方法并不一定非要是一个封装良好的独立实体。测试方法之间经常有隐含的依赖关系暗藏在测试的实现方案中。

### windows安装

1. 下载 https://phar.phpunit.de/phpunit.phar 并将文件保存到你的目录，比如D:\www\wnmp

2. 打开命令行，进入刚才的目录，执行
> D:\www\wnmp> echo @php "%~dp0phpunit.phar" %* > phpunit.cmd

phpunit.phar就是刚才下载的文件，有可能略有不同，带了版本号。

3. 将你的目录加入环境变量

测试一下
> phpunit --version

能看见当前phpunit版本号即表明安装成功。


### 初识

> composer require --dev phpunit/phpunit ^7.0


另有以下可选组件包可用：

PHP_Invoker：一个工具类，可以用带有超时限制的方式调用可调用内容。当需要在严格模式下保证测试的超时限制时，这个组件包是必须的。

DbUnit：移植到 PHP/PHPUnit 上的 DbUnit 用于提供对数据库交互测试的支持。


demo
```
use PHPUnit\Framework\TestCase;

class Demo1 extends TestCase
{
    public function testPushAndPop()
    {
        $stack = [];
        $this->assertEquals(0, count($stack));

        array_push($stack, 'foo');
        $this->assertEquals('foo', $stack[count($stack)-1]);
        $this->assertEquals(1, count($stack));

        $this->assertEquals('foo', array_pop($stack));
        $this->assertEquals(0, count($stack));
    }
}
```


phpunit运行demo，可以看到
> PHPUnit 7.0.3 by Sebastian Bergmann and contributors.

```                                                             
1 / 1 (100%)

Time: 490 ms, Memory: 10.00MB

OK (1 test, 5 assertions)
```

测试通过！






