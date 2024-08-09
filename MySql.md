# 环境搭建



众所周知，在讲解安装之前我们需要先了解怎么去卸载它,-.-?

为什么呢，因为数据库的安装操作很可能会因为之前已经安装数据库而导致安装失败。而且当我们需要更换数据库时，最快的方法就是直接卸载老的，保留数据，然后装到新数据库中。

## 1. MySQL的卸载

### 步骤1：停止MySQL服务

首先在卸载之前我们需要停止mysql的服务。可以通过Ctrl+ALT+Delete键唤醒任务管理器，在“服务”中找到mysql80

### 步骤2：卸载MySQL软件

这应该不用说了把，windows自带卸载程序、电脑管家等卸载、或者用安装程序卸载Remove（开发者都觉得你安装的时候可能需要卸载）。如果是5.7版本，则需要手动删除注册项 

![image-20240807143229317](E:\Code\笔记\笔记图片\image-20240807143229317.png)

### 步骤3：清除ProgramData、环境变量

找到MySQL存放的ProgramData和环境变量可删可不删

### 步骤4：重启电脑

​	

## 2.MySQL的安装

2.1 软件下载

去官网，GPL，MSI，点默认（记得改下安装路径）

## 3.服务启动/停止

```mysql
net start MySQL服务名

net stop MySQL服务名
```

## 4.配置信息

1. 安装位置查询

   登录后输入 show variables like "%char%";

   ![image-20240729095105794](.\笔记图片\image-20240729095105794.png)

2. 找到创建数据库的位置 show variables like "%datadir%";

   ![image-20240729095347965](.\笔记图片\image-20240729095347965.png)

   注意：my.ini 配置文件也在这个路径中，当需要更改时，可以去通过该路径修改

3. 忘记密码  

   - 修改mysql配置文件  设置为无账号模式

     1.  停止现有Mysql服务

     2. 找到my.ini 在mysqld加上skip-grant-tables=1

     3. 重新启动Mysql 再次登录（无需密码）

     4. 执行命令

        ```mysql
        use mysql;
        update user set authentication_string =
        password('密码'),password_last_changed = now() where user ='root';
        
        ```

   8.0以上使用
        ① alter user 'root'@'localhost' identified by 'root';
        	② set password for root@localhost = '123456';

        ```mysql
   ALTER USER 'root'@'localhost' IDENTIFIED WITH 'mysql_native_password' BY 'your_new_password';
   CREATE USER 'root'@'%' IDENTIFIED WITH 'mysql_native_password' BY 'your_new_password';
   GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
   #允许从任何主机连接root用户
        ```

   

     5. 重新修改配置文件 删除 skip-grant-tables=1

   6. 如果使用的是5.0的话还需要更换默认字符集（因为它的默认字符集是latin1）

   修改步骤：

   ​	找到programdata中的my.ini配置文件

   ```
   [mysql]
   default-character-set=utf8
   
   [mysqld]
   dafault-set-server=utf8
   collation-server=utf8_general_ci
   ```

   

# 语句

## SQL分类

DDL（数据定义语言）:

CREATE（创建） \  ALTER（替换） \ DROP（删除） \ RENAME（重命名）\ TRUNCATE(清空表)

DML（数据操作语言）：

INSERT \ DELETE \ UPDATE \ SELECT   (增、删、改、查)

DCL（数据控制语言）:

COMMIT \ ROLLBACK \ SAVEPOINT \ GRANT \ REVOKE

数据库导入

```mysql
source 文件路径名
# 命令行内书写
```



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



# SELECT语句

```mysql
SELECT 字段1 AS 别名,字段2 "别名",字段3 别名 FROM 表名;
```

**去除重复行**（要放就放最前面）

```mysql
SELECT DISTINCT 字段 FROM 表名;
```

**空值参与运算**

**null 不等同于0,'','null'**

**空值参与运算**：结果一定也为空

**IFNULL():**如果记录为NULL，则使用替换值参与运算

```mysql
SELECT  IFNULL(字段，替换值) FROM 表名;
```

**着重号 ``**：如果表名和关键字和保留字冲突使用着重号告诉系统这不是系统的关键字

```mysql
SELECT * FROM `ORDER`;
```

