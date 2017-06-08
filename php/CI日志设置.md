## CI日志设置

本文主要介绍CI中`log_message()`方法的使用及其相关的配置。

```php
log_message($level,$message)
参数：
	·$level: 信息级别，有"error","debug","info"三个级别
	·$message: 信息
```

*application/config/config.php*中与log_message方法相关的配置

```php
//日志级别，0-关闭，1-显示error，2-显示debug，3-显示info，4-显示所有
$config['log_threshold'] = 2;

//设置生成日志的目录，一定要记得修改该目录的权限，否则日志无法写入
$config['log_path'] = '/var/log/open/';

//日志文件的后缀
$config['log_file_extension'] = 'log';

//生成的日志文件的权限
$config['log_file_permissions'] = 0644;
```



