---
title: 设置NPM源
date: 2016-10-15 12:23:31
tags:
	- Node.Js
	- NPM
categories:
	- Node.js
	- NPM
---

## 1. 通过修改配置文件
>在NPM的配置文件中加入配置 (Path：nodejs安装目录\node_modules\npm)

```
registry =https://registry.npm.taobao.org 
```

## 2. 通过 config 配置指向镜像源

```
npm config set registry http://registry.cnpmjs.org //配置指向源
npm info express  //下载安装第三方包
```

## 3. 通过 npm 命令指定下载源

```
npm --registry http://registry.cnpmjs.org info express
```
