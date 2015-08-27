# PHP 5.6.x 版本迁移至 PHP 7.0.x 版本
## 7.0.x 版本开始停用的特性
### PHP4 风格的构造函数
在 PHP4 中类中的函数可以与类名同名，这一特性在 PHP7 中被废弃，同时会发出一个 E_DEPRECATED 错误。当方法名与类名相同，且类不在命名空间中，同时PHP5的构造函数（__construct）不存在时，会产生一个 E_DEPRECATED 错误。
```PHP
<?php
class foo {
    function foo() {
        echo 'I am the constructor';
    }
}
?>
```
上述代码输出：
```PHP
Deprecated: Methods with the same name as their class will not be constructors in a future version of PHP; foo has a deprecated constructor in example.php on line 3
```

### password_hash() 随机因子选项
函数原 salt 量不再需要由开发者提供了。函数内部默认带有 salt 能力，无需开发者提供 salt 值。

## 用户贡献记录
暂无
