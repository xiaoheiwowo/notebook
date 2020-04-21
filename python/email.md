```python

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

    def send(self, email, mail_title, mail_content):
        # 收件人邮箱
        receiver = email
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
        msg["To"] = receiver
        smtp.sendmail(self.sender_from_mail, receiver, msg.as_bytes())
        smtp.quit()

    # 发送邮件
    def send_email(self, to_addr, title, wb_Bytes,
                   attachment_name):
        print('进行邮件整理')
        import smtplib
        mailserver = smtplib.SMTP_SSL(self.host_server)
        from email.mime.multipart import MIMEMultipart
        msg = MIMEMultipart()
        msg['From'] = self.sender_from_mail
        msg['To'] = to_addr
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
        mailserver.sendmail(self.sender_email, [to_addr], msg.as_string())
        mailserver.quit()

        print('发送成功！')

```

