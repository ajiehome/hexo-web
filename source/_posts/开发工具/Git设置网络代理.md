---
title: Git设置网络代理
date: 2023-06-07 15:58:40
tags:
categories: 
- 开发工具
---
## 设置http、https代理

```properties
git config --global http.proxy http://127.0.0.1:10809
git config --global https.proxy socks5://127.0.0.1:10808
```

## 查看http、https代理配置情况

```properties
git config --global --get http.proxy
git config --global --get https.proxy
```

## 取消http、https代理配置

```properties
git config --global --unset http.proxy
git config --global --unset https.proxy
```