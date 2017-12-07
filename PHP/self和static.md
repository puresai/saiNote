### 调用静态变量

> PHP官方也说过，大概是说self调用的就是本身代码片段这个类，而static调用的是从堆内存中提取出来，访问的是当前实例化的那个类，那么 static 代表的就是那个类。

```
class A 
{

  protected static $str = "This is class A";

  public static function getStr()
  {
      echo self::$str;
  }

}

class B extends A
{
  protected static $str = "This is class B";
}


B::getStr();

// 输出
This is class A
```

```
class A 
{

  protected static $str = "This is class A";

  public static function getStr()
  {
      echo static::$str;
  }

}

class B extends A
{
  protected static $str = "This is class B";
}


B::getStr();

// 输出
This is class B
```



### new self()和new static()

> self - 就是这个类，是代码段里面的这个类。

> static - PHP 5.3新特性。当前这个类，有点像$this的意思，从堆内存中提取出来，访问的是当前实例化的那个类，那么 static 代表的就是那个类。

```
class A
{

    public function getSelf()
    {
        return new self();
    }

    public function getStatic()
    {
        return new static();
    }

}

$f = new A();

print get_class($f->getSelf());
print get_class($f->getStatic());

class B extends A
{
    public function getSelf2()
    {
        return new self();
    }

    public function getStatic2()
    {
        return new static();
    }
}

$f = new B();

print get_class($f->getSelf());
print get_class($f->getStatic());
print get_class($f->getSelf2());
print get_class($f->getStatic2());

// 输出
AAABBB
```