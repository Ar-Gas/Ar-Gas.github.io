---
title: Django note
date: 2023-08-26 22:51:00
tags: [web]
categories: [web]
mathjax: true
---

# 环境搭建

## 安装

```cmd
pip install django
```

<!--more-->

## 创建项目

```cmd
django-admin startproject mysite[文件名]
```

生成文件：

```目录
mysite
|--- manage.py         [管理项目文件，例如:运行、类自动生成数据表]
|--- manage
	 |--- __init__.py  
	 |--- settings.py  [项目配置文件，例如:连接那个数据...]
	 |--- urls.py 	   [根路由,URL和函数的对应关系/x1/login ->do_login ]
	 |--- asgi.py      [异步运行项目，编写socket，处理网络请求]
	 |--- wsgi.py      [同步运行项目，编写socket，处理网络请求]
```

