---
title: 一则rabbitmq更换localhost导致的问题
categories: 服务器运维
---


### 存在的问题
> 用原始的guest用户只能登录localhost，在外部访问不了，所以我们要添加一个用户，供外部登录使用：
### 解决方案
1.添加一个用户，我这里以设置的用户名是admin，大家可以按需设置
> rabbitmqctl add_user admin 123456

2.设置admin的角色信息，这里设置为超级管理员管理角色（可登陆管理控制台(启用management plugin的情况下)，可查看所有的信息，并且可以对用户，策略(policy)进行操作）

> rabbitmqctl set_user_tags admin administrator

3.设置用户权限

> rabbitmqctl set_permissions -p “/” admin “." ".” “.*”

设置上面3步，就可以从外部访问登录了！


