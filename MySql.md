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



# 函数



我们在使用 SQL 语言的时候，不是直接和这门语言打交道，而是通过它使用不同的数据库软件，即DBMS。**DBMS** **之间的差异性很大，远大于同一个语言不同版本之间的差异。**实际上，只有很少的函数是被 DBMS 同时支持的。比如，大多数 DBMS 使用（||）或者（+）来做拼接符，而在 MySQL 中的字符串拼

接函数为concat()。大部分 DBMS 会有自己特定的函数，这就意味着**采用** **SQL** **函数的代码可移植性是很差的**，因此在使用函数的时候需要特别注意



MySQL提供的内置函数从 实现的功能角度 可以分为数值函数、字符串函数、日期和时间函数、流程控制函数、加密与解密函数、获取MySQL信息函数、聚合函数等。这里，我将这些丰富的内置函数再分为两类： 单行函数 、 聚合函数（或分组函数） 。

![image-20240810094535423](E:\Code\笔记\笔记图片\image-20240810094535423.png)

## 数值函数

### 基本函数

![image-20240810094414187](E:\Code\笔记\笔记图片\image-20240810094414187.png)

### 角度与弧度互换函数

![image-20240810095538569](E:\Code\笔记\笔记图片\image-20240810095538569.png)

### 三角函数

![image-20240810095501601](E:\Code\笔记\笔记图片\image-20240810095501601.png)

### 指数对数函数

![image-20240810100250580](E:\Code\笔记\笔记图片\image-20240810100250580.png)

### 进制函数

![image-20240810100650866](E:\Code\笔记\笔记图片\image-20240810100650866.png)

## 字符串函数

字符串索引是从1开始的

![image-20240810101013501](E:\Code\笔记\笔记图片\image-20240810101013501.png)

![image-20240810101036814](E:\Code\笔记\笔记图片\image-20240810101036814.png)



## 日期函数

### 获取日期时间

![image-20240810104553382](E:\Code\笔记\笔记图片\image-20240810104553382.png)

### UNIX与时间相互转换

![image-20240810104917672](E:\Code\笔记\笔记图片\image-20240810104917672.png)



### 获取月份、星期、星期数、天数等函数

![image-20240810105024097](E:\Code\笔记\笔记图片\image-20240810105024097.png)

### 从日期中提取特定值

![image-20240810105409004](E:\Code\笔记\笔记图片\image-20240810105409004.png)

### 时间和秒钟转换的函数



![image-20240810105441462](E:\Code\笔记\笔记图片\image-20240810105441462.png)

### 计算日期和时间的函数

![image-20240810105529052](E:\Code\笔记\笔记图片\image-20240810105529052.png)

![image-20240810105600271](E:\Code\笔记\笔记图片\image-20240810105600271.png)

第2组

![image-20240810105729889](E:\Code\笔记\笔记图片\image-20240810105729889.png)

### 日期格式化

![image-20240810110318954](E:\Code\笔记\笔记图片\image-20240810110318954.png)

非GET_FORMAT的格式为

![image-20240810110853286](E:\Code\笔记\笔记图片\image-20240810110853286.png)

GET_FORMAT格式为

![image-20240810111428505](E:\Code\笔记\笔记图片\image-20240810111428505.png)

![image-20240810110929560](E:\Code\笔记\笔记图片\image-20240810110929560.png)



## 流程控制函数

流程处理函数可以根据不同的条件，执行不同的流程，包括IF(),IFNULL(),CASE()函数

![image-20240810111712832](E:\Code\笔记\笔记图片\image-20240810111712832.png)

SQL自带循环语句

## 加密解密

![image-20240810113149644](E:\Code\笔记\笔记图片\image-20240810113149644.png)

用户应该在后端传输时就使用一次加密

PASSWORD( )，ENCODE（）,DECODE( ) 在mysql8.0时已不支持使用，在5.7中支持

## 信息函数

![image-20240810113748950](E:\Code\笔记\笔记图片\image-20240810113748950.png)



## 其他函数

![image-20240810114304419](E:\Code\笔记\笔记图片\image-20240810114304419.png)

![image-20240810114555472](E:\Code\笔记\笔记图片\image-20240810114555472.png)

## 课后练习

```mysql
# 1.显示系统时间(注：日期+时间)
SELECT NOW()
FROM dual;

# 2.查询员工号，姓名，工资，以及工资提高百分之20%后的结果（new salary）
SELECT employee_id,last_name,salary,salary*1.2 'new salary'
FROM employees e;

# 3.将员工的姓名按首字母排序，并写出姓名的长度（length）
SELECT last_name,length(last_name)
FROM employees e
ORDER BY last_name DESC ;

# 4.查询员工id,last_name,salary，并作为一个列输出，别名为OUT_PUT
select concat_ws('-',employee_id,last_name,salary) 'OUT__PUT'
from employees;

# 5.查询公司各员工工作的年数、工作的天数，并按工作年数的降序排序
SELECT DATEDIFF(SYSDATE(), hire_date) / 365 worked_years, DATEDIFF(SYSDATE(),
hire_date) worked_days
FROM employees
ORDER BY worked_years DESC

# 6.查询员工姓名，hire_date , department_id，满足以下条件：雇用时间在1997年之后，department_id 为80 或 90 或110, commission_pct不为空
SELECT last_name, hire_date, department_id
FROM employees
#WHERE hire_date >= '1997-01-01'
#WHERE hire_date >= STR_TO_DATE('1997-01-01', '%Y-%m-%d')
WHERE DATE_FORMAT(hire_date,'%Y') >= '1997'
AND department_id IN (80, 90, 110)
AND commission_pct IS NOT NULL

# 7.查询公司中入职超过10000天的员工姓名、入职时间
SELECT last_name,hire_date
FROM employees e
#WHERE TO_DAYS(NOW()) - to_days(hire_date) > 10000;
WHERE DATEDIFF(NOW(),hire_date) >10000;

# 8.做一个查询，产生下面的结果
<last_name> earns <salary> monthly but wants <salary*3>
SELECT CONCAT(last_name,' earns ',salary,' monthly but wants ',3*salary) as 'Dream salary'
FROM employees;
```

![image-20240810142354516](E:\Code\笔记\笔记图片\image-20240810142354516.png)

```mysql
# 9.使用case-when，按照下面的条件：
job grade
AD_PRES A
ST_MAN B
IT_PROG C
SA_REP D
ST_CLERK E
产生下面的结果:

SELECT CONCAT(last_name,' earns ',salary,' monthly but wants ',3*salary) as 'Dream salary'
FROM employees;

SELECT last_name,job_id,CASE job_id WHEN 'AD_PRES'THEN 'A'
                                    WHEN 'ST-MAN' THEN 'B'
                                    WHEN 'IT_PROG'THEN 'C'
                                    WHEN 'SA_REP' THEN 'D'
                                    WHEN 'ST_CLERK' THEN 'E'
                                    ELSE 'F'
                                    END
FROM employees e;
```

![image-20240810142431107](E:\Code\笔记\笔记图片\image-20240810142431107.png)

# 聚合函数

定义：输入多个记录但是最后出来的结果只有一条，作用于一组数据，并对一组数据返回一个值

## 聚合函数类型

- AVG() : 平均值  AVG() = SUM()/COUNT()
- SUM()：总和 

以上两个方法只能适用于数据类型为数字的字段或变量

- MAX()：最大值

- MIN()：最小值

  

适用于数值类型，字符串类型，日期时间类型

- COUNT()：计算指定字段的个数，但是如果字段中的值为NULL则不计算个数

上述的聚合函数都不会考虑NULL值,并且**聚合函数**无法**嵌套**使用

如果需要统计表中的记录数

![image-20240810155338952](E:\Code\笔记\笔记图片\image-20240810155338952.png)

## group by

根据字段完成表的分组

```mysql
SELECT job_id,AVG(salary)
FROM employees e
group by job_id;
```



使用多个列完成分组

```mysql
-- 查询各个department_id，job_id的平均工资
SELECT department_id,job_id,AVG(salary)
FROM employees e
group by department_id,job_id;
```

注意：

	1. SELECT 中出现的聚合函数的字段必须声明在GROUP BY中
 	2. GROUP BY的位置位于WHERE 后面,ORDER BY、LIMIT前面
 	3. 使用WITH ROLLUP 会再计算一次整体数据的值

```mysql
SELECT job_id,AVG(salary)
FROM employees e
group by job_id with rollup
```

## HAVING

基本使用：用于聚合函数的过滤条件 (其实是运行顺序的原因)

声明位置：GROUP BY之后

前提：在使用GROUP BY分组后才有必要使用HAVING

当过滤条件没有聚合函数时，则此过滤条件建议声明在WHERE中

```mysql
-- 查询各个不萌中最高工资比10000高的部门信息
SELECT department_id,MAX(salary)
FROM employees
GROUP BY department_id
HAVING MAX(salary) >10000;
```

```mysql
-- 查询部门id为10，20，30，40这4个部门中最高工资比10000高的部门信息
SELECT department_id,MAX(salary)
FROM employees
WHERE department_id in(10,20,30,40)
GROUP BY department_id
HAVING MAX(salary)>10000

-- 或者
SELECT department_id,MAX(salary)
FROM employees
GROUP BY department_id
HAVING MAX(salary)>10000 AND department_id in(10,20,30,40);

-- 
/*推荐使用方式1，执行效率更高

*/
```

![image-20240810162653533](E:\Code\笔记\笔记图片\image-20240810162653533.png)

## 课后练习

