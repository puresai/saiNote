# PHPUnit进阶


---

### 测试的依赖关系(@depends)

```
use PHPUnit\Framework\TestCase;

class StackTest extends TestCase
{
    public function testEmpty()
    {
        $stack = [];
        $this->assertEmpty($stack);

        return $stack;
    }

    /**
     * @depends testEmpty  // 标注依赖
     */
    public function testPush(array $stack)
    {
        array_push($stack, 'foo');
        $this->assertEquals('foo', $stack[count($stack)-1]);
        $this->assertNotEmpty($stack);

        return $stack;
    }

    /**
     * @depends testPush
     */
    public function testPop(array $stack)
    {
        $this->assertEquals('foo', array_pop($stack));
        $this->assertEmpty($stack);
    }
}
```

测试可以使用多个 @depends 标注。PHPUnit 不会更改测试的运行顺序，因此你需要自行保证某个测试所依赖的所有测试均出现于这个测试之前。

拥有多个 @depends 标注的测试，其第一个参数是第一个生产者提供的基境，第二个参数是第二个生产者提供的基境，以此类推。

多重依赖demo
```
use PHPUnit\Framework\TestCase;

class Demo3 extends TestCase
{
    public function testA()
    {
        $this->assertTrue(true);
        return 1;
    }

    public function testB()
    {
        $this->assertTrue(true);
        return 2;
    }

    /**
     * @depends testA
     * @depends testB
     */
    public function testC()
    {
        $this->assertEquals(
            [1, 2],
            func_get_args()
        );
    }
}
```


### 数据供给器（@dataProvider）

数据供给器方法必须声明为 public，其返回值要么是一个数组，其每个元素也是数组；要么是一个实现了 Iterator 接口的对象，在对它进行迭代时每步产生一个数组。每个数组都是测试数据集的一部分，将以它的内容作为参数来调用测试方法。

```
use PHPUnit\Framework\TestCase;

class Demo4 extends TestCase
{
    /**
     * @dataProvider provider
     */
    public function testAdd($a, $b, $c)
    {
        $this->assertEquals($c, $a+$b);
    }

    public function provider()
    {
        return [
            [0,3,3],
            [2, 1, 2]
        ];
    }
}
```


### 异常测试

### 错误测试

默认情况下，PHPUnit 将测试在执行中触发的 PHP 错误、警告、通知都转换为异常。利用这些异常，就可以，比如说，预期测试将触发 PHP 错误。






