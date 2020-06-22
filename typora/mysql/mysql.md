# 一、基础篇

## 1.1 sql基础

### 1.1.1 sql分类

1、DDL(Data Definition Languages)  数据定义语言

- 操作列、索引、表、数据库等对象，常用的语句操作关键字有create,drop,alter

2、DML(Data Manipulation Laguage) 数据操纵语句

- 增删改查等操作  常用的语句关键字insert,delete,update,select

3、DCL(Data Control Language) 数据控制语句

- 用于控制不同数据段直接的许可和访问级别。常用的语句关键字grant,revoke

### 1.1.2 DDL

1、创建数据库

```sql
create database dbname;
```

2、使用数据库

```sql
user dbname;
```

3、显示当前数据库的所有表名

```sql
show tables;
```

4、删除数据库

```sql
drop database dbname;
```

5、创建表

```sql
create table table_name(
	ename varchar(10),
    sal decimal(10,2)
);
```

6、查看表结构

```sql
desc table_name;
```

7、查看表的创表语句

​		可以显示表定义之外还能查看字符编码及存储引擎

```sql
show create table table_name \G
```

![image-20200608182926092](D:\notes\notes\typora\mysql\images\image-20200608182926092.png)

8、删除表

```sql
drop table table_name;
```

9、修改表

1. 修改表字段类型

```sql
alter table table_name modify column column_definition [first/after column_name];
```

![image-20200608183509793](D:\notes\notes\typora\mysql\images\image-20200608183509793.png)

2.修改字段名

```sql
alter table table_name change old_column new_column column_definition
```

![image-20200608184459409](D:\notes\notes\typora\mysql\images\image-20200608184459409.png)

**modify和change都可以就该表定义,不同的是change后需要写两次列名，不方便,但是change的优点是可以修改列名,modify不行**



3.增加表字段

```sql
alter table table_name add column column_definition [first/after column]
```

![image-20200608183839029](D:\notes\notes\typora\mysql\images\image-20200608183839029.png)



4.删除表字段

```sql
alter table table_name drop column_name
```

![image-20200608184017620](D:\notes\notes\typora\mysql\images\image-20200608184017620.png)



5.修改表名

```sql
alter table table_name rename new_tablename
```

![image-20200608185017493](D:\notes\notes\typora\mysql\images\image-20200608185017493.png)



### 1.1.3 DML



1、插入数据

```sql
insert into table_name(field1,field2...fieldn) values(value1,value2...valuen);
```



2、更新数据

```sql
update tablename set field1=value1, field2=value2... fieldn=valuen [where condition]
```



更新多张表

```sql
update t1,t2...,tn t1.field1...tn.fieldn [where condition]
```



3、删除数据

```sql
delete from table_name [where condition]
```



4、查询记录

```sql
select * from table_name [where condition]
```



4.1 去重查询

```sql
select distinct column_name from table_name
```



4.2 排序查询

```sql
select * from tablename [where condition] [order by field1 [desc/asc],field2 [desc/asc],...fieldn [desc/asc]]
```



4.3 limit查询

```sql
select ...[limit offset_start,row_count]
```



4.4 最大、最小、和查询

```sql
select sum(sal),max(sal),min(sal) from tablename
```



4.5 表连接

- 内连接
  - 仅显示两张表中相互匹配的数据
- 外连接
  - 左连接
    - 包含左边表的所有记录，甚至是右边表没有和他匹配的记录
  - 右连接
    - 包含右边表所有的记录，甚至是左边表没有和他匹配的记录



![image-20200608191931296](D:\notes\notes\typora\mysql\images\image-20200608191931296.png)



4.5.1 内连接

​		仅仅显示匹配的语句

![image-20200608192013261](D:\notes\notes\typora\mysql\images\image-20200608192013261.png)



4.5.2 左外连接

显示做表的全部数据，即使没有匹配的数据

![image-20200608192233555](D:\notes\notes\typora\mysql\images\image-20200608192233555.png)



4.5.3 右连接

显示右表的全部数据

![image-20200608192357397](D:\notes\notes\typora\mysql\images\image-20200608192357397.png)



4.6 子查询

![image-20200608193145560](D:\notes\notes\typora\mysql\images\image-20200608193145560.png)

如果子查询的记录数唯一，可以将in改为=

