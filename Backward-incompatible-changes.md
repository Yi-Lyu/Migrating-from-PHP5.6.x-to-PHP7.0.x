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




