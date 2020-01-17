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

