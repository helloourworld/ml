---
layout: post
title: 如何用Python发送邮件
category: python email hive
catalog: yes
description: 使用python邮件模块自动发送HTML格式邮件
tags:
    - Python
    - smtplib
    - email
    - bottle
---
## 1 背景介绍

工作当中，每天会有比较大的数据报表的监控，需要手动复制粘贴比较多的内容，重复工作会占用大量的时间，如果实现自动化就能够释放出大量的宝贵时间。因而自动发送邮件项目就诞生啦。

## 2 项目开始

### 2.1 数据源

数据源的稳定是基础前提。本次我们读的数据源在大数据平台上，如何通过python相应接口来读呢？直接上代码：

```Python

# Hive connect Test
import pyhs2

conn = pyhs2.connect(host="NN01.HadoopVM", user='*', password='*', authMechanism="NOSASL")
cur = conn.cursor()
# Show databases
cur.getDatabases()
# Execute query
cur.execute("select * from bank limit 10")
# Return column info from query
print cur.getSchema()
# Fetch table resultsYou can connect to Hive in two modes. Through thrift server and embedded mode.
for i in cur.fetch():
    print i
 ```

很不幸，本司对于python直接访问Hive的接口没有放开。不过，Hive在操作系统的命令是可用的。如下解决方案：

```Python
def run_transfer(data_raw):
    new_data = []
    for i in data_raw:
        read = i.split('\t')
        new_data1 = []
        for j in range(len(read)):
            m = read[j].rstrip('\n')
            new_data1.append(m)
        new_data.append(new_data1)
    return new_data


def run_hive(tablename):
    data_raw = os.popen("""hive -e "
    select  * from %s where dt=%s;"
    """ % (tablename, day_1_ago8)).readlines()
    return run_transfer(data_raw)
```

os.popen() 方法用于从一个命令打开一个管道，并返回一个file read对象, 对其进行读取 read() 的操作可以看到执行的输出。

```Python
>>> z = os.popen('hive -e "select unix_timestamp();"')
>>> z
<open file 'hive -e "select unix_timestamp();"', mode 'r' at 0x7ffff7f2d0c0>
>>> 
Logging initialized using configuration in jar:file:/export/server/apache-hive-1.2.2-bin/lib/hive-common-1.2.2.jar!/hive-log4j.properties
OK
Time taken: 1.404 seconds, Fetched: 1 row(s)

>>> z
<open file 'hive -e "select unix_timestamp();"', mode 'r' at 0x7ffff7f2d0c0>
>>> z.readlines()
['1518144252\n']
```
>> 补充1：os.system()也可以调用系统命令，但是返回只会有返回值（0,1,2）。

```Python
>>> z = os.system('pwd')
/export/grid/05/risklabhome/yulijun1
>>> z
0
```

>> 补充2：commands 模块，可以获得到返回值和输出。

```Python
>>> z = os.system('pwd')
>>> import commands
>>> commands.getstatusoutput('hive -e "select unix_timestamp();"')
(0, '\nLogging initialized...\nOK\n1518145376\nTime taken: 1.265 seconds, Fetched: 1 row(s)')
```

数据源的读取简单介绍到此。

### 2.2 [smtplib模块](https://docs.python.org/3/library/smtplib.html?highlight=smtplib#module-smtplib)

smtplib模块定义了一个SMTP客户端会话对象可以用来发送邮件到任何互联网机SMTP或ESMTP的倾听者守护。SMTP和ESMTP的操作细节，参考RFC 821（简单邮件传输协议）和RFC 1869（SMTP服务扩展）。

smtplib.SMTP(host='', port=0, local_hostname=None, [timeout, ]source_address=None)

SMTP类构造函数，表示与SMTP服务器之间的连接，通过这个连接可以向smtp服务器发送指令，执行相关操作（如：登陆、发送邮件）。所有参数都是可选的。

host：smtp服务器主机名

port：smtp服务的端口，默认是25；如果在创建SMTP对象的时候提供了这两个参数，在初始化的时候会自动调用connect方法去连接服务器。

