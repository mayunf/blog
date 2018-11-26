---
title: learn-ubuntu
date: 2018-11-20 20:02:14
tags:
---

> 本文主要记录一下在使用ubuntu系统时遇到的一些问题，以及解决方法。用作备份

## root用户默认的初始密码

ubuntu安装好后，root初始密码（默认密码）不知道，需要设置。

1. 先用安装时候的用户登录进入系统

2. 输入：`sudo passwd`  按回车

3. 输入新密码，重复输入密码，最后提示passwd：password updated sucessfully
    此时已完成root密码的设置

4. 输入：`su root`

切换用户到root试试.......

参考文档：[https://www.cnblogs.com/yuejin/p/3645294.html](https://www.cnblogs.com/yuejin/p/3645294.html)

## “Unable to locate package” while trying to install packages with APT

```bash
sudo apt-get install <package>
Reading package lists... Done
Building dependency tree       
Reading state information... Done
E: Unable to locate package <package>
```

解决办法：

1. 激活仓库
```bash
sudo add-apt-repository main
sudo add-apt-repository universe
sudo add-apt-repository restricted
sudo add-apt-repository multiverse
```
2. 更新

```bash
sudo apt-get update
```

3. 安装

```bash
sudo apt-get install <package>
```

[参考文档](https://askubuntu.com/questions/378558/unable-to-locate-package-while-trying-to-install-packages-with-apt/481355#481355?newreg=5620a7bb97d742dbb22cbcd1837a0734)


