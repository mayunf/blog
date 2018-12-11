---
title: Specified key was too long; max key length is 1000 bytes
date: 2018-12-03 17:25:55
tags:
- git
---

## 错误原因

Laravel 5.4 以后 默认使用 `utf8mb4` 字符编码，而不是之前的utf8编码。因此运行 `php artisan migrate` 会出现如下错误：

```bash
$ php artisan migrate
Migration table created successfully.

In Connection.php line 664:

  SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was t
  oo long; max key length is 1000 bytes (SQL: alter table `m_users` add uniqu
  e `users_email_unique`(`email`))


In Connection.php line 458:

  SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was t
  oo long; max key length is 1000 bytes

```

MySql支持的utf8编码最大字符长度为3字节，如果遇到4字节的宽字符就会出现插入异常。三个字节UTF-8最大能编码的Unicode字符是0xffff，即Unicode中的基本多文种平面（BMP）。因而包括Emoji表情（Emoji是一种特殊的Unicode编码）在内的非基本多文种平面的Unicode字符都无法使用MySql的utf8字符集存储。

这也应该就是Laravel 5.4改用4字节长度的utf8mb4字符编码的原因之一。不过要注意的是，只有MySql 5.5.3版本以后才开始支持utf8mb4字符编码（查看版本：selection version();）。

## 解决办法

1. 升级MySql版本到5.5.3以上。

2. 手动配置迁移命令migrate生成的默认字符串长度，在AppServiceProvider中调用Schema::defaultStringLength方法来实现配置：

```bash
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\Schema;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        //
        Schema::defaultStringLength(191);
    }

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```
