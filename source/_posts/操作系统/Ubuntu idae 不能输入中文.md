---
title: Ubuntu idae 不能输入中文
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
---
title: ubuntu idae 不能输入中文
date: 2022-08-29 20:48:14.684
updated: 2022-10-13 14:45:23.084
url: /archives/ubuntuidae-bu-neng-shu-ru-zhong-wen
categories: 
- Ubuntu
- Idea
tags: 
- Ubuntu
- Idea
---

解决方案
在`Edit Custom VM options`中配置如下参数
```properties
-Drecreate.x11.input.method=true
```