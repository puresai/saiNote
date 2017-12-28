## instanceof的作用

instanceof可以判断某个对象是否是某个类的实例。

官方的定义：instanceof 用于确定一个 PHP 变量是否属于某一类 class 的实例



### 1. 判断一个对象是否是某个类的实例
```
<?php
class MyClass
{
}

class NotMyClass
{
}

$a = new MyClass;
var_dump($a instanceof MyClass);//输出true
var_dump($a instanceof NotMyClass);//输出false
```

### 2. 判断一个对象是否实现了某个接口
```
<?php
interface MyInterface
{
}

class MyClass implements MyInterface
{
}

$a = new MyClass;
var_dump($a instanceof MyClass);//返回true
var_dump($a instanceof MyInterface);//返回true
```