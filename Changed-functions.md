# PHP 5.6.x 版本迁移至 PHP 7.0.x 版本
## 函数变更
### PHP 核心函数
* [debug_zval_dump()](http://php.net/manual/en/function.debug-zval-dump.php) 输出int类型代替long类型，float类型代替double类型。
* [dirname()](http://php.net/manual/en/function.dirname.php) 可选第二个参数，depth，基于当前目录获取目录名称深度。 
* [getrusage()](http://php.net/manual/en/function.getrusage.php) 不再在windows系统上支持。
* [mktime()](http://php.net/manual/en/function.mktime.php) and [gmmktime()](http://php.net/manual/en/function.gmmktime.php) 不再接受 is_dst 参数。
* [preg_replace()](http://php.net/manual/en/function.preg-replace.php) 不再支持 \e (PREG_REPLACE_EVAL). preg_replace_callback() 将会被代替。
* [setlocale()](http://php.net/manual/en/function.setlocale.php) 第一个参数不再接受字符串。必须用LC_*系列常亮代替。
* exec()、system()、passthru() 函数现在具备空字节保护。

## 用户贡献记录
暂无
