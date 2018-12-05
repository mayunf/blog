---
title: php-fpm优化方法 pm.min_spare_servers、pm.max_spare_servers 的真实意义
date: 2018-12-05 13:08:48
tags:
---

# php-fpm 进程池优化方法

php-fpm进程池开启进程有两种方式，一种是static，直接开启指定数量的php-fpm进程，不再增加或者减少；
另一种则是dynamic，开始时开启一定数量的php-fpm进程，当请求量变大时，动态的增加php-fpm进程数到上限，当空闲时自动释放空闲的进程数到一个下限。
这两种不同的执行方式，可以根据服务器的实际需求来进行调整。

要用到的一些参数，分别是pm、pm.max_children、pm.start_servers、pm.min_spare_servers和pm.max_spare_servers。

pm表示使用那种方式，有两个值可以选择，就是static（静态）或者dynamic（动态）。

下面4个参数的意思分别为：

`pm.max_children`：静态方式下开启的php-fpm进程数量，在动态方式下他限定php-fpm的最大进程数（这里要注意pm.max_spare_servers的值只能小于等于pm.max_children）

`pm.start_servers`：动态方式下的起始php-fpm进程数量。

`pm.min_spare_servers`：动态方式空闲状态下的最小php-fpm进程数量。

`pm.max_spare_servers`：动态方式空闲状态下的最大php-fpm进程数量。

如果dm设置为static，那么其实只有pm.max_children这个参数生效。系统会开启参数设置数量的php-fpm进程。

如果dm设置为dynamic，4个参数都生效。系统会在php-fpm运行开始时启动pm.start_servers个php-fpm进程，然后根据系统的需求动态在pm.min_spare_servers和pm.max_spare_servers之间调整php-fpm进程数。


P.S
pm.min_spare_servers、pm.max_spare_servers这2个参数一开始我以为是指空闲进程，但是后来服务器给我报了一个错误：
pm.start_servers(70) must not be less than pm.min_spare_servers(15) and not greater than pm.max_spare_servers(60)
要求pm.start_servers的值在pm.min_spare_servers和pm.max_spare_servers之间，经过测试，得出上述结论。

参考文档：

[https://blog.csdn.net/a5816138/article/details/53670597](https://blog.csdn.net/a5816138/article/details/53670597)
