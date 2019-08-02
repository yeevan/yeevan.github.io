# phar
要求5.2以上

## 前言
最近在看composer，是下载了一个composer.phar，然后放到/usr/local/bin目录下，就可以全局使用composer了，然而并不懂phar是什么，还以为是个PHP的扩展，要用phpize编译的，看了这篇文章后才懂[韩天峰-php中phar包的使用](http://rango.swoole.com/archives/168)

## 创建
php5.3之后支持了类似Java的jar包，名为phar。用来将多个PHP文件打包为一个文件.可以和tar zip相互转化

官网介绍：

> In addition to providing this service, the phar extension also provides a file-format abstraction method for creating and manipulating tar and zip files through the **PharData** class, much as PDO provides a unified interface for accessing different databases.

就是类似pdo的接口，也可以转化成zip tar包



首先要改php.ini里的readonly关闭，默认是不能写phar包的

```php
$phar = new Phar('swoole.phar');//参数是压缩包的名称
//指定压缩的目录，第2个参数通过正则指定压缩文件的扩展名
$phar->buildFromDirectory(__DIR__.'/../','/\.php$/');
//使用gzip来压缩此文件
$phar->compressFiles(Phar::GZ);

$phar->stopBuffering();
//用来设置启动加载的文件。默认会自动加载并执行lib_config.php文件
$phar->setStub($phar->createDefaultStub('lib_config.php'));
```

## 使用
```php
include 'swoole.phar';
include 'swoole.phar/code/page.php';
```

### 转化成zip
```php
$phar = new Phar('myphar.phar');
$pgz = $phar->convertToExecutable(Phar::TAR, Phar::GZ); // makes myphar.phar.tar.gz
``` 


### 疑问：
回到最初的问题，既然是转成了一个文件，那放在bin目录下，为什么就可以全局使用了呢？

参考这篇文章[戳](http://www.webhek.com/post/packaging-your-php-apps-with-phar.html)


如下操作
实例操作：

```php
$srcRoot = __Dir__.'/src';
$buildRoot = __Dir__.'/build';

/*
$phar = new Phar($buildRoot.'/myapp.phar', FilesystemIterator::CURRENT_AS_FILEINFO| FilesystemIterator::KEY_AS_FILENAME, "myapp.phar");
$phar['index.php'] = file_get_contents($srcRoot.'/index.php');
$phar['common.php'] = file_get_contents($srcRoot.'/common.php');
$phar->setStub($phar->createDefaultStub('index.php'));

copy($srcRoot.'/config.ini', $buildRoot.'/config.ini');
*/


$phar = new Phar('myappdir.phar');//参数是压缩包的名称
//指定压缩的目录，第2个参数通过正则指定压缩文件的扩展名
$phar->buildFromDirectory($srcRoot,'/\.php$/');
//使用gzip来压缩此文件
$phar->compressFiles(Phar::GZ);
// Stop buffering write requests to the Phar archive, and save changes to disk
$phar->stopBuffering();
//用来设置启动加载的文件。默认会自动加载并执行lib_config.php文件
$phar->setStub($phar->createDefaultStub('index.php'));
```
会生成如下：

![image](https://raw.githubusercontent.com/yeevan/phar/master/WX20190111-111644.png)





