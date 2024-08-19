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

### 页面创建

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

路由模块化

通过在项目根目录下的urls.py添加本模块的路径

```python
urlpatterns +=[
    path('catalog/',include('catalog.urls')),
    #使用catalog.urls.py中的所有路由载入根目录下的urls.py中
]
```

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

路由的获取

```html
<a href="{% url 'appname:路由名' %}">Home</a>
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
    db_table = 'table_name' #对应数据库中的表，如果不添加的话会默认模型民来作为默认的表名
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

添加超级用户

python manage.py createsuperuser

RE(正则表达式)

### 视图创建

#### 视图函数

1. 我们可以通过将views中的函数和urls进行连接，从而完成模板和视图以及路由的绑定，如下图的index()

```python
urlpatterns = [
    path('',views.index,name='index'),
]
def index(request):
    num_books = Book.objects.all().count()
    num_instances = Book.objects.all().count()
    num_genres = Genre.objects.all().count()
    num_languages = Language.objects.all().count()
    num_instances_available = BookInstance.objects.filter(status__exact='a').count()
    # 可以借书的个数
    num_authors = Author.objects.count()
    # 返回所有作者的数量
    return render(request,'index.html',
        context={'num_books': num_books, 'num_instances': num_instances,
                 'num_instances_available': num_instances_available, 'num_authors': num_authors,'num_genres': num_genres,'num_languages': num_languages},
    )
```

2. 通过类的列表视图（list）和详细信息视图(detail)方法	

首先同样的，我们仍然需要在urls.py中完成路由的绑定

```python
urlpatterns = [
    path('', views.index, name='index'),
  
    path('books/', views.BookListView.as_view(), name='books'),
     # as_view():首先这是一个classmethod，其次它能够完成创建一个实例的所有工作，并且确保为传入的 HTTP 请求调用正确的处理程序方法。
]
```

接下来就是编写我们的类,首先我们完成列表视图的编写

```python
from django.views import generic
from .models import Book
class BookListView(generic.ListView):
    model = Book
    context_object_name = 'book_list'
#作为一个输入模板变量名 就是 return render(reuqest,xx.html,context={})中context的名字
    template_name = 'my_arbitrary_template_name_list.html'
    # 继承的generic.ListView已经实现了大部分我们需要使用大部分视图功能，使得代码量减少，更快的完成工作
# model将获得数据库中Book表所有的数据，然后将会渲染一个名为“book_list.html”的模板 注：这里教程说会连接到/application_name/templates//application_name/the_model_name_list.html 但是我发现 只要修改template_name就可以了
	
    paginate_by = 10
    
   # 通过此添加，只要有 10 条以上的记录，视图就会开始对发送到模板的数据进行分页。使用 GET 参数访问不同的页面 — 要访问第 2 页，您将使用 URL /catalog/books/？page=2。
    
    
    #queryset = Book.objects.filter(title__icontains='war')[:5]
    #对数据进行筛选，也可以直接重写这个方法
    def get_queryset(self):
        return Book.objects.filter(title__icontains='war')[:5]
    
    def get_context_data(self, **kwargs):
        # Call the base implementation first to get the context
        context = super(BookListView, self).get_context_data(**kwargs)
        # Create any data and add it to the context
        context['some_data'] = 'This is just some data'
        return context
```

然后我们来完成详细视图，

```python
urlpatterns = [
    path('', views.index, name='index'),
    path('books/', views.BookListView.as_view(), name='books'),
    path('book/<int:pk>', views.BookDetailView.as_view(), name='book-detail'),
]
```



`	path()`提供的模式匹配非常简单，对于你只想捕获任何字符串或整数的（非常常见的）情况非常有用。如果需要更精细的过滤（例如，仅过滤具有一定数量字符的字符串），则可以使用 [re_path()](https://docs.djangoproject.com/en/2.0/ref/urls/#django.urls.re_path) 方法。

​	此方法与`path()`的使用一样，除了它允许你使用[正则表达式](https://docs.python.org/3/library/re.html)，以指定模式。例如，上面的路径可以编写为如下所示：这个有点复杂😵

```python
re_path(r'^book/(?P<pk>\d+)$', views.BookDetailView.as_view(), name='book-detail'),
```

View(class-based)

```python
class BookDetailView(generic.DetailView):
    model = Book
    template_name = 'books/book_detail.html'
 # 这就是类视图需要做的
 # 而如果我们需要使用函数则需要如下所示
