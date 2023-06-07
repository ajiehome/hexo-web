---
title: Git常用命令
date: 2023-06-07 15:58:40
tags:
categories: 
- 开发工具
---
# Git基本常用命令

```shell
#初始化一个Git仓库
git init
#把文件添加到本地git仓储缓存
git add "file name"
#把当前目录下全部文件加入到本地git仓储缓存
git add .
#把仓储缓存提交上去
git commit -m"description"
#查看所有的远程分支
git remote -v
#添加远程分支
git remote add origin "git remote warehouse url"
#移除本地添加的远程分支
git remote remove "remote branch name"
#获取远程分支与本地分支合并
git pull
#把本地分支提交到远程分支
git push
#查看所有的远程分支和本地分支

git branch -a
#查看所有的本地分支
git branch
#本地分支与远程分支关联
git branch --set-upstream-to=origin/master master
#创建本地分支
git branch "local branch name"

#清除输入过的账号密码
git config --system --unset credential.helper

#回滚
git log
git checkout [commit id] [file name]
commit id:commit提交的编号
file name:需要回滚的文件名称，不输入则整个版本回滚

#克隆远程仓库到本地
#fileName:为指定本地文件夹的名字，如果没有就会使用远程仓库默认s的名称
git clone "git remote warehouse url" [ specified file name]
```

## 谨慎使用

```shell
#'fatal: refusing to merge unrelated histories':无法合并两个不相干分支，此命令可强行合并建立关系
git pull origin master --allow-unrelated-histories
```

## 文件

`.gitignore`：文件的内容将是忽略提交的部分，也就是git不会检索该文件指定的文件和文件夹

例如：

```shell
HELP.md
target/
!.mvn/wrapper/maven-wrapper.jar
!**/src/main/**
!**/src/test/**

### STS ###
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans
.sts4-cache

### IntelliJ IDEA ###
.idea
*.iws
*.iml
*.ipr

### NetBeans ###
/nbproject/private/
/nbbuild/
/dist/
/nbdist/
/.nb-gradle/
build/

### VS Code ###
.vscode/
```

更多高级操作见下一篇：Git操作