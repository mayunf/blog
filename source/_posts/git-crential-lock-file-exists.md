---
title: Git提交时提示"fatal unable to get credential storage lock File exists"的解决办法
date: 2019-01-31 14:17:30
tags:
- git
---

## 问题

今天在提交git项目时遇到提示：fatal: unable to get credential storage lock: File exists。
但是似乎提交依然成功了,不知道这个报错有什么影响。

## 原因

git的config中`credential.helper`项有重复，我的是同时含有`credential.helper=store`和`credential.helper=manager`


## 解决办法
在git的config中找到credential.helper项，删除其中一项。

1. 首先要确定是否是有config中credential.helper重复引起的问题，执行git config -l可查看所有参数
2. 确定参数配置的位置。分别执行

    `git config --local -l`
    
    `git config --global -l`
    
    `git config --system -l`
    
    可查看当前项目、全局、系统的参数，找到`credential.helper=manager`的那一项
    
3. `git config --system --unset credential.helper` 即可删除`system`中的此参数配置
4. 如果依然有问题，可尝试升级Git版本到2.9.0以上并再次修改config。

## 补充说明
除了使用指令修改config外也可直接找到配置文件修改。

1. 在win中，git的config配置文件路径分别为：
    - local：在当前项目的.git/config文件中，默认.git是隐藏文件
    - global：在%HOME%/.gitconfig中，%HOME%为系统自带环境变量，一般为C:\Users\<username>,相当与linux的~。另外.gitconfig文件可能也是隐藏的
    - system：在git安装目录的mingw64\etc\gitconfig文件中
2. 在linux中，git的config配置文件路径分别为
    - local：也是在当前项目的.git/config文件中
    - global：在/.gitconfig中，为当前用户的主目录
    - system：在根目录/etc/gitconfig文件中
    
    
## 保存 GIT 用户名密码
```bash
git config --global credential.helper store
```
## 参考

[https://www.jianshu.com/p/8a015684f3e1](https://www.jianshu.com/p/8a015684f3e1)
    
    
    