smtplib模块还提供了SMTP_SSL类和LMTP类，对它们的操作与SMTP基本一致。

smtplib.SMTP提供的方法：

     SMTP.set_debuglevel(level)：设置是否为调试模式。默认为False，即非调试模式，表示不输出任何调试信息。

     SMTP.connect([host[, port]])：连接到指定的smtp服务器。参数分别表示smpt主机和端口。注意: 也可以在host参数中指定端口号（如：smpt.yeah.net:25），这样就没必要给出port参数。

     SMTP.docmd(cmd[, argstring])：向smtp服务器发送指令。可选参数argstring表示指令的参数。

     SMTP.helo([hostname]) ：使用"helo"指令向服务器确认身份。相当于告诉smtp服务器“我是谁”。

     SMTP.has_extn(name)：判断指定名称在服务器邮件列表中是否存在。出于安全考虑，smtp服务器往往屏蔽了该指令。

     SMTP.verify(address) ：判断指定邮件地址是否在服务器中存在。出于安全考虑，smtp服务器往往屏蔽了该指令。

     SMTP.login(user, password) ：登陆到smtp服务器。现在几乎所有的smtp服务器，都必须在验证用户信息合法之后才允许发送邮件。

     SMTP.sendmail(from_addr, to_addrs, msg[, mail_options, rcpt_options]) ：发送邮件。这里要注意一下第三个参数，msg是字符串，表示邮件。我们知道邮件一般由标题，发信人，收件人，邮件内容，附件等构成，发送邮件的时候，要注意msg的格式。这个格式就是smtp协议中定义的格式。

     SMTP.quit()：断开与smtp服务器的连接。

参考如下：

```Python
# send mail!
import smtplib
server = smtplib.SMTP()
server.connect("smtp...local", 25)
server.starttls()
server.login(user, password)
server.sendmail(me, mailto_list, msg.as_string())
server.quit()
```

### 2.3 [email模块](https://docs.python.org/3/library/email.html?highlight=email)

email模块是一个管理电子邮件的库。它是专门设计不做任何发送电子邮件的SMTP（RFC 2821）、NNTP、或者其他服务器上；那些如smtplib和nntplib模块功能。email包试图为RFC兼容的可能，支持RFC 5233和RFC 6532，以及这种MIME相关RFC RFC 2045和RFC 2046和RFC 2047和RFC 2183和RFC 2231中。

```Python
# Email!
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.mime.image import MIMEImage # 发送图片
from email.header import Header
me = "**@**.com>"
mailto_list = ["**@**.com>", "**@**.com>"]
msg = MIMEMultipart('related')
msg['From'] = me
msg['Cc'] =  mailto_list[-1]
msg['To'] = ','.join(mailto_list[:-1])
msg['Subject'] = Header(u"Demo", 'utf-8')
msgtext = MIMEText(html, 'html', 'utf-8') # MIMEText(_text[, _subtype[, _charset]])：MIME文本对象，其中_text是邮件内容，_subtype邮件类型，可以是text/plain（普通文本邮件），html/plain(html邮件),  _charset编码，可以是gb2312等
msg.attach(msgtext)

#构造附件1
att1 = MIMEText(open('**.html', 'rb').read(), 'base64', 'gb2312')
att1["Content-Type"] = 'application/octet-stream'
att1["Content-Disposition"] = 'attachment; filename="**.html"'#这里的filename可以任意写，写什么名字，邮件中显示什么名字
msg.attach(att1)

```

### 2.4 [bottle模块](http://bottlepy.org/docs/dev/stpl.html)

![](/images/logo_nav.png)
本次任务还有一个难点，即发送的是带格式的文本，包括表格，字体设计等，以及会有图片等要求。静态HTML是最初考虑的一个实现版本，但是执行起来时间上浪费不起啊，太多的硬编码。怎么办？然后就想啊，能不能动态生成HTML文件呢？之前有用过Flask等框架，是一个不错的选择，备选。但服务器上安装这样的包，还是比较难，没有root伤不起。好在bottle最轻量级的web框架解决了这个问题。开撸。

