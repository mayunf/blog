---
title: git-delete-remote-tags
date: 2018-12-03 17:25:55
tags:
- git
---
## git 删除远程仓库的标签

```bash
git push origin :refs/tags/<tagname>
```

## git 删除本地仓库的标签

```bash
git tag -d <tagname>
```

## 本地tag推送到远程仓库

```bash
git push --tag
```
