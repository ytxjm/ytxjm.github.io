---
layout: post
title: Socket 中Select(I/O复用)使用中应注意的地方
tags: Socket
math: true
date: 2017-09-04  15:32 +0800
---



最近在写进程间通信的模块,其中用到Select.它的好处在于可以在一个线程之内监听若干个socket或文件描述符的可读写或异常状态,在监听数量不多的情况下有不错的效率.函数接口如下:
``` c++
int select(int nfds, 
           fd_set *readfds, 
           fd_set *writefds, 
           fd_set *fd_set *exceptfds, 
           struct timeval *timeout);
```

       1.select函数在使用时,每一次都必须将它的三个集合清空重新设置;
       2.nfds 为最大的描述符的值加1;
       3.timeout 为NULL 表示会一直阻塞直到有读写或异常，如果结构体中的数值为0,select则立刻返回;
       4.返回0,表示超时;-1,表示失败;>0,则表示有读写或异常状态；
       5.如果正监听的描述符在另一个线程中被关闭了,会产生不可预期的结果,所以描述符的管理最好放在一个线程中;
       6.如果一个描述符已经被自身关闭了,然后再把它加入到监听集合中,那select会立马返回-1;
       7.如果一个描述符已经被对方关闭了,然后再把它加入到监听集合中,那Select会立马返回0,告知readset中有可读信息,recv函数返返回0,说明对方已经下线;