感受一下：

```Python
>>> from bottle import template
>>> my_dict={'number': '123', 'street': 'Fake St.', 'city': 'Fakeville'}
>>> template('I live at {{number}} {{street}}, {{city}}', **my_dict)
u'I live at 123 Fake St., Fakeville'
```

就是这么好用。but there is more: any python expression is allowed within the curly brackets as long as it evaluates to a string or something that has a string representation:

```Python
>>> template('Hello {{name.title() if name else "stranger"}}!', name='mArC')
u'Hello Marc!'

# start the expression with an exclamation mark to disable escaping for that expression
>>> template('Hello {{name}}!', name='<b>World</b>')
u'Hello &lt;b&gt;World&lt;/b&gt;!'
>>> template('Hello {{!name}}!', name='<b>World</b>')
u'Hello <b>World</b>!'
```

~~~~HTML
# EMBEDDED PYTHON CODE
% name = "Bob"  # a line of python code
<p>Some plain text in between</p>
<%
  # A block of python code
  name = name.title().strip()
%>
<p>More plain text</p>

# Embedded python code follows regular python syntax, but with two additional syntax rules:
# Indentation is ignored. You can put as much whitespace in front of statements as you want. This allows you to align your code with the surrounding markup and can greatly improve readability.
# Blocks that are normally indented now have to be closed explicitly with an end keyword.

ul>
  % for item in basket:
    <li>{{item}}</li>
  % end
</ul>

% include('header.tpl', title='Page Title')
Page Content
% include('footer.tpl')
~~~

## 3 项目总结

平常的一个发邮件，涉及的内容也是方方面面的，以上内容Get？那么就可以开心地定制属于你的报告模板了。

其实还有一些小的细节需要注意，比如，怎么增加HTML的格式？如何应用CSS来添加样式？

以下是一个table.css的样式，供参考：

~~~

table.hovertable {
	font-family: verdana,arial,sans-serif;
	font-size:8px;
	color:#333333;
	border-width: 0px;
	border-color: #13180e;
	border-collapse: collapse;
}
table.hovertable th {
	background-color:#c3dde0;
	border-width: 0px;
	padding: 4px;
	border-style: solid;
	border-color: #a9c6c9;
}
table.hovertable tr {
	background-color:#d4e3e5;
}
table.hovertable td {
	border-width: 0px;
	font-size:4px;
	padding: 4px;
	border-style: solid;
	border-color: #13180e;
}
~~~

以下是对某一DF写出HTML表格的脚本，请参考：

~~~Python
% for subtitle, theads,items  in reports:
<p font-size:6px>{{subtitle}}</p>
<table class="hovertable" border="1" bordercolor="black" cellspacing="0">
% for i1, i2, i3, i4, i5,i6,i7,i8,i9,i10, i11, i12,i13,i14,i15 in theads:
<tr>
<th>{{i1.strip()}}</th>
<th>{{i2.strip()}}</th>
...
</tr>
%end
% for i1, i2, i3, i4, i5,i6,i7,i8,i9,i10, i11, i12,i13,i14,i15 in items:
<tr onmouseover="this.style.backgroundColor='#ffff66';" onmouseout="this.style.backgroundColor='#d4e3e5';">
% i2 = '{:,}'.format(int(i2))
% i3 = '{:,}'.format(int(i3))
<td>{{i1.strip()}}</td>
<td>{{i2.strip()}}</td>
...
</tr>
%end
</table>
%end
~~~

> 补充3: 增加包查询路径，前文提到，没有root权限，那怎么引用bottle呢？虽然bottle只有一个文件，但是不是放在同一目录下的话，也是需要指定的。如下为在.bashrc下增加PATHPATH包查询路径，请参照：

```vim
# User specific aliases and functions
export PYTHONPATH=$PYTHONPATH:"$HOME/bottle"
```

看似容易，执行难，如果有问题，请咚咚我即可。