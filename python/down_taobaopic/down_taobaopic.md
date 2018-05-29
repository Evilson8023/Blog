# python3.6+win10 淘宝图片爬虫+pyinstaller将python程序打包成exe可执行程序
## 爬虫需求产生
一妹子打算进军淘宝直播，为了给妹子找灵感，遂写了该爬虫，又考虑到妹子不懂python，也不想配置python环境，于是将程序打包成Windows下的可执行程序。

---

## 爬虫代码实现 

```
# -*- coding: utf-8 -*-
# @Author: Evilson
# @Date:   2018-05-15 16:50:25
# @Last Modified by:   Evilson
# @Last Modified time: 2018-05-17 22:03:43

from urllib import parse
from urllib import request
import random
import re
import os

def picdown(piclist,titlelist,headers,fname):
	filepath = os.path.abspath('.')
	#判断image文件夹是否存在，不存在则创建
	if not os.path.exists(fname):
	    os.makedirs(fname)
	for i, url in enumerate(piclist):
		#异常处理及多线程编程方式
		picurl = "http://" + url
		try:
			rq = request.Request(picurl,headers = headers)
			response = request.urlopen(rq)
			f = open(filepath+ "\\%s\\"%fname + titlelist[i] +".jpg", 'wb')
			f.write(response.read())
			f.close()
		except Exception as e:
			print(url + " error:",e)
			continue
	print("宝贝下载完成！")

def rehtml(html,headers,q):
	# 创建正则表达式规则对象，匹配每页里的段子内容，re.S 表示匹配全部字符串内容
	pattern = re.compile('g_page_config =(.*?)"words"', re.S)

	# 将正则匹配对象应用到html源码字符串里，返回这个页面里的所有段子的列表
	content_list = pattern.findall(html)

	#标题地址
	patterntitle = re.compile('"raw_title":"(.*?)","pic_url"', re.S)
	#图片地址
	patternpic = re.compile('"pic_url":"//(.*?)","detail_url"', re.S)

	titlelist = patterntitle.findall(content_list[0])
	piclist = patternpic.findall(content_list[0])
	
	# print(titlelist)
	picdown(piclist,titlelist,headers,q)

def gethtml(url,q):
	ua_list=[
		"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/535.1 (KHTML, like Gecko) Chrome/14.0.835.163 Safari/535.1",
		"Mozilla/5.0 (Windows NT 6.1; WOW64; rv:6.0) Gecko/20100101 Firefox/6.0",
		"Opera/9.80 (Windows NT 6.1; U; zh-cn) Presto/2.9.168 Version/11.50",
		"Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; InfoPath.3; .NET4.0C; .NET4.0E)",
		"Mozilla/5.0 (Windows NT 6.1) AppleWebKit/535.1 (KHTML, like Gecko) Chrome/13.0.782.41 Safari/535.1 QQBrowser/6.9.11079.201"
	]
	ua=random.choice(ua_list)
	headers={
		"User-Agent": ua,
	}
	rq = request.Request(url,headers = headers)
	response = request.urlopen(rq)
	#print(response.read().decode("utf-8"))
	html = response.read().decode("utf-8")
	rehtml(html,headers,q)


def geturl(q,sort="default",s=44):
	url = "https://s.taobao.com/search?"
	syu = s%44
	s = s//44
	#print(s,syu)
	if syu>0:
		s = s + 1
	print("共%d页"%s)
	for i in range(0,s):
		bbinfo = {"q":q,"sort":sort,"s":"%d"%(i * 44)}
		fullurl = url + parse.urlencode(bbinfo)
		print("*"*30)
		print("正在获取第%d页数据"%(i + 1))
		print("%s"%fullurl)
		gethtml(fullurl,q)

def main():
	#获取查询数据并处理
	q = input("请输入您搜索淘宝宝贝：")
	if q=="":
		print("亲，必须输入一个宝贝名称哦！")
		q = input("请输入您搜索淘宝宝贝：")
	sortlist=["default","renqi-desc","sale-desc","credit-desc"]
	sortlist2=["综合","人气","销量","信用"]
	sort = input("宝贝按什么排序：0综合 1人气 2销量 3信用（请输入数字，回车默认综合）")
	if sort=="":
			sort=0
	sort = int(sort)
	if sort<0 or sort>3:
		print("请输入合法数据")
		sort = input("宝贝按什么排序：0综合 1人气 2销量 3信用（请输入数字，回车默认综合）")
		if sort=="":
			sort=0
		sort = int(sort)
	sort2 = sortlist2[sort]
	sort = sortlist[sort]
	print("您选择了%s"%sort2)
	s = input("需要多少条宝贝信息：（请输入数字，如100，回车默认44条）")
	if s=="":
		s=44
	s = int(s)
	if s<=0:
		print ("亲，宝贝条数要大于零啦！")
		s = input("需要多少条宝贝信息：（请输入数字，如100，回车默认44条）")
		s = int(s)
	#获取查询地址
	geturl(q,sort,s)

if __name__ == '__main__':
	main()

```
---
## pyinstaller安装与打包
选择pyinstaller的原因很简单——它简单易操作。

### 安装pyinstaller
1. 打开cmd或者PowerShell，使用pip安装pyinstaller。
```
pip install pyinstaller
```
确保网络连接，回车等待安装完成就行了。
### 打包成exe
1. cd到项目文件

2. 使用pyinstaller打包命令

```
pyinstaller -F YourProjectName.py
```
等待打包完成。

3. 可以在dist文件夹中找到exe文件