```mysql
#1.where子句可否使用组函数进行过滤?
不可以，因为where使用时，SQL语句还并未执行到SELECT,此时SQL完全不知道组函数是什么东西
#2.查询公司员工工资的最大值，最小值，平均值，总和
SELECT MAX(salary), MIN(salary), AVG(salary), SUM(salary)
FROM employees;
#3.查询各job_id的员工工资的最大值，最小值，平均值，总和
SELECT job_id,MAX(salary), MIN(salary), AVG(salary), SUM(salary)
FROM employees
GROUP BY job_id;
#4.选择具有各个job_id的员工人数
SELECT job_id, COUNT(*)
FROM employees
GROUP BY job_id;
# 5.查询员工最高工资和最低工资的差距（DIFFERENCE）
SELECT MAX(salary), MIN(salary), MAX(salary) - MIN(salary) DIFFERENCE
FROM employees;
# 6.查询各个管理者手下员工的最低工资，其中最低工资不能低于6000，没有管理者的员工不计算在内
SELECT manager_id,min(salary)
FROM employees e
WHERE manager_id IS NOT NULL
group by manager_id
HAVING min(salary)>=6000;

# 7.查询所有部门的名字，location_id，员工数量和平均工资，并按平均工资降序
SELECT department_name, location_id, COUNT(employee_id), AVG(salary) avg_sal
FROM employees e RIGHT JOIN departments d
ON e.`department_id` = d.`department_id`
GROUP BY department_name, location_id
ORDER BY avg_sal DESC;
# 8.查询每个工种、每个部门的部门名、工种名和最低工资
SELECT d.department_name,e.job_id,min(salary)
FROM employees e RIGHT JOIN departments d
ON e.department_id = d.department_id
GROUP BY department_name, job_id
UNION ALL
SELECT d.department_name,e.job_id,min(salary)
FROM employees e LEFT JOIN departments d
ON e.department_id = d.department_id
WHERE d.department_id IS NULL
GROUP BY department_name, job_id;
```



# SQL执行顺序

```mysql
/*

SQL92
1. SELECT (DISTINCT)...存在聚合函数
2. FROM .....,....
3. WHERE  (多表连接的条件)(不包含聚合函数的过滤条件)
4. GROUP BY
5. HAVING (包含聚合函数的过滤条件)
6 .ORDER BY (ASC,DESC)
7. LIMIT

SQL99
1. SELECT (DISTINCT)...存在聚合函数
2. FROM .....,(LEFT) (JOIN) .... (ON)...
3. WHERE (不包含聚合函数的过滤条件)
4. GROUP BY
5. HAVING (包含聚合函数的过滤条件)
6 .ORDER BY
7. LIMIT

*/
```

[FROM -> ON -> (LEFT/RIGHT JOIN)  -> WHERE -> GROUP BY -> HAVING] ->[ SELECT -> DISTINCT ]-> [ORDER BY -> LIMIT]

SELECT 是先执行 FROM 这一步的。在这个阶段，如果是多张表联查，还会经历下面的几个步骤：

1. 首先先通过 CROSS JOIN 求笛卡尔积，相当于得到虚拟表 vt（virtual table）1-1；

2. 通过 ON 进行筛选，在虚拟表 vt1-1 的基础上进行筛选，得到虚拟表 vt1-2；

3. 添加外部行。如果我们使用的是左连接、右链接或者全连接，就会涉及到外部行，也就是在虚拟表 vt1-2 的基础上增加外部行，得到虚拟表 vt1-3。

当然如果我们操作的是两张以上的表，还会重复上面的步骤，直到所有表都被处理完为止。这个过程得到是我们的原始数据。

然后进入第三步和第四步，也就是 GROUP 和 HAVING 阶段 。在这个阶段中，实际上是在虚拟表 vt2 的基础上进行分组和分组过滤，得到中间的虚拟表 vt3 和 vt4 。

当我们完成了条件筛选部分之后，就可以筛选表中提取的字段，也就是进入到 SELECT 和 DISTINCT阶段 。

首先在 SELECT 阶段会提取想要的字段，然后在 DISTINCT 阶段过滤掉重复的行，分别得到中间的虚拟表vt5-1 和 vt5-2 。

当我们提取了想要的字段数据之后，就可以按照指定的字段进行排序，也就是 ORDER BY 阶段 ，得到虚拟表 vt6 。

最后在 vt6 的基础上，取出指定行的记录，也就是 LIMIT 阶段 ，得到最终的结果，对应的是虚拟表vt7 。

当然我们在写 SELECT 语句的时候，不一定存在所有的关键字，相应的阶段就会省略。

同时因为 SQL 是一门类似英语的结构化查询语言，所以我们在写 SELECT 语句的时候，还要注意相应的

关键字顺序，**所谓底层运行的原理，就是我们刚才讲到的执行顺序。**



# 子查询

**大白话**：一个查询里嵌套了另外的查询

在很多时候我们查询需要从结果集中获取数据，或者需要从同一个表中先计算得出一个数据结果，然后与这个数据结果进行比较

子查询效率低

```mysql
-- 谁的工资比Abel高
SELECT last_name  #外查询（主查询）
from employee e
where (salary > 
	SELECT salary 
	from employee e 
	where last_name = 'Abel' #内查询（子查询）
      );
```

**注意：**

​	子查询要包含在**括号**内

​	将子查询放在比较条件的**右侧**

​	单行操作对应单行子查询，多行操作对应多行子查询

## 子查询分类

单行子查询 vs 多行子查询 ：按照子查询的结果返回一条还是多个来分类

相关子查询vs 非相关子查询： 

![image-20240811100835816](E:\Code\笔记\笔记图片\image-20240811093044974.png)

```MYSQL
-- 工资大于本部门平均工资的员工信息
SELECT *
FROM employee 
WHERE salary > (
				SELECT AVG(salary)
    			FROM employee
    			GROUP BY department_id
)
;
```



### 单行子查询

![image-20240810205751077](E:\Code\笔记\笔记图片\image-20240810205751077.png)

HAVING 中的子查询

```mysql
-- 查询最低工资大于50号部门最低工资的部门id及其最低工资
SELECT department_id,MIN(salary)
from employee
GROUP BY department_id
HAVING AVG(salary) >(
				select SELECT MIN(salary)
                from employee
    			where department_id = 50
				);
```

### 多行子查询

内查询返回多行

使用多行比较操作符



多行子查询操作符

![image-20240811093044974](E:\Code\笔记\image-20240811093044974.png)

```mysql
-- 查询平均工资最低的部门id

SELECT department_id
FROM employees
GROUP BY department_id
HAVING AVG(salary)<= ALL(
				SELECT AVG(salary)
    			FROM employees
    			GROUP BY department_id
);
```

### 相关子查询

![image-20240811100941845](E:\Code\笔记\笔记图片\image-20240811100941845.png)

EXITS与NOT EXITS 关键字

![image-20240811104030995](E:\Code\笔记\笔记图片\image-20240811104030995.png)

```mysql
-- 查询公司管理者的employee_id,last_name,job_id,department_id

-- 不使用EXITS

SELECT DISTINCT m.employee_id,m.last_name,m.job_id,m.department_id
FROM employees e,employees m
WHERE e.manager_id =m.employee_id 
    
SELECT employee_id,last_name,job_id,department_id
FROM employees
WHERE employee_id IN (    
    SELECT DISTINCT manager_id
    FROM employees
	);

-- 使用EXITS
SELECT employee_id,last_name,job_id,department_id
FROM employees e1
WHERE EXISTS (    
    SELECT *
    FROM employees e2
    WHERE e1.employee_id = e2.manager_id
	);
```

说明：**子查询使用主查询中的列**

```mysql
-- 查询员工中工资大于本部门平均工资的员工的last_name,salary和其department_id

SELECT last_name,salary,department_id
FROM employees outer
WHERE salary >(
				SELECT AVG(salary)
                FROM employees
   				WHERE department_id = outer.department_id
                GROUP BY department_id

);
                
```



```mysql
-- 查询员工的id,salary,按照department_name 排序  在order by中使用子查询

SELECT empolyee_id,salary
FROM employees e
ORDER BY(
    SELECT department_name
    FROM departments d
    WHERE e.department_id =e.department_id
    );
```

子查询位置：除了GROUP BY和LIMIT外都可以使用

## 课后练习

