# PHP5.6.x版本迁移至7.0.x版本
## 向后兼容说明
### 错误和异常处理的变更
许多可以被修正的Fatal错误，在PHP7中将以Exceptions异常的形式抛出。这些Error Exceptions继承于 [Error](http://php.net/manual/en/class.error.php) 类。而 [Error](http://php.net/manual/en/class.error.php) 类则实现了异常基类 [Throwable](http://php.net/manual/en/class.throwable.php) 接口。 <br>
PHP7中详细的Error信息可以参考\[ [PHP7错误](http://php.net/manual/en/language.errors.php7.php) \]。本文中仅仅介绍和向后兼容有关的信息如下。

#### 类构造函数在失败时抛出异常
之前，类构造函数在失败时总是返回NULL或者返回一个不可用的Object，但从PHP7开始，在构造函数初始化失败时会抛出[异常](http://php.net/manual/en/class.exception.php)。

#### 解析错误时会抛出 [解析异常](http://php.net/manual/en/class.parseerror.php)
现在，解析[eval\(\)]错误会抛出一个 [解析异常](http://php.net/manual/en/class.parseerror.php) 对象。其可以通过 [catch](http://php.net/manual/en/language.exceptions.php#language.exceptions.catch) 捕捉，并做相应处理。

#### E_STRICT 等级的报错被重新分配
所有**E_STRICT**级别的报错已重新分配到其他报错等级中。**E_STRICT**常量依然保留，所以当你设置报错等级为 **error_reporting\(E_ALL|E_STRICT\)**时，不会引起报错。<br>
变更情况如下表
![image](https://cloud.githubusercontent.com/assets/1308846/9434941/01402560-4a76-11e5-9943-f9f153745030.png)

### 变量处理环节的变更
由于PHP7采用抽象的语法树解析代码文件，并且过去的PHP版本无法满足该特性，这一变化将引起一些一致性问题。本节详细介绍这块的情况。

#### 对于间接变量、属性、方法的变动
间接的使用变量、属性、方法，将严格按照从左到右的顺序执行，而不会因形式问题导致歧义。下表将表明的这一改变引起的差异。
![image](https://cloud.githubusercontent.com/assets/1308846/9435404/1bf947a2-4a7a-11e5-97bb-96677cc560fb.png)
以上使用老得从右到左的方式的代码，将被重写。通过花括号来明确顺序（见上图中间列），使代码向前兼容PHP7.x，并向后兼容PHP5.x。

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
在实际开发中，不建议使用依靠 [list\(\)](http://php.net/manual/en/function.list.php) 函数的参数来做排顺序这一操作，毕竟这样的hack用法在未来依然有可能被调整。

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
[list\(\)](http://php.net/manual/en/function.list.php) 不再允许拆解[字符串](http://php.net/manual/en/language.types.string.php)变量为字母，[str_split](http://php.net/manual/en/function.str-split.php)函数可以用于做此事。

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

#### [global](http://php.net/manual/en/language.variables.scope.php#language.variables.scope.global) 仅支持简单的变量
[可变变量](http://php.net/manual/en/language.variables.variable.php)将不能再使用[global](http://php.net/manual/en/language.variables.scope.php#language.variables.scope.global)标记。如果真的需要，可以用花括号来间隔开写，例如下面代码：
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
作为一个基本原则，这样的变量套变量的使用方式，在[global](http://php.net/manual/en/language.variables.scope.php#language.variables.scope.global)这种场景下不被提倡。

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
#### 无效的八进制常量
此前，八进制中包含无效数据会自动被截断（0128被当做为012）。现在，一个无效的八进制字面会造成分析错误。

#### 负位置
位移的负数将抛出一个 [ArithmeticError](http://php.net/manual/en/class.arithmeticerror.php)
```PHP
<?php
var_dump(1 >> -1);
?>
```
PHP5中的输出：
```PHP
int(0)
```
PHP7中的输出：
```PHP
Fatal error: Uncaught ArithmeticError: Bit shift by negative number in /tmp/test.php:2
Stack trace:
#0 {main}
  thrown in /tmp/test.php on line 2
```
#### 超出范围的位移
位移（任一方向）超出一个整数的位宽度会得到0。以前，这种转变的行为是依赖于运行环境的机器架构结构。

### [字符串](http://php.net/manual/en/language.types.string.php)处理上的调整
#### 十六进制字符串不再被认为是数字
含十六进制字符串不再被认为是数字。例如：
```PHP
<?php
var_dump("0x123" == "291");
var_dump(is_numeric("0x123"));
var_dump("0xe" + "0x1");
var_dump(substr("foo", "0x1"));
?>
```
在PHP5中得输出：
```PHP
bool(true)
bool(true)
int(15)
string(2) "oo"
```
在PHP7中得输出：
```PHP
bool(false)
bool(false)
int(0)

Notice: A non well formed numeric value encountered in /tmp/test.php on line 5
string(3) "foo"
```
[filter_var\(\)](http://php.net/manual/en/function.filter-var.php) 函数可以用于检查一个字符串中是否包含十六进制数，同时也可以转换一个字符串为十六进制数。
```PHP
<?php
$str = "0xffff";
$int = filter_var($str, FILTER_VALIDATE_INT, FILTER_FLAG_ALLOW_HEX);
if (false === $int) {
    throw new Exception("Invalid integer!");
}
var_dump($int); // int(65535)
?>
```

#### \u{ 可能触发错误
由于新的[Unicode转译语法](http://php.net/manual/en/migration70.new-features.php#migration70.new-features.unicode-codepoint-escape-syntax)，字符串中含有 **\\u{ ** 时会触发Fatal错误。为了避免这一报错，应该避免反斜杠开头。

### 被移除的函数
#### [call_user_method\(\)](http://php.net/manual/en/function.call-user-method.php) 与 [call_user_method_array\(\)](http://php.net/manual/en/function.call-user-method-array.php)
这些函数被在PHP4.1.0开始被标记为过时的，在PHP7开始被删除。建议使用 [call_user_func\(\)](http://php.net/manual/en/function.call-user-func.php) 和 [call_user_func_array\(\)](http://php.net/manual/en/function.call-user-func-array.php) 。你可以考虑下 [变量函数](http://php.net/manual/en/functions.variable-functions.php)或者参考其他函数。

#### [mcrypt](http://php.net/manual/en/book.mcrypt.php) 相关
mcrypt_generic_end\(\) 被删除，建议使用 mcrypt_generic_deinit\(\) 。
此外，废弃的mcrypt_ecb\(\)，mcrypt_cbc\(\)，mcrypt_cfb\(\)和mcrypt_ofb\(\)功能，建议使用目前还支持的mcrypt_decrypt()与适当的MCRYPT_MODE_\*常量。

#### [intl](http://php.net/manual/en/book.intl.php) 相关
datefmt_set_timezone_id\(\)与IntlDateFormatter::setTimeZoneID\(\)被删除，建议使用datefmt_set_timezone\(\)与IntlDateFormatter::setTimeZone\(\)。

#### [set_magic_quotes_runtime\(\)](http://php.net/manual/en/function.set-magic-quotes-runtime.php)
set_magic_quotes_runtime\(\)与它的别名函数magic_quotes_runtime\(\)被删除。他们在PHP5.3.0中就被标记被过时的。

#### [set_socket_blocking\(\)](http://php.net/manual/en/function.set-socket-blocking.php)
set_socket_blocking\(\)已被移除，建议使用stream_set_blocking\(\)。

#### [dl\(\)](http://php.net/manual/en/function.dl.php) 在PHP-FPM中
dl\(\)函数不能在PHP-FPM中使用了，它的功能做在了CLI、嵌入到SAPIs中了。

#### [GD](http://php.net/manual/en/book.image.php) 类型的函数
PostScript Type1字体的支持已经从GD扩展删除，涉及的函数有：
* imagepsbbox()
* imagepsencodefont()
* imagepsextendfont()
* imagepsfreefont()
* imagepsloadfont()
* imagepsslantfont()
* imagepstext()
建议使用TrueType字体和其相关的功能代替。

### 删除INI配置
#### 删除的功能
下面的INI指令以及相关的功能被删除：
* [always_populate_raw_post_data](http://php.net/manual/en/ini.core.php#ini.always-populate-raw-post-data)
* [asp_tags](http://php.net/manual/en/ini.core.php#ini.asp-tags)

#### xsl.security_prefs
xsl.security_prefs指令已被删除。相反，该[xsltprocessor::setsecurityprefs\(\)](http://php.net/manual/en/xsltprocessor.setsecurityprefs.php)方法用于控制在每个处理器上的安全选项。

### 其他不向后兼容的变更
#### [New](http://php.net/manual/en/language.oop5.basic.php#language.oop5.basic.new) 对象不能被引用分配
[New](http://php.net/manual/en/language.oop5.basic.php#language.oop5.basic.new)语句的结果不再能通过引用赋值给一个变量，如下代码：
```PHP
<?php
class C {}
$c =& new C;
?>
```
PHP5中的输出：
```PHP
Deprecated: Assigning the return value of new by reference is deprecated in /tmp/test.php on line 3
```
PHP7中的输出：
```PHP
Parse error: syntax error, unexpected 'new' (T_NEW) in /tmp/test.php on line 3
```

#### 无效的类、接口和特性的名字
下面的名称不能被用来类、接口、特性的名称：
* bool
* int
* float
* string
* NULL
* TRUE
* FALSE
此外，不推荐下列名称，他们已被标记过时
* resource
* object
* mixed
* numeric

#### ASP语法标记、Script PHP语法标记被移除
使用ASP脚本标签，或者Script的PHP代码，已被删除。受影响的标签是：
![image](https://cloud.githubusercontent.com/assets/1308846/9438212/bdeec078-4a8e-11e5-91b5-5e6b92e4019d.png)

#### 禁止调用不确定的情况
[之前PHP5.6的过时说明中](http://php.net/manual/en/migration56.deprecated.php#migration56.deprecated.incompatible-context)，静态调用一个非静态方法，会在静态调用中被提示未定义 $this ，并会报错。
```PHP
<?php
class A {
    public function test() { var_dump($this); }
}

// Note: Does NOT extend A
class B {
    public function callNonStaticMethodOfA() { A::test(); }
}

(new B)->callNonStaticMethodOfA();
?>
```
在PHP5中会输出：
```PHP
Deprecated: Non-static method A::test() should not be called statically, assuming $this from incompatible context in /tmp/test.php on line 8
object(B)#1 (0) {
}
```
在PHP7中会输出：
```PHP
Deprecated: Non-static method A::test() should not be called statically in /tmp/test.php on line 8

Notice: Undefined variable: this in /tmp/test.php on line 3
NULL
```
#### [yield]() 现在开始作为(右)关联运算符
yield 不再需要括号，可以作为一个（右）关联运算符，优先于 **print** 与 ** => **，这将产生下列行为：

```PHP
<?php
echo yield -1;
// Was previously interpreted as
echo (yield) - 1;
// And is now interpreted as
echo yield (-1);

yield $foo or die;
// Was previously interpreted as
yield ($foo or die);
// And is now interpreted as
(yield $foo) or die;
?>
```
括号可以用来消除歧义的情况。

#### 函数不能有多个相同的名称的参数
不允许函数在参数中出现相同名称的参数。例如下列代码，将会产生 **E_COMPILE_ERROR** 的报错。
```PHP
<?php
function foo($a, $b, $unused, $unused) {
    //
}
?>
```

#### [$HTTP_RAW_POST_DATA](http://php.net/manual/en/reserved.variables.httprawpostdata.php) 被移除
$HTTP_RAW_POST_DATA 不再被支持。 可以使用 php://input 流数据来代替实现。

#### \# 注释已被移除
INI文件中以\#符号作为注释的内容已被移除，**;**符号将代替**\#**，这个改变同样适用于PHP.ini文件，以及parse_ini_file()和parse_ini_string()处理文件期间。

## 用户贡献说明
暂无
