---
title: 编译BabaSSL和Tengine支持国密https
categories: 服务器运维
---
## 一、介绍
Tengine 是由淘宝网发起的 Web 服务器项目，它在 Nginx 的基础上，针对大访问量网站的需求，添加了很多高级功能和特性。

BabaSSL 是一款轻巧、灵活且靠谱的密码学和 TLS 协议工具集。BabaSSL 是蚂蚁集团和阿里集团的各主要业务中所使用的底层密码库，目前开源出来供业界使用。BabaSSL 广泛的应用在包括网络、存储、移动端 App 等场景中。
<!--more-->
## 二、目的
BabaSSL 国密双证书协议进行了支持，并统称为 NTLS

此次 Tengine 针对 BabaSSL 中的 NTLS 功能进行了适配，用户如果选择使用 BabaSSL 作为 Tengine 的底层密码库来实现通信加密的能力，则可以无需对 Tengine 进行任何代码改动，原生开启 NTLS 能力。

## 三、步骤

### 1.下载BabaSSL
```shell
wget https://github.com/Tongsuo-Project/Tongsuo/releases/download/8.3.2/BabaSSL-8.3.2.tar.gz
```
### 2.下载tengine
```shell
wget https://github.com/alibaba/tengine/archive/refs/tags/2.4.0.tar.gz
```
### 3.编译tengine
```shell
./configure --add-module=modules/ngx_openssl_ntls \
--without-http_gzip_module \
--with-openssl=../BabaSSL-8.3.2 \
--with-openssl-opt="--strict-warnings enable-ntls" \
--with-http_ssl_module --with-stream \
--with-stream_ssl_module --with-stream_sni \
--prefix=/usr/local/tengine 
```

> 注：没有babassl的场合使用以下：  --with-openssl=/root/openssl-1.1.1g

### 4.安装
```shell
make
make install
```
### 5.配置nginx

```
http {
    include       mime.types;
    default_type  application/octet-stream;
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    #access_log  logs/access.log  main;
    #access_log  "pipe:rollback logs/access_log interval=1d baknum=7 maxsize=2G"  main;
    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;
    #gzip  on;

     server
    {
        listen 443 ssl;
        server_name localhost;
        //开启国密功能
        enable_ntls on;
        //默认证书
        ssl_certificate                 /usr/local/nginx-1.18.0/conf/dcssl-sign.cer;
        ssl_certificate_key             /usr/local/nginx-1.18.0/conf/dcssl-sign.key;
        //国密签名证书
        ssl_sign_certificate            /usr/local/nginx-1.18.0/conf/dcssl-sign.cer;
        ssl_sign_certificate_key        /usr/local/nginx-1.18.0/conf/dcssl-sign.key;
        //国密加密证书
        ssl_enc_certificate             /usr/local/nginx-1.18.0/conf/dcssl-encrypt.cer;
        ssl_enc_certificate_key         /usr/local/nginx-1.18.0/conf/dcssl-encrypt.key;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
        location /test-api{
        proxy_pass http://127.0.0.1:8080/;
        }
    }
}
```

> 可以使用360国密版进行测试

![图片](e4726d7a64b27bd9f987222c1d3e6694.png)

> 安装国密版curl 具体操作见：https://www.gmssl.cn/gmssl/index.jsp
