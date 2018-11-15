---
title: change-git-remote
date: 2018-11-13 09:12:28
tags:
- git
---

更换git远程仓库地址(以下命令建议在git bash 下执行)：

**注意：下列命令中的`mina.api.jwsem.cn` 需要替换为自己的仓库地址名称**

## 首先执行 `git remote -v` 查看当前仓库地址，结果如下

```bash
D:\phpStudy\PHPTutorial\WWW\mina.api.jwsem.cn>git remote -v

origin  ssh://webapps@10.110.30.49/~/git/mina.api.jwsem.cn (fetch)
origin  ssh://webapps@10.110.30.49/~/git/mina.api.jwsem.cn (push)

```
## 复制 `ssh://webapps@10.110.30.49/~/git/mina.api.jwsem.cn` 将地址改为 `ssh://webapps@10.110.30.73/~/git/mina.api.jwsem.cn`
即：地址中的49改为73,`mina.api.jwsem.cn` 需要替换为自己的仓库地址名称执行以下命令：

```bash
git remote set-url origin ssh://webapps@10.110.30.73/~/git/mina.api.jwsem.cn
```

如果没有报错信息即修改成功了。

## 再次执行 `git remote -v` 查看当前仓库地址，结果如下

```bash
D:\phpStudy\PHPTutorial\WWW\mina.api.jwsem.cn>git remote -v

origin  ssh://webapps@10.110.30.73/~/git/mina.api.jwsem.cn (fetch)
origin  ssh://webapps@10.110.30.73/~/git/mina.api.jwsem.cn (push)

```

## 此时执行 `git pull` 第一次会出现以下错误
```bash
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
SHA256:buhkV/c/OVY1lOGF+pKRo/hpDhw7YZ+Yj9o+Qx8bib4.
Please contact your system administrator.
Add correct host key in /c/Users/mayunfeng/.ssh/known_hosts to get rid of this message.
Offending RSA key in /c/Users/mayunfeng/.ssh/known_hosts:3
RSA host key for 10.110.30.73 has changed and you have requested strict checking.
Host key verification failed.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

```
解决方法：
```bash
ssh-keygen -R 10.110.30.73
```

得到以下结果：

```bash
$ ssh-keygen -R 10.110.30.73
# Host 10.110.30.73 found: line 3
/c/Users/mayunfeng/.ssh/known_hosts updated.
Original contents retained as /c/Users/mayunfeng/.ssh/known_hosts.old

```
再次执行 `git pull` 问题得到解决！
