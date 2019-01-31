---
title: 局域网用ssh连接linux服务器特别慢
date: 2019-01-31 11:28:08
tags:
- linux
- ssh
---


解决办法：
1. 修改"UseDNS"的值为“no”（没有的添加该配置选项，注释掉的放开即可）
2. 修改“GSSAPIAuthentication”的值为“no”（没有的添加该配置选项，注释掉的放开即可）

调试办法：
```bash
ssh -v ip地址
```
