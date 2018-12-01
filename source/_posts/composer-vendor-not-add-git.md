---
title: vendor/mayunfeng/* 目录无法提交到git上

date: 2018-12-01 12:35:06

tags:
- composer
- git
---
## 原因

`composer` 安装扩展时使用了 `dev-master`

```bash
composer require mayunfeng/yii2-aliyunoss:dev-master
```



## 避免办法

使用 `--prefer-dist` 或设置 `preferred-install` 为 `dist` 到项目的 `config`.

```bash
composer require --prefer-dist mayunfeng/yii2-aliyunoss:dev-master
```


## 解决办法

### 删除git cache

> 执行前需要删除扩展包文件夹下的 `.git` 文件夹

```bash
git rm --cached vendor/mayunfeng/*

rm 'vendor/mayunfeng/insideapi/.gitignore'
rm 'vendor/mayunfeng/insideapi/README.md'
rm 'vendor/mayunfeng/insideapi/composer.json'
rm 'vendor/mayunfeng/insideapi/phpunit.xml'
......

```

### 查看状态

```bash
git status

On branch dev
Your branch is up to date with 'origin/dev'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    vendor/mayunfeng/insideapi/.gitignore
        deleted:    vendor/mayunfeng/insideapi/README.md
        ......
```

### 重新添加

```bash
git add . && git commit -m 添加扩展包

[dev fd7b4f8] 添加扩展包
 330 files changed, 130652 insertions(+), 11 deletions(-)
 delete mode 160000 vendor/mayunfeng/yii2-aliyunoss
 create mode 100644 vendor/mayunfeng/yii2-aliyunoss/.gitignore
 create mode 100644 vendor/mayunfeng/yii2-aliyunoss/LICENSE
 ...

```


### 再次提交

```bash
git push

Counting objects: 336, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (300/300), done.
Writing objects: 100% (336/336), 2.61 MiB | 6.42 MiB/s, done.
Total 336 (delta 36), reused 229 (delta 23)
remote: Resolving deltas: 100% (36/36), completed with 6 local objects.
......

```

参考文章：

[https://laravel-china.org/docs/composer/2018/should-i-commit-the-dependencies-in-my-vendor-directory/2102](https://laravel-china.org/docs/composer/2018/should-i-commit-the-dependencies-in-my-vendor-directory/2102)
[https://blog.csdn.net/p763833631/article/details/79990489](https://blog.csdn.net/p763833631/article/details/79990489)
[https://blog.csdn.net/xiaotuni/article/details/77885140](https://blog.csdn.net/xiaotuni/article/details/77885140)