```mysql
#1.查询和Zlotkey相同部门的员工姓名和工资
-- 自连接
SELECT e1.last_name,e1.salary
FROM employees e1,employees e2
WHERE e2.last_name = 'Zlotkey'
AND e2.department_id = e1.department_id
AND e1.last_name != 'Zlotkey';

-- 子查询
SELECT last_name, salary
FROM employees
WHERE department_id = (
SELECT department_id
FROM employees
WHERE last_name = 'Zlotkey'
);
#2.查询工资比公司平均工资高的员工的员工号，姓名和工资。
SELECT employee_id,last_name,salary
FROM employees
WHERE salary >(
                SELECT AVG(salary)
                FROM employees
                );
                
#3.选择工资大于所有JOB_ID = 'SA_MAN'的员工的工资的员工的last_name, job_id, salary
SELECT last_name,job_id,salary
FROM employees
WHERE salary > ALL(
    SELECT salary
    FROM employees
    WHERE job_id ='SA_MAN'
);

#4.查询和姓名中包含字母u的员工在相同部门的员工的员工号和姓名
SELECT employee_id, last_name
FROM employees
WHERE department_id = ANY(
SELECT DISTINCT department_id
FROM employees
WHERE last_name LIKE '%u%'
)；

#5.查询在部门的location_id为1700的部门工作的员工的员工号
SELECT employee_id
FROM employees
WHERE department_id IN (
                        SELECT department_id
                        FROM departments
                        where location_id = 1700
    );
    
#6.查询管理者是King的员工姓名和工资
select last_name,salary
FROM employees
WHERE manager_id in (
    SELECT employee_id
    FROM employees
    where last_name='KING'
    );

#7.查询工资最低的员工信息: last_name, salary
SELECT last_name,salary
FROM employees
WHERE salary = (
                SELECT MIN(salary)
                FROM employees
    );


#8.查询平均工资最低的部门信息
SELECT *
FROM departments
WHERE department_id = (
SELECT department_id
FROM employees
GROUP BY department_id
HAVING AVG(salary) <= ALL(
SELECT AVG(salary) avg_sal
FROM employees
GROUP BY department_id
)
);


#9.查询平均工资最低的部门信息和该部门的平均工资（相关子查询）
SELECT d.*,(SELECT AVG(salary) FROM employees WHERE department_id = d.`department_id`)
avg_sal
    FROM departments d
    WHERE department_id = (
        SELECT department_id
        FROM employees
        GROUP BY department_id
        HAVING AVG(salary) <= ALL(
            SELECT AVG(salary) avg_sal
            FROM employees
            GROUP BY department_id
)
);

#10.查询平均工资最高的 job 信息
SELECT *
FROM jobs
WHERE job_id = (
    SELECT job_id
	FROM employees
	GROUP BY job_id
	HAVING AVG(salary) >=all(
        SELECT AVG(salary)
        FROM employees
        GROUP BY job_id
)
    );
    
 
#11.查询平均工资高于公司平均工资的部门有哪些?
SELECT department_id,AVG(salary)
FROM employees
WHERE department_id IS NOT NULL 
GROUP BY department_id
HAVING AVG(salary) >=(
    SELECT AVG(salary)
    FROM employees
    );

#12.查询出公司中所有 manager 的详细信息
SELECT DISTINCT e2.*
FROM employees e1,employees e2
WHERE e1.manager_id =e2.employee_id;

#13.各个部门中 最高工资中最低的那个部门的 最低工资是多少?
SELECT MIN(salary)
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM employees
    GROUP BY department_id
    HAVING MAX(salary) =(
            SELECT MAX(salary)
            FROM employees
        )
    );

#14.查询平均工资最高的部门的 manager 的详细信息: last_name, department_id, email, salary
SELECT employee_id,last_name, department_id, email, salary
FROM employees
    WHERE employee_id IN (
        SELECT DISTINCT manager_id
        FROM employees
        WHERE department_id = (
            SELECT department_id
            FROM employees e
            GROUP BY department_id
            HAVING AVG(salary)>=ALL(
                SELECT AVG(salary)
                FROM employees
                GROUP BY department_id
        )
    )
);

#15. 查询部门的部门号，其中不包括job_id是"ST_CLERK"的部门号
SELECT department_id
FROM departments d
WHERE department_id NOT IN (
    SELECT DISTINCT department_id
    FROM employees
    WHERE job_id = 'ST_CLERK'
);

#16. 选择所有没有管理者的员工的last_name
SELECT last_name
FROM employees e
WHERE NOT EXISTS (
        SELECT *
        FROM employees e2
        where e.manager_id = e2.employee_id
          );


#17．查询员工号、姓名、雇用时间、工资，其中员工的管理者为 'De Haan'
SELECT last_name,employee_id,hire_date
FROM employees
WHERE manager_id = (
    SELECT employee_id
    FROM employees
    WHERE last_name = 'De Haan'
    );

#18.查询各部门中工资比本部门平均工资高的员工的员工号, 姓名和工资（相关子查询）
SELECT last_name,employee_id,salary
FROM employees e1
where salary >(
    select avg(salary)
    from employees e2
    where e1.department_id  =e2.department_id
    ); 
#19.查询每个部门下的部门人数大于 5 的部门名称（相关子查询）
SELECT department_name
FROM departments d
WHERE 5 < (
        select  count(*)
        FROM employees e
        WHERE e.department_id =d.department_id
    );


#20.查询每个国家下的部门个数大于 2 的国家编号（相关子查询）
SELECT country_id
FROM locations l
    WHERE 2 < (
    SELECT COUNT(*)
        FROM departments d
        WHERE l.`location_id` = d.`location_id`
);
```

# 数据库创建和管理



## 创建

创建数据库

```mysql
create database name;
```

创建数据库并设置字符集

```mysql
create database name CHARACTER SET 'gbk';
```

创建数据库,如果不存在则创建的数据库已经成功

```mysql
create database IF NOT EXISTS name;
```

创建数据表

```mysql
CREATE TABLE [IF NOT EXISTS] myemp1(
	id INT,
    emp_name VARCHAR(15),
    hire_date DATE
);
-- 基于已有的表创建,同时导入数据
CREATE TABLE myemp2
AS
SELECT employee_id,last_name,salary
FROM employees;
```



## 管理

显示创建数据库语句

```mysql
show create database/table name;
```

显示数据库

```mysql
show databases;
```

切换数据库

```mysql
use name;
```

显示当前数据库中保存的数据表

```mysql
show table_name;
```

查看指定数据库下保存的数据表

```mysql
SHOW TABLES FROM mysql;
```

## 修改

更新数据库字符集

```mysql 
ALTER DATABASE name CHARACTER SET 'UTF-8';
```

修改表

```mysql
-- 添加一个字段
ALTER TABLE name
ADD column_name DOUBLE(10,2) (FIRST)/(AFTER) column_name; 10位，小数占两位

-- 修改一个字段
ALTER TABLE name MODIFY COLUMN column_name VARCHAR(50) DEFAULT 'aaa'; 

-- 重命名一个字段
ALTER TABLE name
CHANGE old_column_name new_column_name DOUBLE(10,2)

-- 删除字段
ALTER TABLE employees DROP COLUMN column_name
```

重命名表

```mysql
RENAME TABLE oldname TO new_name;

ALTER TABLE oldname
RENAME TO new_name;
```



## 删除

删除数据库

```mysql
DROP DATABASE IF EXISTS name;
```

删除表

```mysql
DROP TABLE IF EXISTS name;
```

清空表(清空表中的数据数据 )  

```mysql
TRUNCATE TABLE name;
-- 不能回滚
DELETE FROM name;
-- 可以选择删除，可以实现回滚前提是开启事务
```



## 提交和回滚

COMMIT：提交数据，一旦执行COMMIT，则数据就被永远保存在数据库中，意味着数据库不能回滚。

ROLLBACK：回滚数据。一旦执行ROLLBACK,则可以数据的回滚，回滚到最近的一次COMMIT之后

DDL 操作一旦执行，就不可回滚，因为执行DDL之后一定会执行一次COMMIT操作

DML默认情况下一旦执行，也不可回滚，但是在执行之前执行了**SET autocommit =FLASE**;就可以回滚了

## 课后练习

```mysql
#1. 创建数据库test01_office,指明字符集为utf8。并在此数据库下执行下述操作
CREATE DATABASE IF NOT EXISTS test01_office CHARACTER SET 'utf8'
#2. 创建表dept01
/*
字段 类型
id INT(7)
NAME VARCHAR(25)
*/
CREATE TABLE dept01(
	id INT(7),
    NAME VARCHAR(25)
);
#3. 将表departments中的数据插入新表dept02中
CREATE TABLE dept02
AS 
SELECT *
FROM xxx.departments
#4. 创建表emp01
/*

字段 类型
id INT(7)
first_name VARCHAR (25)
last_name VARCHAR(25)
dept_id INT(7)
*/
CREATE TABLE emp01(
	id INT(7),
	first_name VARCHAR (25),
	last_name VARCHAR(25),
	dept_id INT(7)
);


#5. 将列last_name的长度增加到50
ALTER TABLE emp01 
MODIFY last_name VARCHAR(50),

#6. 根据表employees创建emp02
CREATE TABLE emp02
AS 
SELECT *
FROM xxx.empolyees

#7. 删除表emp01
DROP TABLE IF EXISTS emp01
#8. 将表emp02重命名为emp01
RENAME TABLE emp02 TO emp01
#9.在表dept02和emp01中添加新列test_column，并检查所作的操作
ALTER TABLE emp01 ADD test_column VARCHAR(10)
DESC emp01
ALTER TABLE dept02 ADD test_column VARCHAR(10)
DESC dept02

#10.直接删除表emp01中的列 department_id
ALTER TABLE emp01
DROP COLUMN department_id;
```



# 拓展 开发规范

```
阿里开发规范：
【参考】TRUNCATE TABLE 比 DELETE 速度快，且使用的系统和事务日志资源少，但 TRUNCATE 无
事务且不触发 TRIGGER，有可能造成事故，故不建议在开发代码中使用此语句。
说明：TRUNCATE TABLE 在功能上与不带 WHERE 子句的 DELETE 语句相同。
```

```
拓展1：阿里巴巴《Java开发手册》之MySQL字段命名

【 强制 】表名、字段名必须使用小写字母或数字，禁止出现数字开头，禁止两个下划线中间只出
现数字。数据库字段名的修改代价很大，因为无法进行预发布，所以字段名称需要慎重考虑。
正例：aliyun_admin，rdc_config，level3_name
反例：AliyunAdmin，rdcConfig，level_3_name
【 强制 】禁用保留字，如 desc、range、match、delayed 等，请参考 MySQL 官方保留字。
【 强制 】表必备三字段：id, gmt_create, gmt_modified。
说明：其中 id 必为主键，类型为BIGINT UNSIGNED、单表时自增、步长为 1。gmt_create,
gmt_modified 的类型均为 DATETIME 类型，前者现在时表示主动式创建，后者过去分词表示被
动式更新
【 推荐 】表的命名最好是遵循 “业务名称_表的作用”。
正例：alipay_task 、 force_project、 trade_config
【 推荐 】库名与应用名称尽量一致。
【参考】合适的字符存储长度，不但节约数据库表空间、节约索引存储，更重要的是提升检索速
度。
正例：无符号值可以避免误存负数，且扩大了表示范围。
表删除 操作将把表的定义和表中的数据一起删除，并且MySQL在执行删除操作时，不会有任何的确认信息提示，因此执行删除操时应当慎重。在删除表前，最好对表中的数据进行 备份 ，这样当操作失误时可以对数据进行恢复，以免造成无法挽回的后果。
同样的，在使用 ALTER TABLE 进行表的基本修改操作时，在执行操作过程之前，也应该确保对数据进行完整的 备份 ，因为数据库的改变是 无法撤销 的，如果添加了一个不需要的字段，可以将其删除；相同的，如果删除了一个需要的列，该列下面的所有数据都将会丢失。
```

