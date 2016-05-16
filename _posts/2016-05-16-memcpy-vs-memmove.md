---
layout: post
title: 'memcpy() vs memmove()'
date: '2016-05-16'
header-img: "img/home.jpg"
tags:
     - C/C++
author: 'Kingback'
---

memcpy没有做源内存块和目的内存块的地址重合处理，但是memmove做了这样的处理。所以memmove会多一次地址比较和一个比较分支。因此，在拷贝比较大的内存时，memcpy优势不明显，但是如果是频繁的拷贝小内存的时候，memcpy的速度就很快。所以不能因为memcpy没有做一个判断处理，就不用memcpy，有利有弊。

下面是实现一个安全的memcpy函数

```

void *kingmemcpy(void *dst, void *src, int n)
{
    assert(dst != NULL && src != NULL);
    
    char *pdst = (char*)dst;
    char *psrc = (char*)src;

    //没有内存重叠
    if (pdst <= psrc || pdst > psrc + n)
    {
        while (n)
        {
            *pdst = *psrc;
            psrc++;
            pdst++;
            n--;
        }
    }
    else
    {
        pdst = pdst + n - 1;
        psrc = psrc + n - 1;

        while (n)
        {
            *pdst = *psrc;
            psrc--;
            pdst--;
            n--;
        }
    }

    return pdst;
}

```

### 参考资料

[stackoverflow](http://stackoverflow.com/questions/4415910/memcpy-vs-memmove)