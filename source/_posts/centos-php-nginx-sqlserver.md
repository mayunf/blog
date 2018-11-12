---
title: Centos 下 安装 PHP + Nginx + Sql server.md
date: 2018-11-07 16:19:34
tags: 
- linux
- nginx
- php
- sqlserver
---

## 1.安装前必要配置

### 1.1 安装epel 仓库（因为CentOs6默认的yum源没有`libmcrypt-devel`这个包）

```
yum install -y epel-release
```

### 1.2 安装必要扩展

```
sudo yum install libxml2 libxml2-devel openssl openssl-devel bzip2 bzip2-devel libcurl libcurl-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel gmp gmp-devel libmcrypt libmcrypt-devel readline readline-devel libxslt libxslt-devel gcc libmcrypt-devel
```

## 2.安装PHP


### 2.1 下载PHP-7.1.15
```
wget -c http://cn2.php.net/get/php-7.1.15.tar.gz/from/this/mirror -O php-7.1.15.tar.gz
```
### 2.2 解压 `php-7.1.15.tar.gz`

```
tar -zxvf  php-7.1.15.tar.gz
```
### 2.3 进入 `php-7.1.15`

```
cd php-7.1.15
```

### 2.4 .编译与安装

```
./configure \
--prefix=/usr/local/php \
--with-config-file-path=/etc \
--enable-fpm \
--with-fpm-user=nginx  \
--with-fpm-group=nginx \
--enable-inline-optimization \
--disable-debug \
--disable-rpath \
--enable-shared  \
--enable-soap \
--with-libxml-dir \
--with-xmlrpc \
--with-openssl \
--with-mcrypt \
--with-mhash \
--with-pcre-regex \
--with-sqlite3 \
--with-zlib \
--enable-bcmath \
--with-iconv \
--with-bz2 \
--enable-calendar \
--with-curl \
--with-cdb \
--enable-dom \
--enable-exif \
--enable-fileinfo \
--enable-filter \
--with-pcre-dir \
--enable-ftp \
--with-gd \
--with-openssl-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib-dir  \
--with-freetype-dir \
--enable-gd-native-ttf \
--enable-gd-jis-conv \
--with-gettext \
--with-gmp \
--with-mhash \
--enable-json \
--enable-mbstring \
--enable-mbregex \
--enable-mbregex-backtrack \
--with-libmbfl \
--with-onig \
--enable-pdo \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-zlib-dir \
--with-pdo-sqlite \
--with-readline \
--enable-session \
--enable-shmop \
--enable-simplexml \
--enable-sockets  \
--enable-sysvmsg \
--enable-sysvsem \
--enable-sysvshm \
--enable-wddx \
--with-libxml-dir \
--with-xsl \
--enable-zip \
--enable-mysqlnd-compression-support \
--with-pear \
--enable-opcache

sudo make && make install
```
这里需要一点时间
### 2.4 .配置php-fpm
```
sudo cp php.ini-production /etc/php.ini
sudo cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
sudo cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf
sudo cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
sudo chmod +x /etc/init.d/php-fpm
```
### 2.5 .启动php-fpm
```bash
/etc/init.d/php-fpm start
```


## 3.安装Nginx

### 3.1 下载Nginx
```
wget http://nginx.org/download/nginx-1.13.9.tar.gz
```
### 3.2 安装Nginx
```
tar -zxvf nginx-1.13.9.tar.gz

cd nginx-1.13.9

./configure --prefix=/usr/local/nginx

```
### 3.2 启动Nginx
```
sudo /usr/local/nginx/sbin/nginx 

sudo /usr/local/nginx/sbin/nginx -s reload

sudo /usr/local/nginx/sbin/nginx -s stop
```
## 4.安装FreeTDS

### 4.1 下载FreeTDS

```
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-patched.tar.gz
```
### 4.2 安装FreeTDS

> 需要注意的就是这里的--with-tdsver=7.1，这个非常重要

```
tar -zxvf freetds-0.91.tar.gz

cd freetds-0.91

./configure --prefix=/usr/local/freetds --with-tdsver=7.1 --enable-msdblib

make && make install
```

### 4.3 验证FreeTDS
```
/usr/local/freetds/bin/tsql -C

/usr/local/freetds/bin/tsql -H 数据库服务器IP -p 1433 -U 用户名 -P 密码
```

### 4.4 添加PHP扩展`pdo_dblib`
```
cd ../php-7.1.15/ext/pdo_dblib

/usr/local/php/bin/phpize

./configure --with-php-config=/usr/local/php/bin/php-config --with-pdo-dblib=/usr/local/freetds/

make && make install
```
### 4.5 在`php.ini`中增加`pdo_dblib.so`

```extension ="pdo_dblib.so"```
