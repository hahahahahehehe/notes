# 一、gradle的安装

​	1、下载解压

​	2、配置环境变量

​	3、使用命令gradle -v  查看是否安装成功

# 二、idea中gradle项目的创建

1、如果是web项目，则勾上web

![image-20200508131331161](D:\notes\notes\typora\gradle\images\image-20200508131331161.png)



2、像创建maven项目一样

![image-20200508131432514](D:\notes\notes\typora\gradle\images\image-20200508131432514.png)

3、选择自动导入，gradle home选择本地gradle安装路径，别用系统默认的 

![image-20200508131553223](D:\notes\notes\typora\gradle\images\image-20200508131553223.png)

# 三、gradle配置文件介绍

## 1、build.gradle

```
plugins {
    id 'java'
}

group 'com.gradle'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

/**
 * 指定所使用的的仓库 ， mavenCentral()表示使用中央仓库，此刻项目中所用的jar都会默认从中央仓库下载到本地指定目录
 */
repositories {
    mavenCentral()
}

/*
* gradle工程所有的jar的坐标都放在dependecies属性内放置
* 每一个jar都由三个基本元素组成
* group , name , version
*
* testCompile  该属性为jar包的作用域
*       表示jar包在测试的时候起作用
* 我们在gradle里面添加坐标的时候都要带上jar包的作用域
* */
dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.12'
}
```

## 2、配置gradle使用本地maven库

1. 将本地maven库地址添加到环境变量，变量名称为GRADLE_USER_HOME

![image-20200508131910296](D:\notes\notes\typora\gradle\images\image-20200508131910296.png)



# 三、相关配置介绍

1、下面表示先从中央仓库下载jar

```
repositories {
    mavenCentral()
}
```



2、下面表示先从本地库查找jar，没有的话再从中央仓库下载jar

```
repositories {
    mavenLocal()
    mavenCentral()
}
```