![image-20240811165420820](E:\Code\笔记\笔记图片\image-20240811165420820.png)

表删除 操作将把表的定义和表中的数据一起删除，并且MySQL在执行删除操作时，不会有任何的确认信息提示，因此执行删除操时应当慎重。在删除表前，最好对表中的数据进行 备份 ，这样当操作失误时可以对数据进行恢复，以免造成无法挽回的后果。

同样的，在使用 ALTER TABLE 进行表的基本修改操作时，在执行操作过程之前，也应该确保对数据进行完整的 备份 ，因为数据库的改变是 无法撤销 的，如果添加了一个不需要的字段，可以将其删除；相同的，如果删除了一个需要的列，该列下面的所有数据都将会丢失。



在MySQL 8.0版本中，InnoDB表的DDL支持事务完整性，即 DDL操作要么成功要么回滚 。DDL操作回滚日志写入到data dictionary数据字典表mysql.innodb_ddl_log（该表是隐藏的表，通过show tables无法看到）中，用于回滚操作。通过设置参数，可将DDL操作日志打印输出到MySQL错误日志中。

分别在MySQL 5.7版本和MySQL 8.0版本中创建数据库和数据表，结果如下：

```mysql
CREATE DATABASE mytest;
USE mytest;
CREATE TABLE book1(
book_id INT ,
book_name VARCHAR(255)
);
SHOW TABLES;
```

（1）在MySQL 5.7版本中，测试步骤如下： 删除数据表book1和数据表book2，结果如下：

```mysql
mysql> DROP TABLE book1,book2;
ERROR 1051 (42S02): Unknown table 'mytest.book2'
```

再次查询数据库中的数据表名称，结果如下：

```mysql
mysql> SHOW TABLES;
Empty set (0.00 sec)
```

从结果可以看出，虽然删除操作时报错了，但是仍然删除了数据表book1。

（2）在MySQL 8.0版本中，测试步骤如下： 删除数据表book1和数据表book2，结果如下：

```mysql
mysql> DROP TABLE book1,book2;
ERROR 1051 (42S02): Unknown table 'mytest.book2'
```

查询结果如下

```
mysql> show tables;
+------------------+
| Tables_in_mytest |
+------------------+
| book1 |
+------------------+
1 row in set (0.00 sec)
```

从结果可以看出，数据表book1并没有被删除。





# DML

## 添加数据



``` mysql
-- 一条一条的添加数据,没有指明添加字段（需要按照声明的添加顺序）
INSERT INTO emp VALUES (1,'Tom','2000-12-21',3400)

-- 指明添加字段(推荐使用)
INSERT INTO emp(id,hire_date,salsary,name) VALUES (1,'2000-12-21',3400,'Tom')

-- 同时插入多条记录
INSERT INTO emp(id,hire_date,salsary,name) VALUES (1,'2000-12-21',3400,'Tom'),(2,'2001-12-21',3200,'Tim')

-- 将查询结果输入到表中
INSERT INTO emp1(id,hire_date,salsary,name)
SELECT employee_id,hire_date,salsary,last_name FROM empolyees;
```



## 更新数据

```mysql
-- UPDATE ....  SET 不加where会批量修改
UPDATE emp1
SET hire_date = CURDATE()
WHERE id = 5;

-- 修改多个字段
UPDATE emp1
SET hire_date = CURDATE(),salary = 6000
WHERE id = 5;
```

## 删除操作

```mysql
DELETE FROM emp1
WHERE id =1;
```

## MySQL8新特性

某一列的值是通过别的列计算得来的，则那条列被称为计算列

```mysql
CREATE TABLE tb1(
	id INT,
   	a  INT,
    b  INT,
    c  INT GENERATED ALWAYS AS (a+b) VIRTUAL #字段c为计算列
 )
```

## 综合训练

```mysql
# 1、创建数据库test01_library
CREATE DATABASE IF NOT EXISTS test01_library;

# 2、创建表 books，表结构如下：
CREATE TABLE IF NOT EXISTS books(
    id INT,
    name VARCHAR(50),
    'authors' VARCHAR(100),
    price FLOAT,
    pubdate YEAR,
    note VARCHAR(100),
    num INT
);

# 3、向books表中插入记录
# 1）不指定字段名称，插入第一条记录
# 2）指定所有字段名称，插入第二记录
# 3）同时插入多条记录（剩下的所有记录）
1)
INSERT INTO books 
VALUES ('Tal of AAA', 'Dickes', 23 ,1995, 'novel', 11)

2)
INSERT INTO books(id,name.'authors',price,pubdate,note,num)
VALUES(2,'EmmaT','Jane lura',35,1993,'Joke',22);
3)
INSERT INTO books (id,name,`authors`,price,pubdate,note,num) VALUES
(3,'Story of Jane','Jane Tim',40,2001,'novel',0),
(4,'Lovey Day','George Byron',20,2005,'novel',30),
(5,'Old land','Honore Blade',30,2010,'Law',0),
(6,'The Battle','Upton Sara',30,1999,'medicine',40),
(7,'Rose Hood','Richard haggard',28,2008,'cartoon',28);

# 4、将小说类型(novel)的书的价格都增加5。
UPDATE books SET price=price+5
WHERE note ='novel';


# 5、将名称为EmmaT的书的价格改为40，并将说明改为drama。
UPDATE books SET price =40,note = 'drama'
WHERE name ='EmmaT'
# 6、删除库存为0的记录。
DELETE FROM books
WHERE num =0;
# 7、统计书名中包含a字母的书
SELECT *
FROM books
WHERE name LIKE '%a%'
# 8、统计书名中包含a字母的书的数量和库存总量
SELECT COUNT(*),SUM(num)
FROM books
WHERE name LIKE '%a%'
# 9、找出“novel”类型的书，按照价格降序排列
SELECT *
FROM book
WHERE note = 'novel'
ORDER BY price DESC;

# 10、查询图书信息，按照库存量降序排列，如果库存量相同的按照note升序排列
SELECT *
FROM book
WHERE note = 'novel'
ORDER BY num DESC,note ASC;
# 11、按照note分类统计书的数量
SELECT note, COUNT(*)
FROM books
GROUP BY note;
# 12、按照note分类统计书的库存量，显示库存量超过30本的
SELECT note, COUNT(*)
FROM books
GROUP BY note
HAVING num >30;
# 13、查询所有图书，每页显示5本，显示第二页
SELECT *
FROM books
LIMIT 5,5;
# 14、按照note分类统计书的库存量，显示库存量最多的
SELECT SUM(num)
FROM books
GROUP BY note
ORDER BY SUM(num)
LIMIT 0,1;
-- 或者
SELECT SUM(num)
FROM books
GROUP BY note
HAVING SUM(num) >= ALL (
    SELECT SUM(num)
    FROM books
    GROUP BY note
);

# 15、查询书名达到10个字符的书，不包括里面的空格 (字符函数)
SELECT *
FROM books
WHERE CHAR_LENGTH(REPLACE(name,' ',''))>=10
# 16、查询书名和类型，其中note值为novel显示小说，law显示法律，medicine显示医药，cartoon显示卡通，joke显示笑话 (case ... when...THEN..END)
SELECT name,note,case note WHEN novel THEN '小说'
						   WHEN law THEN '法律'
						   WHEN medicine THEN '医药'
						   WHEN cartoon THEN '卡通'
						   WHEN joke THEN '笑话'
						   END AS "类型"
FROM books


# 17、查询书名、库存，其中num值超过30本的，显示滞销，大于0并低于10的，显示畅销，为0的显示需要无货 (case ... when...THEN..END)
SELECT name,num,CASE
                    WHEN num>30 THEN '滞销'
                    WHEN num>0 AND num<10 THEN '畅销'
                    WHEN num=0 THEN '无货'
                    ELSE '正常'
                    END AS "库存状态"
FROM books;

# 18、统计每一种note的库存量，并合计总量(GROUP BY,WITH ROLLUP)
SELECT IFNULL(note,'合计总库存量') AS note,SUM(num)
FROM books
GROUP BY note WITH ROLLUP;
# 19、统计每一种note的数量，并合计总量
SELECT IFNULL(note,'合计数量') AS note,COUNT(*)
FROM books
GROUP BY note WITH ROLLUP;

# 20、统计库存量前三名的图书
SELECT * FROM books ORDER BY num DESC LIMIT 0,3;

# 21、找出最早出版的一本书
SELECT * FROM books ORDER BY pubdate ASC LIMIT 0,1;

# 22、找出novel中价格最高的一本书
SELECT * FROM books WHERE note = 'novel' ORDER BY price DESC LIMIT 0,1;

# 23、找出书名中字数最多的一本书，不含空格
SELECT name
FROM books
ORDER BY CHAR_LENGTH(REPLACE(name,' ','')) DESC
Limit 0.1;

```



# 数据类型概述

类型概览

![image-20240812110646906](E:\Code\笔记\笔记图片\image-20240812110646906.png)

属性

![image-20240812111059383](E:\Code\笔记\笔记图片\image-20240812111059383.png)

## 整数类型

整数类型主要有TINYINT,SMALLINT,MEDIUMINT,INT(INTEGER),BIGINT

他们的区别主要在于数据取值范围以及字节区别 (  1个字节占8位 )

![image-20240812110846850](E:\Code\笔记\笔记图片\image-20240812110846850.png)



### 可选属性

M：显示宽度，取值范围是（0，255）例如：int(5):当数据宽度小于5位的时候在数据前面需要用字符填满宽度。需要搭配'ZEROFILL'使用,表示用0来填满宽度，使用时自动添加UNSIGNED

