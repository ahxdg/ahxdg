---
title: 如何做内网项目的https优化
date: 2018-07-11 17:27:23
tags:
---

> 有时候，我们可能会遇到客户要求在自己的内部网络内使用项目。那么我们就需要把项目部署到客户的网络内部，然后又需要让用户使用时浏览器提示是“安全的”，尽管已经是和外部网络隔离了。

问题来了，怎么办呢？

首先，我们需要一个域名，那就去各大域名网站购买一个吧。
其次，就需要申请一个ssl证书了（域名网站一般会有1年的免费域名使用且都有相应教程，这里不做赘述）。
再然后，就是给项目添加https，这里多说一下：
以nginx为例：
在nginx.conf中添加ssl
```conf
server {
    listen 443;
    server_name youdomainname.com;
    ssl on;

    ssl_certificate   cert/youssl.pem;
    ssl_certificate_key  cert/youssl.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location / {
        root html;
        index index.html index.htm;
        # 这里也可以做端口的转发
        # proxy_pass locahost:8081;
    }
}
```
接下来，就需要你把内网电脑上的hosts文件添加一行
```
# 这里的192.168.2.245是内网服务器
192.168.2.245 youdomainname.com

```

是不是很简单...

其实这种方案可以解决相关很多问题，比如：https访问http接口