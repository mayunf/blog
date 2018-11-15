---
title: Laravel执行seeder命令出现class *** does not exist
date: 2018-11-15 11:20:43
tags:
- laravel
- seeder
- php
---
今天执行完  `php artisan db:seed` 发现出现以下错误：

```bash
Seeding: UserSeeder
Seeding: InitDataSeeder

In Container.php line 752:

  Class InitDataSeeder does not exist

```

查阅文档[Laravel 数据库之：数据填充](https://laravel-china.org/docs/laravel/5.5/seeding/1330#24bd5e)

发现，完成 seeder 类的编写之后，你可能需要使用 dump-autoload 命令重新生成 Composer 的自动加载器：

```bash
composer dump-autoload
```

接着就可以使用 Artisan 命令 `db:seed` 来填充数据库了。
默认情况下，`db:seed` 命令将运行 DatabaseSeeder 类，这个类可以用来调用其它 Seed 类。
不过，你也可以使用 --class 选项来指定一个特定的 seeder 类：

```bash
php artisan db:seed

php artisan db:seed --class=UsersTableSeeder
```

你也可以使用 `migrate:refresh` 命令来填充数据库，该命令会回滚并重新运行所有迁移。这个命令可以用来重建数据库：

```bash
php artisan migrate:refresh --seed
```
