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

### 确保app在settings.py中注册

![image-20240731104230474](.\笔记图片\image-20240731104230474.png)

### 主页创建

![img](E:\Code\笔记\笔记图片\5ed2388e6a7dc0d75d3de79f3ea28a4a.png)

从上面我们可以看出首先浏览器向URLS（路由）发出HTTP请求，之后由urls.py 将请求传到view（views.py）,通过导入模型（models.py）和模板（Template）将HTML以HTTP Response的形式返还给浏览器



类似于flaks中

@app.route(/'index')

def index():

​	return "hello world"

path() 中的路由是一个字符串，用于定义要匹配的 URL 模式。该字符串可能包括一个命名变量（尖括号中）

例：`'catalog/<id>/'`。此模式将匹配如 **/catalog/\*any_chars\*/** 的 URL，并将 any_chars 作为具有参数名称 `id` 的字符串传递给视图。我们将在后面的主题中进一步讨论路径方法和路由模式

![image-20240731104719192](.\笔记图片\image-20240731104719192.png)

![image-20240731110354615](.\笔记图片\image-20240731110354615.png)

### 启动django 项目

```
1. 命令行启动
python manage.py runserver

2. pycharm启动 
直接点启动

注意：酷狗音乐会占用8000端口号  ┑ (￣Д ￣)┍
```

### 模板（templates）

```python
def user_list(request):
    #1. 如果在setting中添加'DIRS':[os.path.join(BASE_DIR,'templates')]则会优先去根目录下寻找
    #2. 默认去当前目录下寻找templates目录 (根据app的注册顺序，逐一去他们的templates目录下寻找)
    return  render(request,"user_list.html")
```



```html
{% extends "base_generic.html" %}
{% block title %}
    首页
{% endblock %}
{% block content %}
  <h1>Local Library Home</h1>

  <p>Welcome to <em>LocalLibrary</em>, a very basic Django website developed as a tutorial example on the Mozilla Developer Network.</p>

  <h2>Dynamic content</h2>

  <p>The library has the following record counts:</p>
  <ul>
    <li><strong>Books:</strong> {{ num_books }}</li>
    <li><strong>Copies:</strong> {{ num_instances }}</li>
    <li><strong>Copies available:</strong> {{ num_instances_available }}</li>
    <li><strong>Authors:</strong> {{ num_authors }}</li>
    <li><strong>Genres:</strong> {{ num_genres }}</li>
    <li><strong>Languages:</strong> {{ num_languages }}</li>
  </ul>
{% endblock %}
```



### 静态文件

如果是在app中创建static目录，我们需要在setting.py中修改 STATIC_URL中的值为 '/static/' ，在此之前需要确定'django.contrib.staticfiles'在INSTALLED_APPS 中完成注册，这样才能够成功在app中使用静态文件    *ps:我现在使用的pycham默认是 将STATIC_URL中的值为 'static/'*

在html页面中使用静态文件

1. {% load static %}
2. {% static 'your/static/file/path' %}

### 请求和响应

​	在编写视图函数时，都会有一个request对象,该对象封装了用户所有的请求数据 

```
reuqest.method  --获取用户请求方式
request.GET --获取通过url获得的参数
request.POST --获取通过POST请求获得的参数
HttpResponse --将字符串内容返回给请求者
render 		 --读取HTML的内容 + 渲染（替换）->字符串 返回给浏览器
redirect     --重定向为指定页面: django 告诉浏览器去向指定页面发送请求

```

*tips*: 表单提交时，

建设当前页面为 www.xxx.com/xx/

action的值为 '/xx/'的意思为补全当前域名，www.xxx.com/xx/

'xx/'的意思为：www.xxx.com/xx/xx/

### ORM数据模型

- 创建、修改、删除数据库的表(不用写SQL语句)  (但是不能创建数据库)

- 操作表中的数据(不用写SQL语句)

![img](.\笔记图片\4ea1b6a882ed9f22d075ad698f888526.png)

如上图所示，该数据库总计存在4张表，分别为Book,Genre(题材),

Author，BookInstance(实体书)。他们对其他表存在这对应关系，如Book和Genre，靠经Book的数字表示一本书可以必须有1个Genre或者多个Genre，而靠近Genre的表示一个Genre可以对应0或者多个Book。以此类推

#### 模型创建

```python
from django.db import models

class MyModelName(models.Model):
    #字段名/列名/域
    my_field_name = models.CharField(max_length=20, help_text="Enter field documentation",verbose_name="my_field_name")
     # Metadata
    class Meta:
        ordering = ["-my_field_name"]
     # Methods
    def get_absolute_url(self):
            """
            Returns the url to access a particular instance of MyModelName.
            """
            return reverse('model-detail-view', args=[str(self.id)])
    def __str__(self):
        """
        String for representing the MyModelName object (in Admin site etc.)
        """
        return self.field_name
```

##### 域

​	一个模型可以有任意数量，任意类型的域，每个用一行呈现我们想存储进数据库的数据。对应着数据库中的字段

以下是一些常用参数

- [help_text](https://docs.djangoproject.com/en/1.10/ref/models/fields/#help-text) :提供 HTML 表单文本标签 (eg i 在管理站点中),如上所述。
- [verbose_name](https://docs.djangoproject.com/en/1.10/ref/models/fields/#verbose-name) :字段标签中的可读性名称，如果没有被指定，Django 将从字段名称推断默认的详细名称。
- [default](https://docs.djangoproject.com/en/1.10/ref/models/fields/#default) :该字段的默认值。这可以是值或可呼叫物件 (callable object)，在这种情况下，每次创建新纪录时都将呼叫该物件。
- [null](https://docs.djangoproject.com/en/1.10/ref/models/fields/#null)：如为`True`，即允许 Django 于资料库该栏位写入`NULL`（但栏位型态如为`CharField`则会写入空字串）。预设值是`False`。
- [blank](https://docs.djangoproject.com/en/1.10/ref/models/fields/#blank) :如为 `True`，表单中的字段被允许为空白。默认是`False`，这意味着 Django 的表单验证将强制你输入一个值。这通常搭配 `NULL=True` 使用，因为如果要允许空值，你还希望数据库能够适当地表示它们。
- [choices](https://docs.djangoproject.com/en/1.10/ref/models/fields/#choices) :这是给此字段的一组选项。如果提供这一项，预设对应的表单部件是「该组选项的列表」，而不是原先的标准文本字段。
- [primary_key](https://docs.djangoproject.com/en/1.10/ref/models/fields/#primary-key) :如果是 True，将当前字段设置为模型的主键（主键是被指定用来唯一辨识所有不同表记录的特殊数据库栏位 (column)）。如果没有指定字段作为主键，则 Django 将自动为此添加一个字段。

以下是常用的字段类型:

- [CharField](https://docs.djangoproject.com/en/1.10/ref/models/fields/#django.db.models.CharField) 是用来定义短到中等长度的字段字符串。你必须指定`max_length`要存储的数据。
- [TextField](https://docs.djangoproject.com/en/1.10/ref/models/fields/#django.db.models.TextField) 用于大型任意长度的字符串。你可以`max_length`为该字段指定一个字段，但仅当该字段以表单显示时才会使用（不会在数据库级别强制执行）。
- [IntegerField](https://docs.djangoproject.com/en/1.10/ref/models/fields/#django.db.models.IntegerField) 是一个用于存储整数（整数）值的字段，用于在表单中验证输入的值为整数。
- [DateField](https://docs.djangoproject.com/en/1.10/ref/models/fields/#datefield) 和[DateTimeField](https://docs.djangoproject.com/en/1.10/ref/models/fields/#datetimefield) 用于存储／表示日期和日期／时间信息（分别是`Python.datetime.date` 和 `datetime.datetime` 对象）。这些字段可以另外表明（互斥）参数 `auto_now=Ture`（在每次保存模型时将该字段设置为当前日期），`auto_now_add`（仅设置模型首次创建时的日期）和 `default`（设置默认日期，可以被用户覆盖）。
- [EmailField](https://docs.djangoproject.com/en/1.10/ref/models/fields/#emailfield) 用于存储和验证电子邮件地址。
- [FileField](https://docs.djangoproject.com/en/1.10/ref/models/fields/#filefield) 和[ImageField](https://docs.djangoproject.com/en/1.10/ref/models/fields/#imagefield) 分别用于上传文件和图像（`ImageField` 只需添加上传的文件是图像的附加验证）。这些参数用于定义上传文件的存储方式和位置。
- [AutoField](https://docs.djangoproject.com/en/1.10/ref/models/fields/#autofield) 是一种 **IntegerField** 自动递增的特殊类型。如果你没有明确指定一个主键，则此类型的主键将自动添加到模型中。
- [ForeignKey](https://docs.djangoproject.com/en/1.10/ref/models/fields/#foreignkey) 用于指定与另一个数据库模型的一对多关系（例如，汽车有一个制造商，但制造商可以制作许多汽车）。关系的“一”侧是包含密钥的模型。
- [ManyToManyField](https://docs.djangoproject.com/en/1.10/ref/models/fields/#manytomanyfield) 用于指定[多对多](https://docs.djangoproject.com/en/1.10/ref/models/fields/#manytomanyfield)关系（例如，一本书可以有几种类型，每种类型可以包含几本书）。在我们的图书馆应用程序中，我们将非常类似地使用它们 ForeignKeys，但是可以用更复杂的方式来描述组之间的关系。这些具有参数 `on_delete` 来定义关联记录被删除时会发生什么（例如，值 `models.SET_NULL` 将简单地设置为值 NULL）。

##### 元数据

通过宣告 class Meta 来宣告模型级别的元数据,排序将依赖字段的类型（字符串字段按字母顺序排序，而日期字段按时间顺序排序）。你可以使用减号（-）对字段名称进行前缀，以反转排序顺序。

翻译：理解为数据库中的order by

假设，我们按照以下的顺序来排列书单

```python
class Meta:
    ordering = ['title', '-pubdate']
```

书单通过标题依据--字母排序--排列，从 A 到 Z，然后再依每个标题的出版日期，从最新到最旧排列。

另一个常见的属性是 `verbose_name`,一个 `verbose_name`说明单数和复数形式的类别。

##### 方法

一个模型也可以有方法。类似对象的classmethod

最起码，在每个模型中，你应该定义标准的 Python 类方法 `__str__()`，**来为每个物件返回一个人类可读的字符串**。此字符用于表示管理站点的各个记录（以及你需要引用模型实例的任何其他位置）。通常这将返回模型中的标题或名称字段。

```python
def __str__(self):
    return self.field_name
```

而另一个方法则是`get_absolute_url()`，这函数返回一个在网站上显示个人模型记录的 URL（如果你定义了该方法，那么 Django 将自动在“管理站点”中添加“在站点中查看“按钮在模型的记录编辑栏）。`get_absolute_url()`的典型示例如下：

```python
def get_absolute_url(self):
    """Returns the url to access a particular instance of the model."""
    return reverse('model-detail-view', args=[str(self.id)])
```

假设你将使用 URL `/myapplication/mymodelname/2` 来显示模型的单个记录（其中“2”是 id 特定记录），则需要创建一个 URL 映射器来将响应和 id 传递给“模型详细视图”，`reverse()`函数可以“反转”你的 url 映射器：通过视图函数得到他的url 类似于flask的url_for

##### model管理

###### 创建和修改记录

```python
# Create a new record using the model's constructor.
a_record = MyModelName(my_field_name="Instance #1")
# Save the object into the database.
a_record.save()
```

注：如果在模型定义中未曾声明主键，则Django会自动为该表创建一个id的主键

###### 查找数据

使用模型提供的object属性搜寻记录

```python
#获取book中所有的数据
all_books = Book.objects.all()
#使用filter()筛选出符合条件的数据
wild_books = Book.objects.filter(title__contains = 'wild').first()/all()/next()
number_wild_books = Book.objects.filter(title__contains='wild').count()
```

要比对的字段与比对方法都要被定义在筛选的参数名称里，并且使用这个格式：`比對字段__比對方法` (请注意上方范例中的 `title` 与 `contains` 中间隔了两个底线唷)。在上面我们使用大小写区分的方式比对`title`。还有很多比对方式可以使用： `icontains`(不区分大小写), `iexact`(不大小写区分且完全符合), `exact`(区分大小写但完全符合) 还有 `in`, `gt`(大于), `startswith`,之类的。[全部的用法在这里。](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#field-lookups)

当我们需要通过外键来筛选时，可以通过下划线来指定相关模型的字段

```python
books_containing_genre = Book.objects.filter(genre__name__icontains='fiction')  # Will match on: Fiction, Science fiction, non-fiction etc.

```

还有很多是你可以用索引 (queries) 来做的，包含从相关的模型做向后查询 (backwards searches)、连锁过滤器 (chaining filters)、回传「值的小集合」等。更多资讯可以到 [Making queries](https://docs.djangoproject.com/en/2.0/topics/db/queries/) (Django Docs) 查询。

###### Admin类编写

打开 **admin.py** 在目录应用程序注册一个新的模型	

```python
class AuthorAdmin(admin.ModelAdmin):
    list_display = ('last_name', 'first_name', 'date_of_birth', 'date_of_death')
    # 展示列名

admin.site.register(Author, AuthorAdmin)
# Register the Admin classes for Book using the decorator

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    pass

# Register the Admin classes for BookInstance using the decorator

@admin.register(BookInstance)
class BookInstanceAdmin(admin.ModelAdmin):
    pass
```







RE(正则表达式)



#### 示例流程

在settings.py中修改

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'database_name',
        'USER': 'root',
        'PASSWORD': '<password>',
        'HOST': 'XXX',
        'PORT': 'XXX',
    }
} (连接mysql)
```

对表的CRUD写在app中的models.py中

```python
from django.db import models
class UserModels(models.Model):
    name = models.CharField(max_length=100)
    password = models.CharField(max_length=100)
    age = models.IntegerField()

```

保证该app已在settings.py中注册的前提下执行命令完成对表的创建 （数据库迁移）

1. python manage.py makemigrations

2. python manage.py migrate

创建表

删除表

修改表

在表中新增列时，会给已有数据添加上该列，所以需要对新增列指定对应的数据

```python
age = models.IntegerField(default=2) #设置默认值
data = models.IntegerField(null=True,blank=True) #设置为允许为空 blank用于表单的认证，字段在填写表单时不能为空。
```

操作表中的数据

```python
#添加对象
model.objects.create(your=date ,your=date)
#查找对象
model.objects.filter(字段='条件')#返回一个列表 可以加一个.first()得到第一个
#更新全部或者筛选数据
model.objects.all()/.filter().update(字段='条件')

```

外键链接

```python
class Department(models.Model):
    """ 部门表 """
    title = models.CharField(verbose_name="部门表",max_length=100)


class UserInfo(models.Model):
    """员工表"""

    name = models.CharField(max_length=100)
    password = models.CharField(max_length=100)
    age = models.IntegerField()
    account = models.DecimalField(max_digits=10, decimal_places=2,default=0)
    create_time = models.DateTimeField(auto_now_add=True)
    # 选择存储
    gender_choices = (
        (1,"男"),
        (0,"女")
    )
    gender = models.SmallIntegerField(verbose_name="性别",choices=gender_choices)
    # 如果查询次数特别多才会直接存放名称(加速查找,才允许数据冗余)
    # 外键设置为Department,生成数据列名生成depart_id,
    # to=Department(连接的表),to_fields=id(连接的字段),on_delete=models.CASCADE(级联删除)/SET_NULL(置空 但是需要先允许为空)
    depart = models.ForeignKey(to=Department,to_fields=id,on_delete=models.CASCADE)
```









## 报错集合

### CSRF 

what

why

when

How:

解决1：在网页中加入{% csrf_token %} 我认为不应该是直接加，不然保护了个寂寞