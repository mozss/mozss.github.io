---
title: Nginx常用功能
tags:
  - nginx
categories:
  - code
abbrlink: 62876
date: 2019-05-01 21:06:23
---

<!--more-->
# NGINX 常用功能

NGINX 是一款高性能的 Web 服务器和反向代理服务器，常用于构建高性能和可伸缩性的 Web 应用程序。除了作为 Web 服务器和反向代理服务器之外，NGINX 还可以实现许多其他功能。在本文中，我们将介绍 NGINX 的一些常用功能。

## 静态文件服务

NGINX 可以提供静态文件服务，通过 HTTP 协议将本地存储的文件发送到客户端。其实现非常简单，只需要在 NGINX 的配置文件中增加一条 `location` 指令即可：

```
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
```

在上述配置中，NGINX 将会监听 80 端口，在根 `/` 路径下提供静态文件服务。所有访问该路径下的文件请求都将被定位到 `/usr/share/nginx/html` 文件夹下。

## 反向代理

NGINX 可以作为反向代理服务器，转发来自客户端的请求到后端的多个 Web 服务器。在这种情况下，NGINX 将作为客户端和后端服务器之间的“中间人”，负责转发请求和响应。其实现也非常简单，只需要在 NGINX 的配置文件中增加一条 `server` 指令即可：

```
server {
    listen       80;
    server_name  example.com;

    location / {
        proxy_pass         http://backend;
        proxy_redirect     off;
        proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    # ...
}

upstream backend {
    server backend1.example.com;
    server backend2.example.com;
}
```

在上述配置中，NGINX 将会监听 80 端口，在 `example.com` 的根路径下转发所有请求到名为 `backend` 的后端服务器。后端服务器的地址应该在 `upstream` 块中定义。同时，NGINX 还可以设置一些代理相关的头部信息。

## 负载均衡

NGINX 可以作为负载均衡器，将来自客户端的请求分发到多个后端服务器，并尽可能地平均负载。在这种情况下，NGINX 将作为客户端和后端服务器之间的“中间人”，负责分发请求和响应。其实现也非常简单，只需要在 NGINX 的配置文件中增加一条 `upstream` 指令即可：

```
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend;
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
    }
}
```

在上述配置中，NGINX 将会监听 80 端口，并将来自客户端的请求分发到三个名为 `backend1.example.com`、`backend2.example.com` 和 `backend3.example.com` 的后端服务器上。同时，NGINX 还可以设置一些负载均衡相关的选项。

## URL 重写

NGINX 可以重写客户端请求的 URL，将原始 URL 转换为另一个 URL 并发送到后端服务器。这种功能可用于隐藏后端服务器的实际位置、根据请求内容转发到不同的后端服务器、或调整请求的结构等。其实现需要在 NGINX 的配置文件中增加一条 `location` 指令：

```
location / {
    rewrite ^/(.*)$ /index.php?q=$1 last;
}
```

在上述配置中，NGINX 将会重写客户端请求的 URL，在路径 `/` 下将所有请求重写为 `/index.php?q=$1` 的格式。其中，`$1` 是原始 URL 的捕获组。

## SSL 终端

NGINX 可以作为 SSL 终端，与客户端建立 SSL 连接，然后解密和加密所有传输的数据。其实现需要在 NGINX 的配置文件中增加一条 `server` 指令：

```
server {
    listen       443 ssl;
    server_name  example.com;

    ssl_certificate      /path/to/certificate.pem;
    ssl_certificate_key  /path/to/certificate_key.pem;

    location / {
        proxy_pass http://backend;
        proxy_redirect off;
    }

    # ...
}
```

在上述配置中，NGINX 将会监听 443 端口，并在建立 SSL 连接后将请求分发到名为 `backend` 的后端服务器上。同时，NGINX 还需要指定 SSL 证书和私钥。

## WebSocket 支持

NGINX 可以支持 WebSocket 协议，允许客户端与服务器之间建立双向的实时通信连接。其实现需要在 NGINX 的配置文件中增加一条 `location` 指令：

```
location /ws {
    proxy_pass http://backend;

    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

在上述配置中，NGINX 将会将路径 `/ws` 下的所有 WebSocket 请求转发到名为 `backend` 的后端服务器上。同时，NGINX 还需要设置一些代理相关的头部信息。

## 缓存

NGINX 可以缓存某些请求的响应，避免反复从后端服务器重新获取响应。其实现需要在 NGINX 的配置文件中增加一条 `proxy_cache_path` 指令和一条 `proxy_cache` 指令：

```
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m;

server {
    # ...

    location / {
        proxy_cache_bypass $http_pragma;
        proxy_cache_revalidate on;
        proxy_cache_key "$scheme$request_method$host$request_uri";
        proxy_cache_valid 200 10m;
        proxy_cache_valid 404 1m;

        proxy_pass http://backend;
    }
}
```

在上述配置中，NGINX 将会缓存名为 `my_cache` 的反向代理响应。所有请求的缓存将会被存储在 `/var/cache/nginx` 目录下。同时，NGINX 还可以设置一些缓存相关的选项。

## 总结

在本文中，我们介绍了 NGINX 的一些常用功能，包括静态文件服务、反向代理、负载均衡、URL 重写、SSL 终端、WebSocket 支持和缓存。这些功能可以用于构建高性能和可伸缩性的 Web对不起啦，脑子转不过啦，暂时无法回答的您的问题，请稍后重试！！