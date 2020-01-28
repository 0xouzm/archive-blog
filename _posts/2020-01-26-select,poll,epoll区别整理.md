---
title: select poll epoll 区别
description: 
date: 2020-01-26 14:18:00
categories: linux
tags: IO多路复用 linux
---



最近一直在思考记录的作用，一直觉得看过、理解和记住会说出来是学习知识不同的阶段，以前总是懒得记，看过看懂了就过了，然后再次碰到又会忘，在这次对IO 多路复用的记录中，深刻感觉记录下的东西，确实是印象更深。所花费的时间和精力所得到的收益>>每次碰到再去google



## select

如果自己实现，会写成while True不断阻塞轮训，有消息就去处理，这样的弊端是用户态判断很慢，
于是select通过把fds映射成bitmap拷贝到内核中，通过linux内核态来判断是否有消息需要处理。
然后把有消息的fd置位（标记），然后select程序返回，主程序轮训哪些置位后进行处理。

重点：把fds映射成bitmap拷贝到**内核态**中

缺点：

1. bitmap只有1024位
2. fdset不可重用
3. 还是有用户态到内核态的开销
4. 返回后还是需要O(n)的遍历


## poll

通过struct 解决select前两个问题
```
struct pollfd{
    int fd;
    short events;
    short revents;
}
```
定义events, 置位revents，使结构体可重用

## epoll
用户态和内核态共享epfd，通过重排把有需要处理的fd返回，
做到O(1)复杂度

> 关于水平触发 level-triggered 与边缘触发 edge-triggered
>顾名思义，边缘触发只在转换时发出信号
>而水平触发，只要fd的读内核缓冲区非空，就**一直**发出可读信号，
>只要fd的内核写缓冲不满，有空间可以写入，就**一直**发出可写信号进行通知
>所以边缘触发需要一次性的把缓冲区的数据读完为止，因此，et需要设置fd为non-block

* 为什么et 需要非阻塞模式

  当et检测到有I/O事件时，通过epoll_wait调用会得到有事件通知的fd，对于每个fd，如可读，则必须将fd一直读到空，让errno返回EAGAIN，否则下次的epoll_wait不会返回余下的数据，会丢掉事件。而如果fd不设置成非阻塞模式，则自然会阻塞在最后一次的读或写操作上。

总的来说，et减少了epoll事件被重复触发的次数，效率比lt高，但是代码稍复杂一些。

select，poll，epoll三者都叫做**IO多路复用**，解决单线程切换上下文的开销，用来处理最耗时且不需要什么计算的大量IO操作（e.g. net socket,file read/write ...)


