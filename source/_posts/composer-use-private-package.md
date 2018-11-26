---
title: 使用自有托管 Gitlab 上的私有包
date: 2018-11-26 17:11:04
tags:
- composer
- php
---

## 使用自有托管 Gitlab 上的私有包
推荐使用 vcs 作为版本库类型，并且 Composer 决定获取包的合适的方法。比如，从Github上添加一个 fork，使用它的 API 下载整个版本库的 .zip 文件，而不用克隆。

不过对一个私有的 Gitlab 安装来讲会更复杂。如果用 vcs 作版本库类型，Composer 会检测到它是个 Gitlab 类型的安装，会尝试使用 API 下载包（这要求有 API key。我不想设置，所以我只用 SSH 克隆安装了) ：

首先指明版本库类型是 git：

```bash
"repositories": [
    {
        "type": "git",
        "url": "git@gitlab.mycompany.cz:package-namespace/package-name.git"
    }
]

```

然后指明常用的包：

```bash
"require": {
    "package-namespace/package-name": "1.0.0"
}
```

## 临时使用 fork 下 bug 修复分支的方法

如果在某个公共的库中找到一个 bug，并且在Github上自己的 fork 中修复了它， 这就需要从自己的版本库里安装这个库，而不是官方版本库（要到修复合并且修复的版本释出才行）。

使用 [内嵌别名](https://getcomposer.org/doc/articles/aliases.md#require-inline-alias) 可轻松搞定：

```bash
{
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/you/monolog"
        }
    ],
    "require": {
        "symfony/monolog-bundle": "2.0",
        "monolog/monolog": "dev-bugfix as 1.0.x-dev"
    }
}
```

可以通过 [设置 `path` 作为版本库类型](https://getcomposer.org/doc/05-repositories.md#path) 在本地测试这次修复，然后再 push 更新版本库。

参考文档：

[你必须知道的 17 个 Composer 最佳实践（已更新至 22 个）](https://laravel-china.org/topics/7609/you-have-to-know-17-composer-best-practices-updated-to-22)
