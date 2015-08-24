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

