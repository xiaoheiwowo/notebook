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





### JsonMixin

```python
class JSONBaseMixin(object):
    def to_json(self, exclude_list=()):
        d = {}
        d['object_name'] = self.__class__.__name__
        for col in self.__table__.columns:

            col_name = col.name

            if issubclass(self.__class__, BaseNestedSets):
                if col_name == 'lft':
                    col_name = 'left'
                if col_name == 'rgt':
                    col_name = 'right'

            if col_name in exclude_list:
                continue

            value = getattr(self, col_name)
            if value is None:
                value = ""
            else:
                if isinstance(col.type, DateTime):
                    value = value.strftime('%Y-%m-%d %H:%M:%S')
                elif isinstance(col.type, Date):
                    value = value.strftime('%Y-%m-%d')
                elif isinstance(col.type, Time):
                    value = value.strftime('%H:%M:%S')
                elif isinstance(col.type, db.DECIMAL):
                    value = "%.1f" % value
            d[col_name] = value
        return d
```



```python
from datetime import datetime

from flask import request
from sqlalchemy import func
from sqlalchemy.ext.declarative import declared_attr


class ApiDurationTool(object):
    def __init__(self, app=None, db=None):
        if app:
            self.app = app
        if db:
            self.db = db

    def init_app(self, app, db):
        self.app = app
        self.db = db

        class ApiDurationRecord(db.Model):
            import time

            create_time = db.Column(db.DateTime, server_default=func.now(), default=datetime.now, index=True)
            timestamp = db.Column(db.Integer, default=time.time, index=True)
            is_valid = db.Column(db.Boolean, default=True, index=True)
            update_time = db.Column(db.DateTime, server_default=func.now(), default=datetime.now, onupdate=datetime.now,
                                    index=True)
            object_id = db.Column(db.Integer, primary_key=True, nullable=False)
            path = db.Column(db.String(64), index=True)
            query_count = db.Column(db.Integer, index=True)
            duration = db.Column(db.Float, index=True)

            @declared_attr
            def __tablename__(cls):
                return cls.__name__.lower()

        @app.after_request
        def after_request(response):

            from flask import current_app
            if current_app.config['SQLALCHEMY_RECORD_QUERIES'] and \
                    (
                            response.content_type == 'application/json' or response.content_type == 'text/html; charset=utf-8'):
                from flask_sqlalchemy import get_debug_queries
                querys = get_debug_queries()
                count = len(get_debug_queries())
                if count > 0:
                    d = 0
                    for query in querys:
                        d = d + query.duration
                    print("查询次数与时间", count, d)
                    record = ApiDurationRecord(path=request.path, query_count=count, duration=d)
                    try:
                        db.session.add(record)
                        db.session.commit()
                    except Exception as e:
                        db.session.rollback()

            return response
```



## Flask-ES

```python
# flask 使用 es
from flask_elasticsearch import FlaskElasticsearch
from elasticsearch_dsl.connections import connections
from elasticsearch_dsl import Document, Date, Integer, Keyword, Text

# models
class Example(Document):
    date = Date()
    str = Text()
    int = Integer()

    class Index:
        name = 'example'
        settings = {
          "number_of_shards": 2,
        }

    def save(self, ** kwargs):
        return super(Example, self).save(** kwargs)

# create
e = Example()
e.date = datetime.now()
e.str = ""
e.int = 33
e.save()

# search
e = Example.search().query(
    "range", **{"date":{"gte", start, "lte": end}}
).query( 
    "match_phrase", str=""
).sort("-date").to_dict()

# run
es = FlaskElasticsearc()
app = Flask(__name__)
es.init_app(app)
connections.create_connection(host="localhost:9100")

app.run
```



## ES Api