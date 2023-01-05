---
title: 解决mysql8.0 GROUP BY报错
tag: 服务器运维
---
<!--more-->
### 1、打开mysql安装目录打开my.ini文件
### 2、在[mysqld]中增加以下配置

``` java
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
```