**显示表结构**:显示表中字段的详细信息

```mysql
DESCRIBE/DESC 表名;
```

**使用where过滤数据**  ：紧挨在from后面 （嗯~ o(*￣▽￣*)o from 的小弟）

```mysql
SELECT * FROM 表名 WHERE 条件
```

## 课后练习题

```MYSQL
# 课后练习题

# 1.查询员工12个月的工资总和，并起别名为ANNUAL SALARY
SELECT (1+IFNULL(commission_pct,0))*12*salary AS "annual salary",first_name,last_name
from employees;

# 2.查询employees表中去除重复的job_id以后的数据
SELECT DISTINCT job_id
FROM employees;
# 3.查询工资大于12000的员工姓名和工资
SELECT first_name,last_name,salary
FROM employees
WHERE salary>12000;
# 4.查询员工号为176的员工的姓名和部门号
SELECT department_id,first_name,last_name
FROM employees
WHERE employee_id=176;
# 5.显示表 departments 的结构，并查询其中的全部数据
DESCRIBE departments;
SELECT * FROM departments;
```

# 运算符

## 算术运算符

‘+ ’加 	‘-’ 减  	‘*’ 乘 	‘/’ 除      ‘%’取余

## 比较运算符

```mysql
#二、比较运算符
# =，>, <，>=, <=， !=(不等于<>)，<=>（安全等于）：安全等于可以判断NULL专门为了判断NULL的
# 字符串存在隐式转换，如果转换不成功则将其看作0
#字符串比较时，则会按照ASCII码比较
#   NULL = NULL 只要有NULL参与结果就是NULL 

#查询 basic_salary!=10000
SELECT eid,basic_salary FROM t_salary WHERE basic_salary != 10000;
SELECT eid,basic_salary FROM t_salary WHERE basic_salary <> 10000;

#查询 basic_salary=10000，注意在 Java 中比较是==
SELECT eid,basic_salary FROM t_salary WHERE basic_salary = 10000;

#查询 commission_pct 等于 0.40
SELECT eid,commission_pct FROM t_salary WHERE commission_pct = 0.40;
SELECT eid,commission_pct FROM t_salary WHERE commission_pct <=> 0.40;

#查询 commission_pct 等于 NULL
SELECT eid,commission_pct FROM t_salary WHERE commission_pct IS NULL;
SELECT eid,commission_pct FROM t_salary WHERE commission_pct <=> NULL;

#查询 commission_pct 不等于 NULL
SELECT eid,commission_pct FROM t_salary WHERE commission_pct IS NOT NULL;
SELECT eid,commission_pct FROM t_salary WHERE NOT commission_pct <=> NULL;

#IS NULL 判断是否NULL
SELECT B FROM TABLE WHERE IS NULL

#IS NOT NULL 判断是否不为NULL
SELECT B FROM TABLE WHERE IS NOT NULL

# IN/NOT IN (SET) 属于运算符在离散值中寻找是否存在
SELECT * FROM TABLE WHERE ID IN/NOT IN (10,20,30);


# LEAST \GRATEST 找最大最小

# BETWEEN AND (包括边界值)
select * from table where column BETWEEN 下限 AND 上限 

# LIKE:模糊查询  

# %:代表不确定字数的字符（0个一个或多个）

# _:代表一个不确定的字符

# \：转义字符

# REGEXP\RLIKE：正则表达式
 https://docs.python.org/3/library/re.html
 
```

![image-20240808112050904](E:\Code\笔记\笔记图片\image-20240808112050904.png)

## 逻辑运算符

NOT ！  	非

AND && 	与

OR || 	或

XOR  	异或    满足一个不满足另外一个：追求结果的异同    



| 异或规则 | TRUE  | FALSE |
| -------- | ----- | ----- |
| TRUE     | FLASE | TRUE  |
| FALSE    | TRUE  | FLASE |

运算符的优先级：

![image-20240808112952464](E:\Code\笔记\笔记图片\image-20240808112952464.png)

## 位运算符

![image-20240808113052059](E:\Code\笔记\笔记图片\image-20240808113052059.png)

## 课后练习题

