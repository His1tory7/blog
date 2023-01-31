---
title: git相关操作
tag: 版本控制
---
## 一、介绍
> Git（读音为/gɪt/）是一个开源的分布式版本控制系统，可以有效、高速地处理从很小到非常大的项目版本管理。也是Linus Torvalds为了帮助管理Linux内核开发而开发的一个开放源码的版本控制软件。

## 二、现象
> 当项目中存在多个分支且master分支不允许进行push，该如何进行版本的迭代？
## 三、实现步骤
1.clone主分支代码
```shell
git clone 主分支链接
```
2.选中clone下来的文件，打开命令行（鼠标右键，Git Bash Here）

3.打开终端后，输入指令进入master分支（以哪个为基础的分支）
```shell
git checkout master
```
4.拉取远程master的最新项目到本地master分支
```shell
git pull
```
5.创建新分支，并把当前分支内容复制到新分支中，此时是以主分支创建的新分支。
```shell
git checkout -b yourdev
```
6.把新建的分支push到远程（yourdev）
```shell
git push origin yourdev
```
7.新建的分支进行代码修改。

8.合并至 master 则需要发起Pull Request（合并请求）。具体流程如下：
> (1)在仓库界面找到以下内容，并点击创建合并请求
> 
> (2)选择源分支为yourdev，目标分支为master
> 
> (3)发布合并信息，最终确认合并
> 
> (4)可勾选删除源分支（因为源分支就是为了和主分支合并而新建的）
## 四、扩展
> 比如有两个分支master和tenant，在tenant分支上有多次提交，但是我只想提交某两次在master分支，主分支不允许执行提交操作。

1.查看 tenant分支 提交版本号记录，找到我们要合并的版本号。
```shell
git log tenant
```
2.切换到待合并分支 master
```shell
git checkout master
```
3.执行合并，commit-id为第一步查出的id,多个提交id用逗号分隔
```shell
git cherry-pick commit-id
```
4.创建新分支，并把当前分支内容复制到新分支中（此时也是在master分支新建分支）
```shell
git checkout -b yourdev
```
5.把新建的分支push到远程（yourdev）
```shell
git push origin yourdev
```
6.若需要合并至 master 则需要发起Pull Request（合并请求）。具体流程如下：
> (1)在仓库界面找到以下内容，并点击创建合并请求
>
> (2)选择源分支为yourdev，目标分支为master
>
> (3)发布合并信息，最终确认合并
>
> (4)可勾选删除源分支（因为源分支就是为了和主分支合并而新建的）
