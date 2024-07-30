# 配置信息

1. 安装位置查询

   登录后输入 show variables like "%char%";

   ![image-20240729095105794](.\笔记图片\image-20240729095105794.png)

2. 找到创建数据库的位置 show variables like "%datadir%";

   ![image-20240729095347965](.\笔记图片\image-20240729095347965.png)

   注意：my.ini 配置文件也在这个路径中，当需要更改时，可以去通过该路径修改

3. 忘记密码  

   - 修改mysql配置文件  设置为无账号模式

     1.  停止现有Mysql服务

     2. 找到my.ini 加上skip-grant-tables=1

     3. 重新启动Mysql 再次登录（无需密码）

     4. 执行命令

        ```mysql
        use mysql;
        update user set authentication_string =
        password('密码'),password_last_changed = now() where user ='root';
        ```

     5. 重新修改配置文件 删除 skip-grant-tables=1

# 语句

## 数据库管理操作

```mysql
show databases; 查询数据库

create database name; 创建数据库

drop database name; 删除数据库

use name; 使用数据库

show tables; 显示库中的表
```



## 数据表管理

```mysql
create table name (
	列名 类型，
    列名 类型，
    列名 类型，
)default charset=utf-8;
例：
create table testdb (
	id int auto_increment primary key,  -- 主键（不允许为空，不允许重复）内部维护自增
    name varchar(16) not null, -- 不允许为空
    age int null,   			  -- 允许为空（默认）
    Country varchar(20) default "China" -- 默认值设为China  
)default charset=utf-8;


desc table_name; -- 查看表的格式 （desc -> describe）


```

## 数据行操作

1. ### 新增数据

   ```mysql
   insert into table_name(value_name,.. , ..) values (value1, ..),（value2,..）;
   ```

2. ### 删除数据

   ```mysql
   delete from table_name where 条件
   ```

3. ### 修改数据

   ```mysql
   update table_name set column = value;
   update table_name set col = val,col = val;
   update table_name set col = val where 条件;
   ```

4. ### 查询数据

   ```mysql
   select * from 表名
   select 列名，列名 from 表名 where 条件
   ```

   

## 数据类型

- int

  ```
  int 		 有符号，取值范围：-2147483648~2147483647（有正有负）
  int unsigned 无符号，取值范围：0~4294967295（只有正）
  ```

  

- tinyint

```
tinyint	 		 有符号，取值范围：-128~127（有正有负）默认
tinyint unsigned 无符号，取值范围：0~255（只有正）
```



- bigint

  ```
  bigint  有符号，取值范围：
  	-9223372036854775808~9223372036854775807（有正有负）
  bigint unsigned 无符号，取值范围：
  	0~18446744073709551615（只有正）
  ```

- float

- double

- decimal(m,d)

  ```mysql
  准确的小数值，m是数字总个数（负号不算），d是小数点后个数，m<=65,d<=30
  
  例：
  create t1(
  	id int not null primary key auto_increment,
      salary decimal(8,2) -- 总共8位数字，小数点后2位
  )
  ```

- char，查询快

  ```mysql
  定长字符串 最多可容纳255
  
  name char(11),固定使用11个字符串进行存储，哪怕没有11个字符，在内存中也会占用11个字符
  
  create t1(
  	id int not null primary key auto_increment,
      name char(11)
  )
  ```

  

- varchar,节省空间

  ```mysql
  变长字符串 
  和char相反，实际有多长，内存就占用多少
  ```

- text

  ```mysql
  用于存储长文本，可以存储到65535 (2**16 -1)个字符
  ```

- mediumtext

- longtext

- datetime 

  ```mysql
  YYYY-MM-DD HH:MM:SS (2001-01-01 00:00:00)
  ```

- date

  ```
  YYYY-MM-DD (2001-01-01)
  ```

exercise：用户表

```mysql
create table User(
	id int primary key auto_increment,
    name varchar(62) not null,
    password char(64) not null,
    title varchar(128),
    age tinyint,
    salary decimal(10,2),
    ctim datetime
)
```



# python 进行增删改查

flask ORM模型为例：

```python
1. 配置文件
HOSTNAME  = "127.0.0.1"
PORT = 3306
USERNAME = "root"
PASSWORD = "password"
DATABASE = "db_name"
DB_URI = f'mysql+pymysql://{USERNAME}:{PASSWORD}@{HOSTNAME}:{PORT}/{DATABASE}?charset=utf8'
SQLALCHEMY_DATABASE_URI = DB_URI
2.
2. 引入插件
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

migrate = Migrate()
db = SQLAlchemy()
3. 创建模型
from exts import db

class xxModel(db.model):
    __tablename__:"xx"
    id = db.Column(db.Integer, 		primary_key=True,autoincrement=True)
    username = db.Column(db.String(20), unique=False, 	nullable=False)
    password = db.Column(db.String(20), nullable=False)
    mobile = db.Column(db.String(20), nullable=False)

4. 在c中将app和数据模型连接
from model import xxModel

app = Flask(__name__)
app.config.from_object('config')
db.init_app(app)
migrate.init_app(app, db)

flask db init
flask db migrate
flask db upgrade

5. 编写增删改查命令
from flask import Blueprint, render_template

from exts import db
from model import adminModel


bp = Blueprint('admin', __name__, url_prefix='/admin')


@bp.route('/add')
def add():
    username = "张三"
    password = "11111"
    mobile   = "12321312312"
    admin = adminModel(username = username, password = password,mobile= mobile)
    db.session.add(admin)
    db.session.commit()
    return  "添加成功"
@bp.route('/delete')
def delete():
    username = "张三"
    user = adminModel.query.filter_by(username= username).first()
    db.session.delete(user)
    db.session.commit()
    return "删除成功"


@bp.route('/update')
def update():
    username = "张三"
    user = adminModel.query.filter_by(username= username).first()
    user.password = "233333"
    db.session.commit()
    return "更新成功"

@bp.route('/get')
def get():
    username = "张三"
    user = adminModel.query.filter_by(username=username).first()
    return render_template('getadmin.html', admin=user)

6. 在app中注册蓝图
from blueprint.admin import bp
app.register_blueprint(bp)


```



