## trait新特性

自 PHP 5.4.0 起，PHP 实现了一种代码复用的方法，称为 trait。

Trait 是为类似 PHP 的单继承语言而准备的一种代码复用机制。Trait 为了减少单继承语言的限制，使开发人员能够自由地在不同层次结构内独立的类中复用 method。Trait 和 Class 组合的语义定义了一种减少复杂性的方式，避免传统多继承和 Mixin 类相关典型问题。

Trait 和 Class 相似，但仅仅旨在用细粒度和一致的方式来组合功能。 无法通过 trait 自身来实例化。它为传统继承增加了水平特性的组合；也就是说，应用的几个 Class 之间不需要继承。


demo:

```
<?php
trait Test1
{
    public $name = 'test1'; //trait类中可以用属性
    public function hello1() //trait类中主要成员是方法
    {
        return 'Test1::hello1()';
    }
}
//2.创建triat类Test2
trait Test2
{
    function hello2()
    {
        return 'Test2::hello2()';
    }
}
//3.创建Demo1类
class Demo1
{
    use Test1, Test2;
}
//进行测试
$obj = new Demo1;
echo $obj->hello1(); //访问trait类Test1中的hello1()
echo '<hr>';
echo $obj->name; //访问ttrait类Test1中的$name属性
echo '<hr>';
echo $obj->hello2(); //访问ttrait类Test1中的hello2()
```

trait可以互相嵌套,一个trait类中可以用use导入另一个trait类,理解成代码复制就可以了。


优先级：
> 在同一个类中，同名方法的优先级:子类>Trait类>父类，与就是说，谁离调用者越近，谁的优先级就越高。

```
//1创建一个trait类Test1
trait Test1
{
    public $name = 'test1'; //trait类中可以用属性
    public function hello1() //trait类中主要成员是方法
    {
        return 'Test1::hello1()';

    }
}
//2.创建triat类Test2
trait Test2
{
    use Test1;
    function hello2()
    {
//在Test2中访问Test1中的属性name,注意语法与普通类是一样的
        return 'Test2::hello2()'.$this->name;
    }
}
//3.创建父类Demo
class Demo
{
    public function hello2()
    {
        return '父类Demo::hello2()';
    }
}
//4.创建Demo1类
class Demo1 extends Demo
{
// use Test1, Test2;
    use Test2;
}
//进行测试
$obj = new Demo1;
echo $obj->hello1(); //访问trait类Test1中的hello1()
echo '<hr>';
echo $obj->name; //访问ttrait类Test1中的$name属性
echo '<hr>';
echo $obj->hello2(); //访问ttrait类Test1中的hello2()
```



防止多接口方法被覆盖问题，可以使用别名：
```
trait Test1
{
    public function hello()
    {
        return 'Test1::hello()';
    }
}
//2.创建triat类Test2
trait Test2
{
    public function hello()
    {
        return 'Test2::hello()';
    }
}
//3.创建类Demo
class Demo
{
    use Test1, Test2{
        //用Test1中的hello()方法替代Test2中的同名方法
        Test1::hello insteadof Test2;
        //Test2中的hello()方法用别名访问
        Test2::hello as test2Hello;
    } //这里千万不要加分号 ;
}

//进行测试
$obj = new Demo;
echo $obj->hello(); //访问Test1中的hello()
echo '<hr>';
echo $obj->test2Hello();//别名访问Test2中的hello()
```