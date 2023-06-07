---
title: C++语法_继承
date: 2023-06-07 15:58:40
tags:
categories: 
- C++基础
---
# 继承

**Fight.h**文件

```c++
#ifdel _CLIENT_TEST_H_
#default _CLIENT_TEST_H_
#include <iostream>
#include "Test.h"

namespace fight{
    class Fight: public Test{
      public:
        Figint(){
            wea = 1;
        }
        ~Fight(){

        }
        private:
            int wea;
        public:
            void setWea(int w);
            int getWea();
    };
} //namespace
#endif // _CLIENT_FIGht_H_
```

**Feight.cpp**文件

```c++
#include <iostream>
#include "Fight.h"

namespace fight{
    void Fight::setWea(w){
        wea = w;
    }
    int Fight::getWea(){
        return wea;
    }
}
```

**调用**

```c++
#include <iostream>
#include "Fight.h"

int main(int argc, char* agev[]){
    fight::Fight
}
```
