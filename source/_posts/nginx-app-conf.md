---
title: 记录一个nginx默认配置文件
date: 2018-11-07 14:08:33
tags: nginx
---

> 记录一个nginx默认配置文件

```php
server {
    listen 80;
    server_name {你的域名};
    root "{站点根目录}";

    index index.html index.htm index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log /data/log/nginx/{你的网站标识}-access.log;
    error_log  /data/log/nginx/{你的网站标识}-error.log error;

    sendfile off;

    client_max_body_size 100m;

    include fastcgi.conf;

    location ~ /\.(ht|svn|git) {
       deny all;
    }

    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        #fastcgi_pass /run/php/php7.0-fpm.sock;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}

```