注：从MySQL8.0开始整数数据类型不推荐使用M

UNSIGNED :**无符号**，使得整数类型舍弃负数范围扩大正数表示范围

### 使用范围

TINYINT：一般用于枚举数据，比如系统设定取值范围很小且固定的场景

SMALLINT：可以用于较小范围的统计数据，比图固定资产库存货量

MEDIUMINT：用于较大整数的计算，比如每日车站客流量

INT(INTEGER)：取值范围足够大，比如商品编号

BIGINT：处理特别巨大的整数才会用到，比如双十一交易量、大型门户网站点击量

## 浮点数、定点数、位

### 浮点数

![image-20240812113126505](E:\Code\笔记\笔记图片\image-20240812113126505.png)

![image-20240812113246962](E:\Code\笔记\笔记图片\image-20240812113246962.png)

![image-20240812113347125](E:\Code\笔记\笔记图片\image-20240812113347125.png)

![image-20240812113817728](E:\Code\笔记\笔记图片\image-20240812113817728.png)

### 定点数

![image-20240812113915532](E:\Code\笔记\笔记图片\image-20240812113915532.png)

### 位类型

BIT类型中存储的是二进制值，类似010101

BT(M) ，1<=M<=64 ,M表示的是二进制的位数

## 日期时间类型

![image-20240812134233764](E:\Code\笔记\笔记图片\django.md)

总结：使用数据最好使用DATETIME,一般注册时间、商品发布时间最好使用时间戳 UNIX_TIMESTAMP() 使用BIGINT计算,便于计算。

存数据的时候如果是现在系统时间最好选择NOW( )，但是需要注意时区问题：SET time_zone =""

## 文本字符串类型

CHAR 和VARCHAR类型

主要区别CHAR 是固定长度，VARCHAR是可变长度

CHAR(M) 类型一般需要预先定义字符串长度。如果不指定(M)，则表示长度默认是1个字符。

如果保存时，数据的实际长度比CHAR类型声明的长度小，则会在右侧填充 空格以达到指定的长度。当MySQL检索CHAR类型的数据时，CHAR类型的字段会去除尾部的空格。

定义CHAR类型字段时，声明的字段长度即为CHAR类型字段所占的存储空间的字节数。

抽象化：char就是一份作文纸，它的最上面写着CHAR（M）,如果自己不指定M，那么M会默认为1，之后你就只能写下一个字。但是写下自己设定的（819）,那么就可以写下819的字，但是如果你写下的字少于你设置的M那么后面空余的部位就是全部使用空格来填充，就跟1张没写满的作文纸一样。

VARCHAR（M）：

VARCHAR(M) 定义时， 必须指定 长度M，否则报错。 M最大为21845

MySQL4.0版本以下，varchar(20)：指的是20字节，如果存放UTF8汉字时，只能存6个（每个汉字3字节） ；MySQL5.0版本以上，varchar(20)：指的是20字符。检索VARCHAR类型的字段数据时，会保留数据尾部的空格。VARCHAR类型的字段所占用的存储空间为字符串实际长度加1个字节。

### **哪些情况使用** **CHAR** **或** **VARCHAR** **更好**

**情况1：**存储很短的信息。比如门牌号码101，201……这样很短的信息应该用char，因为varchar还要占个byte用于存储信息长度，本来打算节约存储的，结果得不偿失。

**情况2**：十分频繁改变的column。因为varchar每次存储都要有额外的计算，得到长度等工作，如果一个非常频繁改变的，那就要有很多的精力用于计算，而这些对于char来说是不需要的。			

**情况3：**具体存储引擎中的情况：

MyISAM 数据存储引擎和数据列：MyISAM数据表，最好使用固定长度(CHAR)的数据列代替可变长度(VARCHAR)的数据列。这样使得整个表静态化，从而使 数据检索更快 ，用空间换时间。MEMORY 存储引擎和数据列：MEMORY数据表目前都使用固定长度的数据行存储，因此无论使用CHAR或VARCHAR列都没有关系，两者都是作为CHAR类型处理的。InnoDB 存储引擎，建议使用VARCHAR类型。因为对于InnoDB数据表，内部的行存储格式并没有区分固定长度和可变长度列（所有数据行都使用指向数据列值的头指针），而且**主要影响性能的因素是数据行使用的存储总量**，由于char平均占用的空间多于varchar，所以除了简短并且固定长度的，其他考虑varchar。这样节省空间，对磁盘I/O和数据存储总量比较好。

### **TEXT类型**

在MySQL中，TEXT用来保存文本类型的字符串，总共包含4种类型，分别为TINYTEXT、TEXT、MEDIUMTEXT 和 LONGTEXT 类型。在向TEXT类型的字段保存和查询数据时，系统自动按照实际长度存储，不需要预先定义长度。这一点和VARCHAR类型相同。每种TEXT类型保存的数据长度和所占用的存储空间不同，如下：



![image-20240812145234338](E:\Code\笔记\笔记图片\image-20240812145234338.png)



TEXT文本类型，可以存比较大的文本段，搜索速度稍慢，因此如果不是特别大的内容，建议使用CHAR，VARCHAR来代替。还有TEXT类型不用加默认值，加了也没用。而且text和blob类型的数据删除后容易导致“空洞”，使得文件碎片比较多，所以频繁使用的表不建议包含TEXT类型字段，建议单独分出去，单独用一个表。

# 约束（constraint）

## 为什么需要约束

![image-20240813103323839](E:\Code\笔记\笔记图片\image-20240813103323839.png)

## 定义约束

表级的强制规定

一般在**create table** 时添加约束，**ALTER table** 修改删除约束

## 约束的分类

![image-20240813103754277](E:\Code\笔记\笔记图片\image-20240813103754277.png)

### 非空约束（NOT NULL）

限制某个字段的值不允许为空

![image-20240813104336120](E:\Code\笔记\笔记图片\image-20240813104336120.png)

```mysql
-- 添加NOT NULL
create table test1(
 id INT NOT NULL ,
  last_name VARCHAR(15),
);

-- ALTER TABLE
ALTER TABLE test1
MODIFY last_name VARCHAR(15) (NOT) NULL
```



### 唯一性约束（UNIQUE）

限制某个字段/某列的值不饿能重复

![image-20240813104953392](E:\Code\笔记\笔记图片\image-20240813104953392.png)

```mysql
-- 添加UNIQUE
create table test1(
 id INT UNIQUE, #列级约束
 last_name VARCHAR(15),
    
#表级写法
CONSTRAINT uni_test1_email UNIQUE(email)
);

-- ALTER TABLE
ALTER TABLE test1
MODIFY last_name VARCHAR(15) UNIQUE
-- 或者
ALTER TABLE test1
ADD  CONSTRAINT uni_test1_last_name UNIQUE(last_anme)
```

复合的唯一性约束

```mysql
CREATE TABLE USER(
	id INT,
    name VARCHAR(15),
    `password` VARCHAR(15),
   CONSTRAINT uni_user UNIQUE(`name`,`password`) 
)
-- 只要有一个字段不同就可以使用

#学生表
create table student(
sid int, #学号
sname varchar(20), #姓名
tel char(11) unique key, #电话
cardid char(18) unique key #身份证号
);
#课程表
create table course(
cid int, #课程编号
cname varchar(20) #课程名称
);
#选课表
create table student_course(
id int,
sid int,
cid int,
score int,
unique key(sid,cid) #复合唯一
);

```

删除唯一性约束

![image-20240813110530420](E:\Code\笔记\笔记图片\image-20240813110530420.png)

```mysql
-- 查看约束
SELECT * FROM information_schema.table_constraints WHERE table_name = '表名';
ALTER TABLE USER
DROP INDEX uk_name_pwd;
```





### 主键约束（PRIMARY KEY）

用来唯一标识表中的一行记录

![image-20240813110636681](E:\Code\笔记\笔记图片\image-20240813110636681.png)

```mysql
create table 表名称(
字段名 数据类型,
字段名 数据类型,
字段名 数据类型,
[constraint 约束名] primary key(字段名) #表级模式
);

create table 表名称(
字段名 数据类型 primary key, #列级模式
字段名 数据类型,
字段名 数据类型,

);
--  建表后增加主键约束
ALTER TABLE 表名称 ADD PRIMARY KEY(字段列表); #字段列表可以是一个字段，也可以是多个字段，如果是多个字段的话，是复合主键


-- 复合主键

create table 表名称(
字段名 数据类型,
字段名 数据类型,
字段名 数据类型,
primary key(字段名1,字段名2) #表示字段1和字段2的组合是唯一的，也可以有更多个字段
);

-- 删除主键约束 (根本不会去做)
alter table 表名称 drop primary key;
```

### 自增（AUTO_INCREMENT）

使某个字段的值自增

![image-20240813111349914](E:\Code\笔记\笔记图片\image-20240813111349914.png)

```mysql
-- 建表时
create table 表名称(
字段名 数据类型 primary key auto_increment,
字段名 数据类型 unique not null,
字段名 数据类型 unique,
字段名 数据类型 not null default 默认值,
);
create table 表名称(
字段名 数据类型 default 默认值 ,
字段名 数据类型 unique key auto_increment,
字段名 数据类型 not null default 默认值,,
primary key(字段名)
);
-- 建表后
alter table 表名称 modify 字段名 数据类型 auto_increment;

-- 删除自增约束
alter table 表名称 modify 字段名 数据类型; #去掉auto_increment相当于删除
```

#### MySQL8.0新特性

在MySQL 8.0之前，自增主键AUTO_INCREMENT的值如果大于max(primary key)+1，在MySQL重启后，会重置AUTO_INCREMENT=max(primary key)+1，这种现象在某些情况下会导致业务主键冲突或者其他难以发现的问题。 8.0在日志中使得这个功能得到修复

