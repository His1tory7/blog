---
title: docker ngnix容器代理本地服务报错
tag: 服务器运维
---
<!--more-->
connect() failed (111: Connection refused) while connecting to upstream
### 解决方案1：将127.0.0.1改为docker容器的ip地址

### 解决方案2：docker run时增加 --network host  ——桥接模式
