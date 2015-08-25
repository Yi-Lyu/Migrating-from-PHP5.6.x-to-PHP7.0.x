# PHP5.6.x版本迁移至7.0.x版本
## 新的特性
### 标量类型声明
标量类型声明有两种模式：强制（默认）模式、严格模式。下列参数类型可以使用（无论用强制模式还是严格模式）：字符串（string）、整形（int）、浮点数（float）和布尔型（bool）。其他类型在PHP5中有支持：类名、接口、数组和可被调用的。
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
Array类型的常量可以通过define()来定义。在PHP5.6中仅能通过const定义。
```PHP
<?php
define('ANIMALS', [
    'dog',
    'cat',
    'bird'
]);

echo ANIMALS[1]; // outputs "cat"
?>
```

### 匿名类
可以通过new关键字初始化一个匿名类。匿名类使用场景与完整的类场景相同。
```PHP
<?php
interface Logger {
    public function log(string $msg);
}

class Application {
    private $logger;

    public function getLogger(): Logger {
         return $this->logger;
    }

    public function setLogger(Logger $logger) {
         $this->logger = $logger;
    }
}

$app = new Application;
$app->setLogger(new class implements Logger {
    public function log(string $msg) {
        echo $msg;
    }
});

var_dump($app->getLogger());
?>
```
上面代码输出：
```PHP
object(class@anonymous)#2 (0) {
}
```
详细文档可以参考[匿名类文档](http://php.net/manual/en/language.oop5.anonymous.php)

### Unicode codepoint转译语法
通过十六进制内容与双引号组成的字符串生成Unicode codepoint，可以接受任何有效的codepoint，并且开头的0是可以省略的。
```PHP
echo "\u{aa}";
echo "\u{0000aa}";
echo "\u{9999}";
```
上面代码输出：
```PHP
(same as before but with optional leading 0's)
```

### [Closure::call()](http://php.net/manual/en/closure.call.php)
闭包[Closure::call()](http://php.net/manual/en/closure.call.php)有着更好的性能，简短干练的暂时绑定一个方法到对象上闭包并调用它。
```PHP
<?php
class A {private $x = 1;}

// Pre PHP 7 code
$getXCB = function() {return $this->x;};
$getX = $getXCB->bindTo(new A, 'A'); // intermediate closure
echo $getX();

// PHP 7+ code
$getX = function() {return $this->x;};
echo $getX->call(new A);
```
上述代码输出：
```PHP
1
1
```

### 为 unserialize() 提供过滤
这个特性意在提供更安全的方式解包不可靠的数据。通过白名单的方式来防止代码注入。
```PHP
<?php

// converts all objects into __PHP_Incomplete_Class object
$data = unserialize($foo, ["allowed_classes" => false]);

// converts all objects into __PHP_Incomplete_Class object except those of MyClass and MyClass2
$data = unserialize($foo, ["allowed_classes" => ["MyClass", "MyClass2"]);

// default behaviour (same as omitting the second argument) that accepts all classes
$data = unserialize($foo, ["allowed_classes" => true]);
```

### [IntlChar](http://php.net/manual/en/class.intlchar.php)
新增加的IntlChar类意在于暴露出更多的ICU功能。类自身定义了许多静态方法用于操作unicode字符。
```PHP
<?php

printf('%x', IntlChar::CODEPOINT_MAX);
echo IntlChar::charName('@');
var_dump(IntlChar::ispunct('!'));
```
上述代码输出：
```PHP
10ffff
COMMERCIAL AT
bool(true)
```
若要使用此类，请先安装Intl扩展。

### 断言
断言是向后兼用以增强assert()方法。在代码中启用断言为零成本，并且提供抛出特定异常的能力。<br>
在使用老版本API时，如果第一个参数是一个字符串,那么它将被解析。第二个参数可以是一个简单的字符串(导致AssertionError被触发)，或一个包含一个错误消息的自定义异常对象。
```PHP
<?php

ini_set('assert.exception', 1);

class CustomError extends AssertionError {}

assert(false, new CustomError('Some error message'));
```
上述代码输出：
```PHP
Fatal error: Uncaught CustomError: Some error message
```
这个特性会带来两个PHP。ini设置(以及它们的默认值):
* zend.assertions = 1
* assert.exception = 0
zend.assertions有三种值：
* 1 = 生成并且执行代码（开发模式）
* 0 = 执行代码并且在运行期间跳来跳去
* -1 = 不生成任何代码 (0开销, 生产模式)
assert.exception意味着断言失败时抛出异常。默认关闭保持兼容旧的assert()函数。

### 使用use集体声明
在PHP7之前需要声明一大堆命名空间，但是现在可以通过use的新特性，批量声明。
```PHP
<?php

// Pre PHP 7 code
use some\namespace\ClassA;
use some\namespace\ClassB;
use some\namespace\ClassC as C;

use function some\namespace\fn_a;
use function some\namespace\fn_b;
use function some\namespace\fn_c;

use const some\namespace\ConstA;
use const some\namespace\ConstB;
use const some\namespace\ConstC;

// PHP 7+ code
use some\namespace\{ClassA, ClassB, ClassC as C};
use function some\namespace\{fn_a, fn_b, fn_c};
use const some\namespace\{ConstA, ConstB, ConstC};
```

### Generator Return Expressions
This feature builds upon the generator functionality introduced into PHP 5.5. It enables for a return statement to be used within a generator to enable for a final expression to be returned (return by reference is not allowed). This value can be fetched using the new Generator::getReturn() method, which may only be used once the generator has finishing yielding values.
```PHP
<?php

$gen = (function() {
    yield 1;
    yield 2;

    return 3;
})();

foreach ($gen as $val) {
    echo $val, PHP_EOL;
}

echo $gen->getReturn(), PHP_EOL;
```
上述代码输出：
```PHP
1
2
3
```
Being able to explicitly return a final value from a generator is a handy ability to have. This is because it enables for a final value to be returned by a generator (from perhaps some form of coroutine computation) that can be specifically handled by the client code executing the generator. This is far simpler than forcing the client code to firstly check whether the final value has been yielded, and then if so, to handle that value specifically.

### Generator Delegation
Generator delegation builds upon the ability of being able to return expressions from generators. It does this by using an new syntax of yield from <expr>, where <expr> can be any Traversable object or array. This <expr> will be advanced until no longer valid, and then execution will continue in the calling generator. This feature enables yield statements to be broken down into smaller operations, thereby promoting cleaner code that has greater reusability.
```PHP
<?php

function gen()
{
    yield 1;
    yield 2;

    return yield from gen2();
}

function gen2()
{
    yield 3;

    return 4;
}

$gen = gen();

foreach ($gen as $val)
{
    echo $val, PHP_EOL;
}

echo $gen->getReturn();
```
上述代码输出：
```PHP
1
2
3
4
```

### 通过intdiv()做整除
intdiv()函数来处理整除，并返回一个整数。
```PHP
<?php

var_dump(intdiv(10, 3));
```
上述代码输出：
```PHP
int(3)
```

### session_start() 选项
该特性给session_start()函数提供一些设置能力，当然这些设置可以在PHP.ini中设置。
```PHP
<?php

session_start(['cache_limiter' => 'private']); // sets the session.cache_limiter option to private
```
这个特性还引入了一个新的php.ini设置(session.lazy_write)，默认情况下为true，表示改变会话数据只是重写。

### preg_replace_callback_array() 函数
这个新功能，当你使用preg_replace_callback()函数时代码更清晰可读。在PHP7之前，每个正则表达式都需要回调（ preg_replace_callback() 函数的第二个参数 ）中来实现功能，这会使流程混乱不可控。<br>
现在，回调可以通过与正则表达式绑定着写，只需将正则表达式作为key，回调函数作为value。
```PHP
<?php

$tokenStream = []; // [tokenName, lexeme] pairs

$input = <<<'end'
$a = 3; // variable initialisation
end;

// Pre PHP 7 code
preg_replace_callback(
    [
        '~\$[a-z_][a-z\d_]*~i',
        '~=~',
        '~[\d]+~',
        '~;~',
        '~//.*~'
    ],
    function ($match) use (&$tokenStream) {
        if (strpos($match[0], '$') === 0) {
            $tokenStream[] = ['T_VARIABLE', $match[0]];
        } elseif (strpos($match[0], '=') === 0) {
            $tokenStream[] = ['T_ASSIGN', $match[0]];
        } elseif (ctype_digit($match[0])) {
            $tokenStream[] = ['T_NUM', $match[0]];
        } elseif (strpos($match[0], ';') === 0) {
            $tokenStream[] = ['T_TERMINATE_STMT', $match[0]];
        } elseif (strpos($match[0], '//') === 0) {
            $tokenStream[] = ['T_COMMENT', $match[0]];
        }
    },
    $input
);

// PHP 7+ code
preg_replace_callback_array(
    [
        '~\$[a-z_][a-z\d_]*~i' => function ($match) use (&$tokenStream) {
            $tokenStream[] = ['T_VARIABLE', $match[0]];
        },
        '~=~' => function ($match) use (&$tokenStream) {
            $tokenStream[] = ['T_ASSIGN', $match[0]];
        },
        '~[\d]+~' => function ($match) use (&$tokenStream) {
            $tokenStream[] = ['T_NUM', $match[0]];
        },
        '~;~' => function ($match) use (&$tokenStream) {
            $tokenStream[] = ['T_TERMINATE_STMT', $match[0]];
        },
        '~//.*~' => function ($match) use (&$tokenStream) {
            $tokenStream[] = ['T_COMMENT', $match[0]];
        }
    ],
    $input
);
```

### [CSPRNG](http://php.net/manual/en/book.csprng.php) 系列函数
该特性涵盖两个函数，用于生成安全的整形与字符串，主要用于密码场景。它提供了简单的API和平台无关性。
```PHP
string random_bytes(int length);
int random_int(int min, int max);
```
两个函数在没有足够的随机性时会报E_WARNING错误并且返回false。

### list()可以打开对象实现[ArrayAccess](http://php.net/manual/en/class.arrayaccess.php)

## 用户贡献记录
暂无