### 外键约束（FOREIGN KEY）

限制某个表的某个字段的引用完整性

![image-20240813113533623](E:\Code\笔记\笔记图片\image-20240813113533623.png)



![image-20240813113124149](E:\Code\笔记\笔记图片\image-20240813113124149.png)

```mysql
create table 主表名称(
    字段1 数据类型 primary key,
    字段2 数据类型
);
create table 从表名称(
    字段1 数据类型 primary key,
    字段2 数据类型,
    [CONSTRAINT <外键约束名称>] FOREIGN KEY（从表的某个字段) references 主表名(被参考字段)
);
#(从表的某个字段)的数据类型必须与主表名(被参考字段)的数据类型一致，逻辑意义也一样
#(从表的某个字段)的字段名可以与主表名(被参考字段)的字段名一样，也可以不一样
-- FOREIGN KEY: 在表级指定子表中的列
-- REFERENCES: 标示在父表中的列

create table dept( #主表
    did int primary key, #部门编号
    dname varchar(50) #部门名称
);
create table emp(#从表
    eid int primary key, #员工编号
    ename varchar(5), #员工姓名
    deptid int, #员工所在的部门
    foreign key (deptid) references dept(did) #在从表中指定外键约束
    #emp表的deptid和和dept表的did的数据类型一致，意义都是表示部门的编号
);
说明：
（1）主表dept必须先创建成功，然后才能创建emp表，指定外键成功。
（2）删除表时，先删除从表emp，再删除主表dept

-- 建表后
ALTER TABLE 从表名 ADD [CONSTRAINT 约束名] FOREIGN KEY (从表的字段) REFERENCES 主表名(被引用字段) [on update xx][on delete xx];

ALTER TABLE emp1
ADD [CONSTRAINT emp_dept_id_fk] FOREIGN KEY(dept_id) REFERENCES dept(dept_id);
```



约束等级

![image-20240813113019526](E:\Code\笔记\笔记图片\image-20240813113019526.png)

删除外键约束

```mysql
(1)第一步先查看约束名和删除外键约束
SELECT * FROM information_schema.table_constraints WHERE table_name = '表名称';#查看某个
表的约束名
ALTER TABLE 从表名 DROP FOREIGN KEY 外键约束名;
（2）第二步查看索引名和删除索引。（注意，只能手动删除）
SHOW INDEX FROM 表名称; #查看某个表的索引名
ALTER TABLE 从表名 DROP INDEX 索引名;


mysql> SELECT * FROM information_schema.table_constraints WHERE table_name = 'emp';
mysql> alter table emp drop foreign key emp_ibfk_1;
Query OK, 0 rows affected (0.02 sec)
Records: 0 Duplicates: 0 Warnings: 0

mysql> show index from emp;
mysql> alter table emp drop index deptid;
Query OK, 0 rows affected (0.01 sec)
Records: 0 Duplicates: 0 Warnings: 0
mysql> show index from emp;
```

开发场景

![image-20240813114135788](E:\Code\笔记\笔记图片\image-20240813114135788.png)

阿里开发规范

【 强制 】不得使用外键与级联，一切外键概念必须在应用层（程序层面）解决。

说明：（概念解释）学生表中的 student_id 是主键，那么成绩表中的 student_id 则为外键。如果更新学

生表中的 student_id，同时触发成绩表中的 student_id 更新，即为级联更新。外键与级联更新适用于单机低并发 ，不适合 分布式 、 高并发集群 ；级联更新是强阻塞，存在数据库 更新风暴 的风险；外键影响数据库的 插入速度 。





### 检查约束（CHECK）

检查某个字段的值是否符合要求一般指的是值的范围

MySQL5.7不支持但是8.0支持

```mysql
CREATE TABLE test10(
	id iNT,
    last_name VARCHAR(15),
    salary DECIMAL(10,2) CHECK (salary >2000)
);

-- 再举例
age tinyint check(age >20) 或 sex char(2) check(sex in(‘男’,’女’))
CHECK(height>=0 AND height<3)
CREATE TABLE temp(
id INT AUTO_INCREMENT,
NAME VARCHAR(20),
age INT CHECK(age > 20),
PRIMARY KEY(id)
);
```





### 默认值约束（DEFAULT）

给某个字段/某列指定默认值，一旦设置默认值，在插入数据时，如果此字段没有显式赋值，则赋值为默认值。

```mysql
-- 添加
create table 表名称(
    字段名 数据类型 primary key,
    字段名 数据类型 unique key not null,
    字段名 数据类型 unique key,
    字段名 数据类型 not null default 默认值,
);
-- 示例

create table employee(
eid int primary key,
ename varchar(20) not null,
gender char default '男',
tel char(11) unique not null default '' #默认是空字符串
);

-- 建表后修改
alter table 表名称 modify 字段名 数据类型 default 默认值;
#如果这个字段原来有非空约束，你还保留非空约束，那么在加默认值约束时，还得保留非空约束，否则非空约束就被
删除了
#同理，在给某个字段加非空约束也一样，如果这个字段原来有默认值约束，你想保留，也要在modify语句中保留默认值约束，否则就删除了
alter table 表名称 modify 字段名 数据类型 default 默认值 not null;

-- 删除
lter table 表名称 modify 字段名 数据类型 ;#删除默认值约束，也不保留非空约束
alter table 表名称 modify 字段名 数据类型 not null; #删除默认值约束，保留非空约束


```

## 课后练习

```mysql
-- 已经存在数据库test04_emp，两张表emp2和dept2

CREATE DATABASE test04_emp;
use test04_emp;
CREATE TABLE emp2(
id INT,
emp_name VARCHAR(15)
);
CREATE TABLE dept2(
id INT,
dept_name VARCHAR(15)
);

#1.向表emp2的id列中添加PRIMARY KEY约束
ALTER TABLE emp2
ADD PRIMARY KEY(id)
-- 或者
ALTER TABLE emp2
MODIFY COLUMN ID INT PRIMARY KEY;
#2. 向表dept2的id列中添加PRIMARY KEY约束
ALTER TABLE  dept2
ADD PRIMARY KEY(id)
-- 或者
ALTER TABLE dept2
MODIFY COLUMN id INT PRIMARY KEY;
#3. 向表emp2中添加列dept_id，并在其中定义FOREIGN KEY约束，与之相关联的列是dept2表中的id列。
ALTER emp2
ADD COLUMN dept_id INT;
ALTER emp2
ADD FOREIGN KEY(dept_id) REFERENCES dep2(id);

-- 练习2
# 1、创建数据库test01_library
CREATE DATABASE test01_library;

# 2、创建表 books，表结构如下：
CREATE TABLE books(
	id int,
    name varchar(50),
    authoes varchar(100)
    price float,
    pubdate year,
    note varchar(100),
    num int
);
# 3、使用ALTER语句给books按如下要求增加相应的约束
ALTER books
ADD PRIMARY KEY(id);
ALTER books
MODIFY id INT AUTO_INCREMENT;
#给name等字段增加非空约束
ALTER TABLE books 
MODIFY name VARCHAR(50) NOT NULL;
ALTER TABLE books 
MODIFY `authors` VARCHAR(100) NOT NULL;
ALTER TABLE books 
MODIFY price FLOAT NOT NULL;
ALTER TABLE books 
MODIFY pubdate DATE NOT NULL;
ALTER TABLE books num INT NOT NULL;

#1. 创建数据库test04_company
CREATE DATABASE test04_company;

#2. 按照下表给出的表结构在test04_company数据库中创建两个数据表offices和employees
USE test04_company;
CREATE TABLE offices(
	officeCOde INT
    city VARCHAR(50) NOT NULL,
    address VARCHAR(50) NOT NULL,
    country VARCHAR(50) NOT NULL,
    postalCOde VARCHAR(15) UNIQUE,
    PRIMARY KEY(officeCode)
);
CREATE TABLE employees(
employeeNumber INT(11) PRIMARY KEY AUTO_INCREMENT,
lastName VARCHAR(50) NOT NULL,
firstName VARCHAR(50) NOT NULL,
mobile VARCHAR(25) UNIQUE,
officeCode INT(10) NOT NULL,
jobTitle VARCHAR(50) NOT NULL,
birth DATETIME NOT NULL,
note VARCHAR(255),
sex VARCHAR(5),
CONSTRAINT fk_emp_ofCode FOREIGN KEY(officeCode) REFERENCES offices(officeCode)
);

#3. 将表employees的mobile字段修改到officeCode字段后面
ALTER TABLE emoployees
MODIFY mobile VARCHAR(25) AFTER officeCode
#4. 将表employees的birth字段改名为employee_birth
ALTER TABLE employees CHANGE birth employee_birth DATETIME;
#5. 修改sex字段，数据类型为CHAR(1)，非空约束
ALTER TABLE employees
MODIFY sex char(1) NOT NULL;
#6. 删除字段note
ALTER TABLE employees DROP COLUMN note;

#7. 增加字段名favoriate_activity，数据类型为VARCHAR(100)
ALTER TABLE employees ADD favoriate_activity VARCHAR(100);
#8. 将表employees名称修改为employees_info
RENAME TABLE employees to employees_info;
```





# 面试问题

![image-20240813115442913](E:\Code\笔记\笔记图片\image-20240813115442913.png)



# 视图

1. 常见的数据库对象

![image-20240813143500763](E:\Code\笔记\笔记图片\image-20240813143500763.png)



视图一方面可以帮我们使用表的**一部分**而不是**所有**的表，另一方面也可以针对**不同的用户**制定不同的**查询视图**。比如，针对一个公司的销售人员，我们只想给他看部分数据，而某些特殊的数据，比如采购的价格，则不会提供给他。再比如，人员薪酬是个敏感的字段，那么只给某个级别以上的人员开放，其他人的查询视图中则不提供这个字段。

