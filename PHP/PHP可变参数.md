# PHP可变参数

标签（空格分隔）： 未分类

---

> PHP 在用户自定义函数中支持可变数量的参数列表。在 PHP 5.6 及以上的版本中，由 ... 语法实现；在 PHP 5.5 及更早版本中，使用函数 func_num_args()，func_get_arg()，和 func_get_args() 。

```
function B(...$numbers) {
    $acc = 0;
    foreach ($numbers as $n) {
        $acc += $n;
    }
    return $acc;
}

echo B(1, 2, 3, 4);
```

无需说明了，下面还有一个有意思的写法

```
function A($a, $b) {
    return $a + $b;
}

echo A(...[1, 2]);
```

把参数当做数组传输进去，这个就很方便了，比如Redis的zadd方法，就非常方便。

```
$redis = new \Redis();
$redis->connect('localhost', 6379, 5);
$arr = ['test',100, 200, 300, 400];
echo $redis->zAdd(...($arr));
```

两种写法应用场景还是相当广的，大家可以多用。




