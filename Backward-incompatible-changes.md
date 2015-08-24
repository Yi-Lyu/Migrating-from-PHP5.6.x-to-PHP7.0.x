# PHP5.6.x版本迁移至7.0.x版本
## 先后兼容说明
### 更改错误和异常处理
Many fatal and recoverable fatal errors have been converted to exceptions in PHP 7. These error exceptions inherit from the Error class, which itself implements the Throwable interface, which is now the base interface all exceptions inherit.
A fuller description of how errors operate in PHP 7 can be found on the PHP 7 errors page. This migration guide will merely enumerate the changes that affect backward compatibility.