- 视图是一种虚拟表，本身不具备数据，
- 视图都是建立再已有的基础上，视图赖以建立的这些表被称为基表



![image-20240813153030449](E:\Code\笔记\笔记图片\image-20240813153030449.png)

## 创建视图

```mysql
-- 完整版
CREATE [OR REPLACE]
[ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
VIEW 视图名称 [(字段列表)]
AS 查询语句
[WITH [CASCADED|LOCAL] CHECK OPTION]
-- 简化版
CREATE VIEW 视图名称
AS 查询语句

-- 示例 针对于单表
CREATE VIEW vu_emp1
AS
SELECT employee_id,last_name,salary
from emps;
select * from vu_emp1;

CREATE VIEW vu_emp2
AS
SELECT employee_id emp_id,last_name,salary # 查询语句中字段的别名会作为视图中的字段
from emps;
select * from vu_emp2;

CREATE VIEW vu_emp3（emp_id,`NAME`,monthly_sal）#和查询的字段一一匹配
AS
SELECT employee_id,last_name,salary 
from emps;
select * from vu_emp2;


-- 在基表中不存在的字段
CREATE VIEW vu_emp_sal
AS
SELECT department_id,AVG(salary) avg_sal
from emps
WHERE department_id IS NOT NULL
GROUP BY department_id;
select * from vu_emp_sal;

-- 针对于多表  
CREATE VIEW  vu_emp_dept
AS
SELECT e.employee_id,e.department_id,d.department_name
FROM emps e JOIN depts d
ON e.department_id = d.department_id;
SELECT * FROM vu_emp_dept;

-- 利用视图对数据进行格式化
CREATE VIEW emp_depart
AS
SELECT CONCAT(last_name,'(',department_name,')') AS emp_dept
FROM employees e JOIN departments d
WHERE e.department_id = d.department_id

-- 基于视图创建视图
create view vu_emp4
as
select employee_id,las_name
from vu_emp1

-- 查看视图
1. show tables;
2. DESC vu_emp1;
3. SHOW TABLE STATUS LIKE 'vu_emp1';(/G)
4. show create  view vu_emp1;

```

对视图的修改会同时修改表中的数据，反之也是

```mysql
-- 一般情况下可以进行更新数据
UPDATE vu_emp1
SET salary = 20000
WHERE employee_id = 101;

DELETE FROM vu_emp1
WHERE employee_id =101;

-- 在修改基表中不存在的字段时，不会更新成功
-- 要使视图可更新，视图中的行和底层基本表中的行之间必须存在 一对一 的关系。另外当视图定义出现如下情况时，视图不支持更新操作：
-- 在定义视图的时候指定了“ALGORITHM = TEMPTABLE”，视图将不支持INSERT和DELETE操作；
-- 视图中不包含基表中所有被定义为非空又未指定默认值的列，视图将不支持INSERT操作；
-- 在定义视图的SELECT语句中使用了 JOIN联合查询 ，视图将不支持INSERT和DELETE操作；
-- 在定义视图的SELECT语句后的字段列表中使用了 数学表达式 或 子查询 ，视图将不支持INSERT，也不支持UPDATE使用了数学表达式、子查询的字段值；
-- 在定义视图的SELECT语句后的字段列表中使用 DISTINCT 、 聚合函数 、 GROUP BY 、 -- HAVING 、UNION 等，视图将不支持INSERT、UPDATE、DELETE；
-- 在定义视图的SELECT语句中包含了子查询，而子查询中引用了FROM后面的表，视图将不支持
-- INSERT、UPDATE、DELETE；
-- 视图定义基于一个不可更新视图 ；
-- 常量视图。

```

虽然可以更新视图数据，但总的来说，视图作为 虚拟表 ，主要用于 方便查询 ，不建议更新视图的数据。**对视图数据的更改，都是通过对实际数据表里数据的操作来完成的。**

## 修改视图

```mysql
-- 方式1
CREATE OR REPLACE VIEW vu_emp1
AS
    SELECT employee_id,last_name,salary,email
from emps
WHERE salary>7000;
select * from vu_emp1;
-- 方式2
ALTER VIEW vu_emp1
AS
    SELECT employee_id,last_name,salary,email,hire_date
FROM emps;
select * from vu_emp1;
```

## 删除视图

```mysql
DROP VIEW IF EXISTS vu_emp1;
```

## 优点

![image-20240813162359326](E:\Code\笔记\笔记图片\image-20240813162359326.png)

​	![image-20240813162418218](E:\Code\笔记\笔记图片\image-20240813162418218.png)

## 缺点

![image-20240813162555663](E:\Code\笔记\笔记图片\image-20240813162555663.png)

## 课后练习

```mysql
#1. 使用表employees创建视图employee_vu，其中包括姓名（LAST_NAME），员工号（EMPLOYEE_ID），部门号(DEPARTMENT_ID)
CREATE VIEW employee_vu
AS
SELECT last_name,employee_id,department_id
FROM emps;
#2. 显示视图的结构
DESC employee_vu;
#3. 查询视图中的全部内容
SELECT * FROM employee_vu;
#4. 将视图中的数据限定在部门号是80的范围内
CREATE OR REPLACE VIEW employee_vu
AS
SELECT last_name,employee_id,department_id
FROM emps
WHERE department_id =80;

#1. 创建视图emp_v1,要求查询电话号码以‘011’开头的员工姓名和工资、邮箱
CREATE VIEW emp_v1
AS
SELECT last_name,salary,email
    FROM emps
where phone_number like '011%';
#2. 要求将视图 emp_v1 修改为查询电话号码以‘011’开头的并且邮箱中包含 e 字符的员工姓名和邮箱、电话号码
CREATE  VIEW emp_v1
AS
SELECT last_name,salary,email
    FROM emps
WHERE phone_number LIKE '011%'
AND email LIKE '%e%';
#3. 向 emp_v1 插入一条记录，是否可以？
不可以,视图只是对原有数据进行修改而已
#4. 修改emp_v1中员工的工资，每人涨薪1000
UPDATE emp_v1
SET salary = salary + 1000;

#5. 删除emp_v1中姓名为Olsen的员工
DELETE FROM emp_v1
WHERE last_name ='Olsen'
#6. 创建视图emp_v2，要求查询部门的最高工资高于 12000 的部门id和其最高工资

CREATE  VIEW emp_v2
AS
SELECT MAX(salary) max_salary,department_id
FROM emps
GROUP BY department_id
HAVING max_salary >12000;

#7. 向 emp_v2 中插入一条记录，是否可以？
不可以
#8. 删除刚才的emp_v2 和 emp_v1
DROP VIEW  IF EXISTS emp_v1;
```



# 存储过程与函数

## 存储过程

定义（WHAT）：一组预先封装好的SQL语句

执行流程(HOW)：存储过程预先存储在MySQL服务器，需要执行的时候，客户端只需要向服务端发出调用存储过程的命令，服务器端就可以使用存储过程

好处（WHY）：

1. 简化操作，减少了重复SQL语句的书写
2. 减少操作过程中的失误，提升了效率
3. 减少网络传输量
4. 减少了SQL语句暴露在网上的风险，提高了数据查询的安全性

### 和视图、函数的对比：

**视图**是**虚拟表**，通常**不会**对**底层数据**直接操作，而**存储过程**可以直接操作**底层数据**

相较于函数，存储过程是没有返回值的

分类

![image-20240814101007182](E:\Code\笔记\笔记图片\image-20240814101007182.png)

### 创建存储过程

```mysql
CREATE PROCEDURE 存储过程名(IN|OUT|INOUT 参数名 参数类型,..）
[characteristics ...]
BEGIN 
	存储过程体
END
```

参数含义

![image-20240814101444524](E:\Code\笔记\笔记图片\image-20240814101444524.png)

![image-20240814101704153](E:\Code\笔记\笔记图片\image-20240814101704153.png)

![image-20240814102037491](E:\Code\笔记\笔记图片\image-20240814102037491.png)

#### 示例

```mysql
-- 创建 (无参数无返回值)
DELIMITER $

CREATE PROCEDURE select_all_date()
BEGIN
    SELECT * FROM emps;
end $

DELIMITER ;

-- 调用
 CALL select_all_date();
 
# 带OUT
# 查看最低工资
DELIMITER $
CREATE PROCEDURE show_min_salary(OUT msalary DOUBLE)
BEGIN
    SELECT MIN(salary) INTO msalary
    FROM emps;
end $
DELIMITER ;

call show_min_salary(@msalary);
select @msalary;

# 带IN
DELIMITER $
CREATE PROCEDURE show_someone_salary(IN empname VARCHAR(20))
BEGIN
    SELECT salary
    FROM emps
    WHERE last_name = empname;
end $
DELIMITER ;
-- 方式1
call show_someone_salary('Abel');
-- 方式2
SET @empname = 'Abel';
call show_someone_salary(@empname);


# 带IN OUT
DELIMITER $
CREATE PROCEDURE show_someone_salary2(IN empname VARCHAR(20),OUT esalary DOUBLE)
BEGIN
    SELECT salary INTO esalary
    FROM emps
    WHERE last_name = empname;
end $
DELIMITER ;

set @empname = 'Abel';
call show_someone_salary2(@empname,@esalary)
select @esalary;

# 带INOUT
DELIMITER $
CREATE PROCEDURE show_mgr_name(INOUT empname varchar(25))
BEGIN
    SELECT mag.last_name INTO empname
    FROM emps e join emps mag
    ON e.manager_id = mag.employee_id
    WHERE e.last_name = empname;
end $
DELIMITER ;

set @empname = 'Abel';
call show_mgr_name(@empname);
select @empname;
```

### 如何调试

![image-20240814104538705](E:\Code\笔记\笔记图片\image-20240814104538705.png)

## 存储函数

