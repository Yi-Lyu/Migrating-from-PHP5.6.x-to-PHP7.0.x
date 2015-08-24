# PHP5.6.x版本迁移至7.0.x版本
## 新的特性
### 标量类型声明
标量类型声明有两种模式：强制（默认）模式、严格模式。下列类型的参数可以被运用（无论用强制模式还是严格模式）：字符串（string）、整形（int）、浮点数（float）和布尔型（bool）。其他类型在PHP5中有支持：类名、接口、数组和可被调用的。
```PHP
<?php
// Coercive mode
function sumOfInts(int ...$ints)
{
    return array_sum($ints);
}

var_dump(sumOfInts(2, '3', 4.1));
```
上述例子输出：
```PHP
int(9)
```
当开启严格模式后，一个 [declare](http://php.net/manual/en/control-structures.declare.php) 声明必须置于PHP脚本文件开头，这意味着严格声明标量是基于文件可配的。这个指令不仅影响参数的类型声明，也影响到函数的返回值声明（详见下面的返回值声明）。<br>
详细的标量类型声明的文档与示例，可以查看[类型声明](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration)页面。

### 返回类型声明
PHP7新增了返回类型声明，类似于参数类型声明，返回类型声明提前声明了函数返回值的类型。可用的声明类型与参数声明中可用的类型相同。
```PHP
<?php

function arraysSum(array ...$arrays): array
{
    return array_map(function(array $array): int {
        return array_sum($array);
    }, $arrays);
}

print_r(arraysSum([1,2,3], [4,5,6], [7,8,9]));
```
上述代码返回值为:
```PHP
Array
(
    [0] => 6
    [1] => 15
    [2] => 24
)
```
详细的返回值声明相关的文档和示例代码可以查阅[返回值声明](http://php.net/manual/en/functions.returning-values.php#functions.returning-values.type-declaration)文档。

### NULL合并算子（操作符“??”）
空合并算子的操作符为??，已经作为一种语法糖用于日常需求中用于三元表达式，它与isset()同时发生。如果变量存在且不为空，它就会返回对应的值，相反，它返回它的第二个操作数。
```PHP
<?php
// Fetches the value of $_GET['user'] and returns 'nobody'
// if it does not exist.
$username = $_GET['user'] ?? 'nobody';
// This is equivalent to:
$username = isset($_GET['user']) ? $_GET['user'] : 'nobody';

// Coalesces can be chained: this will return the first
// defined value out of $_GET['user'], $_POST['user'], and
// 'nobody'.
$username = $_GET['user'] ?? $_POST['user'] ?? 'nobody';
?>
```
### 宇宙飞船操作符（[RFC](https://wiki.php.net/rfc/combined-comparison-operator)）
宇宙飞船操作符用于比较两个表达式。它返回一个大于0、等于0、小于0的数，用于表示$a与$b之间的关系。比较的原则是沿用PHP的[常规比较规则](http://php.net/manual/en/types.comparisons.php)进行的。
```PHP
<?php
// Integers
echo 1 <=> 1; // 0
echo 1 <=> 2; // -1
echo 2 <=> 1; // 1

// Floats
echo 1.5 <=> 1.5; // 0
echo 1.5 <=> 2.5; // -1
echo 2.5 <=> 1.5; // 1
 
// Strings
echo "a" <=> "a"; // 0
echo "a" <=> "b"; // -1
echo "b" <=> "a"; // 1
?>
```
### 通过define()定义常量数组
Array类型的常量可以通过define()来定义了。在PHP5.6中仅能通过const定义。
```PHP

