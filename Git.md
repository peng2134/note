# Git

三个区域（都在本地）

1. 工作区

   在项目的文件目录下进行增删改查的地方不会收到版本库的影响

   （版本库：暂存区，仓库区）

2. 暂存区

   存储每天中一小阶段的工作

3. 仓库区

   在用户分支上存储每天的工作



## 操作

初始化git 

git init 



配置

git config  user.name name

git config  user.email xxxx@xx.com





查看文件的工作区域 红色一般为工作区，绿色为暂存区文件

git status



上传到工作区

git add

上传到仓库区

git commit -m '你要添加的注释'



查看日志

git log



回退版本

git reset 

![image-20240819161522281](E:\Code\笔记\笔记图片\image-20240819161522281.png)

查看历史记录版本

git reflog

根据版本号回退

git rest --hard 版本号



撤销操作



从暂存区恢复到工作区

git reset HEAD 文件



丢弃工作区改动

git checkout 文件



远程clone文件

git clone

