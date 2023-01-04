---
title: ssh连接卡住的解决方法
tag: 服务器运维
---
## 一、现象
> 1.使用xshell连接远程主机的时候一直卡在To escape to local shell, press 'Ctrl+Alt+]'.，要等很久才能连上：
Connecting to 192.168.123.208:22...
Connection established.
To escape to local shell, press 'Ctrl+Alt+]'.
## 二、解决方法
> vi /etc/ssh/sshd_config
UseDNS no # 修改UseDNS yes为UseDNS no，并去掉前面的#。
## 三、原理
> UseDNS选项打开状态下，当客户端试图登录SSH服务器时，服务器端先根据客户端的IP地址进行DNS PTR反向查询出客户端的主机名，然后根据查询出的客户端主机名进行DNS正向A记录查询，验证与其原始IP地址是否一致，这是防止客户端欺骗的一种措施。但一般我们的是动态IP不会有PTR记录，打开这个选项是在白白浪费时间，关闭即可。
