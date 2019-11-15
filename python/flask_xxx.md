## 定时任务 flask_apscheduler

```python
from flask import Flask, request
from flask_apscheduler import APScheduler

app = Flask(__name__)
scheduler = APScheduler()

scheduler.init_app(app)
scheduler.start()


def task1(a, b):
    print('mession1')

def task2(a, b):
    print('mession2')

# 添加定时任务
# trigger='cron' 表示是一个定时任务
scheduler.add_job(
    func=task1, 
    id='1', 
    args=(1, 2), 
    trigger='cron', 
    day_of_week='0-6', 
    hour=0, 
    minute=0,
    second=15,
    replace_existing=True
)
# trigger='interval' 表示是一个循环任务，每隔多久执行一次
scheduler.add_job(
    func=task2,
    id='2',
    args=(1, 2),
    trigger='interval',
    seconds=10,
    replace_existing=True
)

if __name__ == "__main__":
    app.run()

```

## 时区管理 flask_moment

```python
from flask_moment import Moment
moment = Moment()
moment.init_app(app)
```

## 跨域资源分享 flask_cors（Cross-Origin Resource Sharing）

- 同源策略

    同源策略会阻止一个域的javascript脚本和另外一个域的内容进行交互。所谓同源（即指在同一个域）就是两个页面具有相同的协议（protocol），主机（host）和端口号（port）

```python
from flask_cors import CORS
CORS(app=app, supports_credentails=True)
app.run()
```

```python
# 阻止跨域是浏览器的限制，在Headers里加入Access-Control-Allow-Origin 等参数可以通过跨域
@app.after_request
def af_request(resp):     
    """
    #请求钩子，在所有的请求发生后执行，加入headers。
    :param resp:
    :return:
    """
    resp = make_response(resp)
    resp.headers['Access-Control-Allow-Origin'] = '*'
    resp.headers['Access-Control-Allow-Methods'] = 'GET,POST'
    resp.headers['Access-Control-Allow-Headers'] = 'x-requested-with,content-type'
    return resp
```

