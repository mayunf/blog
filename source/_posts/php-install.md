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
