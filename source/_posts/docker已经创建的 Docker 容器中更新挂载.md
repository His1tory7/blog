---
title: docker已经创建的 Docker 容器中更新挂载
tag: 服务器运维
---
<!--more-->
如果你想在已经创建的 Docker 容器中更新挂载，你可以使用 docker container update 命令。使用 docker container update 命令的语法如下：


```java 
docker container update [选项] <容器名>
例如，如果你想更新一个名为 my-nginx 的容器，将容器中的 /usr/share/nginx/html 目录挂载到主机的 /path/to/nginx/html 目录，你可以使用以下命令：

Copy code
docker container update --mount type=bind,source=/path/to/nginx/html,target=/usr/share/nginx/html my-nginx
这条命令会更新 my-nginx 容器，并将容器中的 /usr/share/nginx/html 目录挂载到主机的 /path/to/nginx/html 目录。

请注意，docker container update 命令可以用来更新容器的各种设置，而不仅仅是挂载。例如，你也可以使用这个命令来更改容器的 CPU 和内存限制，或者添加新的网络连接等等。你可以使用 docker container update --help 命令查看所有可用的选项。

