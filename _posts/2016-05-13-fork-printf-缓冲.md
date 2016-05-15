---
layout: post
title: 'fork函数'
date: '2016-05-13'
header-img: "img/home.jpg"
tags:
     - C/C++
author: 'Kingback'
---

### fork与I/O缓冲区

在进程中用fork()函数可以创建新的进程。子进程是父进程的副本。fork会把整个父进程空间会原模原样地复制到子进程中，包括指令，变量值，程序调用栈，环境变量，缓冲区，文件描述符等等。父进程和子进程不共享存储空间部分，但会共享代码段。

APUE中fork函数实例。

```

#include <unistd.h>
#include <stdio.h>

int glob = 6;
char buf[ ] = "a write to stdout\n";

int main()
{
    int var;
    pid_t pid;

    var = 88;

    if (write(STDOUT_FILENO, buf, sizeof(buf) - 1) != sizeof(buf) - 1){
        perror("write error\n");
        return -1;
    }

    printf("before fork\n");

    if ((pid = fork()) < 0){
        perror("fork error\n");
        return -1;
    } else if (pid == 0){
        glob++;
        var++;
    } else {
        sleep (2);
    }

    printf("pid = %d, glob = %d, var = %d\n", getpid(), glob, var);

    return 0;
}

```

执行程序后，得到：

```

[wanghui@centos-6 fork]$ gcc fork.c -o test
[wanghui@centos-6 fork]$ ./test
a write to stdout
before fork
pid = 29264, glob = 7, var = 89    子进程的变量值变了
pid = 29263, glob = 6, var = 88    父进程的变量值没变
[wanghui@centos-6 fork]$ ./test > temp.out
[wanghui@centos-6 fork]$ cat temp.out
a write to stdout
before fork
pid = 29266, glob = 7, var = 89
before fork
pid = 29265, glob = 6, var = 88

```

一般来说，fork之后父进程和子进程哪一个先执行是不确定的，取决于内核的调度算法。故这里父进程sleep2s，让子进程先执行。write函数是不带缓冲的，但是标准I/O是带缓冲的。

* 输出到终端设备是行缓冲。输出条件如下：

    * 调用fflush，刷新缓冲区
    * 缓冲区满了
    * 遇到\n \r这些字符
    * 遇到scanf这些要取缓冲区的
    * 线程或者进程退出

* 输出到非终端设备，如文件，则是全缓冲。线程或者进程退出后，写到相应的文件中。

所以第一次输出到终端控制台，write无缓冲直接输出，printf遇\n行缓冲标识直接输出。但是第二次输出到文件，write无缓冲直接输出，printf成了全缓冲的并没有输出，子进程拷贝了父进程的缓冲区和代码段，所以当父子进程退出时，刷新缓冲区，就有两条printf输出。

### printf几个字符

这在笔试题中很常见。下面的两段程序输出a的个数分别是多少个。

```
for(i = 0; i < 2; i++)
{
      fork();
      printf("a");
}
```


```
for(i = 0; i < 2; i++)
{
      fork();
      printf("a\n");
}
```

第一个输出了输出8个a，第二个输出6个a。
重点一是fork之后创建了几个新进程
![img](/img/2016/05/fork()流程图.png)

重点二是printf把a放入了缓存中，并没有输出。所以子进程拷贝了父进程的缓冲区，

### 参考资料

[博客](http://www.cnblogs.com/xiongge/p/3619295.html)