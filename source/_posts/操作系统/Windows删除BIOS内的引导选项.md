---
title: Windows删除BIOS内的引导选项
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
# 删除BIOS内的UEFI引导选项

1. 首先使用管理员打开`cmd`面板
2. 输入`diskpart`进入工具
3. 输入`list disk`查看磁盘窗台
4. 使用`select disk [磁盘编号]`选择包含`UEFI`分区的磁盘
5. 使用`list partition`列举出所有的分区
6. 使用`select partition [分区编号]`选中`UTFI`分区
7. 使用`assign letter [挂载号]`挂载分区为某一磁盘驱动器
8. 打开文件管理器，发现有一个新的分区出现，就是`EFI`分区
9. 我们没有权限打开`EFI`分区，所以使用管理员运行记事本，使用打开的方式进入到`EFI`分区
10. 里面对应的文件夹就是不同系统的引导，删除对应即可，千万不能删除不知道的分区
10. 最后使用`remove letter [挂载号]`移除分区即可