![image-20200608193336296](D:\notes\notes\typora\mysql\images\image-20200608193336296.png)

在某些情况下可以将子查询转换为表连接,将上面的子查询转化为表连接

![image-20200608193751393](D:\notes\notes\typora\mysql\images\image-20200608193751393.png)



4.7 记录联合

```sql
select * from t1
UNION/UNION ALL
select * from t2
UNION/UNION ALL
...
UNION/UNION ALL
slect * from tn
```

UNION与UNION ALL的区别：

- UNION ALL 直接将结果合并到一起
- UNION   在UNION ALL效果之后的结果进行distinct去重

![image-20200608194350893](D:\notes\notes\typora\mysql\images\image-20200608194350893.png)

### 1.1.4 DCL

1、创建一个对test数据库下的所有表具有查询和新增的demo用户

![image-20200608194934593](D:\notes\notes\typora\mysql\images\image-20200608194934593.png)



2、使用demo用户登录

![image-20200608195103067](D:\notes\notes\typora\mysql\images\image-20200608195103067.png)



3、查询数据

![image-20200608195246751](D:\notes\notes\typora\mysql\images\image-20200608195246751.png)



4、收回demo用户的select权限

![image-20200608195426651](D:\notes\notes\typora\mysql\images\image-20200608195426651.png)



5、使用demo用户查询，显示无权限

![image-20200608195531244](D:\notes\notes\typora\mysql\images\image-20200608195531244.png)



1.2 mysql支持的数据类型

## 1.5 常用函数

### 1.5.1 字符串函数

| 函数                  |                         功能                         |
| --------------------- | :--------------------------------------------------: |
| CONCAT(S1,S2...,Sn)   |             连接S1,S2,...Sn为一个字符串              |
| INSERT(str,x,y,instr) | 将str从x位置开始，y个字符长度的子串替换为字符串instr |
| LOWER(str)            |                    将str变为小写                     |
| UPPER(str)            |                    将str变为大写                     |
| LEFT(str,x)           |              返回字符串最左边的x个字符               |
| RIGHT(str,x)          |              返回字符串最右边的x个字符               |
| LPAD(str,n,pad)       |   用字符串对str左边进行填充，直到长度为n个字符长度   |
| RPAD(str,n,pad)       |   用字符串对str右边进行填充，直到长度为n个字符长度   |
| TRIM(str)             |                 清除字符串两边的空格                 |
| LTRIM(str)            |                 清除字符串左边的空格                 |
| RTRIM(str)            |                 清除字符串右边的空格                 |
| REPEAT(str,x)         |                 返回str重复x次的结果                 |
| REPLACE(str,a,b)      |                 用b替换str中的所有a                  |
| STRCMP(s1,s2)         |                   比较字符串s1和s2                   |
| SUBSTRING(str,x,y)    |         返回str从x位置起y个字符串长度的子串          |



### 1.5.2 数值函数

| 函数          | 功能                       |
| ------------- | -------------------------- |
| ABS(x)        | 返回x的绝对值              |
| CEIL(x)       | 返回大于x的最小整数        |
| FLOOR(x)      | 返回小于x的最大整数        |
| MOD(x/y)      | 返回x/y的模                |
| RAND()        | 返回0~1之间的随机数        |
| ROUND(x,y)    | 返回x四舍五入有y位小数的值 |
| TRUNCATE(x,y) | 返回x截断位y位小数的数字   |



### 1.5.3 时间日期函数

| 函数                  | 功能                                   |
| --------------------- | -------------------------------------- |
| CURDATE()             | 返回当前日期                           |
| CURTIME()             | 返回当前时间                           |
| NOW()                 | 返回当前的日期和时间                   |
| UNIX_TIMESTAMP(date)  | 返回日期date的unix时间戳               |
| FROM_UNIXTIME         | 返回UNIX时间戳的日期值                 |
| WEEK(date)            | 返回日期date为一年中的第几周           |
| YEAR(date)            | 返回date的年份                         |
| HOUR(time)            | 返回time的小时数                       |
| MINUTE(time)          | 返回time的分钟数                       |
| MONTHNAME(date)       | 返回date的月份名                       |
| DATE_FORMAT(date,fmt) | 按照fmt格式化日期date                  |
| DATEDIFF(t1,t2)       | 返回起始时间t1，结束时间t2之间隔的天数 |
|                       |                                        |

