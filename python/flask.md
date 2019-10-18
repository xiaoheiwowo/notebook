# Flask

## 运行flask应用配置环境变量


```sh
export FLASK_DEBUG=True
export FLASK_APP=manager.py
flask shell
```


## 使用LocalProxy自定义上下文

```python
from werkzeug.local import LocalProxy


current_employee = LocalProxy(lambda: get_employee())  # type: Employee

def get_employee():
    if has_request_context() and not hasattr(_request_ctx_stack.top, 'employee'):
        return None
    return getattr(_request_ctx_stack.top, 'employee', None)


def login_require(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        employee = Employee.query.filter_by(object_id == request.args.get('employee_id')).first()
        if employee:
            _request_ctx_stack.top.employee = employee
            return func(*args, **kwargs)
        return custom(code=404, msg='404')
    return wrapper

@api.route('/', methods=['GET'])
@login_require
def index():
    # 在接口中可以使用current_employee
    return 'index'
```

## 中间件

```python
# 获取响应时长
@app.before_request
def before_request():
    request.response_start = datetime.now().timestamp()
    if 'Encryption' in request.headers and request.headers['Encryption'] == '2':
        if request.method == 'GET':
            value = request.args.get('v')
            if value:
                data = AES128DE(value)
                requestData = json.loads(data)
                args = MultiDict(requestData)
                request.args = args
        else:
            value = request.form.get('v')
            if value:
                data = AES128DE(value)
                requestData = json.loads(data)
                print(requestData)
                form = MultiDict(requestData)
                request.form = form
```


```python
@app.after_request
def after_request(response):
    if 'Encryption' in request.headers and request.headers['Encryption'] in ['1', '2'] and response.status_code == 200:
        res = AES128(response.data.decode('utf-8'))
        response.data = res.encode()
        response.content_type = 'text/html; charset=utf-8'
    length = int((datetime.now().timestamp() - request.response_start) * 1000)
    if request.method == 'GET' and (response.content_type == 'application/json' or 'Encryption' in request.headers):
        etag_res = "W/" + push_md5(response.data)
        response.headers['ETag'] = etag_res
        # if request.headers['If-None-Match']:
        if 'If-None-Match' in request.headers.keys():
            etag = request.headers['If-None-Match']
            if etag == etag_res:
                res = make_response()
                res.status_code = 304
                return res


    if current_app.config['SQLALCHEMY_RECORD_QUERIES'] and \
    (response.content_type == 'application/json' or response.content_type == 'text/html; charset=utf-8'):
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



## 参数检查

```python
import functools
import datetime
import re
import json

from flask import request

from enum import IntEnum
from micro_service_response_tool import custom


# 校验参数类型
class CheckType(IntEnum):
    int = 0,
    email = 1,
    phone = 2,
    noEmoji = 3,
    json = 4,
    date = 5,
    datetime = 6,
    float = 7,
    password = 8,
    time = 9,
    other = 999,


def check_request_params(**checks):
    """
    传参方法
    args1=('args1', True, CheckType.int),
    args2=('args2', False, CheckType.json),
    :param checks:
    :return:
    """
    def check_request_params_func(func=None):
        @functools.wraps(func)
        def test(*args, **kwargs):
            errors = []
            for key, parm in checks.items():
                name = parm[0]
                if request.method == 'POST':
                    value = request.form.get(key)
                else:
                    value = request.args.get(key)
                required = parm[1]
                check = parm[2]
                error, res = request_params_value_check(name, required, value, check)
                kwargs[key] = res
                if error:
                    errors.append(error)

            if errors:
                return custom(code=777, msg=','.join(errors))
            return func(*args, **kwargs)

        return test

    return check_request_params_func


def request_params_value_check(name, required, value, check):
    res = value
    if required:
        if not value and not isinstance(value, int):
            return "%s不能为空" % name, res
    if check == CheckType.int:
        if value:
            try:
                res = int(value)
            except Exception as e:
                return "%s应为整数" % name, res
    elif check == CheckType.email:
        if value and not re.compile(r'^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$').match(value):
            return "%s应为email格式" % name, res
    elif check == CheckType.phone:
        if value and not re.compile(r"^1\d{10}$").match(value):
            return "%s应为电话号码" % name, res
    elif check == CheckType.noEmoji:
        if value and not re.compile(r'^[a-zA-Z0-9\u4E00-\u9FA5]+$').match(value):
            return "%s包含非法字符" % name, res
    elif check == CheckType.json:
        if value:
            try:
                res = json.loads(value)
            except Exception as e:
                return "%s应为JSON格式" % name, res
    elif check == CheckType.datetime:
        if value:
            try:
                if len(value) == 16:
                    res = datetime.strptime(value, '%Y-%m-%d %H:%M')
                else:
                    res = datetime.strptime(value, "%Y-%m-%d %H:%M:%S")
            except Exception as e:
                return "%s应为datetime:格式" % name, res
    elif check == CheckType.date:
        if value:
            try:
                res = datetime.strptime(value, '%Y-%m-%d').date()
            except Exception as e:
                return "%s应为date格式" % name, res
    elif check == CheckType.time:
        if value:
            try:
                if len(value) == 5:
                    res = datetime.strptime(value, '%H:%M').time()
                else:
                    res = datetime.strptime(value, '%H:%M:%S').time()
            except Exception as e:
                return '%s应为time格式' % name, res
    elif check == CheckType.float:
        if value:
            try:
                res = float(value)
            except Exception as e:
                return '%s应为Float格式' % name, res

    return None, res