```mysql

【题目】
# 1.选择工资不在5000到12000的员工的姓名和工资
SELECT salary,last_name
FROM employees
WHERE salary NOT BETWEEN 5000 AND 12000;

# 2.选择在20或50号部门工作的员工姓名和部门号
SELECT first_name,last_name,department_id
FROM employees
WHERE department_id IN (20,50);

# 3.选择公司中没有管理者的员工姓名及job_id
SELECT first_name,last_name,job_id
FROM employees
WHERE manager_id IS NULL;

# 4.选择公司中有奖金的员工姓名，工资和奖金级别
SELECT first_name,last_name,salary,commission_pct
FROM employees
WHERE commission_pct IS NOT NULL;

# 5.选择员工姓名的第三个字母是a的员工姓名
SELECT first_name,last_name
FROM employees
WHERE first_name LIKE '__a%' or last_name LIKE '__a%';

# 6.选择姓名中有字母a和k的员工姓名
SELECT first_name,last_name
FROM employees
WHERE last_name REGEXP '[ak]' OR first_name REGEXP '[ak]' ;
# 7.显示出表 employees 表中 first_name 以 'e'结尾的员工信息
SELECT first_name,last_name
FROM employees
WHERE first_name REGEXP 'e$';
# 8.显示出表 employees 部门编号在 80-100 之间的姓名、工种
SELECT first_name,last_name,job_id
FROM employees
WHERE department_id BETWEEN 80 AND 100;
# 9.显示出表 employees 的 manager_id 是 100,101,110 的员工姓名、工资、管理者id
SELECT first_name,last_name,salary,manager_id
FROM employees
WHERE manager_id IN (100,101,110);
```

# 排序与分页

## 1.ORDER BY排序数据

### 1.1排序规则

使用OREDER BY

- ASC（ascend）：升序（order by默认）
- DESC（descend）：降序
- ORDER BY 在SELECT语句结尾
- 别名可以在ORDER BY中使用
  - (为什么WHERE不可用,因为执行时，先从from 再到where 然后才到select,此时别名才被定义，所以WHERE不能使用)

示例

```mysql
-- 一级排序
SELECT last_namem,job_id
FROM employees
WHERE department_id in (50,60,70)
ORDER BY hire_date DESC;

-- 二级排序 在一级排序时，还出现相同情况，则需要二级排序
SELECT employee_id,salary，department_id
FROM employees
ORDER BY department_id DESC,salary ASC;
```

## 2.LIMIT分页

### 2.1 分页

```mysql

-- 每页显示20条记录，此时显示第1页
SELECT last_name
FROM employees
LIMIT 0,20 -- 偏移量为0（从第一个开始），向下显示20条记录

-- 每页显示20条记录，此时显示第2页
SELECT last_name
FROM employees
LIMIT 20,20 -- 偏移量为20(从第21个开始)，向下显示20条记录

-- 每页显示pageSize条记录，测试显示第pageNo页
SELECT last_name
FROM employees
LIMIT （pageNo-1）*pageSize,pageSize
```

### 2.2  加入WHERE...ORDER BY....

```mysql
SELECT last_name,salary
FROM employees
WHERE salary >6000
ORDER BY salary DESC
LIMIT 0,10; 
```

### MySQL8.0:新特性

```mysql
SELECT last_name,salary
FROM employees
LIMIT  显示数量 OFFSET 偏移量; 
```

好处：网络传输量变小，提升查询效率

### 课后练习题

```mysql
#1. 查询员工的姓名和部门号和年薪，按年薪降序,按姓名升序显示  注意order by不能识别带引号的别名
SELECT last_name,department_id,(salary*12) annual_salary
FROM employees
ORDER BY annual_salary DESC,last_name;
#2. 选择工资不在 8000 到 17000 的员工的姓名和工资，按工资降序，显示第21到40位置的数据
SELECT last_name,salary
FROM employees
WHERE salary NOT BETWEEN 8000 AND 17000
ORDER BY salary DESC
LIMIT 20,20;

#3. 查询邮箱中包含 e 的员工信息，并先按邮箱的字节数降序，再按部门号升序

SELECT last_name,email,department_id
FROM employees
#where email like '%e%'
WHERE email REGEXP '[e]'
ORDER BY LENGTH(email) DESC,department_id ASC;
```

