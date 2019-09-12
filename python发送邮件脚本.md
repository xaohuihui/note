## 邮件发送脚本

```python
#coding:utf-8
# #!/usr/bin/python
import smtplib ,os
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from dotenv import load_dotenv, find_dotenv
load_dotenv(find_dotenv())
'''
SERVER_EMAIL = 'pachong321@163.com'
# Mailserver configuration EMAIL_HOST = "smtp.163.com"
EMAIL_PORT = 25
EMAIL_HOST_USER='pachong321@163.com'
EMAIL_HOST_PASSWORD='pachong'
'''
def send_mail(sub, content, status = False, filename = ''):  #to_list：收件人；sub：主题；content：邮件内容
    message = MIMEMultipart()  # mailto_list=["1035332816@qq.com", "15537770552@163.com"]#此处设置 要接受通知邮件的邮箱地址
    mailto = os.environ.get('sjryx')
    mailto_list = mailto.split(',')  # 此处设置 要接受通知邮件的邮箱地址
    mail_host = "smtpdm.aliyun.com"  # 设置服务器
    mail_user = "alert@mail.epmap.org"  # 用户名
    mail_pass = "ALERTalert123"  # 口令
    mail_postfix = "epmap.org"  # 发件箱的后缀
    message['To'] = ",".join(mailto_list) #收件邮箱地址
    message['From'] = 'epmap:sd_credit' #发件人详情
    message['Subject'] = sub  #主题
    message.attach(MIMEText(content, 'html', 'utf-8')) #正文内容
    if status:         # 构造附件，传送当前目录下的 test.txt 文件
        fileatt = MIMEText(open(filename, 'rb').read(), 'base64', 'gb2312')
        fileatt["Content-Type"] = 'application/octet-stream'         		# 这里的filename可以任意写，写什么名字，邮件中显示什么名字
        fileatt["Content-Disposition"] = 'attachment; filename="' + filename + '"'
        message.attach(fileatt)
    try:
        s = smtplib.SMTP() #创建邮件发送实例
        s.connect(mail_host)  #连接smtp服务器
        s.login(mail_user,mail_pass)  #登陆服务器
        me = "<"+mail_user+"@"+mail_postfix+">"
        s.sendmail(me, mailto_list, message.as_string()) #发送邮件
        s.close()
        print '邮件发送成功'
        return True
    except Exception, e:
        print '邮件发送失败', str(e)
        import traceback
        traceback.print_exc()
        return False

      ```
