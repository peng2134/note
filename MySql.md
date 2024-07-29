# 配置信息

1. 安装位置查询

   登录后输入 show variables like "%char%";

   ![image-20240729095105794](E:\Code\笔记图片\image-20240729095105794.png)

2. 找到创建数据库的位置 show variables like "%datadir%";

   ![image-20240729095347965](E:\Code\笔记图片\image-20240729095347965.png)

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

## 数据库操作

```mysql
show databases; 查询数据库

create database name; 创建数据库

drop database name; 删除数据库

user name; 使用数据库

show tables; 显示库中的表
```



 