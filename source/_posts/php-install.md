---
title: php-install
date: 2020-11-18 09:49:49
tags:
---
# php 安装更新常用命令

## 获取php安装时的configure
``php -i | grep configure``

``php -i | grep configure | sed -e "s/Configure Command => //; s/'//g"``

php 7.3 不支持 ```--with-mcrypt```, ```--enable-gd-native-ttf```