```





# FLASK PROJECT



## 项目结构
```html
<dir project_name>blog
    <dir app>app
        <file app_init>__init__.py
        <dir/file models>models.py
        <dir api>api
            <file api_init>__init__.py
            <file view>***.py
            ...
        <dir/file util>***.py
        ...
        <dir test>test
    <dir/file script>script
    <file manager>manager.py
    <file config>config.py
    <file requirements>requirements.py
...
```




## config.py
```python
import os


basedir = os.path.abspath(os.path.dirname(__file__))

class Config:
    # 秘钥
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'asdf;lkj'
    # 配置
    SQLALCHEMY_COMMIT_ON_TEARDOWN = False
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    SQLALCHEMY_POOL_SIZE = 500




# 开发测试环境配置
class DevelopConfig(Config):
    DEBUG = True
    SERVER_URL = "http://221.238.157.245:8039"
    SQLALCHEMY_DATABASE_URI = 'mysql+pymysql://root:dbadmin@172.27.106.1/daka5_microserve?charset=utf8mb4'
    SENTRY_DSN = 'http://f1e82cdb492747fe8a93d434f4da498c:13a83349c62742babce3d5e8d239230d@47.93.42.146:9000/34'
    REDIS_URL = "redis://:troila@172.27.106.3:6379/0"


# 生产环境配置
class ProductionConfig(Config):
    DEBUG = False


config = {
'development': DevelopConfig,
'production': ProductionConfig,
'default': DevelopConfig
}


```

## manager.py


```python
# manager.py
app = create_app(os.getenv('FLASK_CONFIG') or 'default')
manager = Manager(app)
migrate = Migrate(app, db)


sentry = Sentry(app, dsn=app.config['SENTRY_DSN'])
app.sentry = sentry


def make_shell_context():
    return dict(app=app, db=db)


manager.add_command("shell", Shell(make_context=make_shell_context))
manager.add_command('db', MigrateCommand)


if __name__ == '__main__':
    manager.run()
```

## App


```python
# __init__.py(app_init)
from flask import Flask
from flask_redis import FlaskRedis


from config import config
from flask_cors import CORS


db = SQLAlchemy()
redis_store = FlaskRedis()


def create_app_migrate_and_job(config_name):
    app = Flask(__name__)
    app.config.from_object(config[config_name])
    db.app = app
    db.init_app(app)
    redis_store.init_app(app)
    return app



def create_app(config_name):
    app = Flask(__name__)
    CORS(app, supports_credentials=True)
    app.config.from_object(config[config_name])
    db.app = app
    db.init_app(app)
    redis_store.init_app(app)
    from api import api as api_1
    app.register_blueprint(api_1, url_prefix='/')
    # ...
    return app
```

## Api

```python
# __init__.py(api_init)
from flask import Blueprint
api = Blueprint("api", __name__)


from . import view
```


```python
# view
from . import api
@api.route('', methods=['GET'])
def index():
	return 'index'
```

## Script.py


```python
# script.py
from flask_script import Manager
from app import create_app_migrate_and_job, db

sys.path.append(os.path.dirname(__file__) + os.sep + '../')
app = create_app_migrate_and_job('default')
manager = Manager(app)

def progress_wraps(func):
    import functools
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print('开始')
        res = func(*args, **kw)
        length = (datetime.now().timestamp() - start_time)
        print('   最终用时 %f s' % (length))
        return res
    return wrapper


@manager.command
@progress_wraps
def migrate_enterprise_set():
    from app.models.checkset_model import EnterpriseSet
    from app.models.enterprise import Enterprise
    enterprises = Enterprise.query.filter(
        Enterprise.object_id != '',
        Enterprise.object_id != None,
        ).all()
    count = len(enterprises)
    for index, e in enumerate(enterprises):
        print("\r >>> 正在迁移----------------{0}/{1}".format(index + 1, count), end='')
        new_record = EnterpriseSet(
            enterprise_id=e.object_id,
            allow_change_mobile=e.allow_change_mobile,
            allow_wechat_check=e.allow_wechat_check,
            face_input_type=e.face_input_type,
            ban_cheat=e.ban_cheat,
            visibility=e.visibility,
            )
        db.session.add(new_record)
    if index != 0 and index % 1000 == 0:
        db.session.commit()
    db.session.commit() 
```


## 运行

`python3 script.py migrate_enterprise_set`


## 迁移
```sh
python manager.py db init
python manager.py db migrate # 生成迁移脚本，可以手动修改
python manager.py db upgrade/downgrade # 执行迁移
# Tip: 只有第一次需要init，执行前需要删除alembic_version中的版本号
```
