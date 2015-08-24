# PHP 5.6.x 版本迁移至 PHP 7.0.x 版本
## 写在前面：
**版权声明**：原文来自[PHP.net](http://php.net/manual/en/migration70.php)，此处仅作翻译。<br/>
**关于修正**：本人翻译能力有限，若有对原文理解不对或翻译不对的地方，欢迎push。
## 目录
* [向后兼容性说明](./Backward-incompatible-changes.md)
* [新的特性](./New-features.md)
* [SAPI中的改动](./Sapi-changes.md)
* [7.0.x版本开始停用的特性](./Deprecated.md)
* [函数变更](./Changed-functions.md)
* [新的函数](http://php.net/manual/en/migration70.new-functions.php)
* [新的类与接口](http://php.net/manual/en/migration70.classes.php)
* [新的全局常量](http://php.net/manual/en/migration70.constants.php)
* [被移除的扩展与SAPIs](http://php.net/manual/en/migration70.removed-exts-sapis.php)
* [其他修改](http://php.net/manual/en/migration70.other-changes.php)

## PHP7 依然处于开发中
```PHP
[警告] 
    PHP7目前仍处于开发中并且短时间内还不能完成，本文中的一些新
    特性、修改还需等7.0版本正式发布后才能敲定（还有可能修改）。
    请你在将PHP7部署到线上生成环境之前再来确认下最新的变更是否
    对你的业务有影响。
```
PHP7是一个全新的大版本，我们在努力让大家做到无缝的升级。这次版本变更专注在移除旧特性以保证语言的一致性。<br>
这里有一些已经被确定的[不兼容](./Backward-incompatible-changes.md)和[新特性](./New-features.md)，请大家切换生产环境的PHP版本到PHP7之前，这些环节需要特别关注测试。<br>
这里提供一些其他PHP版本的升级指南：[5.0.x](http://php.net/manual/en/migration5.php) [5.1.x](http://php.net/manual/en/migration51.php) [5.2.x](http://php.net/manual/en/migration52.php) [5.3.x](http://php.net/manual/en/migration53.php) [5.4.x](http://php.net/manual/en/migration54.php) [5.5.x](http://php.net/manual/en/migration55.php) 和 [5.6.x](http://php.net/manual/en/migration56.php)

## 用户贡献说明 
暂无

