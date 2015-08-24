# PHP 5.6.x 版本迁移至 PHP 7.0.x 版本
## SAPI中的改动
### [FPM](http://php.net/manual/en/book.fpm.php)
* Fixed bug #65933 (Cannot specify config lines longer than 1024 bytes).
* Listen = port now listen on all addresses (IPv6 and IPv4-mapped).
