---
title: selenium + phantomjs - 环境搭建
date: 2016-08-27 14:21:55
tags: 
	- Python
	- selenium
	- phantomjs
categories:
 	- Python
---
## python + selenium + phantomjs -环境搭建

### 1. 安装 selenium

```bash
pip install -U selenium
```

### 2. 安装PhantomJS

1. 从[PhantomJs官网下载](http://phantomjs.org/download.html)
2. 解压缩并Copy bin目录下的Phantomjs.exe 到Python的安装目录

### 3. 测试
运行如下代码，如果打印出百度的标题，则说明运行成功

```python
# -*- coding:utf-8 -*-
from selenium import webdriver
driver = webdriver.PhantomJS()
driver.get("http://www.baidu.com")
data = driver.title
print(data)
driver.quit()
```