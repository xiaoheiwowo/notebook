## 阻塞模型一般过程

- 创建socket，发生bind, listen, accept
- 进程进行一次阻塞调用recv，将该进程放到等待队列
- 当网卡接收数据，发生中断，中断处理将进程放回就绪队列
- 进程执行时可以读取数据



## select

遍历读列表，写列表，异常列表

## epoll



```
epoll_create, epoll_ctl, epoll_wait
双向列表保存就绪socket
红黑树保存所有socket （通过ctl建立eventpoll对象与socket的关系）
回调函数的功能：1将socket放到rdlist，2将eventpoll的等待队列里的进程放到就绪队列
回调函数是当中断处理时调用的
epoll_wait ：检查rdlist是否有对象，如果是空，阻塞进程（进程放到eventpoll对象的等待队列里）
```