def book_detail_view(request, primary_key):
    try:
        book = Book.objects.get(pk=primary_key)
    except Book.DoesNotExist:
        raise Http404('Book does not exist')

    return render(request, 'catalog/book_detail.html', context={'book': book})
```

#### 分页

前面我们设置了一页最多显示数量，现在我们来设置分页需求

这一般在模板中完成

```django
 {% block pagination %}
                {% if is_paginated %}
                    <div class="pagination">
            <span class="page-links">
                {% if page_obj.has_previous %}
                    <a href="{{ request.path }}?page={{ page_obj.previous_page_number }}">previous</a>
                {% endif %}
                <span class="page-current">
                    Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}.
                </span>
                {% if page_obj.has_next %}
                    <a href="{{ request.path }}?page={{ page_obj.next_page_number }}">next</a>
                {% endif %}
            </span>
                    </div>
                {% endif %}
            {% endblock %}
```

上面的代码首先会确认是否开启了分页，如果确认开启，它会根据需要添加下一个和上一个链接（以及当前页码）。

​	page_obj是一个 Paginator 对象，如果在当前页面上使用分页，该对象将存在。它使您可以获取有关当前页面，先前页面，有多少页面等... 所有信息。为了创建分页连接，使用{{request.path}}去得到当前页面URL

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





### 会话框架

 什么是会话？

​	嗯~ o(*￣▽￣*)o  ，	从字面意思来看会话就是一个人和另一个使用一种语言对话交流，那么第一个问题就出来了，对话双方都是谁？在web构造中，对话双方是浏览器和服务器，ok，我们知道了聊天的双方，那么接下来我们需要知道双方是通过什么语言交流的，毕竟如果让一个说只会说中文的人和只会说韩语的人交流是非常困难的，除非他们都能理解另外一种语言，而在web开发中，这个语言就是HTTP协议（这里称HTTP协议为“语言”其实并不恰当，它更像是一种约定俗成的规定，双方都认同，当对方做出规定的动作时，另一方能够明白发生了什么），

​	HTTP协议是无状态的。协议无状态的事实，意味着客户端和服务器之间的消息，完全相互独立 - 没有基于先前消息的“序列”或行为的概念。因此，如果你想拥有一个追踪与客户的持续关系的网站，你需要自己实现。

​	会话是 Django（以及大多数 Internet）用于跟踪站点和特定浏览器之间“状态”的机制。会话允许你为每个浏览器存储任意数据，并在浏览器连接时，将该数据提供给站点。然后，通过“密钥”引用与会话相关联的各个数据项，“密钥”用于存储和检索数据。

​	Django 使用包含特殊会话 ID 的 cookie，来识别每个浏览器，及其与该站点的关联会话。默认情况下，实际会话数据存储在站点数据库中（这比将数据存储在 cookie 中更安全，因为它们更容易受到恶意用户的攻击）。你可以将 Django 配置为，将会话数据存储在其他位置（缓存，文件，“安全”cookie），但默认位置是一个良好且相对安全的选项。

#### 第一步 启动会话（Session）

在settings.py中进行如下设置

```python
INSTALLED_APPS = [
    # …
    'django.contrib.sessions',
    # …

MIDDLEWARE = [
    # …
    'django.contrib.sessions.middleware.SessionMiddleware',
    # …
]
```



#### 使用Session

我们可以从一个视图中的request参数（作为视图的第一个参数传入的`HttpRequest`）来连接session，表示与当前用户的特定连接（或者更确切地说，是与当前浏览器的连接，由此站点的浏览器 cookie 中的会话 ID 标识）。

​	session是一个类似字典对象，这是因为HTTP协议使用的是json而json和字典对象非常类似。你可以在视图中多次读取和写入，并根据需要进行修改。你可以执行所有常规的字典操作，包括清除所有数据，测试是否存在密钥，循环数据等。大多数情况下，你只需使用标准“字典”API，来获取和设置值。

​	下面是一些对于session的操作

```python
# 得到一个key为my_car的session值
my_car = request.session['my_car']

# 得到一个key为my_car的session值，如果不存在值将其设置为‘mini’
my_car = request.session.get('my_car', 'mini')

# 设置一个session值
request.session['my_car'] = 'mini'

# 删除一个session值
del request.session['my_car']
```

#### 保存Session	

​	当我们通过session key来更新数据时，此django会自动帮我们保存

```python
request.session['my_car'] = 'mini'
```

​	但是如果我们通过session 中的数据来进行修改时，django将会无法识别

```python
# Session object not directly modified, only data within the session. Session changes not saved!
request.session['my_car']['wheels'] = 'alloy'

# Set session as modified to force data updates/cookie to be saved.
request.session.modified = True
```

​	或者我们可以通过在settings.py中添加`SESSION_SAVE_EVERY_REQUEST = True`来保证每一次修改都会得到更新

#### 	简单案例

```python
def index(request):

    num_authors = Author.objects.count()  

    # 访问次数
    num_visits = request.session.get('num_visits', 0)
    request.session['num_visits'] = num_visits + 1
  # 每当对当前网页发出一次请求，num_visits +1,此方法也可用来测试浏览器是否支持cookie
    context = { 
        'num_books': num_books,
        'num_instances': num_instances,
        'num_instances_available': num_instances_available,
        'num_authors': num_authors,
        'num_visits': num_visits,
    }

    # Render the HTML template index.html with the data in the context variable.
    return render(request, 'index.html', context=context)

```

### 用户授权与许可

#### 启用身份验证

当我们执行了django-admin startproject来创建应用程序时，所有必要的配置都已完成，当我们第一次调用 `python manage.py migrate` 时，创建了用户和模型权限的数据库表。它们在settins.py如下

```python
INSTALLED_APPS = [
    ...
    'django.contrib.auth',  #Core authentication framework and its default models.
    'django.contrib.contenttypes',  #Django content type system (allows permissions to be associated with models).
    ....

MIDDLEWARE = [
    ...
    'django.contrib.sessions.middleware.SessionMiddleware',  #Manages sessions across requests
    ...
    'django.contrib.auth.middleware.AuthenticationMiddleware',  #Associates users with requests using sessions.
    ....

```

#### 创建用户和分组

我们可以通过python manage.py createsuperuser也可以通过编程的方式创建如下

```python
from django.contrib.auth.models import User

# Create user and save to the database
user = User.objects.create_user('myusername', 'myemail@crazymail.com', 'mypassword')

# Update fields and then save again
user.first_name = 'John'
user.last_name = 'Citizen'
user.save()

```

但是我们可以通过django提供的admin/站点实现图形化创建

#### 设置身份验证视图

第一步在添加到项目 urls.py 之后我们只需要编写对应的模板网页即可，不需要进行代码实现

```python
# Use include() to add URLS from the catalog application and authentication system
from django.urls import include

#Add Django site authentication urls (for login, logout, password management)
urlpatterns += [
    path('accounts/', include('django.contrib.auth.urls')),
]
```

如果此时我们可以查看 http://127.0.0.1:8000/accounts/ 我们会看到如下提示

```
accounts/ login/ [name='login']
accounts/ logout/ [name='logout']
accounts/ password_change/ [name='password_change']
accounts/ password_change/done/ [name='password_change_done']
accounts/ password_reset/ [name='password_reset']
accounts/ password_reset/done/ [name='password_reset_done']
accounts/ reset/<uidb64>/<token>/ [name='password_reset_confirm']
accounts/ reset/done/ [name='password_reset_complete']
```

#### 测试已验证身份的用户

我们可以在网页中使用{{user}}变量，得到我们想用的信息，一般来说我们会首先测试{{ user.is_authenticated }}来确认该用户是否有资格访问特定内容

```django
  <ul class="sidebar-nav">
    …
   {% if user.is_authenticated %}
     <li>User: {{ user.get_username }}</li>
     <li>
       <form id="logout-form" method="post" action="{% url 'logout' %}">
         {% csrf_token %}
         <button type="submit" class="btn btn-link">Logout</button>
       </form>
     </li>
   {% else %}
     <li><a href="{% url 'login' %}?next={{ request.path }}">Login</a></li>
   {% endif %}
    …
  </ul>

```

上方代码就是首先确认用户是否登录，如果登录则显示登出按钮，没有的话就显示登录按钮。

#### 视图验证

如果我们打算使用view来完成对用户的验证，django也提供了对应的修饰器帮助我们完成这项工作。

```python
from django.contrib.auth.decorators import login_required

@login_required
def my_view(request):
    # …
 # 加入login_required后,如果用户已经登录，则该函数将会继续执行下去，如果没有完成登录则会自动重定向回到在settings.py 定义过的登录URL(settings.LOGIN_URL)，在该页面完成登录
```

### 表单

​	那么首先，我们来看一看一个最简单的表单的组成有什么

![image-20240805143102416](E:\Code\笔记\笔记图片\image-20240805143102416.png)

我们可以看到一个有着提示内容的输入框，一个提交按钮，那么现在让我们看看它的代码长什么样

```html
<form action="/team_name_url/" method="post">
  <label for="team_name">Enter name: </label>
  <input
    id="team_name"
    type="text"
    name="name_field"
    value="Default name for team." />
  <input type="submit" value="OK" />
</form>

```

​	在HTML代码中分3个部分form ,label,input。

​	第一个我要介绍的是form标签，作为一个表单标签，它会提交表单内输入的所有的内容使用`method`的方法传输到`action` 指定的URL。第二个label标签用于提示输入的内容。第三个input标签用于填入我们的值，其中`type`属性决定了哪一个种组件将会展示，`name` 和`id`用于标识在js/CSS/HTML中的使用，而`value` 定义字段显示的初始值。匹配团队标签使用`label` 标签指定（请参阅上面的“输入名称”Enter name），其中`for` 字段包含相关`input`输入的`id` 值。最后的submit按钮只要一按就会提交一切输入内容到服务器中，在本例被提交的只有team_name

#### 表单处理

​	和之前我们在视图创建的流程非常相像，视图得到请求，完成所有需要的操作(包括从模型中读取数据，然后生成并返回一个HTML页面)以下是表单处理的大致流程(从绿色开始)

![image-20240805144741118](E:\Code\笔记\笔记图片\image-20240805144741118.png)

基于上图，Django 表单处理的主要内容是：

1. 在用户第一次请求时，显示默认表单。
   - 表单可能包含空白字段（例如，如果你正在创建新记录），或者可能预先填充了初始值（例如，如果你要更改记录，或者具有有用的默认初始值）。
   - 此时表单被称为未绑定，因为它与任何用户输入的数据无关（尽管它可能具有初始值）。
2. 从提交请求接收数据，并将其绑定到表单。
   - 将数据绑定到表单，意味着当我们需要重新显示表单时，用户输入的数据和任何错误都可取用。
3. 清理并验证数据。
   - 清理数据会对输入执行清理（例如，删除可能用于向服务器发送恶意内容的无效字符）并将其转换为一致的 Python 类型。
   - 验证检查值是否适合该字段（例如，在正确的日期范围内，不是太短或太长等）
4. 如果任何数据无效，请重新显示表单，这次使用任何用户填充的值，和问题字段的错误消息。
5. 如果所有数据都有效，请执行必要的操作（例如保存数据，发送表单和发送电子邮件，返回搜索结果，上传文件等）
6. 完成所有操作后，将用户重定向到另一个页面。

#### Form类

​	`Form` 类是 Django 表单处理系统的核心。它指定表单中的字段、其布局、显示窗口小部件、标签、初始值、有效值，以及（一旦验证）与无效字段关联的错误消息。该类还提供了使用预定义格式（表，列表等）在模板中呈现自身的方法，或者用于获取任何元素的值（启用细粒度手动呈现）的方法。

`Form` 的声明语法，与声明`Model`非常相似，并且共享相同的字段类型（以及一些类似的参数）。这是有道理的，因为在这两种情况下，我们都需要确保每个字段处理正确类型的数据，受限于有效数据，并具有显示/文档的描述。

要创建表单，我们导入表单库，从`Form` 类派生，并声明表单的字段。我们的图书馆图书续借表单的一个非常基本的表单类如下所示：

```python
from django import forms

class RenewBookForm(forms.Form):
    renewal_date = forms.DateField(help_text="Enter a date between now and 4 weeks (default 3).")

```

在本例中，我们有一个 [`DateField`](https://docs.djangoproject.com/zh-hans/2.0/ref/forms/fields//#datefield) 用于输入续借日期，该日期将使用空白值在 HTML 中呈现，默认标签为“续借日期：”，以及一些有用的用法文本：“输入从现在到 4 周之间的日期（默认为 3）周）。”由于没有指定其他可选参数，该字段将使用 [input_formats](https://docs.djangoproject.com/zh-hans/2.0/ref/forms/fields/#django.forms.DateField.input_formats) 接受日期：YYYY-MM-DD（2016-11-06）、MM/DD/YYYY（02/26/2016）、MM/DD/YY（10/25/16），并且将使用默认[小部件](https://docs.djangoproject.com/zh-hans/2.0/ref/forms/fields/#widget)呈现：[DateInput](https://docs.djangoproject.com/zh-hans/2.0/ref/forms/widgets/#django.forms.DateInput)。

还有许多其他类型的表单字段，你可以从它们与等效模型字段类的相似性中大致认识到：

[`BooleanField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#booleanfield), [`CharField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#charfield), [`ChoiceField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#choicefield), [`TypedChoiceField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#typedchoicefield), [`DateField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#datefield), [`DateTimeField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#datetimefield), [`DecimalField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#decimalfield), [`DurationField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#durationfield), [`EmailField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#emailfield), [`FileField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#filefield), [`FilePathField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#filepathfield), [`FloatField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#floatfield), [`ImageField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#imagefield), [`IntegerField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#integerfield), [`GenericIPAddressField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#genericipaddressfield), [`MultipleChoiceField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#multiplechoicefield), [`TypedMultipleChoiceField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#typedmultiplechoicefield), [`NullBooleanField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#nullbooleanfield), [`RegexField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#regexfield), [`SlugField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#slugfield), [`TimeField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#timefield), [`URLField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#urlfield), [`UUIDField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#uuidfield), [`ComboField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#combofield), [`MultiValueField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#multivaluefield), [`SplitDateTimeField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#splitdatetimefield), [`ModelMultipleChoiceField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#modelmultiplechoicefield), [`ModelChoiceField`](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#modelchoicefield).

下面列出了大多数字段共有的参数（这些参数具有合理的默认值）：

- [required](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#required): 如果为`True`，则该字段不能留空或给出`None`值。默认情况下需要字段，因此你可以设置`required=False`以允许表单中的空白值。
- [label](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#label): 在 HTML 中呈现字段时使用的标签。如果未指定[label](https://docs.djangoproject.com/zh-hans/2.0/ref/forms/fields/#label)，则 Django 将通过大写第一个字母、并用空格替换下划线（例如续订日期）的方式，从字段名称创建一个。
- [label_suffix](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#label-suffix): 默认情况下，标签后面会显示冒号（例如续借日期:)。此参数允许你指定包含其他字符的不同后缀。
- [initial](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#initial): 显示表单时，字段的初始值。
- [widget](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#widget): 要使用的显示小部件。
- [help_text](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#help-text) （如上例所示）：可以在表单中显示的附加文本，用于说明如何使用该字段。
- [error_messages](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#error-messages): 字段的错误消息列表。如果需要，你可以使用自己的消息，覆盖这些消息。
- [validators](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#validators): 验证时将在字段上调用的函数列表。
- [localize](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#localize): 启用表单数据输入的本地化（有关详细信息，请参阅链接）。
- [disabled](https://docs.djangoproject.com/en/2.0/ref/forms/fields/#disabled): 如果为`True`，该字段会被显示，但无法编辑其值。默认值为`False`。

#### 验证

验证最简单的方法就是重写clean_<fieldname>()方法

我们重新在forms.py加入以下内容

```python
import datetime

from django import forms

from django.core.exceptions import ValidationError
from django.utils.translation import gettext_lazy as _

class RenewBookForm(forms.Form):
    renewal_date = forms.DateField(help_text="Enter a date between now and 4 weeks (default 3).")

    def clean_renewal_date(self):
        data = self.cleaned_data['renewal_date']

        # Check if a date is not in the past.
        if data < datetime.date.today():
            raise ValidationError(_('Invalid date - renewal in past'))

        # Check if a date is in the allowed range (+4 weeks from today).
        if data > datetime.date.today() + datetime.timedelta(weeks=4):
            raise ValidationError(_('Invalid date - renewal more than 4 weeks ahead'))

        # Remember to always return the cleaned data.
        return data

```

clean_renewal_date方法从`self.cleaned_data['renewal_date']` 获取数据，两个日期异常的错误

之后我们需要创建视图函数并在urls.py中注册

```python
import datetime

from django.shortcuts import render, get_object_or_404
from django.http import HttpResponseRedirect
from django.urls import reverse

from catalog.forms import RenewBookForm

def renew_book_librarian(request, pk):
    book_instance = get_object_or_404(BookInstance, pk=pk)

    # If this is a POST request then process the Form data
    if request.method == 'POST':

        # Create a form instance and populate it with data from the request (binding):
        form = RenewBookForm(request.POST)

        # Check if the form is valid:
        if form.is_valid():
            # process the data in form.cleaned_data as required (here we just write it to the model due_back field)
            book_instance.due_back = form.cleaned_data['renewal_date']
            book_instance.save()

            # redirect to a new URL:
            return HttpResponseRedirect(reverse('all-borrowed'))

    # If this is a GET (or any other method) create the default form.
    else:
        proposed_renewal_date = datetime.date.today() + datetime.timedelta(weeks=3)
        form = RenewBookForm(initial={'renewal_date': proposed_renewal_date})

    context = {
        'form': form,
        'book_instance': book_instance,
    }

    return render(request, 'book_renew_librarian.html', context)
```

首先，我们导入我们的表单（`RenewBookForm`）和视图函数中使用的许多其他有用的对象/方法：

- [`get_object_or_404()`](https://docs.djangoproject.com/en/2.0/topics/http/shortcuts/#get-object-or-404): 根据模型的主键值，从模型返回指定的对象，如果记录不存在，则引发`Http404` 异常（未找到）。
- [`HttpResponseRedirect`](https://docs.djangoproject.com/en/2.0/ref/request-response/#django.http.HttpResponseRedirect): 这将创建指向指定 URL 的重定向（HTTP 状态代码 302）。
- [`reverse()`](https://docs.djangoproject.com/en/2.0/ref/urlresolvers/#django.urls.reverse): 这将从 URL 配置名称和一组参数生成 URL。它是我们在模板中使用的 `url` 标记的 Python 等价物。
- [`datetime`](https://docs.python.org/3/library/datetime.html): 用于操作日期和时间的 Python 库。

  在视图中，我们首先使用 `get_object_or_404()`中的 `pk` 参数，来获取当前的 `BookInstance` （如果这不存在，视图将立即退出，页面将显示“未找到”错误）。如果这不是 `POST` 请求（由 `else` 子句处理），那么我们创建默认表单，传递 `renewal_date` 字段的`initial` 初始值（如下面的**粗体**所示，这是从当前日期起的 3 周）。

​	创建表单后，我们调用 `render()` 来创建 HTML 页面，指定模板和包含表单的上下文。在这种情况下，上下文还包含我们的 `BookInstance`，我们将在模板中使用它，来提供有关我们正在续借的书本信息。

但是，如果这是一个`POST` 请求，那么我们创建表单对象，并使用请求中的数据填充它。此过程称为“绑定”，并且允许我们验证表单。然后我们检查表单是否有效，它运行所有字段上的所有验证代码 - 包括用于检查我们的日期字段，实际上是有效日期的通用代码，以及用于检查日期的特定表单的`clean_renewal_date()`函数在合适的范围内。

如果表单无效，我们再次调用`render()` ，但这次在上下文中传递的表单值将包含错误消息。

如果表单有效，那么我们可以开始使用数据，通过 `form.cleaned_data`属性访问它（例如 `data = form.cleaned_data['renewal_date']`）。这里我们只将数据保存到关联的`BookInstance` 对象的`due_back` 值中。

​	这就是表单处理本身所需的一切，但我们仍然需要将视图，限制为图书馆员可以访问。我们应该在 `BookInstance` （“`can_renew`”）中创建一个新的权限，但为了简单起见，我们只需使用`@permission_required`函数装饰器，和我们现有的 `can_mark_returned` 权限。

#### 表单模型

其实django已经为我们写好了表单模型

```python
from django.forms import ModelForm

from catalog.models import BookInstance

class RenewBookModelForm(ModelForm):
    def clean_due_back(self):
       data = self.cleaned_data['due_back']

       # Check if a date is not in the past.
       if data < datetime.date.today():
           raise ValidationError(_('Invalid date - renewal in past'))

       # Check if a date is in the allowed range (+4 weeks from today).
       if data > datetime.date.today() + datetime.timedelta(weeks=4):
           raise ValidationError(_('Invalid date - renewal more than 4 weeks ahead'))

       # Remember to always return the cleaned data.
       return data

    class Meta:
        model = BookInstance
        fields = ['due_back']
        labels = {'due_back': _('Renewal date')}
        help_texts = {'due_back': _('Enter a date between now and 4 weeks (default 3).')}

```

这一段代码包含了我们上述的所有内容，我们唯一需要的添加的就是需要添加到表单中的 `fields` 以及相关类

#### 通用编辑视图

```python
from django.views.generic.edit import CreateView, UpdateView, DeleteView
from django.urls import reverse_lazy
from .models import Author

class AuthorCreate(CreateView):
    model = Author
    fields = '__all__'
    initial={'date_of_death':'05/01/2018',}

class AuthorUpdate(UpdateView):
    model = Author
    fields = ['first_name','last_name','date_of_birth','date_of_death']

class AuthorDelete(DeleteView):
    model = Author
    success_url = reverse_lazy('authors')
```

上方代码完成了对作者的创建、更新、删除3个视图功能的搭建。其中model表示连接的模型表，fields表示，允许进行修改的字段，initial表示初始值的设置，success_url,代表成功后将会跳转到的地址。此外之所以使用reverse_lazy()，success_url 相当于类属性，在类加载的时候，属性就会被赋值，也就是会对别名进行解析，这里我理解的是django中url的加载在所有类加载后，因此找不到对应的url。 reverse_lazy()里实际也是调用了reverse(),只是延迟这个过程至该url被访问时。



### 测试

#### 测试种类

单元测试：

​	验证各个组件的功能行为，通常是类别和功能级别。

回归测试：

​	测试重现历史错误。最初运行每个测试，以验证错误是否已修复，然后重新运行，以确保在以后更改代码之后，未重新引入该错误。

集成测试：

​	验证组件分组在一起使用时的工作方式。集成测试了解组件之间所需的交互，但不一定了解每个组件的内部操作。它们可能涵盖整个网站的简单组件分组。

**备注：**其他常见类型的测试，包括黑盒，白盒，手动，自动，金丝雀，烟雾，一致性，验收，功能，系统，性能，负载和压力测试。查找它们以获取更多信息。

​	Django 提供了一个测试框架，其中包含基于 Python 标准[`unittest`](https://docs.python.org/3/library/unittest.html#module-unittest)库的小型层次结构。尽管名称如此，但该测试框架适用于单元测试和集成测试。Django 框架添加了 API 方法和工具，以帮助测试 Web 和 Django 特定的行为。这允许你模拟请求，插入测试数据以及检查应用程序的输出。Django 还提供了一个 API（[LiveServerTestCase](https://docs.djangoproject.com/en/2.0/topics/testing/tools/#liveservertestcase)）和[使用不同测试框架](https://docs.djangoproject.com/en/2.0/topics/testing/advanced/#other-testing-frameworks)的工具，例如，你可以与流行的 [Selenium](https://developer.mozilla.org/zh-CN/docs/Learn/Tools_and_testing/Cross_browser_testing/Your_own_automation_environment) 框架集成，以模拟用户与实时浏览器交互。

​	要编写测试，你可以从任何 Django（或 unittest）测试基类（[SimpleTestCase](https://docs.djangoproject.com/en/2.0/topics/testing/tools/#simpletestcase), [TransactionTestCase](https://docs.djangoproject.com/en/2.0/topics/testing/tools/#transactiontestcase), [TestCase](https://docs.djangoproject.com/en/2.0/topics/testing/tools/#testcase), [LiveServerTestCase](https://docs.djangoproject.com/en/2.0/topics/testing/tools/#liveservertestcase)）派生，然后编写单独的方法，来检查特定功能，是否按预期工作（测试使用“assert”方法来测试表达式导致 `True`或 `False`值，或者两个值相等，等等。）当你开始测试运行时，框架将在派生类中执行所选的测试方法。测试方法独立运行，具有在类中定义的常见设置和/或拆卸行为，如下所示。

```python
class YourTestClass(TestCase):
    def setUp(self):
        # Setup run before every test method.
        pass

    def tearDown(self):
        # Clean up run after every test method.
        pass

    def test_something_that_will_pass(self):
        self.assertFalse(False)

    def test_something_that_will_fail(self):
        self.assertTrue(False)
```

#### 需要测试的是什么？

你应该测试自己代码的所有方面，但不要测试 Python 或 Django 的一部分提供的任何库或功能。

​    例如，考虑下面定义的 `Author`模型。你不需要显式测试 `first_name` 和 `last_name` 是否已在数据库中正确储存为`CharField`，因为这是 Django 定义的内容（当然，在实践中，你将不可避免地在开发期间测试此功能）。你也不需要测试`date_of_birth`是否已被验证为日期字段，因为这也是 Django 中实现的东西。

​	但是，你应该检查输入标签中的内容（名字，姓氏，出生日期，死亡），以及为文本分配的字段大小（100 个字符），因为这些是你的设计的一部分，可能会在将来被打破/改变。

```python
class Author(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    date_of_birth = models.DateField(null=True, blank=True)
    date_of_death = models.DateField('Died', null=True, blank=True)

    def get_absolute_url(self):
        return reverse('author-detail', args=[str(self.id)])

    def __str__(self):
        return '%s, %s' % (self.last_name, self.first_name)

```

同样的还需要检查两个方法是否能按照逻辑运行，在 get_absolute_url方法中，reverse是由django完成逻辑的不许测试，所以我们需要测试的`'author-detail'`网页是否真实存在，以及是否完成路由的注册和分配等

​	我们也同样希望出生和死亡的日期限制在合理的值，并检查出生后是否死亡。在 Django 中，此约束将添加到表单类中（尽管你可以为字段定义验证器，这些字段似乎仅在表单级别使用，而不是在模型级别使用）。

#### 开始测试

```python
from django.test import TestCase

```

​	通常，你将为要测试的每个模型/视图/表单添加测试类别，并使用个别方法来测试特定功能。在其他情况下，你可能希望有一个分开的测试类，来测试特定用例，使用个别的测试函数，来测试该用例的各个方面（例如，测试模型字段已正确验证的类，以及测试每个可能的失败案例的函数）。同样，结构很大程度上取决于你，但如果你保持一致，那就最好了。

```python
class YourTestClass(TestCase):
    @classmethod
    def setUpTestData(cls):
        print("setUpTestData: Run once to set up non-modified data for all class methods.")
        pass

    def setUp(self):
        print("setUp: Run once for every test method to set up clean data.")
        pass

    def test_false_is_false(self):
        print("Method: test_false_is_false.")
        self.assertFalse(False)

    def test_false_is_true(self):
        print("Method: test_false_is_true.")
        self.assertTrue(False)

    def test_one_plus_one_equals_two(self):
        print("Method: test_one_plus_one_equals_two.")
        self.assertEqual(1 + 1, 2)

```

执行顺序1. setUpTestData 2.setUp 3. 测试方法

模型测试

```python
from django.test import TestCase

from catalog.models import Author

class AuthorModelTest(TestCase):
    @classmethod
    def setUpTestData(cls):
        # Set up non-modified objects used by all test methods
        Author.objects.create(first_name='Big', last_name='Bob')

    def test_first_name_label(self):
        author = Author.objects.get(id=1)
        field_label = author._meta.get_field('first_name').verbose_name
        self.assertEqual(field_label, 'first name')

    def test_date_of_death_label(self):
        author = Author.objects.get(id=1)
        field_label = author._meta.get_field('date_of_death').verbose_name
        self.assertEqual(field_label, 'died')

    def test_first_name_max_length(self):
        author = Author.objects.get(id=1)
        max_length = author._meta.get_field('first_name').max_length
        self.assertEqual(max_length, 100)

    def test_object_name_is_last_name_comma_first_name(self):
        author = Author.objects.get(id=1)
        expected_object_name = f'{author.last_name}, {author.first_name}'
        self.assertEqual(str(author), expected_object_name)

    def test_get_absolute_url(self):
        author = Author.objects.get(id=1)
        # This will also fail if the urlconf is not defined.
        self.assertEqual(author.get_absolute_url(), '/catalog/author/1')


```



## 报错集合

### CSRF 

what

why

when

How:

解决1：在网页中加入{% csrf_token %} 我认为不应该是直接加，不然保护了个寂寞

### logout

django 5中 logout 需要通过表单提交否则会报405的错误，但是在以下版本不会出现

### 虚拟环境安装

建议使用python3 -m pip install 包名 不然容易安装到默认环境里