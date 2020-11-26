---
title: php-install
date: 2020-11-18 09:49:49
tags:
---
# php 安装更新常用命令

## 获取php安装时的configure
``php -i | grep configure``

``php -i | grep configure | sed -e "s/Configure Command => //; s/'//g"``

## 查看php扩展安装目录
``/usr/local/php/bin/php-config --extension-dir``

## centos7 安装php7.3.3   

### 去掉不支持的扩展
```--with-mcrypt```, ```--enable-gd-native-ttf```

### configure: error: off_t undefined; check your library configuration
```shell script
# 添加搜索路径到配置文件
vim /etc/ld.so.conf 
#添加如下几行
/usr/local/lib64
/usr/local/lib
/usr/lib
/usr/lib64 
#保存退出
:wq 
# 使之生效
ldconfig -v
```

### /usr/local/include/zip.h:59:21: fatal error: zipconf.h: No such file or directory 
```shell script
cp /usr/local/lib/libzip/include/zipconf.h /usr/local/include/zipconf.h
```
### 安装 lib
```shell script
./configure && make && make install
```

### php7.3.24 安装配置
```shell script
 ./configure  --prefix=/usr/local/php --with-config-file-path=/etc --enable-fpm --with-fpm-user=nginx --with-fpm-group=nginx --enable-inline-optimization --disable-debug --disable-rpath --enable-shared --enable-soap --with-xmlrpc --with-openssl --with-pcre-regex --with-sqlite3 --with-zlib --enable-bcmath --with-iconv --with-bz2 --enable-calendar --with-curl --with-cdb --enable-dom --enable-exif --enable-fileinfo --enable-filter --with-pcre-dir --enable-ftp --with-gd --with-openssl-dir --with-jpeg-dir --with-png-dir --with-freetype-dir --enable-gd-jis-conv --with-gettext --with-gmp --with-mhash --enable-json --enable-mbstring --enable-mbregex --enable-mbregex-backtrack --with-libmbfl --with-onig --enable-pdo --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --with-zlib-dir --with-pdo-sqlite --with-readline --enable-session --enable-shmop --enable-simplexml --enable-sockets --enable-sysvmsg --enable-sysvsem --enable-sysvshm --enable-wddx --with-libxml-dir --with-xsl --enable-zip --enable-mysqlnd-compression-support --with-pear  --enable-opcache
```

```shell script

cp /usr/local/php70/etc/php.ini /usr/local/php/etc/php.ini
cp /usr/local/php70/etc/php-fpm.conf /usr/local/php/etc/php-fpm.conf
cp /usr/local/php70/etc/php-fpm.d/www.conf /usr/local/php/etc/php-fpm.d/www.conf

```


###  PHP Startup: Unable to load dynamic library 'php_pdo_sqlsrv_73_nts.so'

#### 加入微软的源
```shell script
curl https://packages.microsoft.com/config/rhel/7/prod.repo > /etc/yum.repos.d/mssqlrelease.repo
```

#### 安装驱动（三个都要装上，缺一不可）
```shell script
yum install msodbcsql mssql-tools unixODBC-devel
```

#### 下载扩展
```shell script
https://github.com/microsoft/msphpsql
```

#### 选择对应的php版本进行下载，然后解压

#### 然后把文件移动到扩展文件下面
```shell script
/usr/local/php/bin/php-config --extension-dir
```

#### 配置文件修改
```shell script
vi /etc/php.ini

extension=php_sqlsrv_73_nts.so
extension=php_pdo_sqlsrv_73_nts.so

:wq!
```

#### 重启php-fpm
```shell script
/etc/init.d/php-fpm restart
```


[https://blog.csdn.net/lizarel/article/details/108066652](原文)
