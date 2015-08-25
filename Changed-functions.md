# PHP 5.6.x 版本迁移至 PHP 7.0.x 版本
## 函数变更
### PHP 核心函数
* [debug_zval_dump()](http://php.net/manual/en/function.debug-zval-dump.php) 现在输出int和float类型，对应替代之前的long和double类型
* [dirname()](http://php.net/manual/en/function.dirname.php) 现有第二个参数(depth)可选，基于当前目录获取目录名称深度。
* [getrusage()](http://php.net/manual/en/function.getrusage.php) 不再支持windows系统
* [mktime()](http://php.net/manual/en/function.mktime.php) 及 [gmmktime()](http://php.net/manual/en/function.gmmktime.php) 不再接受 is_dst 参数。
* [preg_replace()](http://php.net/manual/en/function.preg-replace.php) 不再支持` \e (PREG_REPLACE_EVAL)`. 以 `preg_replace_callback()` 作为替代。
* [setlocale()](http://php.net/manual/en/function.setlocale.php) 第一个参数不再接受字符串。必须用 `LC_*` 系列常亮代替。
* exec()、system()、passthru() 函数现在具备空字节保护。

## 用户贡献记录
暂无