# 多表查询

多表查询，也被称为关联查询，指两个或多个表一同完成查询操作。

前提，它们之间一定存在着关系（一对多，一对一），一定有关联字段，这个字段可能有设置外键，也可能没设置。

## 1.1案例说明

![image-20240809102342905](E:\Code\笔记\笔记图片\image-20240809102342905.png)



## 1.2多表查询的原因

为什么需要多表查询避免多次访问服务器，避免冗余字段的出现，浪费空间

## 1.3多表查询实现

### 笛卡尔积（交叉连接）

​	假设现在有两个集合X和Y，x有（1,2,3）3个数字，y中有（4，5）现在分别从两个集合中选取一个数字组合，组合的个数就是两集合元素个数相乘，此处为6个组合，得出的积就被称为笛卡尔积，

​	在SQL中，笛卡尔积也被称为CROSS  JOIN,将任意表进行连接，即使这两张表不相关，也会出现笛卡尔积。

```mysql
SELECT last_name, department_name
FROM employees, departments;
-- 每个员工和每个部门都匹配了一次，出现了错误，因为我们缺少了连接的条件

SELECT last_name, department_name
FROM employees, departments
WHERE employees.department_id = departments.department_id;
-- 添加条件后可以正确显示
-- 多表查询的逻辑为先生成一张笛卡尔表，再通过WHERE 进行条件筛选

-- 最后练习 查询员工所在的城市 
SELECT last_name, department_name,city
FROM employees emp, departments dep,locations loc;
WHERE emp.department_id = ep.department_id 
and dep.location_id = loc.location_id;
```

![image-20240809103558428](E:\Code\笔记\笔记图片\image-20240809103558428.png)

### 注意

1. 从**SQL优化**的角度来看，建议**多表查询**时，**每个字段都指明其所在的表**。（因为要用**索引**），
2. 为了提高可读性，可以为表起别名，**注意有别名后必须使用别名**！
3. 如果有**n**个表，进行多表查询就需要至少**n-1**个连接条件

## 1.4 多表查询分类（分类角度）

### 等值连接 vs 非等值连接

上述情况就是等值连接，所以此时主要做非等值连接的演示

```mysql
SELECT last_name,salary ,grade_level
FROM employees e,job_grades j;
WHERE e.salary BETWEEN j.lowest_sal AND j.highest_sal;

```



### 自连接 vs 非自连接

```mysql
-- 自连接 自己连接自己
SELECT emp.last_name,emp.employee_id_id,manager.employee_id_id,manager.last_name
FROM employees emp,employees manager
WHERE emp.manager_id = manager.employee_id
```





### 内连接 vs 外连接

内连接：合并具有一列的两个以上的表的行，结果集中不包含一个表与另一个表不匹配的行

```mysql
SELECT last_name, department_name
FROM employees, departments -- 只是把左表和右表满足条件的数据查出
WHERE employees.department_id = departments.department_id;
```

#### 外连接

合并具有一列的两个以上的表的行，结果集中除了包含一个表与另一个表匹配的行外，还有左表或右表中不匹配的行。

#### 外连接分类

左外连接（包含左表不满足条件），右外连接（包含右表不满足条件），满外连接（包含左、右表不满足条件）

```mysql
-- 查询所有的员工last_name,depart_name

-- SQL92实现内连接
SELECT last_name, department_name
FROM employees, departments 
WHERE employees.department_id = departments.department_id;

-- SQL92实现外连接，使用 + ，给数据缺少的表加上字段 ------MySQL不支持SQL92语法
SELECT last_name, department_name
FROM employees, departments 
WHERE employees.department_id = departments.department_id(+);

-- SQL99 使用JOIN .. ON的方法，并且MySQL支持

-- SQL99实现内连接
SELECT last_name,department_name
FROM employees e (INNER) JOIN departments d
ON e.department_id = d.department_id;
JOIN locations l
ON d.location_id = l.location_id

-- SQL99实现外连接
SELECT last_name,department_name
FROM employees e LEFT (OUTER) JOIN departments d
ON e.department_id = d.department_id;
JOIN locations l
ON d.location_id = l.location_id

-- SQL99 满外连接 MySQL不支持
SELECT last_name,department_name
FROM employees e FULL (OUTER) JOIN departments d
ON e.department_id = d.department_id;
JOIN locations l
ON d.location_id = l.location_id

实现效果

```

