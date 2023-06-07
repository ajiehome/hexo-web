---
title: Windows11改小状态栏
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
在注册表如下路径

```propreties
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced
```

新建一个`DWORD`
名称为：`TaskbarSi `
值为：`0、1、2`