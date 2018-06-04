> ArrayAccess提供像访问数组一样访问对象的能力的接口。

具体有一下几个方法：
```
ArrayAccess {
/* 方法 */
abstract public boolean offsetExists ( mixed $offset )
abstract public mixed offsetGet ( mixed $offset )
abstract public void offsetSet ( mixed $offset , mixed $value )
abstract public void offsetUnset ( mixed $offset )
}
```
文档看php手册 ArrayAccess（数组式访问）接口

调用情况看实例：
```
<?php
/**
 * @author 13sai
 */
class TestAccess implements ArrayAccess {
    
    private $data = [];
    
    public function __get($key) {
        print_r(__METHOD__);
        return $this->data[$key];
    }
    
    public function __set($key,$value) {
        print_r(__METHOD__);
        $this->data[$key] = $value;
    }
    
    public function __isset($key) {
        print_r(__METHOD__);
        return isset($this->data[$key]);
    }
    
    public function __unset($key) {
        print_r(__METHOD__);
        unset($this->data[$key]);
    }

    public function offsetSet($offset,$value) {
        print_r(__METHOD__);
        if (is_null($offset)) {
            $this->data[] = $value;
        } else {
            $this->data[$offset] = $value;
        }
    }
    
    public function offsetGet($offset) {
        print_r(__METHOD__);
        return $this->offsetExists($offset) ? $this->data[$offset] : null;
    }


    public function offsetExists($offset) {
        print_r(__METHOD__);
        return isset($this->data[$offset]);
    }
    
    public function offsetUnset($offset) {
        print_r(__METHOD__);
        if ($this->offsetExists($offset)) {
            unset($this->data[$offset]);
        }
    }

}

$animal = new TestAccess();
echo('<br>1.');
var_dump(empty($animal->dog));

echo('<br>2.');
var_dump(empty($animal['pig']));

echo('<br>3.');
$animal->dog = 'dog';

echo('<br>4.');
$animal['pig'] = 'pig';

echo('<br>13.');
var_dump(empty($animal->dog));

echo('<br>14.');
var_dump(empty($animal['pig']));

echo('<br>5.');
var_dump(isset($animal->dog));

echo('<br>6.');
var_dump(isset($animal['pig']));

echo('<br>7.');
var_dump($animal->pig);

echo('<br>8.');
var_dump($animal['dog']);

echo('<br>9.');
unset($animal['dog']);

echo('<br>10.');
unset($animal->pig);

echo('<br>11.');
var_dump($animal['pig']);

echo('<br>12.');
var_dump($animal->dog);
```

输出调用可运行，一看便知！

```
1.TestAccess::__isset
D:\project\test\access.php:126:boolean true

2.TestAccess::offsetExists
D:\project\test\access.php:129:boolean true

3.TestAccess::__set
4.TestAccess::offsetSet
13.TestAccess::__issetTestAccess::__get
D:\project\test\access.php:139:boolean false

14.TestAccess::offsetExistsTestAccess::offsetGetTestAccess::offsetExists
D:\project\test\access.php:142:boolean false

5.TestAccess::__isset
D:\project\test\access.php:145:boolean true

6.TestAccess::offsetExists
D:\project\test\access.php:148:boolean true

7.TestAccess::__get
D:\project\test\access.php:151:string 'pig' (length=3)

8.TestAccess::offsetGetTestAccess::offsetExists
D:\project\test\access.php:154:string 'dog' (length=3)

9.TestAccess::offsetUnsetTestAccess::offsetExists
10.TestAccess::__unset
11.TestAccess::offsetGetTestAccess::offsetExists
D:\project\test\access.php:163:null

12.TestAccess::__get
( ! ) Notice: Undefined index: dog in D:\project\test\access.php on line 26
Call Stack
#	Time	Memory	Function	Location
1	0.2010	384480	{main}( )	...\access.php:0
2	0.2010	385368	TestAccess->__get( string(3) )	...\access.php:166
 D:\project\test\access.php:166:null
 ```