#### UNION 使用

使用UNION ，可以将多条SELECT语句结合，并将他们的结果组合成单个结果集。

条件：俩个表对应的列数和数据类型必须相同，并且相互对应。如果不使用UNION ALL，会去除重复记录，使用了UNION ALL则不会去重

```mysql
SELECT *
FROM employees
WHERE department_id>90
UNION
SELECT *
FROM employees
WHERE email LIKE '%a%';
```



#### 7种join

![image-20240809115006748](E:\Code\笔记\笔记图片\image-20240809115006748.png)

### 自然连接

会自动将查询两张连接表中**所有相同的字段**，然后进行**等值连接**

示例：

```mysql
-- SQL99
SELECT employee_id,last_name,department_name
FROM employees e NATURAL JOIN departments d;
-- SQL92
SELECT employee_id,last_name,department_name
FROM employees e JOIN departments d
ON e.department_id = d.department_id
AND e.manager_id = d.manager_id;
```

### USING 

```mysql
SELECT employee_id,last_name,department_name
FROM employees e JOIN departments d
ON e.department_id = d.department_id;
-- 等价于 适用于同名字段
SELECT employee_id,last_name,department_name
FROM employees e JOIN departments d
USING(department_id);
```

## 总结

多表查询的约束条件有 JOIN ON 、WHERE、 USING

**WHERE**：使用WHERE 进行多表查询的底层逻辑是，首先系统将两个表生成一个笛卡尔积，之后通过WHERE筛选条件

**JOIN ON**：使用JOIN ON 将多表分类查询分为两中情况，一个是内连接，另一种是外连接。此外，外连接也可以分为 LEFT JOIN 和 RIGHT JOIN，这取决于我们想要选择的表格，当我们的想查询的表格在左侧时，我们使用LEFT JOIN，相反我们使用RIGHT JOIN，但注意，一般就用LEFT JOIN就好（毕竟就是换个位置的事）。

**USING**: 就是ON的简便写法，但**前提**是他们的**字段名相同**才能使用。



注意：

1. 超过3个表严禁使用join,并且需要join的字段，数据类型必须保持绝对一致，保证被关联的字段需要有索引
2. 当我们要使用多表连接查找所有值时，都需要使用外连接，而且而当我们需要得到在a中存在但b中不存在时，我们只需要在外连接的基础上添加一个不满足的条件。

## 课后练习题

```mysql
# 1.显示所有员工的姓名，部门号和部门名称。

SELECT e.last_name,e.department_id,d.department_name
FROM employees e left join departments d
ON e.department_id  =d.department_id;

# 2.查询90号部门员工的job_id和90号部门的location_id
SELECT job_id, location_id
FROM employees e, departments d
WHERE e.`department_id` = d.`department_id`
AND e.`department_id` = 90;
# 3.选择所有有奖金的员工的 last_name , department_name , location_id , city
SELECT last_name , department_name , d.location_id , city
FROM employees e
LEFT OUTER JOIN departments d
ON e.`department_id` = d.`department_id`
LEFT OUTER JOIN locations l
ON d.`location_id` = l.`location_id`
WHERE commission_pct IS NOT NULL;

# 4.选择city在Toronto工作的员工的 last_name , job_id , department_id , department_name 
SELECT last_name,job_id,e.department_id,department_name
FROM employees e ,departments d ,locations l
WHERE city = 'Toronto'
AND e.department_id = d.department_id
AND d.location_id = l.location_id;
-- 或者
SELECT last_name , job_id , e.department_id , department_name
FROM employees e
JOIN departments d
ON e.`department_id` = d.`department_id`
JOIN locations l
ON l.`location_id` = d.`location_id`
WHERE l.`city` = 'Toronto';
# 5.查询员工所在的部门名称、部门地址、姓名、工作、工资，其中员工所在部门的部门名称为’Executive’ (内连接)
SELECT department_name, street_address, last_name, job_id, salary
FROM employees e JOIN departments d
ON e.department_id = d.department_id
JOIN locations l
ON d.`location_id` = l.`location_id`
WHERE department_name = 'Executive'
# 6.选择指定员工的姓名，员工号，以及他的管理者的姓名和员工号，结果类似于下面的格式 (自连接)
employees Emp# manager Mgr#
kochhar 101 king 100

SELECT emp.last_name employees, emp.employee_id "Emp#", mgr.last_name manager,
mgr.employee_id "Mgr#"
FROM employees emp
LEFT OUTER JOIN employees mgr
ON emp.manager_id = mgr.employee_id;
# 7.查询哪些部门没有员工
SELECT d.department_name
FROM departments d LEFT JOIN employees e
ON e.department_id = d.`department_id`
WHERE e.department_id IS NULL
# 8. 查询哪个城市没有部门
SELECT l.city
FROM locations l left join  departments d
ON l.location_id = d.location_id
WHERE d.location_id is null;
# 9. 查询部门名为 Sales 或 IT 的员工信息
SELECT employee_id,last_name,department_name
FROM employees e,departments d
WHERE e.department_id = d.`department_id`
AND d.`department_name` IN ('Sales','IT');
```

