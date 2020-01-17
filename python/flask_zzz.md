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

