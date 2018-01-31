```python
def sendmail():
    import  smtplib
    from email.mime.text import MIMEText
    from email.utils import formataddr
    msg = MIMEText('邮件内容','plain','utf-8')
    msg['From'] = formataddr(['bob','liangml0418@163.com'])
    msg['To'] = formataddr(["走人",'1729562020@qq.com'])
    msg['Subject'] = '主题'
    server = smtplib.SMTP('smtp.163.com',25)
    server.login('liangml0418@163.com','邮箱密码')
    server.sendmail('liangml0418@163.com',['1729232020@qq.com'],msg.as_string())
    server.quit()
sendmail()
```
