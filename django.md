# day01 初识Django

## 1. 默认文件介绍

manage.py: 项目的管理，启动项目、创建app、数据管理

asgi.py(异步), wsgi.py(同步): 接受网络请求

urls.py: URL和函数的对应关系 经常操作

settings.py：项目配置文件 数据库连接，注册app....

## 2. APP

```
- 项目
	- app，用户管理 [表结构、函数、HTML模板、CSS]
	- app，后台管理 [表结构、函数、HTML模板、CSS]
	- app，后台管理 [表结构、函数、HTML模板、CSS]
	- app，网站    [表结构、函数、HTML模板、CSS]
	- app，API    [表结构、函数、HTML模板、CSS]
```

创建命令 ： python manage.py startapp app_name

```
└─test_app
    │  admin.py				【提供admin后台管理】
    │  apps.py				【固定】app启动类
    │  models.py			【数据库ORM模型】
    │  tests.py				【单元测试】
    │  views.py				【视图函数】
    │  __init__.py
    │
    └─migrations			【数据库迁移】
            __init__.py
```

## 3. 快速上手

- 确保app在settings.py中注册

  ![image-20240731104230474](E:\Code\笔记\笔记图片\image-20240731104230474.png)

- 编写视图函数与url关系 在urls.py中编写

  类似于flaks中

  @app.route(/'index')

  def index():

  ​	return "hello world"

  ![image-20240731104719192](E:\Code\笔记\笔记图片\image-20240731104719192.png)

  ![image-20240731110354615](E:\Code\笔记\笔记图片\image-20240731110354615.png)

- 启动django 项目

  ```
  1. 命令行启动
  python manage.py runserver
  
  2. pycharm启动 
  直接点启动
  
  注意：酷狗音乐会占用8000端口号  ┑ (￣Д ￣)┍
  ```

- 模板（templates）

  ```python
  def user_list(request):
      #1. 如果在setting中添加'DIRS':[os.path.join(BASE_DIR,'templates')]则会优先去根目录下寻找
      #2. 默认去当前目录下寻找templates目录 (根据app的注册顺序，逐一去他们的templates目录下寻找)
      return  render(request,"user_list.html")
  ```

- 静态文件

  如果是在app中创建static目录，我们需要在setting.py中修改 STATIC_URL中的值为 '/static/' ，在此之前需要确定'django.contrib.staticfiles'在INSTALLED_APPS 中完成注册，这样才能够成功在app中使用静态文件    *ps:我现在使用的pycham默认是 将STATIC_URL中的值为 'static/'*

  在html页面中使用静态文件

  1. {% load static %}
  2. {% static 'your/static/file/path' %}