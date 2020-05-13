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

  # email



```python
from email.header import Header
from email.mime.text import MIMEText
from smtplib import SMTP_SSL

from config import Config

# 邮件发送
class SendEmail:
    def __init__(self):
        # 邮箱smtp服务器
        self.host_server = 'smtp.troila.com'
        # 发件人使用的邮箱
        self.sender_email = 'dk_rwfp@troila.com'
        # 邮箱的密码
        self.pwd = 'Zl20190520'
        # 指定发件人的邮箱
        self.sender_from_mail = 'dk_rwfp@troila.com'

    def send(self, emails, mail_title, mail_content):
        # 收件人邮箱
        receivers = emails
        # 邮件的正文内容
        mail_content = mail_content
        # 邮件标题
        mail_title = mail_title

        # ssl登录
        smtp = SMTP_SSL(self.host_server)
        smtp.set_debuglevel(0)
        smtp.ehlo(self.host_server)
        smtp.login(self.sender_email, self.pwd)
        msg = MIMEText(mail_content, "html", 'utf-8')
        msg["Subject"] = Header(mail_title, 'utf-8')
        msg["From"] = self.sender_from_mail
        msg["To"] = ",".join(receivers)
        smtp.sendmail(self.sender_from_mail, receivers, msg.as_bytes())
        smtp.quit()

    # 发送邮件
    def send_email(self, to_adsdrs, title, wb_Bytes,
                   attachment_name):
        print('进行邮件整理')
        import smtplib
        mailserver = smtplib.SMTP_SSL(self.host_server)
        from email.mime.multipart import MIMEMultipart
        msg = MIMEMultipart()
        msg['From'] = self.sender_from_mail
        msg['To'] = ",".join(to_adsdrs)
        msg['Subject'] = Header(title, 'utf-8').encode()

        # 设置附件的MIME和文件名，这里是png类型:
        from email.mime.base import MIMEBase
        mime = MIMEBase('application', "octet-stream", filename=attachment_name)
        # 加上必要的头信息:
        mime.add_header('Content-Disposition', 'attachment', filename=attachment_name)
        # mime.add_header('Content-ID', '<0>')
        # mime.add_header('X-Attachment-Id', '0')
        # 把附件的内容读进来:
        mime.set_payload(wb_Bytes)
        # 用Base64编码:
        from email import encoders
        encoders.encode_base64(mime)
        # 添加到MIMEMultipart:
        msg.attach(mime)

        mailserver.set_debuglevel(1)
        print('准备进行登录')
        mailserver.login(self.sender_email, self.pwd)
        print('登录成功,准备进行邮件发送')
        mailserver.sendmail(self.sender_email, to_adsdrs, msg.as_string())
        mailserver.quit()

        print('发送成功！')

        
SendEmail().send(["xx@xx.com"], "title", "content")
```

