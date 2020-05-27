# 一、mybatis开始案例

1、准备依赖

```java
<dependencies>

        <!--mybatis包-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version>
        </dependency>

        <!--junit测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>

        <!--mysql数据库-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.46</version>
        </dependency>

        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.2</version>
        </dependency>

        <!--日志-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.12</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.12</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
    </dependencies>
```

2、准备数据库和表数据

```sql
CREATE TABLE `country` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `countryname` varchar(255) DEFAULT NULL,
  `countrycode` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;
```



3、配置mybatis主配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!--指定log4j输出日志-->
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>

    <!--配置了包的别名-->
    <typeAliases>
        <package name="com.demo.bean" />
    </typeAliases>
	
    <!--编写数据源信息，这里用的是非数据池形式-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC">
                <property name="" value="" />
            </transactionManager>
            <!--非数据池-->
            <dataSource type="UNPOOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>

	<!--编写映射文件的位置-->
    <mappers>
        <mapper resource="com/demo/mapper/CountryMapper.xml"></mapper>
    </mappers>
</configuration>
```



4、创建实体类和mapper

```java
package com.demo.mapper;

/**
 * @author Administrator
 * @time 2020/5/21
 */
public interface CountryMapper {
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">


<mapper namespace="com.demo.mapper.CountryMapper">
    <!--本来此处的resiultType值要写入全类名com.demo.bean.Country,但是在主配置文件写入了别名typeAliases,所以直接写入类名即可-->
    <select id="selectAll" resultType="Country">
        select  id,countryname,countrycode from country
    </select>
</mapper>
```

```java
package com.demo.bean;

/**
 * @author Administrator
 * @time 2020/5/21
 */
public class Country {

    private Long id ;
    private String countryname;
    private String countrycode ;

    public Country(){}

    public Country(Long id , String countryname , String countrycode){
        this.id = id ;
        this.countryname = countryname ;
        this.countrycode = countrycode ;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getCountryname() {
        return countryname;
    }

    public void setCountryname(String countryname) {
        this.countryname = countryname;
    }

    public String getCountrycode() {
        return countrycode;
    }

    public void setCountrycode(String countrycode) {
        this.countrycode = countrycode;
    }
}
```



5、配置log4j查看mybatis查询数据库的过程

```properties
#全局配置
log4j.rootLogger=ERROR,stdout

#Mybatis日志配置
#TRACE是日志的最低级别，能打印出所有的日志，适合开发时使用
#log4j.logger.com.demo.mapper对应的是com.demo.mapper下的包
log4j.logger.com.demo.mapper=TRACE

#控制台输出配置
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.ConversionPattern=%5p [%t] - %m%n
```



6、编写测试代码

```java
package com.demo;

import com.demo.bean.Country;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.BeforeClass;
import org.junit.Test;

import java.io.IOException;
import java.io.Reader;
import java.util.List;

/**
 * @author Administrator
 * @time 2020/5/21
 */
public class CountryTest {

    private static SqlSessionFactory sqlSessionFactory ;

