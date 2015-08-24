# PHP5.6.x版本迁移至7.0.x版本
## 先后兼容说明
### 错误和异常处理的变更
许多的Fatal错误，包括一些可以被修正的Fatal错误，在PHP7中以Exceptions异常对待。这些Error Exceptions继承于 [Error](http://php.net/manual/en/class.error.php) 类。而 [Error](http://php.net/manual/en/class.error.php) 类则继承于异常基类 [Throwable](http://php.net/manual/en/class.throwable.php) 接口。 <br>
PHP7中详细的Error相关的信息可以参考页面\[ [PHP7错误](http://php.net/manual/en/language.errors.php7.php) \]。本文中仅仅介绍和向后兼容有关的信息如下。

#### 内部构造函数在失败时抛出异常
在PHP7之前，类内部的构造函数在失败时总是返回NULL或者返回一个不可用的Object，但此版本开始，在构造函数初始化失败时总是会抛出[异常](http://php.net/manual/en/class.exception.php)。

#### 解析错误时会抛出 [解析异常](http://php.net/manual/en/class.parseerror.php)
Parser errors now throw a ParseError object. Error handling for eval() should now include a catch block that can handle this error.
解析错误，现在开始会抛出一个 [解析异常](http://php.net/manual/en/class.parseerror.php) 对象。[eval\(\)](http://php.net/manual/en/function.eval.php) 函数现在开始可以通过 [catch](http://php.net/manual/en/language.exceptions.php#language.exceptions.catch) 捕捉异常，随之做相应处理。

#### E_STRICT 等级的报错被重新分配
所有**E_STRICT**级别的报错已重新分配到其他报错等级中。**E_STRICT**常量依然保留，所以当你设置报错等级为 **error_reporting\(E_ALL|E_STRICT\)**时，不会引起报错。<br>
变更情况如下表
![image](https://cloud.githubusercontent.com/assets/1308846/9434941/01402560-4a76-11e5-9943-f9f153745030.png)

### 变量处理环节的变更
PHP7开始使用一种抽象的语法树来解析PHP代码文件。老版本的PHP因为PHP解析器的局限性是不可能实现这一特性的。但此处的改动引起了一些一致性问题，破坏了向后兼容性。本节详细介绍这块的情况。

#### 对于间接变量、属性、方法的变动 
间接的使用变量、属性、方法，现在开始严格按照从左到右的顺序执行，而不是以前混合各种形式产生各种可能。下表表明的这一改变引起的差异。
![image](https://cloud.githubusercontent.com/assets/1308846/9435404/1bf947a2-4a7a-11e5-97bb-96677cc560fb.png)
这块使用老得从右到左的方式的代码，必须重写了。通过花括号来明确顺序（见上图中间列），以使代码向前兼容PHP7.x，并向后兼容PHP5.x。

#### 对于 [list\(\)](http://php.net/manual/en/function.list.php) 函数处理上的修改
##### [list\(\)](http://php.net/manual/en/function.list.php) 不再按照相反顺序插入元素
[list\(\)](http://php.net/manual/en/function.list.php)函数从此开始按照原数组中的顺序插入到函数参数制定的位置上，不再翻转数据。这点修改只会作用在[list\(\)](http://php.net/manual/en/function.list.php)函数参数一起用了数组的\[\]符号时。举例如下：
```PHP
<?php
list($a[], $a[], $a[]) = [1, 2, 3];
var_dump($a);
?>
```
上述例子在PHP5中的输出为：
```PHP
array(3) {
  [0]=>
  int(3)
  [1]=>
  int(2)
  [2]=>
  int(1)
}
```
而在PHP7中的输出为：
```php
array(3) {
  [0]=>
  int(1)
  [1]=>
  int(2)
  [2]=>
  int(3)
}
```
在实际开发中，不建议使用依靠 [list\(\)](http://php.net/manual/en/function.list.php) 函数的参数来做排顺序这一操作，毕竟这样的hack用法在未来还是有可能调整。

##### [list\(\)](http://php.net/manual/en/function.list.php) 函数参数不再允许为空
[list\(\)](http://php.net/manual/en/function.list.php) 构造时不再允许参数为空的情况，下列情况将不再支持！
```PHP
<?php
list() = $a;
list(,,) = $a;
list($x, list(), $y) = $a;
?>
```

##### [list\(\)](http://php.net/manual/en/function.list.php) 函数不再支持拆解字符串
[list\(\)](http://php.net/manual/en/function.list.php) 不再允许拆解[字符串](http://php.net/manual/en/language.types.string.php)变量为字母，(str_split\(\))[http://php.net/manual/en/function.str-split.php]函数可以用于做此事。

#### 在数组中的元素通过引用方式创建时，数组顺序会被改变
数组中的元素在通过引用方式创建时，其数组顺序会被自动的改变。例如：
```PHP
<?php
$array = [];
$array["a"] =& $array["b"];
$array["b"] = 1;
var_dump($array);
?>
```
PHP5中的输出：
```PHP
array(2) {
  ["b"]=>
  &int(1)
  ["a"]=>
  &int(1)
}
```
PHP7中的输出
```PHP
array(2) {
  ["a"]=>
  &int(1)
  ["b"]=>
  &int(1)
}
```

#### [Global](http://php.net/manual/en/language.variables.scope.php#language.variables.scope.global) 仅支持简单的变量
[变量为值的变量](http://php.net/manual/en/language.variables.variable.php)的情况将不能再使用[Global](http://php.net/manual/en/language.variables.scope.php#language.variables.scope.global)标记。如果真的需要，可以用花括号来间隔开写，例如下面代码：
```PHP
<?php
function f() {
    // Valid in PHP 5 only.
    global $$foo->bar;

    // Valid in PHP 5 and 7.
    global ${$foo->bar};
}
?>
```
作为一个基本原则，这样的变量套变量的使用方式，在[Global](http://php.net/manual/en/language.variables.scope.php#language.variables.scope.global)这种场景下是不被提倡的。

#### 函数参数中的括号不再影响(行为?)
在PHP5中，参数若使被引用的并且使用括号，会没有报错发生。但在PHP7开始，这种场景都会印发一个报错。
```PHP
<?php
function getArray() {
    return [1, 2, 3];
}

function squareArray(array &$a) {
    foreach ($a as &$v) {
        $v **= 2;
    }
}

// Generates a warning in PHP 7.
squareArray((getArray()));
?>
```
上述示例代码将会产生如下输出：
```PHP
Notice: Only variables should be passed by reference in /tmp/test.php on line 13
```

### [foreach](http://php.net/manual/en/control-structures.foreach.php) 的改变
关于 [foreach](http://php.net/manual/en/control-structures.foreach.php) 的修改比较少，主要是修改了数组遍历时的数组指针，以及修改了数组的迭代。

#### [foreach](http://php.net/manual/en/control-structures.foreach.php) 遍历期间不再修改数组指针
在PHP7之前，当数组通过[foreach](http://php.net/manual/en/control-structures.foreach.php)迭代时，数组指针会移动。现在开始，不再如此，见下面代码：
```PHP
<?php
$array = [0, 1, 2];
foreach ($array as &$val) {
    var_dump(current($array));
}
?>
```
PHP5中的输出
```PHP
int(1)
int(2)
bool(false)
```
PHP7中的输出
```PHP
int(0)
int(0)
int(0)
```

#### [foreach](http://php.net/manual/en/control-structures.foreach.php) 通过值遍历时，操作的值为数组的副本
当默认使用通过值遍历数组时，[foreach](http://php.net/manual/en/control-structures.foreach.php)实际操作的是数组的迭代副本，而非数组本身。这就意味着，[foreach](http://php.net/manual/en/control-structures.foreach.php)中的操作不会修改原数组的值。

#### [foreach](http://php.net/manual/en/control-structures.foreach.php) 通过引用遍历时，有更好的迭代特性
当使用引用遍历数组时，现在[foreach](http://php.net/manual/en/control-structures.foreach.php)在迭代中更好的跟踪变化。例如，在迭代中添加一个迭代值到数组中，例如下面代码：
```PHP
<?php
$array = [0];
foreach ($array as &$val) {
    var_dump($val);
    $array[1] = 1;
}
?>
```
在PHP5的输出为：
```PHP
int(0)
```
在PHP7的输出为：
```PHP
int(0)
int(1)
```

#### [non-Traversable](http://php.net/manual/en/class.traversable.php) 对象的遍历
[non-Traversable](http://php.net/manual/en/class.traversable.php) 对象的遍历与通过引用遍历相似，具有相同的行为特性，[在遍历期间对原数据进行的操作将会被感知](http://php.net/manual/en/migration70.incompatible.php#migration70.incompatible.foreach.by-ref)。

### [整形](http://php.net/manual/en/language.types.integer.php)处理上的调整
