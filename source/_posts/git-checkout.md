---
title: git如何快速恢复被删除的文件
date: 2018-11-05 17:54:09
tags: git
---

### 首先找到什么时候删除的文件

```php
git log --diff-filter=D --summary
```

### 恢复被删除的文件
> $commit~1 是指被删除的commit 的上一个提交，因为在commit 时已经删除了文件
```php
git checkout $commit~1 filename
```


