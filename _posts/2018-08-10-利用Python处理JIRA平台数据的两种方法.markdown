---
layout: post
title: 利用Python处理JIRA平台数据的两种方法
date: 2018-8-10 11:11:11.000000000 +08:00
tags: Python
---

在公司实习期间收到一个小任务要求从公司内部的JIRA平台上抓取一些信息并且做一些处理。  

以下是两种抓取方式：

### 方法一：爬虫

##### 模拟登录

首先在浏览器中打开相关的JIRA的登录页面，使用浏览器的inspect功能查看Network标签。可以在login.jsp的Headers标签找到登录所需要的Form Data。我们公司的是这样的格式的（所有JIRA的登录都大同小异）：

```
os_username: # 登录用户名
os_password: # 登录密码
os_destination: 
user_role: 
atl_token: 
login: Log In
```

于是就利用urllib的urlencode来记录这些数据：

```python
# POST data
postdata = urllib.urlencode({
	'os_username' : usrname,
	'os_password' : pwd,
	'os_cookie' : 'true',
	'os_destination' : '',
	'user_role' : '',
	'atl_token' : '',
	'login' : 'Log In',
	})
```

用户名可以选择用`raw_input()`函数来获取，密码可以用Python的`getpass`包的`getpass.getpass()`来解决。当然，如果这个程序只是自己使用直接在程序里写入就可以了。

所以具体的登录操作如下：

```python
import urllib
import urllib2
import cookielib

# Get username and password from input
usrname = raw_input("Username:")
pwd = getpass.getpass("Password:")

# POST data，这里具体填什么根据之前inspect查到的
postdata = urllib.urlencode({
	'os_username' : usrname,
	'os_password' : pwd,
	'os_cookie' : 'true',
	'os_destination' : '',
	'user_role' : '',
	'atl_token' : '',
	'login' : 'Log In',
	})

# Initialize a CookieJar to handle Cookie message
cookie = cookielib.CookieJar()

# Initialize a new opener to make use of the CookieJar
opener = urllib2.build_opener(urllib2.HTTPCookieProcessor(cookie))

# Initialize a request
req = urllib2.Request(
	url = '登录JIRA的URL',
	data = postdata
	)

# Access the login url
res = opener.open(req)

if res.code == 200:
    print("Login success!")
```

如果res的code值为200就说明登录成功啦！接下来就能根据工单号来做数据分析了。

##### 数据分析

数据分析主要方法是打开网页源代码，查看需要的元素，使用正则表达式筛选处理数据后输出。

这里主要使用了re这个包来处理数据。但是由于本人正则表达式学的并不是很好，所以这里就不过多赘述了。

给个学习正则表达式的[链接](http://www.zjmainstay.cn/regexp-one)，希望能够有所帮助。

如果想要记录下分析的数据，可以使用csv这个包，把数据写入csv文件后利用Microsoft Excel进行进一步处理和分析。

##### 爬虫法总结

爬虫法是一个比较麻烦的方法，由于获取数据的方式比较原始，所以需要比较高的学习成本来解决数据分析的问题，并且由于不同网站的数据存在差异，所以需要人工的（查看网页源码）进行数据排查。但是爬虫法一旦熟练，所有类型的网站的数据分析都会比较容易上手，并且学习爬虫法的同时也能对正则表达式有更好的了解。

### 方法二：JIRA API

爬虫法以后，我发现利用正则表达查找数据实在是太麻烦，于是继续寻求解决方案。

最后发现JIRA方面其实是提供了一个名为`jira`的库的，这样一来，事情就方便多了。

先上两个文档的链接：[JIRA API](https://docs.atlassian.com/jira/REST/cloud/)、[Python JIRA](http://jira.readthedocs.io/en/master)

接下来是一些步骤：

##### 安装

安装很简单，在terminal用pip安装就好：

```shell
pip install jira
```

##### 使用账号密码登录

和爬虫法类似，用户名密码可以利用`raw_input()`和`getpass.getpass()`输入解决或者自行定义变量：

```python
# Login setup
jira_server = 'JIRA的URL'
jira_username = raw_input('Username:')
jira_password = getpass.getpass('Password:')

# Establish JIRA connection
myjira = JIRA(jira_server,basic_auth=(jira_username,jira_password))
```

利用JIRA Query Language(JQL)筛选想要查询的issue。JQL的表达可以看[链接](https://confluence.atlassian.com/jiracore/blog/2015/07/search-jira-like-a-boss-with-jql)，也可以在JIRA的Search for issues上筛选后点击advance后复制相应的JQL表达式进行筛选。

```python
issues = myjira.search_issues('JQL表达式放这',maxResults = 200000)# maxResults是最多的issue的数量，根据自己需求填写。
```

接下来对于筛选下来的数据，根据JIRA API的成员的访问方式进行调取、分析和输出即可。



### 参考

+ [Python学习笔记（四）--简单的爬虫](https://blog.csdn.net/iov_aaron/article/details/44923905)
+ [Python链接JIRA增删改查issue](https://blog.csdn.net/weixin_38208401/article/details/76161487)
+ [正则表达式入门和高级教程与在线测试工具](http://www.zjmainstay.cn/regexp-one)
+ [JIRA API](https://docs.atlassian.com/jira/REST/cloud/)
+ [Python JIRA](http://jira.readthedocs.io/en/master)