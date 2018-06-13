## PHP两个好用的函数处理函数

### call_user_func — 把第一个参数作为回调函数调用

```
demo:


function increment($var)
{
    echo 'hello '.$var;
}
$a = '13sai';
call_user_func('increment', $a);


class A
{
    function increment($var)
    {
        echo 'hello '.$var;
    }
}


$a = 'world';
call_user_func([(new A()), 'increment'], $a);

```



### call_user_func_array — 调用回调函数，并把一个数组参数作为回调函数的参数

```
<?php
function foobar($arg, $arg2) {
    echo __FUNCTION__, " got $arg and $arg2\n";
}
class foo {
    function bar($arg, $arg2) {
        echo __METHOD__, " got $arg and $arg2\n";
    }
}


// Call the foobar() function with 2 arguments
call_user_func_array("foobar", array("one", "two"));

// Call the $foo->bar() method with 2 arguments
$foo = new foo;
call_user_func_array(array($foo, "bar"), array("three", "four"));
```


> 说到这里，可以看看可变参数函数(5.6+)


```
<?php
function sum(...$numbers) {
    $acc = 0;
    foreach ($numbers as $n) {
        $acc += $n;
    }
    return $acc;
}

echo sum(1, 2, 3, 4, 5);

```


------

重点来了，当可变参数长度函数遇上call_user_func_array

试想，我们希望传入不定长度参数，而参数又只是数组。那么你可以这么做：

```
function sum(...$numbers) {
    $acc = 0;
    foreach ($numbers as $n) {
        $acc += $n;
    }
    return $acc;
}

print_r(call_user_func_array("sum", [7,8,9,10]));

```