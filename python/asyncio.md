```python3
loop = asyncio.get_event_loop()
tasks = [] # 异步任务列表
loop.call_at(callback)
loop.call_soon()
loop.call_later()
loop.call_soon_threadsafe()
# loop.run_until_complete(asyncio.wait([tasks]))
loop.run_forever()

# 将阻塞业务放到线程中执行
executor = ThreadPoolExecutor(10)
loop.run_in_executor(executor, get_url, "args")
```



协程有独立的寄存器上下文和栈空间。协程调度切换时，将寄存器上下文和栈保存，保存现场。等待下次调度回当前协程是在讲寄存器和栈恢复，程序又进入之前的控制流位置。

- 协程的优点
    - 无需线程上下文切换，无需系统调度，提高性能，由程序员担任调度
    - 无需加锁，因为实际是单线程的运行
    - 方便切换控制流，简化编程模型
    - 高并发 高扩展 低成本 一个cpu可支持上万协程，
- 缺点
    - 无法利用多核资源，因为本质是单线程程序
    - 如果发生阻塞操作，会阻塞整个程序

python通过生成器实现对协程的支持。yield

理解

yield返回一个值，并保存当前状态，交出程序控制权

next(g) 相当于重新启动协程，恢复程序状态。





async & await

使用async可以定义协程对象，使用await可以将异步的耗时的操作挂起（yield）当前函数（协程）交出控制权。

协程遇到await，事件循环会挂起当前协程，（启动await后的异步任务）由程序调度执行别的协程，直到其他的协程挂起或者执行完毕，再切换协程。当await后的异步任务完成时，await所在的协程就可以被继续执行了。



future & 回调

 

