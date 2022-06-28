---
title: shell-install-php-composer-nginx-git
date: 2022-06-28 09:39:09
tags:
- linux
- shell
- php
- nginx
- composer
---

```shell
#! /bin/bash
# 安装git
if ! type git >/dev/null 2>&1; then
  yum -y install git
  echo "git install Success." >>/var/log/install-php7.log
else
  echo "git skip install." >>/var/log/install-php7.log
fi

#基础包
yum install -y bash gcc gcc-c++ glibc make cmake libaio-devel gmp-devel libmcrypt-devel zlib zlib-devel openssh openssl openssl-devel pcre pcre-devel libjpeg libjpeg-devel libicu-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel xinetd lrzsz dos2unix postfix libtool openldap-devel sqlite-devel libcurl-devel
#该服务器没安装mysql的话
if ! type mysql >/dev/null 2>&1; then
  yum -y install mysql-devel
  echo "mysql-devel install Success." >>/var/log/install-php7.log
fi

#其它centos7必备
yum install net-tools vim wget -y

##编译安装 php7
php7="false"

cd /home/soft || mkdir /home/soft

#先编译安装php7.4的依赖oniguruma
#yum install -y autoconf automake libtool
#wget https://github.com/kkos/oniguruma/archive/v6.9.4.tar.gz -O oniguruma-6.9.4.tar.gz
#tar -zxvf oniguruma-6.9.4.tar.gz
#cd oniguruma-6.9.4/
#./autogen.sh && ./configure --prefix=/usr
#make && make install

# 安装libzip
tar -zxvf libzip-1.2.0.tar.gz || wget https://nih.at/libzip/libzip-1.2.0.tar.gz -O libzip-1.2.0.tar.gz --no-check-certificate && tar -zxvf libzip-1.2.0.tar.gz

cd libzip-1.2.0 || exit
./configure && make -j4 && make install
# 解决usr/local/include/zip.h:59:21: fatal error: zipconf.h: No such file or directory
cp /usr/local/lib/libzip/include/zipconf.h /usr/local/include/zipconf.h

# 安装php
cd /home/soft || exit
tar -xvf php-7.3.33.tar.gz || wget https://www.php.net/distributions/php-7.3.33.tar.gz && tar -xvf php-7.3.33.tar.gz

cd php-7.3.33/ && ./configure --prefix=/usr/local/php --sysconfdir=/usr/local/php/etc --with-config-file-path=/usr/local/php/etc --with-config-file-scan-dir=/usr/local/php/etc/php.d --with-freetype-dir=/usr/include/freetype2/freetype/ --enable-fpm --enable-mbstring --enable-zip --enable-mysqlnd --with-iconv --with-zlib --enable-xml --with-curl --with-gd --with-openssl --with-mhash --enable-bcmath --enable-sockets --enable-pcntl --with-xmlrpc --enable-zip --enable-soap --enable-opcache --with-pdo-mysql --enable-maintainer-zts --with-jpeg-dir=/usr && make clean && make && make install && php7="true"

if [ ${php7} != "true" ]; then
  echo "php7 install not Success." >>/var/log/install-php7.log
  exit 1
else
  echo "php7 install Success." >>/var/log/install-php7.log
fi

# 配置php
mkdir /usr/local/php/etc/php.d -p
cd /home/soft/php-7.3.33/sapi/fpm || exit
cp init.d.php-fpm /etc/init.d/php-fpm
chmod +x /etc/init.d/php-fpm
chkconfig --add php-fpm
chkconfig php-fpm on
cp php-fpm.conf /usr/local/php/etc/php-fpm.conf
mkdir /usr/local/php/etc/php-fpm.d -p
cp www.conf /usr/local/php/etc/php-fpm.d/www.conf
cd /home/soft/php-7.3.33 || exit
cp php.ini-production /usr/local/php/etc/php.ini
cp /usr/local/php/bin/php /usr/bin/php

cat >/usr/local/php/etc/php.d/10-opcache.ini <<EOFF
zend_extension=opcache.so

opcache.enable=1

opcache.memory_consumption=128

opcache.interned_strings_buffer=8

opcache.max_accelerated_files=4000

opcache.blacklist_filename=/etc/php.d/opcache*.blacklist
EOFF

##redis 扩展
redisextend="false"
cd /home/soft || exit
unzip develop.zip || wget  --no-check-certificate -c https://github.com/phpredis/phpredis/archive/develop.zip && unzip develop.zip
cd phpredis-develop/ && /usr/local/php/bin/phpize && ./configure --with-php-config=/usr/local/php/bin/php-config && make && make install && echo "extension=redis.so" >>/usr/local/php/etc/php.d/20.redis.ini && redisextend="true"

if [ ${redisextend} != "true" ]; then
  echo "redisextend install not Success." >>/var/log/install-php7.log
  exit 2
else
  echo "redisextend install Success." >>/var/log/install-php7.log
fi

##igbinary扩展
#igbinaryextend="false"
#cd /home/soft || exit
#wget --no-check-certificate -c https://github.com/igbinary/igbinary/archive/master.zip && unzip master.zip && cd igbinary-master && /usr/local/php/bin/phpize && ./configure CFLAGS="-O2 -g" --enable-igbinary --with-php-config=/usr/local/php/bin/php-config && make && make install && echo "extension=igbinary.so" >>/usr/local/php/etc/php.d/20.igbinary.ini && igbinaryextend="true"
#
#if [ ${igbinaryextend} != "true" ]; then
#  echo "igbinaryextend install not Success." >>/var/log/install-php7.log
#  exit 3
#else
#  echo "igbinaryextend install Success." >>/var/log/install-php7.log
#fi

# 安装composer
if ! type composer >/dev/null 2>&1; then
  curl -o /usr/bin/composer https://mirrors.aliyun.com/composer/composer.phar
  chmod +x /usr/bin/composer
  composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
  echo "composer install Success." >>/var/log/install-php7.log
fi

# 安装 nginx
yum -y install pcre-devel
tar -zxvf nginx-1.22.0.tar.gz || wget -O nginx-1.22.0.tar.gz  http://nginx.org/download/nginx-1.22.0.tar.gz && tar -zxvf nginx-1.22.0.tar.gz
cd nginx-1.22.0 && ./configure --prefix=/usr/local/nginx --with-http_ssl_module && make && make install
cp /usr/local/nginx/sbin/nginx /usr/bin/nginx

```