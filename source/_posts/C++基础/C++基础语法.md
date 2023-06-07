---
title: C++基础语法
date: 2023-06-07 15:58:40
tags:
categories: 
- C++基础
---
# C++基础语法

**构造函数与析构函数**

1. 一个类中析构函数只能有一个，而构造函数可以有很多个
2. 析构函数没有函数参数、没有返回值、没有返回类型
3. 由于没有函数参数，所以不能被重载
4. 构造函数在类的构建时调用，析构函数在类的声明周期结束时调用

Test.h文件

```c++
#ifdef _TEST_H_ 
#defind _TEST_H_ 

#include <iostream>
using namespace sth
namespace test{
    class Test{
        public:
            Test(){
                this.sex=0;
                this.sex=0L;
            } //构造函数
            ~Test(){} //析构函数
        public:
            void setSex(int sex); //公共方法
            void setPhone(long phone); 
            int getSex();
            long getPhone();
        private:
            int sex; //私有属性
            long phone;
    };
} //namespace
#endif //_TEST_H_
```

Test.cpp文件

```c++
#include <iostream>
#include "Test.h"

namespace test{
    void Test::setSex(int s){
        sex = s;
    }
    void Test::setPhone(long p){
        phone = p;
    }
    int Test::getSex(){
        return sex;
    }
    long Test::getPhone(){
        return phone;
    }
}
```

**名存空间**

`using namespace 'Name'`：选择名存空间

`namespace 'Name'`：创建名存空间关键字