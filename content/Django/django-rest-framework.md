---
title: "Django Rest FrameWork"
date: 2015-11-01 18:30
---
##Django Restful

###Restful 简介
***要理解RESTful架构，最好的方法就是去理解Representational State Transfer这个词组到底是什么意思，它的每一个词代表了什么涵义***

一种软件架构风格，设计风格而不是标准，只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

***表述性状态转移***
	
REST提出了一些设计概念和准则：

　　1.网络上的所有事物都被抽象为资源（resource）；

　　2.每个资源对应一个唯一的资源标识（resource identifier）；

　　3.通过通用的连接器接口（generic connector interface）对资源进行操作；

　　4.对资源的各种操作不会改变资源标识；

　　5.所有的操作都是无状态的（stateless）。

***资源***：

> 资源是网络上网络上一个实体，可以是一张图片，音乐，文本等
我们可以用一个URI（统一资源定位符）去指向一个资源以便去访问获取它，这个资源
所对应的url是特定性的，不予重复

***表现层***

>	我们把资源展现出来的形式叫做“表现成”	比如，文本可以用txt格式表现，也可以用HTML格式、XML格式、JSON格式表现，甚至可以采用二进制格式；图片可以用JPG格式也可以用PNG格式表现。
	
>   ***URI***只是代表 资源的实体，不代表任何形式
>   比如：有些网址最后的".html"后缀名是不必要的，因为（***.html***）这个后缀名表示格式，属于"表现层"范畴，

>   HTTP请求的头信息中用Accept和Content-Type字段指定，这两个字段才是对"表现层"的描述。

***状态转化***

>   在访问网站的时候，我们必定要与服务器有一个互动的过程，而在HTTP通信协议中，所有的状态都要交给服务器来保存（***HTTP协议，是一个无状态协议***）

>    如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）。

>    如 HTTP协议中的：
>    
>     GET用来获取资源，
> 
>     POST用来新建资源（也可以用于更新资源），
> 
>     PUT用来更新资源，DELETE用来删除资源。


###简单的DJANGO RESTFUL 入门

***Install***

	pip install djangorestframework
	pip install pygments   使用这个包，做代码高亮显示
	
	pip install httpie     使用http命令 获取json
	pip install curl       使用curl命令post资源

***Getting started***

一：

	1. django-admin.py startproject djangoProject  创建django项目
	2. cd djangoProject
	3. python manage.py startapp djangoApp     在djangoProject下创建Application
	
二：

在 djangoProject 文件中 找到 seeting.py 文件 添加 rest_framework和 snippets
	
	INSTALLED_APPS = (
    	...
    	'rest_framework',
    	'snippets',
	)
在djangoProject/urls.py中，将snippets app的url包含进来

	urlpatterns = patterns('',
    	url(r'^', include('djangoApp.urls')),
	)
三：

创建model

	# coding=utf-8
	from django.db import models


	class Teacher(models.Model):
    	name = models.CharField(max_length=30)
    	email = models.EmailField(blank=True)
    	password = models.CharField(max_length=50)

使用python manage.py syncdb 同步数据库

四：

创建序列化类

在djangoApp 中新建文件serializers.py
并将下面内容拷贝到文件中

	# coding=utf-8

	from restful.models import Teacher
	from rest_framework import serializers


	class TeacherSerializers(serializers.ModelSerializer):
    	class Meta:
       		model = Teacher
       		file = ('name', 'email', 'password')

    def restore_object(self, attributes, instance=None):
        if instance:
            instance.name = attributes['name']
            instance.email = attributes['email']
            instance.password = attributes['password']

            return instance

        return User(**attributes)

> // TODO 为什么去序列化


***五***

创建view.py 视图

	# coding=utf-8
	from django.http import HttpResponse
	from django.views.decorators.csrf import csrf_exempt
	from rest_framework.renderers import JSONRenderer
	from rest_framework.parsers import JSONParser
	from restful.models import Teacher
	from restful.serializers import TeacherSerializers
	
	
	class JSONResponse(HttpResponse):
	    """
	    An HttpResponse that renders it's content into JSON.
	    """
	    def __init__(self, data, **kwargs):
	        content = JSONRenderer().render(data)
	        kwargs['content_type'] = 'application/json'
	        super(JSONResponse, self).__init__(content, **kwargs)
	
	
	@csrf_exempt
	def teacher_list(request, num):
	
	    """
	    List all code snippets, or create a new snippet.
	    """
	    if request.method == "GET":
	        teacher = Teacher.objects.get(name=num)
	        ser = TeacherSerializers(teacher)
	        return JSONResponse(ser.data)
	
	    elif request.method == "POST":
	        data = JSONParser().parse(request)
	        serializer = TeacherSerializers(data=data)
	        if serializer.is_valid():
	            serializer.save()
	            return JSONResponse(serializer.data, status=201)
	        else:
	            return JSONResponse(serializer.errors, status=400)

##六：
	
修改 django 路由
	
	url(r'^teacher/(\d+)', teacher_list),


七：

测试
	
	python manage.py runserver
	
	访问 http://127.0.0.1:8000/admin/
	在Techer中添加数据
	如：
		name：222
		email：admin@qq.com
		password：111111
	
	保存退出
	
	然后再 浏览器中 访问http://127.0.0.1：8000/teacher/222
	
	即可获取json 数据：
			{"id":2,"name":"222","email":"admin@qq.com","password":"111111"}


####参考
[http://www.django-rest-framework.org/](http://www.django-rest-framework.org/ "django_restful 官网")

[http://blog.csdn.net/zhaoyingm/article/details/8531617](http://blog.csdn.net/zhaoyingm/article/details/8531617 "csdn博客")
