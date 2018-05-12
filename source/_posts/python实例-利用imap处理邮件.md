---
title: '[python实例] - 利用imap处理邮件'
date: 2018-05-10 13:09:05
tags:
  - python
---

> 其实，这里不只是有IMAP~

之所以选择IMAP，因为它能在本地对远程服务器做修改，而POP不能。
至于IMAP和POP之间的区别，我将在后面的文章中写一篇详尽的说明。

第一步：引入需要的包

```python
import imaplib  # 连接邮件服务器，获取邮件
import email.parser import Parser # 解析邮件
import subprocess # 运行脚本
import threading  # 设置定时器

# 下面两行后面有说明
from commonds import commonds
cmds = commonds()
```

第二步：定义一个开始函数: start
```python
def start():
  user = '******' # 邮箱登录名
  password = '*******'  # 客户端授权码
  connact(user, password) # 进入下一步
```

第三步：定义一个连接函数: connact
```python
def connact(u, p):
  global conn # 定义一个全局变量
  conn = imaplib.IMAP4_SSL(port = '993', host = 'imap.163.com') # 连接网易的邮件服务器
  s, d = conn.login(u, p) # 登录
  s, d = conn.select() # 选择邮件文件夹（默认为收件箱）
  getMails() # 进入下一步
```

第四步：定义一个获取邮件的函数：getMails
```python
def getMails():
  s, msgs = conn.search(None, 'All')  # 获取所有邮件
  msgList = msgs[0].split() # 格式化列表
  lastMsg = msgList[len(msgList)-1] # 最后一封邮件

  data = conn.fetch(lastMsg, '(RFC822)') # 获取邮件内容

  msg = data[1][0][1].decode('utf-8') # 转码
  msg = Parser().parseter(msg) # 解析邮件

  controls = getControls(msg) # 获取邮件的内容

  # 其实这是一个利用邮件控制的电脑的脚本
  # 而这个方法就是根据邮件内容，执行相应命令
  # 命令与执行方法，在commonds.py文件中
  if controls['isControl']:
    controlSomething(controls, lastMsg) # 根据邮件内容执行相应处理
  # 设置定时器，每隔1秒请求新的数据
  timer = threading.Timer(1, getMails)
  timer.start()
```

第五步：定义一个判断命令的函数：getControls
```python
def getControls(msg):
  o = {}
  Flag = False
  value = msg.get('From', '')
  offset = value.find('<')
  if value[offset+1:len(value)-1] == '发送邮件的邮箱地址'
    subj = msg.get('Subject','')
    if subj.find('control:') > -1:
      Flag = True
      o['doWhat'] = subj[3:].strip()
    else:
      Flag = False
  o['isControl'] = Flag
  return o
```
第六步：定义一个执行函数：controlSomething
```python
def controlSomething(params, lMsg):
  doWhat = params['doWhat']
  cmd = cmds[doWhat]
  # 判断是否存在对应的命令以及获取的命令邮件是否被删除（防止重复执行）
  if cmd and deleteMail(lMsg):
    subprocess.Popen(cmd, shell=True) # 执行命令
```
定义删除邮件的函数：deleteMail
```python
def deleteMail(msg):
  conn.store(msg, '+FLAGS', '\\Deleted') # 标记要删除的邮件
  return conn.expunge() # 删除邮件
```

当然还有start函数的执行：
```python
if __name__ == '__main__'
  start()
```

下面简单列一下commonds.py的内容：
```python
# commonds.py
def commonds():
  return {
    'playMusic': 'start orpheus:',
    'stopMusic': 'taskkill /f /t /im cloudmusic.exe',
    'shutdown': 'shutdown -s -t 0 -f'
  }
```

不用看了，就这些了。