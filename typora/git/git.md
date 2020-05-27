# 一、git结构

![image-20200503161530087](D:\notes\notes\typora\git\images\image-20200503161530087.png)

# 二、git和代码托管中心

代码托管中心的任务：维护远程库

1、局域网环境下

- GitLab服务器

2、外网环境下

- GitHub
- 码云

# 三、本地库和远程库

## 3.1 团队内部协作

![image-20200503161932788](D:\notes\notes\typora\git\images\image-20200503161932788.png)

## 3.2 跨团队协作

![image-20200503162014474](D:\notes\notes\typora\git\images\image-20200503162014474.png)

# 四、git基本命令

## 4.1 本地库初始化

- git init
- 效果  会生成.git文件(暂存区，版本库)

![image-20200506125229317](D:\notes\notes\typora\git\images\image-20200506125229317.png)

- 注意：.git 目录中存放的是本地库相关的子目录和文件，不要删除，也不要胡 乱修改

## 4.2 设置签名

形式：

​		用户名：tom 

​		Email 地址：goodMorning@atguigu.com

作用：区分不同开发人员的身份

辨析:这里设置的签名和登录远程库(代码托管中心)的账号、密码没有任何关系。



命令：

​		项目级别/仓库级别：仅在当前本地库范围内有效

​				git **config** user.name tom_pro 

​				git **config** user.email goodMorning_pro@atguigu.com 

​				信息保存位置：./.git/config 文件

![image-20200506125740830](D:\notes\notes\typora\git\images\image-20200506125740830.png)

​		系统用户级别：登录当前操作系统的用户范围

​				git config **--global** user.name tom_glb 

​				git config **--global** goodMorning_pro@atguigu.com 

​				信息保存位置：~/.gitconfig 文件

![image-20200506125835942](D:\notes\notes\typora\git\images\image-20200506125835942.png)

​		级别优先级

​				就近原则：项目级别优先于系统用户级别，二者都有时采用项目级别的签名 

​				如果只有系统用户级别的签名，就以系统用户级别的签名为准 

​				二者都没有不允许



## 4.3 基本操作

### 4.3.1 git status

​		查看工作区、暂存区状态

### 4.3.2 添加

​		git add [file name]   

​		将工作区的“新建/修改”添加到暂存区

​		git add -A 

​		将所有文件添加到暂存区

### 4.3.3 提交

git commit -m "commit message" [file name]

将暂存区的内容提交到本地库

### 4.3.4 查看历史记录

git log

![image-20200506130302821](D:\notes\notes\typora\git\images\image-20200506130302821.png)



多屏显示控制方式： 

空格向下翻页 

b 向上翻页 

q 退出



git log --pretty=oneline

![image-20200506130339082](D:\notes\notes\typora\git\images\image-20200506130339082.png)

git log --oneline

![image-20200506130359138](D:\notes\notes\typora\git\images\image-20200506130359138.png)

git reflog

![image-20200506130419752](D:\notes\notes\typora\git\images\image-20200506130419752.png)

HEAD@{移动到当前版本需要多少步}

### 4.3.5 前进后退

本质

![image-20200506130516083](D:\notes\notes\typora\git\images\image-20200506130516083.png)

基于索引值操作[推荐]

- git reset --hard [局部索引值]
- git reset --hard a6ace91

使用^符号：只能后退

- git reset --hard HEAD^
- 注：一个^表示后退一步，n 个表示后退 n 步

使用~符号：只能后退

- git reset --hard HEAD~n
- 注：表示后退 n 步



**reset** **命令的三个参数对比**

--soft 参数

- 仅仅在本地库移动 HEAD 指针

![image-20200506130753152](D:\notes\notes\typora\git\images\image-20200506130753152.png)

--mixed 参数

- 在本地库移动 HEAD 指针
- 重置暂存区

![image-20200506130838372](D:\notes\notes\typora\git\images\image-20200506130838372.png)

--hard 参数

- 在本地库移动 HEAD 指针
- 重置暂存区
- 重置工作区

### 4.3.6 比较文件差异

git diff [文件名]

- 将工作区中的文件和暂存区进行比较

git diff [本地库中历史版本] [文件名]

- 将工作区中的文件和本地库历史记录比较

不带文件名比较多个文件





