---
title: StarUML激活
date: 2023-06-07 15:58:40
tags:
categories: 
- 开发工具
---
# StarUML激活
确切的来说并不是激活StarUML，只是去除了StarUML在未激活时的弹窗提示

原理：对StarUML的依赖启动app.asar文件进行反编译拿到源码，把源码中未激活提示部分移除掉即可，并且关闭自动升级，避免文件被覆盖。


1、app.asar 所使用的时asar程序，那么我们需要先安装一个node，通过node下载asar应用  
2、找到StarUML的安装目录，在安装目录的resource文件夹下有一个app.asar文件  
3、使用asar反编译 `asar extract app.asar app`生成源代码，源代码在app文件夹内，注意：如果是在C盘的管理员目录，需要用管理员身份执行CMD。  
4、修改`app\src\engine\license-manager.js`文件中checkLicenseValidity方法的代码  
```javascript
checkLicenseValidity () {
    this.validate().then(() => {
    setStatus(this, true)
    }, () => {
    //setStatus(this,false)  <-- comment this line
    setStatus(this, true) //<-- add this line
    //UnregisteredDialog.showDialog() <-- comment this line
    })
}
```
5、修改`app\src\app-context.js`文件的appReady方法的代码，禁止自动更新
```javascript

// if (this.preferences.get('checkUpdate.checkUpdateOnStart')) {
//    ipcRenderer.send('check-update')
// }
```
6、重新打包覆盖原来的app.asar文件 `asar pack app app.asar`