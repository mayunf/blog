---
title: '错误：RSA host key for 10.110.30.73 has changed and you have requested strict checking.（已解决）'
date: 2018-11-12 19:14:24
tags: 
- linux
- ssh
- git
---

今天在执行`git pull`后,报错：

> 先执行了 `git remote set-url origin ssh://git@ip:22/~/git/www.baidu.com/`

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
因为重装后，本地机和服务器内部ssh对不上导致错误，因此，只需要删除本地机ssh缓存信息，即可恢复
在本地机输入一下命令行：

```bash
ssh-keygen -R [服务器ip address]
```

得到以下结果：

```bash
$ ssh-keygen -R 10.110.30.73
# Host 10.110.30.73 found: line 3
/c/Users/mayunfeng/.ssh/known_hosts updated.
Original contents retained as /c/Users/mayunfeng/.ssh/known_hosts.old

```
再次执行 `git pull` 问题得到解决！
