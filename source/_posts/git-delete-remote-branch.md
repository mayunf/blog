---
title: git-delete-remote-branch
date: 2018-12-03 17:25:55
tags:
- git
---



## 删除远程`branch`

### 查看全部分支

```bash
git branch -a

* dev
  master
  remotes/online/dev
  remotes/online/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/dev-test
```

### 删除远程分支
```bash
git push origin --delete dev-test
```

## 删除本地分支

```bash
git branch -d dev
```
