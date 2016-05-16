---
layout: post
title: '进程地址空间分布'
date: '2016-05-16'
header-img: "img/home.jpg"
tags:
     - C/C++
author: 'Kingback'
---

### APUE中的一个典型C内存空间分布图
![img](/img/2016/05/process-address-space.jpg)

地址空间由低到高分别是：正文段，数据段，bss段，堆，栈。再往上还有命令行参数和环境变量。下面依次介绍：

* 正文段（又称代码段）：存放程序执行代码的内存区域。大小在程序运行前已经确定，通常是只读。

* 数据段：存放程序中已初始化的全局变量或静态变量的内存区域，属于静态内存分配。

* bss段：和数据段不同，存放的是没有初始化的全局变量或静态变量，属于静态内存分配，程序一开始就将其变量清零了。bss是"Block Started by Symbol(由符号开始的块)"的简称，更有"Better Save Space(更有效地节省空间)"。由于bss段只保存没有值的变量，所以事实上不需要保存变量的副本。运行时所需要的bss段的大小记录在目标文件中，但不像其他段，并不占用目标文件的空间。

* 堆：动态存储分配，用malloc等函数分配内存。

* 栈：保存局部变量，临时数据，传递到函数中的参数等。

### size 命令列出目标文件各个部分所占的字节数


```
[wanghui@centos-6 fork]$ size a.out
   text    data     bss     dec     hex filename
   1104     256       8    1368     558 a.out

```

size命令显示内存程序映像中的text, data, bss三个段大小, 以及这3个段大小之和的十进制和十六进制表示. （由于堆栈是在程序执行时动态分配的, size无法显示它们的大小）

### 探索堆栈

编译运行下面的程序，可以发现系统中堆栈的大致位置。

```

#include <stdio.h>
int main()  
{  
    int i = 0;  
    printf("The Top Stack is %p\n",&i);  
    return 0;  
}  

```

在不同的计算机架构和不同OS中，堆栈位置可能不相同。但绝大多数的CPU中，堆栈是向下增长的，也就是朝着地地址方向生长。