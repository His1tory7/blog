---
title: docker常用命令
categories: 服务器运维
---

### 1.docker run

##### docker run 是 Docker 的一个命令，它用于在 Docker 容器中运行一个指定的镜像。使用 docker run 命令可以在 Docker 容器中执行指定的命令，并且这个命令运行在一个新的容器中，容器会被自动创建。例如，要在一个新的容器中运行一个 Bash shell，可以使用下面的命令：
```shell
docker run -it ubuntu /bin/bash
```
###### 其中，-it 参数表示要交互式地运行容器并分配一个终端，ubuntu 是镜像的名称，/bin/bash 是要在容器中运行的命令。
<!--more-->
### 2.docker create
##### docker create 命令用于创建一个新的容器，但不会启动容器。该命令的格式如下：
```shell
docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
```
###### 其中，IMAGE 是要创建容器的镜像，COMMAND 和 ARG 是要在容器中运行的命令和参数。OPTIONS 是可选的，用于设置容器的选项，比如指定容器名称、设置环境变量、挂载卷等。例如，下面的命令可以创建一个名为 my-container 的容器，并使用 ubuntu:18.04 镜像：
```shell
docker create --name my-container ubuntu:18.04
```
###### 创建完成后，可以使用 docker start 命令启动容器。

> 请注意，docker create 命令只是创建了一个容器，并不会真正运行容器。如果要运行容器，需要使用 docker start 命令。


### 3.docker container update
##### 在已经创建的Docker容器中更新挂载
> 注意：如果你使用的是 Docker 19.03 或更高版本，你可以使用 --mount 选项在容器启动时指定挂载。例如，如果你想在启动 my-nginx 容器时将容器中的 /usr/share/nginx/html 目录挂载到主机的 /path/to/nginx/html 目录，你可以使用以下命令：
```shell
docker container update [选项] <容器名>
```
###### 例如，如果你想更新一个名为 my-nginx 的容器，将容器中的 /usr/share/nginx/html 目录挂载到主机的 /path/to/nginx/html 目录，你可以使用以下命令：
```shell
docker container update --mount type=bind,source=/path/to/nginx/html,target=/usr/share/nginx/html my-nginx
```
###### 这条命令会更新 my-nginx 容器，并将容器中的 /usr/share/nginx/html 目录挂载到主机的 /path/to/nginx/html 目录。

> 请注意，docker container update 命令可以用来更新容器的各种设置，而不仅仅是挂载。例如，你也可以使用这个命令来更改容器的 CPU 和内存限制，或者添加新的网络连接等等。你可以使用 docker container update --help 命令查看所有可用的选项
### 4.docker container start
##### 更老的 Docker 版本，或者想要在已存在的容器新增/更新挂载，你可以使用 -v 标志。例如，如果你想将主机的 /path/to/nginx/html 目录挂载到容器中的 /usr/share/nginx/html 目录，你可以使用以下命令来创建容器：
```shell
docker container start -p 80:80 --name my-nginx -v /path/to/nginx/html2:/usr/share/nginx/html
```
> 如果你使用 -v 标志来启动 Docker 容器，会在容器中更新挂载。如果你在启动容器时指定的挂载与之前创建容器时指定的挂载不同，会替换之前的挂载。

### 5.docker cp
##### 如果要在已经创建的 Docker 容器中添加一条主机和宿主机的文件映射，你需要使用 Docker 的 cp 命令。
<!--more-->
```shell
docker cp <本地文件路径> <容器名>:<容器中的目标路径>
```
###### 例如，如果你想将本地文件 /path/to/myfile 拷贝到已经运行的容器 mycontainer 中的 /tmp 目录下，你可以使用以下命令：
```shell
docker cp /path/to/myfile mycontainer:/tmp
```
###### 你也可以使用 -a 选项，这样就可以保留源文件的所有权限和时间戳：
```shell
docker cp -a /path/to/myfile mycontainer:/tmp
```
