---
title: Javascript的dom操作
date: 2023-06-07 15:58:40
tags:
categories: 
- 前端杂烩
---
# JS原生DOM操作

```javascript
let mainView = document.getElementById("main");
                console.log(mainView)
                let textDom = document.createElement("img");
                let x = Math.random()*200+"px";
                let y = (Math.random()*300+100)+"px";
                textDom.style.top = y;
                textDom.style.width = "20px";
                textDom.style.height = "20px";
                textDom.style.left = x;
                textDom.style.border = "solid 1px #cccccc";
                textDom.style.zIndex = "999";
                textDom.style.position = "absolute";
                textDom.setAttribute("src","../../static/log.jpg");
                // textDom.setAttribute("src","http://a2.att.hudong.com/36/48/19300001357258133412489354717.jpg");
                mainView.appendChild(textDom)
                console.log(mainView)
```