```mysql
#1.所有有门派的人员信息

SELECT e.name,deptName
FROM t_dept d JOIN t_emp e
ON d.id =e.deptId;
（ A、B两表共有）
#2.列出所有用户，并显示其机构信息
SELECT e.name,d.deptName
FROM t_emp e left join t_dept d
ON  e.deptId = d.id;
（A的全集）
#3.列出所有门派
select *
from t_dept;
（B的全集）
#4.所有不入门派的人员
SELECT *
FROM t_emp e LEFT JOIN t_dept d
ON e.deptId = d.id
WHERE d.Id IS NULL;
（A的独有）
#5.所有没人入的门派
SELECT *
FROM t_dept d LEFT JOIN t_emp e
ON e.deptId = d.id
WHERE e.deptId IS NULL;
（B的独有）
#6.列出所有人员和机构的对照关系
(AB全有)
#MySQL Full Join的实现 因为MySQL不支持FULL JOIN,下面是替代方法
#left join + union(可去除重复数据)+ right join
SELECT *
FROM t_dept d LEFT JOIN t_emp e
ON e.deptId = d.id
UNION
SELECT *
FROM t_dept d RIGHT JOIN t_emp e
ON e.deptId = d.id
WHERE d.id IS NULL;
-- 或者 UNION ALL
SELECT *
FROM t_dept d LEFT JOIN t_emp e
ON e.deptId = d.id
UNION ALL
SELECT *
FROM t_dept d RIGHT JOIN t_emp e
ON e.deptId = d.id
WHERE d.id IS NULL;


#7.列出所有没入派的人员和没人入的门派
SELECT *
FROM t_dept d LEFT JOIN t_emp e
ON e.deptId = d.id
WHERE e.deptId is null
UNION
SELECT *
FROM t_dept d RIGHT JOIN t_emp e
ON e.deptId = d.id
WHERE d.id IS NULL;
（A的独有+B的独有
```



# SQL标准



## SQL92

## SQL99

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
from model import xxModel


bp = Blueprint('xx', __name__, url_prefix='/xx')


@bp.route('/add')
def add():
    username = "张三"
    password = "11111"
    mobile   = "12321312312"
    admin = xxModel(username = username, password = password,mobile= mobile)
    db.session.add(admin)
    db.session.commit()
    return  "添加成功"
@bp.route('/delete')
def delete():
    username = "张三"
    user = xxModel.query.filter_by(username= username).first()
    db.session.delete(user)
    db.session.commit()
    return "删除成功"


@bp.route('/update')
def update():
    username = "张三"
    user = xxModel.query.filter_by(username= username).first()
    user.password = "233333"
    db.session.commit()
    return "更新成功"

@bp.route('/get')
def get():
    username = "张三"
    user = xxModel.query.filter_by(username=username).first()
    return render_template('getadmin.html', admin=user)

6. 在app中注册蓝图
from blueprint.admin import bp
app.register_blueprint(bp)
```



