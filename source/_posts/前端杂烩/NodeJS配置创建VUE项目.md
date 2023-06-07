---
title: NodeJS配置创建VUE项目
date: 2023-06-07 15:58:40
tags:
categories: 
- 前端杂烩
---
# nodejs

查看node版本

```shell
node -v
```

查看npm版本

```shell
npm -v
```

查看配置文件

```shell
npm config list
```

修改本地仓库

```shell
npm config set prefix "C:\Software\node\node_global"
npm config set cache "C:\Software\node\node_cache"
```

修改镜像站

```shell
npm config set registry=https://registry.npm.taobao.org
```

node更新

```shell
*# 全局安装n
$ npm install -g n 
# 升级到最新稳定版
$ n stable
# 升级到最新版
$ n latest
# 升级到定制版
$ n v7.10.0
# 切换使用版本
$ n 7.10.0 (ENTER)
```



配置文件所在位置`C:\Users\Administrator\.npmrc`

获取镜像信息（非安装）

```shell
npm info vue
```

更新模块命令

```shell
#老版本安装方式 2.*
npm install npm –g
```

```shell
#老版本安装方式 3.* /4.*
npm install -g @vue/cli
```

- npm install`：表示更新
- `第二个npm`：表示需要更新的模块
- `-g`：表示安装到global目录下

配置NodeJS的环境变量

```shell
path: D:\software\nodejs
```

配置global仓的环境变量

```shell
path: D:\software\nodejs\node_global
```

新增一个`NODE_PATH`环节变量（子项的全局命令）

```shell
D:\software\nodejs\node_global\node_modules
```

安装vue，安装vue-routher，安装vue脚手架，验证是否安装成功

```shell
npm install vue -g
npm install vue-routher -g
npm install @vue/cli -g
vue -V
```

---

**老版本构建方式**

1、构建初始化vue项目

```shell
vuew init webpack vue01
```

2、进入vue01的目录，初始化安装依赖

```shell
npm install
```

3、运行vue01的dev状态

```shell
npm run dev
```

然后访问**[http://localhost:8080](http://localhost:8080/)**

4、编译生成静态文件

```shell
npm run build
```

---

**新版本构建方式**

```shell
vue create vue01
```

可以选择创建的方式，moban是我创建的模板，可以选择default默认，或者Manually重新选择
> √ Babel    
× TyreScript  
× Progressive Web App (PWA) Support  
√ Routher  
√ Vuex
√ CSS Pre-processors  
× Linter / Formatter  
× Unit Testing   
× E2E Testing