---
title: '异常记录：ajax请求失败 chrome报错net::ERR_INCOMPLETE_CHUNKED_ENCODING'
date: 2018-11-22 09:41:41
tags:
- ajax
- chrome
---

记一次解决chrome报错net::ERR_INCOMPLETE_CHUNKED_ENCODING

## 先给出解决办法：

调整/fastcgi_temp权限为配置nginx的那个用户。

```bash
chown -R webapps:webapps /usr/local/nginx/fastcgi_temp
```

## 问题原因

因为之前请求时正常的，中间改过一次服务端的 nginx 启用用户。直接查看了网站的nginx访问日志。

发现了下面的错误：
```bash
2018/11/22 09:28:14 [crit] 41558#0: *57127 open() "/usr/local/nginx/fastcgi_temp/2/23/0000000232" failed (13: Permission denied) while reading upstream, client: 10.110.30.108, server: www.zentao.test, request: "GET /index.php?m=my&f=index HTTP/1.1", upstream: "fastcgi://127.0.0.1:9000", host: "www.zentao.test"
```

/usr/local/nginx/fastcgi_temp

## 拓展：fastcgi_temp 目录的作用
先简单的说一下 Nginx 的 buffer 机制，对于来自 FastCGI Server 的 Response，Nginx 将其缓冲到内存中，然后依次发送到客户端浏览器。缓冲区的大小由 fastcgi_buffers 和 fastcgi_buffer_size 两个值控制。
比如如下配置：
```bash
fastcgi_buffers      8 4K;
fastcgi_buffer_size  4K;
```

fastcgi_buffers 控制 nginx 最多创建 8 个大小为 4K 的缓冲区，而 fastcgi_buffer_size 则是处理 Response 时第一个缓冲区的大小，不包含在前者中。所以总计能创建的最大内存缓冲区大小是 8*4K+4K = 36k。而这些缓冲区是根据实际的 Response 大小动态生成的，并不是一次性创建的。比如一个 8K 的页面，Nginx 会创建 2*4K 共 2 个 buffers。

当 Response 小于等于 36k 时，所有数据当然全部在内存中处理。如果 Response 大于 36k 呢？fastcgi_temp 的作用就在于此。多出来的数据会被临时写入到文件中，放在这个目录下面。同时你会在 error.log 中看到一条类似 warning：

```bash
2010/03/13 03:42:22 [warn] 3994#0: *1 an upstream response is buffered to a temporary file
/usr/local/nginx/fastcgi_temp/1/00/0000000001 while reading upstream, 
client: 192.168.2.1,
server: pma.verdana.cn,
request: "POST /tbl_structure.php HTTP/1.1",
upstream: "fastcgi://127.0.0.1:9000", 
host: "pma.verdana.cn",
referrer: "http://pma.verdana.cn/tbl_structure.php"
```

显然，缓冲区设置的太小的话，Nginx 会频繁读写硬盘，对性能有很大的影响，但也不是越大越好，没意义，呵呵！


当代理文件大小超过配置的proxy_temp_file_write_size值时，nginx会将文件写入到临时目录下（默认为/proxy_temp）。

如果nginx对/proxy_temp没有权限，就写不进去。

参考资料：

[https://blog.csdn.net/u012383839/article/details/72875199](https://blog.csdn.net/u012383839/article/details/72875199)
[https://blog.csdn.net/crx05/article/details/70210323](https://blog.csdn.net/crx05/article/details/70210323)


