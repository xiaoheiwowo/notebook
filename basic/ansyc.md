并发 同一时间段执行多个任务，同一时刻只有一个任务运行

并行 同时又多个程序运行

同步 在IO操作中等待操作完成后返回

异步 在IO操作中不等待操作完成立即返回

阻塞 在IO操作等待是线程挂起

非阻塞 在IO操作等待是线程保持运行，通过轮询查询操作状态

---



阻塞IO python 默认的socket 的 connect，send，recv都是阻塞的

非阻塞IO

信号驱动式IO

IO多路复用 select + callback + event_loop

异步IO 由操作系统将数据从内核拷贝到用户空间



---

```js
let promise = new Promise(function (resolve, reject){
    // 方法同步执行
    if () {
        resolve(value) // 执行后then中的方法异步执行，加到微任务队列中
        } else {
            reject(error)
        }
})

promise.then(function(a){}, function(b){})

// 执行优先级：同步代码 > promise > setTimeout
```

```js
// async await 封装 promise
async function a_fun() {
    let data = await get_url() // 异步执行 得到的数据不能返回到当前”线“程
    console.log(data)
}

a_fun()
```