![image-20240814105229784](E:\Code\笔记\笔记图片\image-20240814105229784.png)

### 代码举例

```mysql
-- 示例
DELIMITER $
CREATE FUNCTION emial_by_name()
RETURNS VARCHAR(25)
    DETERMINISTIC
    CONTAINS SQL
    READS SQL DATA
BEGIN
    return (SELECT email FROM emps where last_name ='Abel');
end $
DELIMITER ;

select emial_by_name();

# 保证函数创建的成功
SET GLOBAL log_bin_trust_function_creators = 1;

DELIMITER $
CREATE FUNCTION email_by_id(emp_id INT)
RETURNS VARCHAR(25)
BEGIN
    return (select email from emps where employee_id= emp_id);
end $
DELIMITER ;

select email_by_id(100);

set @empid= 102;
select email_by_id(@empid);
```

## 存储函数和存储过程的不同

![image-20240814110610725](E:\Code\笔记\笔记图片\image-20240814110610725.png)

## 存储过程和函数的查看、修改、删除

```mysql
-- 查看
show create procedure show_mgr_name;
show create function email_by_id;

show procedure status LIKE 'show_max_salary';
show function status ;

-- 修改只是修改相关特性

ALTER PROCEDURE show_max_salary
SQL SECURITY INVOKER
COMMENT '查询最高工资';


-- 删除
drop function if exists name;
```

## 缺点

![image-20240814111923005](E:\Code\笔记\笔记图片\image-20240814111923005.png)

## 课后练习

```mysql
#0.准备工作
CREATE DATABASE test15_pro_func;
USE test15_pro_func;
#1. 创建存储过程insert_user(),实现传入用户名和密码，插入到admin表中
CREATE TABLE admin(
id INT PRIMARY KEY AUTO_INCREMENT,
user_name VARCHAR(15) NOT NULL,
pwd VARCHAR(25) NOT NULL
);

DELIMITER $
CREATE PROCEDURE insert_user(empname VARCHAR(15),emp_pwd VARCHAR(25))
BEGIN
    INSERT INTO admin(user_name, pwd) VALUES (empname,emp_pwd);
end $
DELIMITER ;
CALL insert_user('张无忌','123456');
#2. 创建存储过程get_phone(),实现传入女神编号，返回女神姓名和女神电话
CREATE TABLE beauty(
id INT PRIMARY KEY AUTO_INCREMENT,
NAME VARCHAR(15) NOT NULL,
phone VARCHAR(15) UNIQUE,
birth DATE
);
INSERT INTO beauty(NAME,phone,birth)
VALUES
('朱茵','13201233453','1982-02-12'),
('孙燕姿','13501233653','1980-12-09'),
('田馥甄','13651238755','1983-08-21'),
('邓紫棋','17843283452','1991-11-12'),
('刘若英','18635575464','1989-05-18'),
('杨超越','13761238755','1994-05-11');
SELECT * FROM beauty;

DELIMITER $
CREATE PROCEDURE get_phone(IN bid INT ,OUT phone VARCHAR(20) ,OUT name VARCHAR(20))
BEGIN
    SELECT b.NAME,b.phone INTO name,phone FROM beauty b WHERE b.id = bid;
end $
DELIMITER ;

call get_phone(1,@phone,@name);
select @phone,@name;
#3. 创建存储过程date_diff()，实现传入两个女神生日，返回日期间隔大小
DELIMITER $
CREATE PROCEDURE date_diff(date1 DATE,date2 DATE,OUT diff INT)
BEGIN SELECT DATEDIFF(date1,date2) INTO diff;
END $
DELIMITER ;
#4. 创建存储过程format_date(),实现传入一个日期，格式化成xx年xx月xx日并返回
DELIMITER $
CREATE PROCEDURE format_date(IN date1 DATE,OUT format_date VARCHAR(50))
BEGIN
    SELECT DATE_FORMAT(date1,'%Y年%m月%d日') INTO format_date;
end $
DELIMITER ;
call format_date('1990-01-01',@format_date);
select @format_date;
#5. 创建存储过程beauty_limit()，根据传入的起始索引和条目数，查询女神表的记录

DELIMITER $
CREATE PROCEDURE beauty_limit(IN start INT,IN count INT)
BEGIN
    SELECT * FROM beauty LIMIT start,count;
end $
DELIMITER ;
call beauty_limit(2,3);
#创建带inout模式参数的存储过程

#6. 传入a和b两个值，最终a和b都翻倍并返回
DELIMITER $
CREATE PROCEDURE double_num(INOUT a INT,INOUT b INT)
BEGIN
    SET a = a * 2;
    SET b = b * 2;
END $
DELIMITER ;
#7. 删除题目5的存储过程
drop procedure if exists beauty_limit;
#8. 查看题目6中存储过程的信息
show create procedure double_num;

#无参有返回
#1. 创建函数get_count(),返回公司的员工个数
CREATE FUNCTION get_count()
RETURNS INT
    DETERMINISTIC
    CONTAINS SQL
    READS SQL DATA
BEGIN
    RETURN (SELECT COUNT(*) FROM emps);
END;
#有参有返回
#2. 创建函数ename_salary(),根据员工姓名，返回它的工资
DELIMITER $
CREATE FUNCTION ename_salary(empname varchar(25))
RETURNS DOUBLE
    DETERMINISTIC
    CONTAINS SQL
    READS SQL DATA
BEGIN
    RETURN (SELECT salary FROM emps
            WHERE last_name = empname);
END $
DELIMITER ;
#3. 创建函数dept_sal() ,根据部门名，返回该部门的平均工资
DELIMITER $
CREATE FUNCTION dept_sal(deptname varchar(25))
RETURNS DOUBLE
    DETERMINISTIC
    CONTAINS SQL
    READS SQL DATA
BEGIN
    RETURN (SELECT AVG(salary)
            FROM emps join depts
            ON emps.department_id = depts.department_id
            WHERE depts.department_name = deptname);
END $
DELIMITER ;

#4. 创建函数add_float()，实现传入两个float，返回二者之和
DELIMITER $
CREATE FUNCTION dept_sal(a float,b float)
RETURNS float
    DETERMINISTIC
    CONTAINS SQL
    READS SQL DATA
BEGIN
    RETURN (select a+b);
END $
DELIMITER ;
```



# 变量、流程控制与游标

变量分为系统变量和用户自定义变量



## 系统变量

系统变量又可以分为全局系统变量（global）和会话系统变量（session）

全局系统变量顾名思义，在所有场合下修改都会生效除非重启服务，

而会话系统变量则独立于每个会话，会话也就是当客户端对数据库发起一次请求时会建立的链接，会话1的修改并不会影响会话2中的修改

```mysql
-- 查看系统变量

-- 看全局
show global variables;
-- 看会话
show session variables ;

-- 看指定系统变量（用两个@@开头）

select @@global.变量名

select @@session.变量名

-- 先看会话变量里有没有，在看全局变量有没有
select @@变量名;

-- 修改系统变量
-- 修改系统文件后，重启mysql服务

-- 在运行期间使用set,但是一重启就会失效

SET @@global.变量名=变量值
-- 或
SET global 变量名=变量值
```

## 用户变量

用户变量分类又会分为会话用户变量和局部变量



会话用户变量和会话变量一样，只对当前连接会话有效

局部变量只在BEGIN END语句块中有效

- 会话用户变量

作用域：当前会话

定义位置：会话的任何地方

语法，加@ 不用指明类型

```mysql
-- 变量的声明和赋值
-- 1.
set @用户变量 = 值
set @用户变量 := 值
-- 2.
select @用户变量 := 表达式【from 等子句】
select 表达式 into @用户变量 【from 等子句】
-- 使用
select @变量名
```

- 局部变量

作用域：定义的BEGIN END中

定义位置：BEGIN END的第一句话

语法，不加@ 需要指明类型

```mysql
-- 声明
DECLARE 变量名 类型 [default 值]；

-- 赋值
-- 1、
SET 赋值 = 值
-- 2、
select 字段名或表达式 into 变量名 FROM 表

-- 使用
select 局部变量名；
```

## 定义条件与处理程序

**定义条件**是事先定义程序执行过程中可能遇到的问题，**处理程序**定义了在遇到问题时，应当采取的处理方式，并且保证存储过程或错误时能继续执行

sql自己的 try catch final



定义条件

```mysql
declare 错误名称 condition for 错误码（或错误条件）

-- 示例 定义Field_NOT_BE_NULL对应mtsql中违法非空约束的错误类型 ERROR 1048（23000）

DECLARE Field_NOT_BE_NULL condition for 1048

-- 使用sqlstate_value
DECLARE Field_NOT_BE_NULL condition for SQLSTATE '23000'
```

处理程序

```mysql
DECLARE 处理方式 HANDLER FOR 错误类型 处理语句
```

![image-20240819110145119](E:\Code\笔记\笔记图片\image-20240819110145119.png)

```mysql
#方法1：捕获sqlstate_value
DECLARE CONTINUE HANDLER FOR SQLSTATE '42S02' SET @info = 'NO_SUCH_TABLE';
#方法2：捕获mysql_error_value
DECLARE CONTINUE HANDLER FOR 1146 SET @info = 'NO_SUCH_TABLE';
#方法3：先定义条件，再调用
DECLARE no_such_table CONDITION FOR 1146;
DECLARE CONTINUE HANDLER FOR NO_SUCH_TABLE SET @info = 'NO_SUCH_TABLE';
#方法4：使用SQLWARNING
DECLARE EXIT HANDLER FOR SQLWARNING SET @info = 'ERROR';
#方法5：使用NOT FOUND
DECLARE EXIT HANDLER FOR NOT FOUND SET @info = 'NO_SUCH_TABLE';
#方法6：使用SQLEXCEPTION
DECLARE EXIT HANDLER FOR SQLEXCEPTION SET @info = 'ERROR';
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

# 

