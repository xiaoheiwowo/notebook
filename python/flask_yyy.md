```python
import threading
from flask import jsonify

from micro_service_response_tool.enum import ResponseCode


class ResponseTool(object):
    _instance_lock = threading.Lock()

    def __init__(self, app=None, db=None):
        if app:
            self.app = app
        if db:
            self.db = db

    def init_app(self, app, db):
        self.app = app
        self.db = db

    def __new__(cls, *args, **kwargs):
        if not hasattr(ResponseTool, "_instance"):
            with ResponseTool._instance_lock:
                if not hasattr(ResponseTool, "_instance"):
                    ResponseTool._instance = object.__new__(cls)
        return ResponseTool._instance


def get_response(code, msg='', data=None):
    response = jsonify({'code': code, 'msg': msg, 'data': data})
    response.status_code = 200
    return response


def succeed(message='', data=''):
    return get_response(ResponseCode.success.value, message, data)


def custom(code=-1, msg='', data=''):
    ResponseTool().db.session.rollback()
    return get_response(code, msg, data)


def custom_with_code_error(code_error):
    try:
        res = custom(code=code_error.code, msg=code_error.message)
    except Exception as e:
        ResponseTool().db.session.rollback()
        res = server_error()
    return res


def server_error():
    return custom(-1, '服务器异常，请稍后重试')


def usually(message='', data=''):
    try:
        ResponseTool().db.session.commit()
    except Exception as e:
        print(e)
        ResponseTool().db.session.rollback()
        return server_error()
    return succeed(message, data)


def usually_with_callback(message='', data='', callback=None, param=()):
    try:
        ResponseTool().db.session.commit()
    except Exception as e:
        print(e)
        ResponseTool().db.session.rollback()
        return server_error()
    if callback:
        return succeed(message, callback(*param))
    else:
        return succeed(message, data)
```





```python
import json
import requests
from code_message_exception import CodeMessageException
from micro_service_request_tool.enum import MicroServiceRequestCode


class RequestTool:

    def __init__(self, app=None, server_name=None, authentication=None):
        self.app = app
        self.gateway = None
        self.server_name = server_name
        self.authentication = authentication
        if app is not None:
            self.init_app(app)

    def init_app(self, app, server_name, authentication):
        self.gateway = app.config['MICRO_SERVICE_REQUEST_TOOL_GATE_WAY']
        self.server_name = server_name
        self.authentication = authentication

    def get_host(self):
        return self.gateway + '/' + self.server_name

    def get_url(self, path):
        return self.get_host() + '/' + path

    def get_headers(self):
        return {"Authorization": self.authentication}

    def response_handler(self, res):
        if res.status_code == 200:
            try:
                json_res = json.loads(res.text)
            except Exception as e:
                raise CodeMessageException(MicroServiceRequestCode.parser_error.value, '解析异常')
            if 'code' in json_res and 'msg' in json_res:
                if json_res['code'] == 200:
                    return json_res
                else:
                    raise CodeMessageException(json_res['code'], json_res['msg'], json_res)
            else:
                raise CodeMessageException(MicroServiceRequestCode.parser_error.value, '解析异常')
        else:
            raise CodeMessageException(MicroServiceRequestCode.net_error.value, '网络异常')

    def get(self, path, param):
        res = requests.get(self.get_url(path), param, headers=self.get_headers())
        return self.response_handler(res)

    def post(self, path, data=None, json=None):
        res = requests.post(self.get_url(path), data, json, headers=self.get_headers())
        return self.response_handler(res)
```



```python
import threading

class Healthy(object):
    _instance_lock = threading.Lock()

    def __init__(self, app=None):
        if app:
            self.app = app

    def init_app(self, app):
        self.app = app
        from . import healthy


    def __new__(cls, *args, **kwargs):
        if not hasattr(Healthy, "_instance"):
            with Healthy._instance_lock:
                if not hasattr(Healthy, "_instance"):
                    Healthy._instance = object.__new__(cls)
        return Healthy._instance
# healty
from flask import Response
from . import Healthy

app = Healthy().app
@app.route("/healthy")
def healthy():
    return Response("hello")
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

```python
import base64
import hashlib
import json
from datetime import datetime
from Crypto.Cipher import AES
from flask import request, current_app, make_response
from werkzeug.datastructures import MultiDict


def auto_fill_plaintext(x):
    while len(x) % 16 != 0:
        x += '\0'
    return x.encode()


def push_md5(s):
    if isinstance(s, str):
        m = hashlib.md5(s.encode())
    else:
        m = hashlib.md5(s)
    return m.hexdigest()


def get_gkey():
    return current_app.config['REQUEST_ENCRYPTION_KEY']


def AES128( text):
    key = get_gkey().encode("utf8")
    BS = AES.block_size
    pad = lambda s: s + (BS - len(s) % BS) * chr(BS - len(s) % BS)
    aes = AES.new(key, AES.MODE_ECB)  # ECB模式
    res = aes.encrypt(auto_fill_plaintext(pad(text)))
    res = base64.encodebytes(res)
    res = res.decode('utf-8')
    res = res.replace('\n', '')
    return res


def AES128DE(text):
    key = get_gkey().encode("utf8")
    def unpad(s): return s[0:-ord(s[-1])]
    cipher = AES.new(key, AES.MODE_ECB)
    decryptByts = base64.decodebytes(text.encode())
    msg = cipher.decrypt(decryptByts)
    msg = msg.decode()
    paddingLen = ord(msg[len(msg) - 1])
    return msg[0:-paddingLen]


class EncryptionTool:
    def __init__(self, app=None):
        self.app = app
        self.gkey = None
        if app is not None:
            self.init_app(app)

    def init_app(self, app):
        self.app = app
        self.gkey = app.config['REQUEST_ENCRYPTION_KEY']

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

        @app.after_request
        def after_request(response):
            if 'Encryption' in request.headers and request.headers['Encryption'] in ['1',
                                                                                     '2'] and response.status_code == 200:
                res = AES128(response.data.decode('utf-8'))
                response.data = res.encode()
                response.content_type = 'text/html; charset=utf-8'
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
            return response
```