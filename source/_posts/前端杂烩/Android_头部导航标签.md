---
title: Android_头部导航标签
date: 2023-06-07 15:58:40
tags:
categories: 
- 前端杂烩
---
# Toolbar
关于toolbar使用的几个要点，包如下
```xml
androidx.appcompat.widget.Toolbar
```
这里作为toolbar的最左标签使用
```xml
app:navigationIcon="@mipmap/sidebar"
```
最左侧按钮点击事件，这里的点击事件有一个很重要的事情，就是只有在toolbar提交之后才能生效，也就是
```java
setSupportActionBar(toolbar);
```
```java
@Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        if (item.getItemId() ==android.R.id.home){
            drawerLayout.openDrawer(Gravity.LEFT);
        }
        return super.onOptionsItemSelected(item);
    }
```
而其他的所有操作，如设置title，logo等等方法，也要在提交了toolbar之后才能生效，例如
```java
# 设置title无效
getSupportActionBar().setDisplayShowTitleEnabled(false);
```
还有设置toolbar的title的颜色字体大小，等等信息