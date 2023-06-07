---
title: Shell笔记
date: 2023-06-07 15:58:40
tags:
categories: 
- 脚本语言
---
# Shell笔记

```shell
# 定义变量tips
tips='Hello Word ！'
# 使用tips变量
echo ${tips}
# 设置为只读
readonly tips
# 删除变量
unset tips
```

选择器语法

```shell
# 获取第一个参数，默认是从0开始，但是由于第0个是sh脚本名称所以从第一个开始
param1=$1;

case $param1 in
  'start')
    echo 'service start ...';;
  'restart')
    echo 'service restart ...';;
    'stop')
      echo 'service stop ...';;
    *)
      echo 'comment not exists ...';;
esac
```

条件语法

```shell
param=$1;
if [[ param == 'start' ]]; then
  echo 'service start ...';
fi

if [[ param == 'start' ]]; then
  echo 'service start ...';
elif [[ param == 'restart' ]]; then
  echo 'service restart ...';  
fi
```

循环语法

```shell
param1=$1;
while [[ condition ]]; do
  echo "----$param1----";
  let "param1-=1";
done

for (( i = 0; i < 10; i++ )); do
  #statements
done
```

**要点**

1. shell的变量定义为直接定义，变量名与内容之间不能有空格
2. 使用变量的时候，需要使用美元符号，可以用花括号括起来，也可以不用，主要是为了区分边界
3. readonly设置变量为只读模式，其后不可再赋值，只能读取
4. unset删除指定变量
5. shell语法需要注意单引号和双引号，单引号的字符串内不能再使用相关变量，而双引号可以
6. 变量赋值方式 let variable=variable+1;  let "variable++"; ((variable++))