	//在测试类之前运行
    @BeforeClass
    public static void init(){
        try {
            Reader reader = Resources.getResourceAsReader("mybatis-config.xml");
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
            reader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private void printCountryList(List<Country> countryList){
        for(Country country :countryList){
            System.out.println("id:"+country.getId()+",name:"+country.getCountryname()+",code:"+country.getCountrycode());
        }
    }

    @Test
    public void testSelectAll(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        List<Country> selectAll = sqlSession.selectList("selectAll");
        printCountryList(selectAll);
        sqlSession.close();
    }
}
```



7、结果

数据正常从数据库获取

```java
==>  Preparing: select id,countryname,countrycode from country 
==> Parameters: 
<==    Columns: id, countryname, countrycode
<==        Row: 1, 中国, cn
<==        Row: 2, 美国, us
<==        Row: 3, 俄罗斯, ru
<==        Row: 4, 英国, gb
<==        Row: 5, 法国, fr
<==      Total: 5
id:1,name:中国,code:cn
id:2,name:美国,code:us
id:3,name:俄罗斯,code:ru
id:4,name:英国,code:gb
id:5,name:法国,code:fr
```



# 二、mybatis XML方式基本用法

## 1、前期配置



1、创建五张表:用户表、角色表、权限表、用户角色表、角色权限表

用户表

```sql
CREATE TABLE `sys_user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '用户ID',
  `user_name` varchar(50) DEFAULT NULL COMMENT '用户名',
  `user_password` varchar(50) DEFAULT NULL COMMENT '密码',
  `user_email` varchar(50) DEFAULT NULL COMMENT '邮箱',
  `user_info` text COMMENT '简介',
  `head_img` blob COMMENT '头像',
  `create_time` datetime DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1002 DEFAULT CHARSET=utf8 COMMENT='用户表';
```

角色表

```sql
CREATE TABLE `sys_role` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '角色ID',
  `role_name` varchar(50) DEFAULT NULL COMMENT '角色名',
  `enabled` int(11) DEFAULT NULL COMMENT '有效标志',
  `create_by` bigint(20) DEFAULT NULL COMMENT '创建人',
  `create_time` datetime DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8 COMMENT='角色表';
```

权限表

```sql
CREATE TABLE `sys_privilege` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '权限ID',
  `privilege_name` varchar(50) DEFAULT NULL COMMENT '权限名称',
  `privilege_url` varchar(200) DEFAULT NULL COMMENT '权限url',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8 COMMENT='权限表';
```

用户角色表

```sql
CREATE TABLE `sys_user_role` (
  `user_id` bigint(20) DEFAULT NULL COMMENT '用户ID',
  `role_id` bigint(20) DEFAULT NULL COMMENT '角色ID'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='用户角色关联表';
```

角色权限表

```sql
CREATE TABLE `sys_role_privilege` (
  `role_id` bigint(20) DEFAULT NULL COMMENT '角色ID',
  `privilege_id` bigint(20) DEFAULT NULL COMMENT '权限ID'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='角色权限关联表';
```



2、创建实体类

My Batis 默认是遵循“下画线转驼峰”命名方式的

byte []这个类型 般对应数据库中的 BLOB、LONGVARBINARY以及一些和二进制流有关的字段类型

SysUser.java

```java
package com.demo.bean;

import java.util.Date;

/**
 * @ClassName: SysUser
 * @author: Administrator
 * @date: 2020/5/21 18:38
 * @Description
 * 用户类
 */
public class SysUser {

    private Long id ;
    private String userName;
    private String userPassword;
    private String userEmail;
    private String userInfo;
    private byte[] headImg;
    private Date createTime;

    public SysUser(){}

    public SysUser(Long id,String userName,String userPassword,String userEmail,String userInfo,byte[] headImg,Date createTime){
        this.id = id;
        this.userName=userName;
        this.userPassword=userPassword;
        this.userEmail=userEmail;
        this.userInfo=userInfo;
        this.headImg=headImg;
        this.createTime=createTime;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public String getUserPassword() {
        return userPassword;
    }

    public void setUserPassword(String userPassword) {
        this.userPassword = userPassword;
    }

    public String getUserEmail() {
        return userEmail;
    }

    public void setUserEmail(String userEmail) {
        this.userEmail = userEmail;
    }

    public String getUserInfo() {
        return userInfo;
    }

    public void setUserInfo(String userInfo) {
        this.userInfo = userInfo;
    }

    public byte[] getHeadImg() {
        return headImg;
    }

    public void setHeadImg(byte[] headImg) {
        this.headImg = headImg;
    }

    public Date getCreateTime() {
        return createTime;
    }

    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }
}
```



SysUserRole

```java
package com.demo.bean;

/**
 * @ClassName: SysUserRole
 * @author: Administrator
 * @date: 2020/5/21 18:46
 * @Description
 */
public class SysUserRole {

    private Long userId;
    private Long roleId;

    public SysUserRole(){}

    public SysUserRole(Long userId, Long roleId) {
        this.userId = userId;
        this.roleId = roleId;
    }

    public Long getUserId() {
        return userId;
    }

    public void setUserId(Long userId) {
        this.userId = userId;
    }

    public Long getRoleId() {
        return roleId;
    }

    public void setRoleId(Long roleId) {
        this.roleId = roleId;
    }
}
```



SysRole

```java
package com.demo.bean;

import java.util.Date;

/**
 * @ClassName: SysRole
 * @author: Administrator
 * @date: 2020/5/21 18:48
 * @Description
 */
public class SysRole {

    private Long id ;
    private String roleName ;
    private Integer enabled ;
    private Long createBy ;
    private Date dateTime ;

    public SysRole() {
    }

    public SysRole(Long id, String roleName, Integer enabled, Long createBy, Date dateTime) {
        this.id = id;
        this.roleName = roleName;
        this.enabled = enabled;
        this.createBy = createBy;
        this.dateTime = dateTime;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getRoleName() {
        return roleName;
    }

    public void setRoleName(String roleName) {
        this.roleName = roleName;
    }

    public Integer getEnabled() {
        return enabled;
    }

    public void setEnabled(Integer enabled) {
        this.enabled = enabled;
    }

    public Long getCreateBy() {
        return createBy;
    }

    public void setCreateBy(Long createBy) {
        this.createBy = createBy;
    }

    public Date getDateTime() {
        return dateTime;
    }

    public void setDateTime(Date dateTime) {
        this.dateTime = dateTime;
    }
}
```



SysRolePrivilege

```java
package com.demo.bean;

/**
 * @ClassName: SysRolePrivilege
 * @author: Administrator
 * @date: 2020/5/21 18:50
 * @Description
 */
public class SysRolePrivilege {

    private Long roleId ;
    private Long privilegeId ;

    public SysRolePrivilege() {
    }

    public SysRolePrivilege(Long roleId, Long privilegeId) {
        this.roleId = roleId;
        this.privilegeId = privilegeId;
    }

    public Long getRoleId() {
        return roleId;
    }

    public void setRoleId(Long roleId) {
        this.roleId = roleId;
    }

    public Long getPrivilegeId() {
        return privilegeId;
    }

    public void setPrivilegeId(Long privilegeId) {
        this.privilegeId = privilegeId;
    }
}
```

SysPrivilege

```java
package com.demo.bean;

/**
 * @ClassName: SysPrivilege
 * @author: Administrator
 * @date: 2020/5/21 18:52
 * @Description
 */
public class SysPrivilege {

    private Long id ;
    private String privilegeName ;
    private String privilegeUrl ;

    public SysPrivilege() {
    }

    public SysPrivilege(Long id, String privilegeName, String privilegeUrl) {
        this.id = id;
        this.privilegeName = privilegeName;
        this.privilegeUrl = privilegeUrl;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getPrivilegeName() {
        return privilegeName;
    }

    public void setPrivilegeName(String privilegeName) {
        this.privilegeName = privilegeName;
    }

    public String getPrivilegeUrl() {
        return privilegeUrl;
    }

    public void setPrivilegeUrl(String privilegeUrl) {
        this.privilegeUrl = privilegeUrl;
    }
}
```



3、创建实体类对应的Mapper接口和xml映射文件

![image-20200521190713835](D:\notes\notes\typora\mybatis\images\image-20200521190713835.png)

4、编写主配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!--指定log4j输出日志-->
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>

    <!--配置了 个包的别名-->
    <typeAliases>
        <package name="com.demo.bean" />
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC">
                <property name="" value="" />
            </transactionManager>
            <dataSource type="UNPOOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>


    <mappers>
        <mapper resource="com/demo/mapper/UserMapper.xml"></mapper>
        <mapper resource="com/demo/mapper/RoleMapper.xml"></mapper>
        <mapper resource="com/demo/mapper/PrivilegeMapper.xml"></mapper>
        <mapper resource="com/demo/mapper/UserRoleMapper.xml"></mapper>
        <mapper resource="com/demo/mapper/RolePrivilegeMapper.xml"></mapper>
    </mappers>
</configuration>
```

这里Mappers标签中将映射文件一个个加进来了，后面如果有新的映射文件还得更改主配置，很麻烦

可以使用package方式，这种方式会先查找com.demo.mapper包下的所有`接口`,循环对接口做如下操作

1. 判断接口对应的命名 间是否己经存在，如果存在就抛出异常，不存在就继续进行接下来的操作。
2. 加载接口对应的xml映射文件 将接口全限定名转换为路径 例如 将接口com.demo.mapper.UserMapper 转换为 com/demo/mapper/UserMapper xml,以.xml后缀搜索 XML 资源，如果找到就解析 XML
3. 处理接口中的注解方法。

```xml
<mappers>
       <package name="com.demo.mapper" />
    </mappers>
```



## 2、select用法

编写一个根据用户id查询用户数据的方法



1、在UserMapper接口中添加方法

```java
public interface UserMapper {

    SysUser getUserById(Long id);
}
```



2、在UserMapper.xml映射文件添加如下内容

```xml
<?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE mapper
                PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--对于列名和字段名不一致的需要进行配置，一致的话mybatis默认自动配置-->
<mapper namespace="com.demo.mapper.UserMapper">
    <resultMap id="userMap" type="com.demo.bean.SysUser">
        <id property="id" column="id" />
        <result property="userName" column="user_name" />
        <result property="userPassword" column="user_password" />
        <result property="userEmail" column="user_email" />
        <result property="userInfo" column="user_info" />
        <result property="headImg" column="head_img" />
        <result property="createTime" column="create_time" />
    </resultMap>


    <select id="getUserById" resultType="Country">
        select * from sys_user where id = #{id}
    </select>
</mapper>
```

XML 中的 select 标签的 id 属性值和定义的接口方法名是一样的,MyBatis 就是通过这种方式将接口方法和 XML 定义的 SQL 语句关联到起来的，如果接口方法没有和XML 中的 id 属性值相对应，启动程序便会报错。映射 XML 和接口的命名需要符合如下规则:

1. 当只使用 XML 而不使用接 口的时候 namespace 的值可以设置为任意不重复的名称
2. 标签的 id 属性值在任何 候都不能出现英文句号".",并且同 一命名空间下不能现重复的 id
3. 因为接口方法是可以重载的，所以接口中可以出现多个同名但参数不同的方法，但是XML中id 的值不能重复，因而接口中的所有同名方法会对应着 XML 中的同一id的方法

接口中定义的返回值类型必须和XML中配置的 resultType 类型一致，否则就会因为类型不一致而抛出异常，返回值类型是由 XM L中的 resultType（或 resultMap 中的 type ）决定的，不是由接口中写的返回值类型决定的



当使用resulutype为全类名时，则需要在sql语句中对列名和属性名不一致的设置别名,，通过设置别名使最终的查询结果列和 resultType 指定对象的属性名保持一致，进而实现自动映射。

```xml
<select id="selectAll" resultType="com.demo.bean.SysUser">
        select id,
        user_name userName,
        user_password userPassword,
        user_email userEmail,
        user_info userInfo,
        head_img headImg,
        create_time createTime
        from sys_user
    </select>
```



在数据库中，大多数数据库设置不区分大小写，所以下划线命名的方式很常见，如user_name等，而在java中一般驼峰式命名很常见，如userName等，所以mybatis提供了一个全局属性mapUnderscoreToCamelCase，配置这个属性为True可以自动将以下划线命名的数据库类映射到以驼峰式命名的java类中，这个属性默认为false,要使用此功能，需要在主配置文件(mybatis-config.xml)中配置

```xml
<settings>
        <setting name="mapUnderscoreToCamelCase" value="true" />
    </settings>
```

