# 一、mybatis环境搭建

## 1、环境搭建流程

1、创建maven工程并导入jar

2、创建实体类和dao的接口

3、创建mybatis的主配置文件

4、创建mybatis的映射文件

```xml
<dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.38</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
```



主体类和dao

Person.java

```java
package com.bean;

import java.util.Date;

public class Person {

    private Integer id ;
    private String username ;
    private Date birthday ;
    private String sex ;
    private String address;

    public Person() {
    }

    public Person(Integer id, String username, Date birthday, String sex, String address) {
        this.id = id;
        this.username = username;
        this.birthday = birthday;
        this.sex = sex;
        this.address = address;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Person{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", birthday=" + birthday +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}
```

IUserDao.java

```java
package com.dao;

import com.bean.Person;

import java.util.List;

public interface IUserDao {

    List<Person> findAll();
}
```

mybatis主配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--mybatis的主配置文件-->
<configuration>
    <!--配置环境-->
    <environments default="development">
        <!--配置mysql环境-->
        <environment id="development">
            <!--配置事务的类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源(连接池)-->
            <dataSource type="POOLED">
                <!--配置连接数据库的4个基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定映射配置文件位置，映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
        <mapper resource="com/dao/*.xml"/>
    </mappers>
</configuration>
```



mybatis映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.dao.IUserDao">

    <select id="findAll">
        select * from USER
    </select>
</mapper>
```

log4j.propeties

```properties
log4j.rootCategory=debug,CONSOLE,LOGFILE

log4j.logger.org.apache.axis.enterprise=FATAL,CONSOLE

# console
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=[mybatisTest] %-5p %d{yyyy-MM-dd HH\:mm\:ss} - %C.%M(%L)[%t] - %m%n
#log4j.appender.CONSOLE.Threshold=INFO
#log4j.appender.CONSOLE.Target=System.out
#log4j.appender.CONSOLE.Encoding=UTF-8

# all
#log4j.logger.com.demo=INFO, DEMO
log4j.appender.LOGFILE=org.apache.log4j.FileAppender
log4j.appender.LOGFILE.File=d:\\logs\\mybatisTest.log
log4j.appender.LOGFILE.Append=true
log4j.appender.LOGFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.LOGFILE.layout.ConversionPattern=[mybatisTest] %-5p %d{yyyy-MM-dd HH\:mm\:ss} - %C.%M(%L)[%t] - %m%n
```



## 2、注意事项

1、mybatis的映射文件必须和dao接口的包结构相同

2、mybatis映射文件的mapper标签的namespace的取值必须是dao接口的全类名

3、映射文件的操作配置id属性的取值必须是dao接口的方法名

当我们遵从了以上三点之后，在开发中我们就无须在写dao的实现类



# 二、mybatis入门案例

## 1、基于xml配置文件

步骤

```
1、读取配置文件
2、创建SqlSessionFactory工厂
3、使用工厂生产SqlSession对象
4、使用SqlSession创建Dao接口的代理对象
5、使用代理对象执行方法
6、释放资源
```

```java
@Test
    public void test01() throws Exception{
        /**
         * 1、读取配置文件
         * 2、创建SqlSessionFactory工厂
         * 3、使用工厂生产SqlSession对象
         * 4、使用SqlSession创建Dao接口的代理对象
         * 5、使用代理对象执行方法
         * 6、释放资源
         */

        InputStream in = Resources.getResourceAsStream("mybatis-config.xml");

        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);

        SqlSession sqlSession = factory.openSession();

        IUserDao iUserDao = sqlSession.getMapper(IUserDao.class);
        List<Person> all = iUserDao.findAll();
        for(Person person :all){
            System.out.println(person);
        }

        sqlSession.close();
        in.close();
    }
```

UserDao.xml

**resultType是必须的，不然mybatis查询完成之后不知道将数据封装到哪里**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.dao.IUserDao">

    <select id="findAll" resultType="com.bean.Person">
        select * from USER
    </select>
</mapper>
```

结果

数据成功返回

```java
Person{id=1, username='zhangsan', birthday=Mon May 18 00:00:00 CST 2020, sex='男', address='123'}
Person{id=2, username='lisi', birthday=Sun May 17 00:00:00 CST 2020, sex='女', address='456'}
Person{id=3, username='王五', birthday=Sat May 16 00:00:00 CST 2020, sex='男', address='浙江'}
Person{id=4, username='赵柳', birthday=Fri May 15 00:00:00 CST 2020, sex='女', address='江苏'}
```

### 2.2.1 sqlSession.getMapper源码分析

![](images\非常重要的一张图-分析代理dao的执行过程.png)





![](images\非常重要的一张图.png)







## 2、基于注解

1、在IUserDao中加上相应注解及sql语句

```java
package com.dao;

import com.bean.Person;
import org.apache.ibatis.annotations.Select;

import java.util.List;

public interface IUserDao {

    @Select("select * from user where username='zhangsan'")
    List<Person> findAll();
}
```

2、mybatis的主配置文件中的mapper指定文件的属性要改变，将mapper内resource属性改为相应的class属性

```xml
<mappers>
        <mapper class="com.dao.IUserDao"/>
    </mappers>
```

3、结果

成功获取相应的结果

```java
Person{id=1, username='zhangsan', birthday=Mon May 18 00:00:00 CST 2020, sex='男', address='123'}
```



## 3、手动编写dao实现类

1、编写IUserDao的实现类

```java
package com.dao.impl;

import com.bean.Person;
import com.dao.IUserDao;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;

import java.util.List;

public class UserDao implements IUserDao {

    private SqlSessionFactory factory ;

    public UserDao(SqlSessionFactory factory){
        this.factory = factory ;
    }

    @Override
    public List<Person> findAll() {
        SqlSession sqlSession = factory.openSession();
        List<Person> personList = sqlSession.selectList("com.dao.IUserDao.findAll");
        sqlSession.close();
        return personList;
    }
}
```

2、编写测试类

```java
@Test
    public void test01() throws Exception{
        /**
         * 1、读取配置文件
         * 2、创建SqlSessionFactory工厂
         * 3、使用工厂生产SqlSession对象
         * 4、使用SqlSession创建Dao接口的代理对象
         * 5、使用代理对象执行方法
         * 6、释放资源
         */

        InputStream in = Resources.getResourceAsStream("mybatis-config.xml");

        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);

        IUserDao userDao = new UserDao(factory);
        List<Person> all = userDao.findAll();
        for(Person person :all){
            System.out.println(person);
        }
        in.close();
    }
```

3、结果

成功返回

```java
Person{id=1, username='zhangsan', birthday=Mon May 18 00:00:00 CST 2020, sex='男', address='123'}
```



### 2.4.1 sqlSession的sql指定源码分析

![](images\非常重要的一张图-分析编写dao实现类Mybatis的执行过程.png)



## 4、设计者模式分析

1、在真实的项目开发中，寻找配置文件一般都不使用绝对路径和相对路径(eg:d:\a.xml或src/main/java/a.xml)，因为可能d盘不存在并且web项目打包之后的src目录也会不存在，一般使用以下两种方法

- 使用类加载器，只能读取类路径下的配置文件
- 使用request.getContext().getRealPath() 得到当前项目部署的真实路径

```java
 InputStream in = Resources.getResourceAsStream("mybatis-config.xml");
```

![](images\入门案例的分析.png)



创建SqlSessionFactory使用了构建者模式

- 构建者模式：将对象的创建细节隐藏，让用户直接调用方法创建对象

生产SqlSession使用了工厂模式

- 解耦(降低类与类之间的依赖关系)

创建Dao接口实现了使用了代理模式

- 不修改源码的基础上对已有的方法增强



## 5、自定义mybatis组件

1、分析mybatis的流程，查看我们需要在哪些方面自定义

![](D:\notes\notes\typora\mybatis\images\自定义mybatis开发流程图.png)





![](images\查询所有的分析.png)

<img src="D:\notes\notes\typora\mybatis\images\自定义Mybatis分析.png"  />

2、在入门案例中mybatis提供的类

- ```
  class Resources
  class SqlSessionFactoryBuilder
  interface SqlSessionFactory
  interface SqlSession
  ```

去掉pom.xml的mybatis依赖

```xml
<dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.38</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
```



我们要自定义上面四个类的功能，使能完成mybatis的功能,按照操作步骤来

```java
@Test
    public void test01() throws Exception{
        /**
         * 1、读取配置文件
         * 2、创建SqlSessionFactory工厂
         * 3、使用工厂生产SqlSession对象
         * 4、使用SqlSession创建Dao接口的代理对象
         * 5、使用代理对象执行方法
         * 6、释放资源
         */

        InputStream in = Resources.getResourceAsStream("mybatis-config.xml");

        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);
        SqlSession sqlSession = factory.openSession();
        IUserDao userDao = sqlSession.getMapper(IUserDao.class);
        List<Person> all = userDao.findAll();
        for(Person person :all){
            System.out.println(person);
        }
        in.close();
    }
```



1、创建Resources类(将配置文件读取为输入流)

获取当前类的类加载器来读取文件的

```java
package com.mybatis.build.resource;

import java.io.InputStream;

public class Resources {


    /**
     * 根据传入的参数获取一个字节输入流
     * @param filePath
     * @return
     */
    public static InputStream getResourceAsStream(String filePath){
        InputStream resource = Resources.class.getClassLoader().getResourceAsStream(filePath);
        return resource;
    }
}
```



2、创建SqlSessionFactoryBuilder类来创建SqlSessionFactory实例

创建SqlSessionFactory需要得到mybatis主配置文件的数据源信息以及映射文件的sql值，我们将配置文件信息封装到一个名为Configuration的类中，而Configuration类中的数据则需要读取xml文件获取，读取xml文件我们这里使用dom4j和xpath来解析xml文件



导入dom4j和xpath依赖

```xml
<dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.38</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>

        <dependency>
            <groupId>dom4j</groupId>
            <artifactId>dom4j</artifactId>
            <version>1.6.1</version>
        </dependency>
        <dependency>
            <groupId>jaxen</groupId>
            <artifactId>jaxen</artifactId>
            <version>1.1.6</version>
        </dependency>
    </dependencies>
```



创建Mapper类存放sql语句和返回类型

```JAVA
package com.custom.bean;

/**
 * @ClassName: Mapper
 * @author: Administrator
 * @date: 2020/6/8 13:03
 * @Description
 */
public class Mapper {

    private String queryString;
    private String resultType;

    public Mapper() {
    }

    public Mapper(String queryString, String resultType) {
        this.queryString = queryString;
        this.resultType = resultType;
    }

    public String getQueryString() {
        return queryString;
    }

    public void setQueryString(String queryString) {
        this.queryString = queryString;
    }

    public String getResultType() {
        return resultType;
    }

    public void setResultType(String resultType) {
        this.resultType = resultType;
    }

    @Override
    public String toString() {
        return "Mapper{" +
                "queryString='" + queryString + '\'' +
                ", resultType='" + resultType + '\'' +
                '}';
    }
}
```





创建配置文件数据类Configuration

其中setMappers方法采用追加的方式，防止有数据覆盖的问题

```java
package com.mybatis.build.cfg;

import java.util.HashMap;
import java.util.Map;

/**
 * @author 黑马程序员
 * @Company http://www.ithiema.com
 * 自定义mybatis的配置类
 */
public class Configuration {

    private String driver;
    private String url;
    private String username;
    private String password;

    private Map<String,Mapper> mappers = new HashMap<String,Mapper>();

    public Map<String, Mapper> getMappers() {
        return mappers;
    }
	
    public void setMappers(Map<String, Mapper> mappers) {
        this.mappers.putAll(mappers);//此处需要使用追加的方式
    }

    public String getDriver() {
        return driver;
    }

    public void setDriver(String driver) {
        this.driver = driver;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```



创建一个读取xml的类，并将数据存放到Configuration

读取xml文件的类XMLConfigBuilder

```java
package com.mybatis.build.utils;

//import com.itheima.mybatis.annotations.Select;


import com.mybatis.build.cfg.Configuration;
import com.mybatis.build.cfg.Mapper;
import com.mybatis.build.resource.Resources;
import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

import java.io.IOException;
import java.io.InputStream;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * @author 黑马程序员
 * @Company http://www.ithiema.com
 *  用于解析配置文件
 */
public class XMLConfigBuilder {



    /**
     * 解析主配置文件，把里面的内容填充到DefaultSqlSession所需要的地方
     * 使用的技术：
     *      dom4j+xpath
     */
    public static Configuration loadConfiguration(InputStream config){
        try{
            //定义封装连接信息的配置对象（mybatis的配置对象）
            Configuration cfg = new Configuration();

            //1.获取SAXReader对象
            SAXReader reader = new SAXReader();
            //2.根据字节输入流获取Document对象
            Document document = reader.read(config);
            //3.获取根节点
            Element root = document.getRootElement();
            //4.使用xpath中选择指定节点的方式，获取所有property节点
            List<Element> propertyElements = root.selectNodes("//property");
            //5.遍历节点
            for(Element propertyElement : propertyElements){
                //判断节点是连接数据库的哪部分信息
                //取出name属性的值
                String name = propertyElement.attributeValue("name");
                if("driver".equals(name)){
                    //表示驱动
                    //获取property标签value属性的值
                    String driver = propertyElement.attributeValue("value");
                    cfg.setDriver(driver);
                }
                if("url".equals(name)){
                    //表示连接字符串
                    //获取property标签value属性的值
                    String url = propertyElement.attributeValue("value");
                    cfg.setUrl(url);
                }
                if("username".equals(name)){
                    //表示用户名
                    //获取property标签value属性的值
                    String username = propertyElement.attributeValue("value");
                    cfg.setUsername(username);
                }
                if("password".equals(name)){
                    //表示密码
                    //获取property标签value属性的值
                    String password = propertyElement.attributeValue("value");
                    cfg.setPassword(password);
                }
            }
            //取出mappers中的所有mapper标签，判断他们使用了resource还是class属性
            List<Element> mapperElements = root.selectNodes("//mappers/mapper");
            //遍历集合
            for(Element mapperElement : mapperElements){
                //判断mapperElement使用的是哪个属性
                Attribute attribute = mapperElement.attribute("resource");
                if(attribute != null){
                    System.out.println("使用的是XML");
                    //表示有resource属性，用的是XML
                    //取出属性的值
                    String mapperPath = attribute.getValue();//获取属性的值"com/itheima/dao/IUserDao.xml"
                    //把映射配置文件的内容获取出来，封装成一个map
                    Map<String, Mapper> mappers = loadMapperConfiguration(mapperPath);
                    //给configuration中的mappers赋值
                    cfg.setMappers(mappers);
                }else{
                    /*System.out.println("使用的是注解");
                    //表示没有resource属性，用的是注解
                    //获取class属性的值
                    String daoClassPath = mapperElement.attributeValue("class");
                    //根据daoClassPath获取封装的必要信息
                    Map<String,Mapper> mappers = loadMapperAnnotation(daoClassPath);
                    //给configuration中的mappers赋值
                    cfg.setMappers(mappers);*/
                }
            }
            //返回Configuration
            return cfg;
        }catch(Exception e){
            throw new RuntimeException(e);
        }finally{
            try {
                config.close();
            }catch(Exception e){
                e.printStackTrace();
            }
        }

    }

    /**
     * 根据传入的参数，解析XML，并且封装到Map中
     * @param mapperPath    映射配置文件的位置
     * @return  map中包含了获取的唯一标识（key是由dao的全限定类名和方法名组成）
     *          以及执行所需的必要信息（value是一个Mapper对象，里面存放的是执行的SQL语句和要封装的实体类全限定类名）
     */
    private static Map<String,Mapper> loadMapperConfiguration(String mapperPath)throws IOException {
        InputStream in = null;
        try{
            //定义返回值对象
            Map<String,Mapper> mappers = new HashMap<String,Mapper>();
            //1.根据路径获取字节输入流
            in = Resources.getResourceAsStream(mapperPath);
            //2.根据字节输入流获取Document对象
            SAXReader reader = new SAXReader();
            Document document = reader.read(in);
            //3.获取根节点
            Element root = document.getRootElement();
            //4.获取根节点的namespace属性取值
            String namespace = root.attributeValue("namespace");//是组成map中key的部分
            //5.获取所有的select节点
            List<Element> selectElements = root.selectNodes("//select");
            //6.遍历select节点集合
            for(Element selectElement : selectElements){
                //取出id属性的值      组成map中key的部分
                String id = selectElement.attributeValue("id");
                //取出resultType属性的值  组成map中value的部分
                String resultType = selectElement.attributeValue("resultType");
                //取出文本内容            组成map中value的部分
                String queryString = selectElement.getText();
                //创建Key
                String key = namespace+"."+id;
                //创建Value
                Mapper mapper = new Mapper();
                mapper.setQueryString(queryString);
                mapper.setResultType(resultType);
                //把key和value存入mappers中
                mappers.put(key,mapper);
            }
            return mappers;
        }catch(Exception e){
            throw new RuntimeException(e);
        }finally{
            in.close();
        }
    }

    /**
     * 根据传入的参数，得到dao中所有被select注解标注的方法。
     * 根据方法名称和类名，以及方法上注解value属性的值，组成Mapper的必要信息
     * @param daoClassPath
     * @return
     */
    /*private static Map<String,Mapper> loadMapperAnnotation(String daoClassPath)throws Exception{
        //定义返回值对象
        Map<String,Mapper> mappers = new HashMap<String, Mapper>();

        //1.得到dao接口的字节码对象
        Class daoClass = Class.forName(daoClassPath);
        //2.得到dao接口中的方法数组
        Method[] methods = daoClass.getMethods();
        //3.遍历Method数组
        for(Method method : methods){
            //取出每一个方法，判断是否有select注解
            boolean isAnnotated = method.isAnnotationPresent(Select.class);
            if(isAnnotated){
                //创建Mapper对象
                Mapper mapper = new Mapper();
                //取出注解的value属性值
                Select selectAnno = method.getAnnotation(Select.class);
                String queryString = selectAnno.value();
                mapper.setQueryString(queryString);
                //获取当前方法的返回值，还要求必须带有泛型信息
                Type type = method.getGenericReturnType();//List<User>
                //判断type是不是参数化的类型
                if(type instanceof ParameterizedType){
                    //强转
                    ParameterizedType ptype = (ParameterizedType)type;
                    //得到参数化类型中的实际类型参数
                    Type[] types = ptype.getActualTypeArguments();
                    //取出第一个
                    Class domainClass = (Class)types[0];
                    //获取domainClass的类名
                    String resultType = domainClass.getName();
                    //给Mapper赋值
                    mapper.setResultType(resultType);
                }
                //组装key的信息
                //获取方法的名称
                String methodName = method.getName();
                String className = method.getDeclaringClass().getName();
                String key = className+"."+methodName;
                //给map赋值
                mappers.put(key,mapper);
            }
        }
        return mappers;
    }*/
}
```



创建SqlSessionFactoryBuilder

因为SqlSessionFactory是接口，所以实际上是创建工厂类的实现类SqlSessionFactoryImpl

```java
package com.mybatis.build;

import com.mybatis.build.cfg.Configuration;
import com.mybatis.build.factory.SqlSessionFactory;
import com.mybatis.build.factory.SqlSessionFactoryImpl;
import com.mybatis.build.utils.XMLConfigBuilder;

import java.io.InputStream;

/**
 * 用于创建一个SqlSessionFactory对象
 */
public class SqlSessionFactoryBuilder {

    /**
     * 根据参数的字节输入流来构建一个SqlSessionFactory工厂
     * @param in
     * @return
     */
    public SqlSessionFactory build(InputStream in){
        Configuration configuration = XMLConfigBuilder.loadConfiguration(in);
        return new SqlSessionFactoryImpl(configuration);
    }
}
```



工厂类接口SqlSessionFactory

```java
package com.mybatis.build.factory;

public interface SqlSessionFactory {

    SqlSession openSession();
}
```



工厂类的实现类SqlSessionFactoryImpl

因为SqlSession也是个接口，所以openSession()方式实际上创建的也是SqlSession的实现类

```java
package com.mybatis.build.factory;

import com.mybatis.build.cfg.Configuration;

/**
 * SqlSession的实现类
 */
public class SqlSessionFactoryImpl implements SqlSessionFactory {

    private Configuration configuration;

    public SqlSessionFactoryImpl(Configuration configuration){
        this.configuration = configuration ;
    }


    @Override
    public SqlSession openSession() {

        return new SqlSessionImpl(configuration);
    }
}
```



SqlSession接口

SqlSession有一个获取dao层接口(IUseDao)代理类的方法getMapper,因为返回类型无法确定，所以这里使用的是泛型

```java
package com.mybatis.build.factory;

public interface SqlSession {

    /**
     * 根据参数创建一个代理对象
     * @param daoInterfaceClass dao的接口字节码
     * @param <T>
     * @return
     */

    <T> T getMapper(Class<T> daoInterfaceClass);

    /**
     * 释放资源
     */
    void close();
}
```



SqlSession实现类SqlSessionImpl

因为动态代理类需要完成对数据库的操作，所以得拿到Connnection对象，这里的Connection对象是通过自定义类DataSourceUtil获取的

```java
package com.mybatis.build.factory;

import com.mybatis.build.cfg.Configuration;
import com.mybatis.build.proxy.MapperProxy;
import com.mybatis.build.utils.DataSourceUtil;

import java.lang.reflect.Proxy;
import java.sql.Connection;
import java.sql.SQLException;

public class SqlSessionImpl implements SqlSession {


    private Configuration configuration;
    private Connection connection;

    SqlSessionImpl(Configuration configuration){
        this.configuration = configuration ;
        connection = DataSourceUtil.getConnection(configuration);
    }

    /**
     * 用于创建代理对象
     * daoInterfaceClass dao的接口字节码
     * @return
     */
    @Override
    public <T> T getMapper(Class<T> daoInterfaceClass) {
        return (T)Proxy.newProxyInstance(daoInterfaceClass.getClassLoader(),new Class[]{daoInterfaceClass},new MapperProxy(configuration.getMappers(),connection));
    }

    @Override
    public void close() {
        try {
            connection.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```



获取Connection实例类DataSourceUtil

```java
package com.mybatis.build.utils;

import com.mybatis.build.cfg.Configuration;

import java.sql.Connection;
import java.sql.DriverManager;

/**
 * @author 黑马程序员
 * @Company http://www.ithiema.com
 * 用于创建数据源的工具类
 */
public class DataSourceUtil {

    /**
     * 用于获取一个连接
     * @param cfg
     * @return
     */
    public static Connection getConnection(Configuration cfg){
        try {
            Class.forName(cfg.getDriver());
            return DriverManager.getConnection(cfg.getUrl(), cfg.getUsername(), cfg.getPassword());
        }catch(Exception e){
            throw new RuntimeException(e);
        }
    }
}
```



SqlSessionImpl的代理实现类MapperProxy,此代理类是基于jdk代理来创建的

基于JDK的动态代理就需要知道两个类：

​		1.InvocationHandler（接口）

​		2.Proxy（类）

```java
package com.mybatis.build.proxy;

import com.mybatis.build.cfg.Mapper;
import com.mybatis.build.utils.Executor;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.sql.Connection;
import java.util.Map;

/**
 * @author 黑马程序员
 * @Company http://www.ithiema.com
 */
public class MapperProxy implements InvocationHandler {

    //map的key是全限定类名+方法名
    private Map<String,Mapper> mappers;
    private Connection conn;

    public MapperProxy(Map<String, Mapper> mappers, Connection conn){
        this.mappers = mappers;
        this.conn = conn;
    }

    /**
     * 用于对方法进行增强的，我们的增强其实就是调用selectList方法
     * @param proxy
     * @param method
     * @param args
     * @return
     * @throws Throwable
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //1.获取方法名
        String methodName = method.getName();
        //2.获取方法所在类的名称
        String className = method.getDeclaringClass().getName();
        //3.组合key
        String key = className+"."+methodName;
        //4.获取mappers中的Mapper对象
        Mapper mapper = mappers.get(key);
        //5.判断是否有mapper
        if(mapper == null){
            throw new IllegalArgumentException("传入的参数有误");
        }
        //6.调用工具类执行查询所有
        return new Executor().selectList(mapper,conn);
    }
}
```



代理类中的查询是使用工具类Executor来实现的

Executor工具类

```java
package com.mybatis.build.utils;

import com.mybatis.build.cfg.Mapper;

import java.beans.PropertyDescriptor;
import java.lang.reflect.Method;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.util.ArrayList;
import java.util.List;

/**
 * @author 黑马程序员
 * @Company http://www.ithiema.com
 * 负责执行SQL语句，并且封装结果集
 */
public class Executor {

    public <E> List<E> selectList(Mapper mapper, Connection conn) {
        PreparedStatement pstm = null;
        ResultSet rs = null;
        try {
            //1.取出mapper中的数据
            String queryString = mapper.getQueryString();//select * from user
            String resultType = mapper.getResultType();//com.itheima.domain.User
            Class domainClass = Class.forName(resultType);
            //2.获取PreparedStatement对象
            pstm = conn.prepareStatement(queryString);
            //3.执行SQL语句，获取结果集
            rs = pstm.executeQuery();
            //4.封装结果集
            List<E> list = new ArrayList<E>();//定义返回值
            while(rs.next()) {
                //实例化要封装的实体类对象
                E obj = (E)domainClass.newInstance();

                //取出结果集的元信息：ResultSetMetaData
                ResultSetMetaData rsmd = rs.getMetaData();
                //取出总列数
                int columnCount = rsmd.getColumnCount();
                //遍历总列数
                for (int i = 1; i <= columnCount; i++) {
                    //获取每列的名称，列名的序号是从1开始的
                    String columnName = rsmd.getColumnName(i);
                    //根据得到列名，获取每列的值
                    Object columnValue = rs.getObject(columnName);
                    //给obj赋值：使用Java内省机制（借助PropertyDescriptor实现属性的封装）
                    PropertyDescriptor pd = new PropertyDescriptor(columnName,domainClass);//要求：实体类的属性和数据库表的列名保持一种
                    //获取它的写入方法
                    Method writeMethod = pd.getWriteMethod();
                    //把获取的列的值，给对象赋值
                    writeMethod.invoke(obj,columnValue);
                }
                //把赋好值的对象加入到集合中
                list.add(obj);
            }
            return list;
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            release(pstm,rs);
        }
    }


    private void release(PreparedStatement pstm,ResultSet rs){
        if(rs != null){
            try {
                rs.close();
            }catch(Exception e){
                e.printStackTrace();
            }
        }

        if(pstm != null){
            try {
                pstm.close();
            }catch(Exception e){
                e.printStackTrace();
            }
        }
    }
}
```



到此使用测试类来测试数据可以完成mybatis的功能,得到数据库查询的结果

```xml
Person{id=1, username='zhangsan', birthday=2020-05-18, sex='男', address='123'}
Person{id=2, username='lisi', birthday=2020-05-17, sex='女', address='456'}
Person{id=3, username='王五', birthday=2020-05-16, sex='男', address='浙江'}
Person{id=4, username='赵柳', birthday=2020-05-15, sex='女', address='江苏'}
```



# 三、mybatis CURD

pom.xml

```xml
<dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.38</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
```



mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mybatis的主配置文件-->
<configuration>
    <!--配置环境-->
    <environments default="development">
        <!--配置mysql环境-->
        <environment id="development">
            <!--配置事务的类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源(连接池)-->
            <dataSource type="POOLED">
                <!--配置连接数据库的4个基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定映射配置文件位置，映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
        <mapper resource="com/dao/UserDao.xml" />
    </mappers>
</configuration>
```

UserDao.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.dao.IUserDao">

</mapper>
```

User

```java
package com.bean;

import java.util.Date;

/**
 * @ClassName: User
 * @author: Administrator
 * @date: 2020/5/28 13:10
 * @Description
 */
public class User {

    private Integer id ;
    private String userName ;
    private Date birthday ;
    private String sex ;
    private String address ;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", userName='" + userName + '\'' +
                ", birthday=" + birthday +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}
```

IUserDao

```java
package com.dao;

import com.bean.User;

import java.util.List;

public interface IUserDao {

}
```

测试方法准备

```java
package com.demo;


import com.bean.User;
import com.dao.IUserDao;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.InputStream;
import java.util.Date;
import java.util.List;

public class MybatisTest {

    InputStream in = null ;
    SqlSession sqlSession = null ;
    IUserDao userDao = null ;

	//在测试方法之前执行
    @Before
    public void init() throws Exception{
        in = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory build = new SqlSessionFactoryBuilder().build(in);
        sqlSession = build.openSession();
        userDao = sqlSession.getMapper(IUserDao.class);
    }
	
    //在测试方法之后执行
    @After
    public void destroy() throws Exception{
        in.close();
        sqlSession.close();
    }

}
```



## 1、查询

### 1、简单查询

1、在IUserDao新增查询所有和根据条件查询方法

```java
//查找全部
    List<User> findAll();

    //根据id查询数据
    User findUserById(Integer id);
```

2、在UserDao.xml中写入相应的sql语句

- id必须与IUserDao的方法名称一致
- 有返回值的话则resultType必须需要，否则会报找不到映射类型的错误

```xml
<!--
	1、id必须与IUserDao的方法名称一致
	2、有返回值的话则resultType必须需要，否则会报找不到映射类型的错误
-->
<select id="findAll" resultType="com.bean.User">
        select * from USER
    </select>

    <select id="findUserById" parameterType="Integer" resultType="com.bean.User">
        select * from user where id=#{id}
    </select>
```

3、调用测试方法

```java
@Test
    public void findAll(){
        List<User> all = userDao.findAll();
        for(User user:all){
            System.out.println(user);
        }
    }

    @Test
    public void findUserById(){
        User userById = userDao.findUserById(1);
        System.out.println(userById);
    }
```

在parameterType中一些基本类型参数，我们可以写全限定类名，也可以直接写名字，究其原因是mybatis早已将基本类型都设置了别名

可以查看TypeAliasRegistry源码

```java
package org.apache.ibatis.type;

import java.math.BigDecimal;
import java.math.BigInteger;
import java.sql.ResultSet;
import java.util.ArrayList;
import java.util.Collection;
import java.util.Collections;
import java.util.Date;
import java.util.HashMap;
import java.util.Iterator;
import java.util.List;
import java.util.Locale;
import java.util.Map;
import java.util.Set;

import org.apache.ibatis.io.ResolverUtil;
import org.apache.ibatis.io.Resources;

/**
 * @author Clinton Begin
 */
public class TypeAliasRegistry {

  private final Map<String, Class<?>> TYPE_ALIASES = new HashMap<String, Class<?>>();

  public TypeAliasRegistry() {
    registerAlias("string", String.class);

    registerAlias("byte", Byte.class);
    registerAlias("long", Long.class);
    registerAlias("short", Short.class);
    registerAlias("int", Integer.class);
    registerAlias("integer", Integer.class);
    registerAlias("double", Double.class);
    registerAlias("float", Float.class);
    registerAlias("boolean", Boolean.class);

    registerAlias("byte[]", Byte[].class);
    registerAlias("long[]", Long[].class);
    registerAlias("short[]", Short[].class);
    registerAlias("int[]", Integer[].class);
    registerAlias("integer[]", Integer[].class);
    registerAlias("double[]", Double[].class);
    registerAlias("float[]", Float[].class);
    registerAlias("boolean[]", Boolean[].class);

    registerAlias("_byte", byte.class);
    registerAlias("_long", long.class);
    registerAlias("_short", short.class);
    registerAlias("_int", int.class);
    registerAlias("_integer", int.class);
    registerAlias("_double", double.class);
    registerAlias("_float", float.class);
    registerAlias("_boolean", boolean.class);

    registerAlias("_byte[]", byte[].class);
    registerAlias("_long[]", long[].class);
    registerAlias("_short[]", short[].class);
    registerAlias("_int[]", int[].class);
    registerAlias("_integer[]", int[].class);
    registerAlias("_double[]", double[].class);
    registerAlias("_float[]", float[].class);
    registerAlias("_boolean[]", boolean[].class);

    registerAlias("date", Date.class);
    registerAlias("decimal", BigDecimal.class);
    registerAlias("bigdecimal", BigDecimal.class);
    registerAlias("biginteger", BigInteger.class);
    registerAlias("object", Object.class);

    registerAlias("date[]", Date[].class);
    registerAlias("decimal[]", BigDecimal[].class);
    registerAlias("bigdecimal[]", BigDecimal[].class);
    registerAlias("biginteger[]", BigInteger[].class);
    registerAlias("object[]", Object[].class);

    registerAlias("map", Map.class);
    registerAlias("hashmap", HashMap.class);
    registerAlias("list", List.class);
    registerAlias("arraylist", ArrayList.class);
    registerAlias("collection", Collection.class);
    registerAlias("iterator", Iterator.class);

    registerAlias("ResultSet", ResultSet.class);
  }
```





### 2、模糊查询

IUserDao.java

```java
List<User> getUserByName(String userName);
```

UserDao.xml

```xml
<select id="getUserByName" resultMap="user">
        select * from user where username like #{username}
    </select>
```

测试类

1、使用ognl表达式#{}在参数处加%拼接字符串,ognl表达式会给参数添加引号在添加到sql语句，防注入，${}不会

```java
@Test
    public void getUserByName(){
        IUserDao userDao = sqlSession.getMapper(IUserDao.class);
        List<User> users = userDao.getUserByName("%s%");
        for(User user:users){
            System.out.println(user);
        }
    }
```

结果

#{}是使用了占位符(preparstatment方式)

![image-20200530232730527](D:\notes\notes\typora\mybatis\images\image-20200530232730527.png)



2、或者使用${}

UserDao.xml

```xml
<select id="getUserByName" resultMap="user">
        select * from user where username like '%${value}%'
    </select>
```

测试

```java
@Test
    public void getUserByName(){
        IUserDao userDao = sqlSession.getMapper(IUserDao.class);
        List<User> users = userDao.getUserByName("s");
        for(User user:users){
            System.out.println(user);
        }
    }
```

结果

使用${}是直接拼接字符串的方式(statement方式)

![image-20200530232343112](D:\notes\notes\typora\mybatis\images\image-20200530232343112.png)



![](images\无标题.png)





使用${}拼接的模糊查询在xml中的参数必须写成${value}，这是固定格式，不能更改，这是因为在TextSqlNode的handleToken中将数据放到了固定key的键值对中，他的key就叫value

![image-20200530233001046](D:\notes\notes\typora\mybatis\images\image-20200530233001046.png)



### 3、使用pojo包装类的查询

编写复杂类型QueryVO,里面是User类型

```java
public class QueryVO {

    private User user ;

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }

    @Override
    public String toString() {
        return "QueryVO{" +
                "user=" + user +
                '}';
    }
}
```

IUserDao.java

```java
List<User> getUserByName(QueryVO queryVO);
```

UserDao.xml

```xml
#{}中内容的写法：
	它用的是 ognl 表达式。
	由于我们方法的参数是 一个 QueryVO 对象，此处要写QueryVO 对象中的属性名称。即user，而User属性的名称为userName，所以写成了下面的形式(因为java严格区分大小写，所以不能写成#{user.username}或者其他的样子，严格按照属性名称来)

ognl 表达式：
它是 apache 提供的一种表达式语言，全称是：
Object Graphic Navigation Language 对象图导航语言
它是按照一定的语法格式来获取数据的。
语法格式就是使用 #{对象.对象}的方式
<select id="getUserByName" resultMap="user">
        select * from user where username like #{user.userName}
    </select>
```

测试

```java
@Test
    public void getUserByName(){
        QueryVO queryVO = new QueryVO();
        User user = new User();
        user.setUserName("%s%");
        queryVO.setUser(user);
        IUserDao userDao = sqlSession.getMapper(IUserDao.class);
        List<User> users = userDao.getUserByName(queryVO);
        for(User user1:users){
            System.out.println(user1);
        }
    }
```







## 2、新增

1、IUserDao

```java
//插入数据
    void insertUser(User user);
```



2、UserDao.xml

```xml
<insert id="insertUser" parameterType="com.bean.User">
        insert into user(username,birthday,sex,address) values(
        #{userName},#{birthday},#{sex},#{address}
        )
    </insert>
```



3、测试

```java
@Test
    public void insertUser(){
        User user = new User();
        user.setUserName("liuhaha");
        user.setBirthday(new Date());
        user.setSex("男");
        user.setAddress("湖北");
        userDao.insertUser(user);
    }
```

会发现没报错，但是数据却没有插进去,里面说得设置自动提交事务，这里我们手动提交事务

Rolling back JDBC Connection

Resetting autocommit to true on JDBC Connection

```java
[mybatisTest] DEBUG 2020-05-28 13:55:54 - org.apache.ibatis.transaction.jdbc.JdbcTransaction.openConnection(137)[main] - Opening JDBC Connection
[mybatisTest] DEBUG 2020-05-28 13:55:54 - org.apache.ibatis.datasource.pooled.PooledDataSource.popConnection(406)[main] - Created connection 334203599.
[mybatisTest] DEBUG 2020-05-28 13:55:54 - org.apache.ibatis.transaction.jdbc.JdbcTransaction.setDesiredAutoCommit(101)[main] - Setting autocommit to false on JDBC Connection [com.mysql.jdbc.JDBC4Connection@13eb8acf]
[mybatisTest] DEBUG 2020-05-28 13:55:54 - org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug(159)[main] - ==>  Preparing: insert into user(username,birthday,sex,address) values( ?,?,?,? ) 
[mybatisTest] DEBUG 2020-05-28 13:55:54 - org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug(159)[main] - ==> Parameters: liuhaha(String), 2020-05-28 13:55:54.265(Timestamp), 男(String), 湖北(String)
[mybatisTest] DEBUG 2020-05-28 13:55:54 - org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug(159)[main] - <==    Updates: 1
[mybatisTest] DEBUG 2020-05-28 13:55:54 - org.apache.ibatis.transaction.jdbc.JdbcTransaction.rollback(80)[main] - Rolling back JDBC Connection [com.mysql.jdbc.JDBC4Connection@13eb8acf]
[mybatisTest] DEBUG 2020-05-28 13:55:54 - org.apache.ibatis.transaction.jdbc.JdbcTransaction.resetAutoCommit(123)[main] - Resetting autocommit to true on JDBC Connection [com.mysql.jdbc.JDBC4Connection@13eb8acf]
[mybatisTest] DEBUG 2020-05-28 13:55:54 - org.apache.ibatis.transaction.jdbc.JdbcTransaction.close(91)[main] - Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@13eb8acf]
[mybatisTest] DEBUG 2020-05-28 13:55:54 - org.apache.ibatis.datasource.pooled.PooledDataSource.pushConnection(363)[main] - Returned connection 334203599 to pool.
```

测试

数据成功插入

```java
@Test
    public void insertUser(){
        User user = new User();
        user.setUserName("liuhaha");
        user.setBirthday(new Date());
        user.setSex("男");
        user.setAddress("湖北");
        userDao.insertUser(user);
        sqlSession.commit();
    }
```



新增是因为id是自增的，所以我们不知道id是多少，但是可以在sql中添加selectKey来得到id

UserDao.xml

- keyColumn    数据库对应的列名
- keyProperty   类对应的属性名
- order   在insert语句之前执行还是之后执行(默认是AFTER)

```xml
<insert id="insertUser">
        insert into user(username,birthday,sex,address) values(
        #{userName},#{birthday},#{sex},#{address}
        )

        <selectKey keyColumn="id" keyProperty="id" resultType="Integer" order="AFTER">
            select last_insert_id()
        </selectKey>
    </insert>
```

测试

```java
@Test
    public void insertUser(){
        User user = new User();
        user.setUserName("liuhaha");
        user.setBirthday(new Date());
        user.setSex("男");
        user.setAddress("湖北");
        System.out.println("增加前的user"+user);
        userDao.insertUser(user);
        sqlSession.commit();
        System.out.println("增加后的user"+user);
    }
```

结果

将id信息封装进了user

```java
增加前的userUser{id=null, userName='liuhaha', birthday=Fri May 29 13:04:21 CST 2020, sex='男', address='湖北'}
增加后的userUser{id=13, userName='liuhaha', birthday=Fri May 29 13:04:21 CST 2020, sex='男', address='湖北'}
```





## 3、更新

IUserDao

```java
//更新数据
    void updateUser(User user);
```

UserDao.xml

```xml
<update id="updateUser">
        update user set birthday=#{birthday} where id=#{id}
    </update>
```

测试

```java
@Test
    public void updateUser(){
        User user = new User();
        user.setId(10);
        user.setBirthday(new Date(2019,1,1));
        userDao.updateUser(user);
        sqlSession.commit();
    }
```

## 4、删除

IUserDao

```java
//删除数据
    void deleteUser(Integer id);
```

UserDao.xml

```xml
<delete id="deleteUser">
        delete from user where id=#{id}
    </delete>
```

测试

```java
@Test
    public void deleteUser(){
        userDao.deleteUser(10);
        sqlSession.commit();
    }
```



## 5、类的属性名与表的列名不对应的情况

当标的列名为

```sql
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(30) DEFAULT NULL,
  `birthday` date DEFAULT NULL,
  `sex` varchar(5) DEFAULT NULL,
  `address` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=14 DEFAULT CHARSET=utf8;
```



将类的列名除了userName，其他都改了和表名不对应

User.java

```java
public class User {

    private Integer userId ;
    private String userName ;
    private Date userBirthday ;
    private String userSex ;
    private String userAddress ;

    public Integer getUserId() {
        return userId;
    }

    public void setUserId(Integer userId) {
        this.userId = userId;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public Date getUserBirthday() {
        return userBirthday;
    }

    public void setUserBirthday(Date userBirthday) {
        this.userBirthday = userBirthday;
    }

    public String getUserSex() {
        return userSex;
    }

    public void setUserSex(String userSex) {
        this.userSex = userSex;
    }

    public String getUserAddress() {
        return userAddress;
    }

    public void setUserAddress(String userAddress) {
        this.userAddress = userAddress;
    }

    @Override
    public String toString() {
        return "User{" +
                "userId=" + userId +
                ", userName='" + userName + '\'' +
                ", userBirthday=" + userBirthday +
                ", userSex='" + userSex + '\'' +
                ", userAddress='" + userAddress + '\'' +
                '}';
    }
}
```



1、查询结果

IUserDao.java

```java
//查找全部
    List<User> findAll();
```



UserDao.xml

```xml
<select id="findAll" resultType="com.bean.User">
        select * from USER
    </select>
```



测试

```java
@Test
    public void findAll(){
        List<User> all = userDao.findAll();
        for(User user:all){
            System.out.println(user);
        }
    }
```



结果

只有userName属性才注入了值

**为什么userName可以注入值呢，明明列名是username，属性名是userName，不一样？**

- 因为windows下的mysql不区分大小写，所以能把值注入进去，但是linux下的mysql区分大小写，就无法注入进去了

```java
User{userId=null, userName='zhangsan', userBirthday=null, userSex='null', userAddress='null'}
User{userId=null, userName='lisi', userBirthday=null, userSex='null', userAddress='null'}
User{userId=null, userName='王五', userBirthday=null, userSex='null', userAddress='null'}
User{userId=null, userName='赵柳', userBirthday=null, userSex='null', userAddress='null'}
```



在列名和属性名不一样的情况下我们可以有两种方式指定列名和属性名的对应情况



1、使用别名

UserDao.xml

```xml
<select id="findAll" resultType="com.bean.User">
        select id as userId,
               username as userName,
               birthday as userBirthday,
               sex as userSex,
               address as userAddress
        from user
    </select>
```

结果

数据都注入进了对象

```xml
User{userId=1, userName='zhangsan', userBirthday=Mon May 18 00:00:00 CST 2020, userSex='男', userAddress='123'}
User{userId=2, userName='lisi', userBirthday=Sun May 17 00:00:00 CST 2020, userSex='女', userAddress='456'}
User{userId=3, userName='王五', userBirthday=Sat May 16 00:00:00 CST 2020, userSex='男', userAddress='浙江'}
User{userId=4, userName='赵柳', userBirthday=Fri May 15 00:00:00 CST 2020, userSex='女', userAddress='江苏'}
```



2、使用mybatis提供的resultMap标签来执行属性

UserDao.xml

1、先使用resultMap标签创建好新的对象引用

2、在相应的sql语句标签设置新的返回类型(这时候的返回类型用resultMap，而不是resultType)

```xml
<!--
        resultMap标签指定列与属性的对应
            id  新对应关系的对象引用
            type  与表对应的类
            <id></id> 指定主键的对应
            <result></result>  指定一般列的对应
    -->
    <resultMap id="user" type="com.bean.User">
        <id column="id" property="userId" />
        <result column="username" property="userName" />
        <result column="sex" property="userSex" />
        <result column="birthday" property="userBirthday" />
        <result column="address" property="userAddress" />
    </resultMap>

    <select id="findAll" resultMap="user">
        select * from user
    </select>
```



结果

表数据完整的注入对象

```xml
User{userId=1, userName='zhangsan', userBirthday=Mon May 18 00:00:00 CST 2020, userSex='男', userAddress='123'}
User{userId=2, userName='lisi', userBirthday=Sun May 17 00:00:00 CST 2020, userSex='女', userAddress='456'}
User{userId=3, userName='王五', userBirthday=Sat May 16 00:00:00 CST 2020, userSex='男', userAddress='浙江'}
User{userId=4, userName='赵柳', userBirthday=Fri May 15 00:00:00 CST 2020, userSex='女', userAddress='江苏'}
```



# 四、mybatis传统的Dao层开发

所谓的传统的dao层开发是不使用sqlSession.getMapeer(IUserDao.class)方法获取IUserDao的代理对象，而是我们自己开发IUserDao的实现类的方式



1、编写IUserDao的实现类UserDaoImpl

UserDaoImpl

**sqlSession的statement参数是UserDao.xml中的namespace+id**

```java
package com.dao.impl;

import com.bean.User;
import com.dao.IUserDao;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;

import java.util.List;

/**
 * @ClassName: UserDaoImpl
 * @author: Administrator
 * @date: 2020/5/29 13:38
 * @Description
 */
public class UserDaoImpl implements IUserDao {

    private SqlSession sqlSession ;

    public UserDaoImpl(SqlSessionFactory sqlSessionFactory){
        sqlSession = sqlSessionFactory.openSession();

    }

    @Override
    public List<User> findAll() {
        List<User> users = sqlSession.selectList("com.dao.IUserDao.findAll");
        sqlSession.close();
        return users;
    }

    @Override
    public User findUserById(Integer id) {
        User user = sqlSession.selectOne("com.dao.IUserDao.findUserById", id);
        return user;
    }

    @Override
    public void insertUser(User user) {

    }

    @Override
    public void updateUser(User user) {

    }

    @Override
    public void deleteUser(Integer id) {

    }
}
```

测试类

```java
@Test
    public void findAll(){
        IUserDao userDao = new UserDaoImpl(sqlSessionFactory);
        List<User> all = userDao.findAll();
        for(User user:all){
            System.out.println(user);
        }
    }

    @Test
    public void findUserById(){
        IUserDao userDao = new UserDaoImpl(sqlSessionFactory);
        User user = userDao.findUserById(4);
        System.out.println(user);
    }
```

结果  正常显示

```xml
User{userId=4, userName='赵柳', userBirthday=Fri May 15 00:00:00 CST 2020, userSex='女', userAddress='江苏'}
```



# 五、mybatis主配置文件

```xml
-properties（属性）
	--property
-settings（全局配置参数）
	--setting
-typeAliases（类型别名）
	--typeAliase
	--package
-typeHandlers（类型处理器）
-objectFactory（对象工厂）
-plugins（插件）
-environments（环境集合属性对象）
	--environment（环境子属性对象）
		---transactionManager（事务管理）
		---dataSource（数据源）
-mappers（映射器）
	--mapper
	--package
```



## 1、properties

可以直接将属性写入properties标签中，也可以由properties指定配置文件位置从而将数据加载到配置文件



1、直接将属性写入properties标签

mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mybatis的主配置文件-->
<configuration>

    <properties>
        <property name="driver" value="com.mysql.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/test" />
        <property name="username" value="root" />
        <property name="password" value="root" />
    </properties>

    <!--配置环境-->
    <environments default="development">
        <!--配置mysql环境-->
        <environment id="development">
            <!--配置事务的类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源(连接池)-->
            <dataSource type="POOLED">
                <!--配置连接数据库的4个基本信息-->
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定映射配置文件位置，映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
        <mapper resource="com/dao/UserDao.xml" />
    </mappers>
</configuration>
```



2、由properties标签直接加载配置文件

```xml
<configuration>
	<!-- 配置连接数据库的信息
		resource 属性：用于指定 properties 配置文件的位置，要求配置文件必须在类路径下
			resource="jdbcConfig.properties"
		url 属性：
			URL： Uniform Resource Locator 统一资源定位符
				http://localhost:8080/mystroe/CategoryServlet URL
				协议 主机 端口 URI
		URI：Uniform Resource Identifier 统一资源标识符
			/mystroe/CategoryServlet
			它是可以在 web 应用中唯一定位一个资源的路径
-->
    <!--<properties resource="datasource.properties" />-->
	<properties url="file:///D:/idea_project/mybatisTest/src/main/resources/datasource.properties" />
    <!--配置环境-->
    <environments default="development">
        <!--配置mysql环境-->
        <environment id="development">
            <!--配置事务的类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源(连接池)-->
            <dataSource type="POOLED">
                <!--配置连接数据库的4个基本信息-->
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定映射配置文件位置，映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
        <mapper resource="com/dao/UserDao.xml" />
    </mappers>
</configuration>
```



## 2、typeAliases

1、typeAlias

​		给单个类定义别名

在没有配置别名时，resultType和parameterType只能是全限定类名

```xml
<select id="findAll" resultType="com.bean.User">
        select * from user
    </select>
```



配置别名之后

将实体类com.bean.User定义别名，这样在sql映射文件中的resultType和parameterType中就不需要写全限定类名，只需要写别名，不区分大小写

```xml
<typeAliases>
        <typeAlias alias="user" type="com.bean.User" />
    </typeAliases>
```



别名配置的是user，我的resultType写的是USER,还是可以映射，可以看出是不区分大小写的

```XML
<select id="findAll" resultType="USER">
        select * from user
    </select>
```



2、package

​			给指定包下所有的类定义别名，别名默认为类名称

```xml
<typeAliases>
        <package name="com.bean" />
    </typeAliases>
```



## 3、mappers

​		映射器：指定包含sql语句映射文件的位置



1、resource

```xml
<mappers>
        <mapper resource="com/dao/UserDao.xml" />
    </mappers>
```



2、class

使用 mapper 接口类路径，主要是使用注解形式指定dao接口

```xml
<mappers>
        <mapper class="com.dao.IUserDao"></mapper>
    </mappers>
注意：此种方法要求 mapper 接口名称和 mapper 映射文件名称相同，且放在同一个目录中。
```



3、package

注册指定包下的所有 mapper 接口

```xml
<mappers>
        <package name="com.dao" />
    </mappers>
注意：此种方法要求 mapper 接口名称和 mapper 映射文件名称相同，且放在同一个目录中。
```



# 六、mybatis连接池与事务

## 6.1 连接池

mybatis中的连接池分为三种：

- UNPOOLED   不使用连接池的数据源
- POOLED   使用连接池的数据源
- JNDI    使用JNDI实现的数据源

相应的mybatis内部分别定义了实现java.sql.DataSource接口UnpooledDataSource,PooledDataSource来表示UNPOOLED和POOLED类的数据源



PooledDataSource接口持有一个UnpooledDataSource的引用，当PooledDataSource需要创建java.sql.Connection实例时，还是通过UnpooledDataSource来创建，PooledDataSource只是提供一种缓存连接池机制



一般我们使用的都是PooledDataSource数据源

```xml
<dataSource type="POOLED">
                <!--配置连接数据库的4个基本信息-->
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
MyBatis 在初始化时，根据<dataSource>的 type 属性来创建相应类型的的数据源 DataSource，即：
type=”POOLED”：MyBatis 会创建 PooledDataSource 实例
type=”UNPOOLED” ： MyBatis 会创建 UnpooledDataSource 实例
type=”JNDI”：MyBatis 会从 JNDI 服务上查找 DataSource 实例，然后返回使用
```

源码分析POOLED

PooledDataSource.java

![](D:\notes\notes\typora\mybatis\images\PooledDataSource.png)

UnpooledDataSource.java

![](D:\notes\notes\typora\mybatis\images\UnpooledDataSource.png)

## 6.2 事务

在之前我们在进行增删改的时候，提交事务之后才能生效

```java
@Test
    public void updateUser(){
        User user = new User();
        user.setId(10);
        user.setBirthday(new Date(2019,1,1));
        userDao.updateUser(user);
        sqlSession.commit();
    }
```



可以看到jdbc的autocommit设置为false

![image-20200531161808140](D:\notes\notes\typora\mybatis\images\image-20200531161808140.png)



我们可以设置自动提交事务

从SqlSessionFactory中有设置自动提交的方法

​		SqlSession openSession(boolean autoCommit);

```java
public interface SqlSessionFactory {

  SqlSession openSession();

  SqlSession openSession(boolean autoCommit);
  SqlSession openSession(Connection connection);
  SqlSession openSession(TransactionIsolationLevel level);

  SqlSession openSession(ExecutorType execType);
  SqlSession openSession(ExecutorType execType, boolean autoCommit);
  SqlSession openSession(ExecutorType execType, TransactionIsolationLevel level);
  SqlSession openSession(ExecutorType execType, Connection connection);

  Configuration getConfiguration();

}
```



我们只需要在获取SqlSession时，设置为true

```java
@Before
    public void init() throws Exception{
        in = Resources.getResourceAsStream("mybatis-config.xml");
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(in);
        sqlSession = sqlSessionFactory.openSession(true);
    }
```



# 七、动态SQL语句

- if
- where
- foreach



## 7.1 if

有时我们需要根据参数动态的获取数据，有条件就根据条件查询，没条件就不用了条件查询



我们写个案例：username属性有值，就根据username查询用户，否则就查询所有用户

IUserDao.java

```java
/**
     * 动态根据条件查询
     */
    List<User> findUser(User user);
```

UserDao.xml

```xml
<select id="findUser" resultType="user">
        select * from user where 1=1
        <if test="username != null">
            and username=#{username}
        </if>
    </select>
```

测试

```java
@Test
    public void findUser(){
        IUserDao userDao = sqlSession.getMapper(IUserDao.class);
        User u = new User();
        u.setUsername("lisi");
        List<User> users = userDao.findUser(u);
        for(User user:users){
            System.out.println(user);
        }
    }
```



## 7.2 where

在下面的if标签中，都需要写出where1=1的语句，否则无法判断出if下面的条件是否得加and

```xml
<select id="findUser" resultType="user">
        select * from user where 1=1
        <if test="username != null">
            and username=#{username}
        </if>
    </select>
```



where标签可以解决这个问题，他可以根据条件来判断是否增加and,他与上面的是等价的

```xml
<select id="findUser" resultType="user">
        select * from user
        <where>
            <if test="username != null">
                and username=#{username}
            </if>
        </where>
    </select>
```



## 7.3 foreach

当我们的参数为一个集合时，就得使用foreach标签，比如：

```sql
SELECT * FROM USERS WHERE username LIKE '%张%' AND id IN (10,89,16)
```



IUserDao.java

```java
/**
     * foreach标签的用法
     */
    List<User> findUserUserForeach(QueryVO queryVO);
```



QueryVO

```java
package com.bean;

import java.util.List;

/**
 * @ClassName: QueryVO
 * @author: Administrator
 * @date: 2020/5/30 23:44
 * @Description
 */
public class QueryVO {

    private List<Integer> ids;

    public List<Integer> getIds() {
        return ids;
    }

    public void setIds(List<Integer> ids) {
        this.ids = ids;
    }

    @Override
    public String toString() {
        return "QueryVO{" +
                "ids=" + ids +
                '}';
    }
}
```

UserDao.xml

```xml
<select id="findUserUserForeach" resultType="user">
        select * from user
        <where>
          <if test="ids != null and ids.size() > 0">
            <foreach collection="ids" open="id in (" item="id" separator="," close=")">
                #{id}
            </foreach>
          </if>
        </where>
    </select>
```

测试

```java
@Test
    public void findUserUserForeach(){
        IUserDao userDao = sqlSession.getMapper(IUserDao.class);
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(3);
        QueryVO queryVO = new QueryVO();
        queryVO.setIds(list);
        List<User> users = userDao.findUserUserForeach(queryVO);
        for(User user:users){
            System.out.println(user);
        }
    }
```



## 7.4 简化编写sql片段

对于常常出现的sql片段，我们可以把它用sql标签定义以下，然后复用



对于下面的文件来说select * from user都在用，我们可以把这个sql定义以下加以复用

```xml
<select id="findUser" resultType="user">
        select * from user
        <where>
            <if test="username != null">
                and username=#{username}
            </if>
        </where>
    </select>

    <select id="findUserUserForeach" resultType="user">
        select * from user
        <where>
          <if test="ids != null and ids.size() > 0">
            <foreach collection="ids" open="id in (" item="id" separator="," close=")">
                #{id}
            </foreach>
          </if>
        </where>
    </select>
```



我们用sql标签把重复的sql标出，然后通过include调用

```xml
<sql id="all">
        select * from user
    </sql>

    <select id="findUser" resultType="user">
        <include refid="all" />
        <where>
            <if test="username != null">
                and username=#{username}
            </if>
        </where>
    </select>

    <select id="findUserUserForeach" resultType="user">
        <include refid="all" />
        <where>
          <if test="ids != null and ids.size() > 0">
            <foreach collection="ids" open="id in (" item="id" separator="," close=")">
                #{id}
            </foreach>
          </if>
        </where>
    </select>
```



# 八、mysql多表查询

- 一对多
- 多对多

表之间的关系有几种：
		一对多
		多对一
		一对一
		多对多



	举例：
		用户和订单就是一对多
		订单和用户就是多对一
			一个用户可以下多个订单
			多个订单属于同一个用户
		人和身份证号就是一对一
			一个人只能有一个身份证号
			一个身份证号只能属于一个人


## 8.1 一对一

### 8.1.1 方式一

我们已经有了用户表和类，所以我们得定义一个账户表和类

建表sql

```sql
CREATE TABLE `account` (
  `ID` int(11) NOT NULL COMMENT '编号',
  `UID` int(11) default NULL COMMENT '用户编号',
  `MONEY` double default NULL COMMENT '金额',
  PRIMARY KEY  (`ID`),
  KEY `FK_Reference_8` (`UID`),
  CONSTRAINT `FK_Reference_8` FOREIGN KEY (`UID`) REFERENCES `user` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;



insert  into `account`(`ID`,`UID`,`MONEY`) values (1,1,1000),(2,3,1000),(3,4,2000);
```



创建Account类

```java
package com.bean;

/**
 * @ClassName: Account
 * @author: Administrator
 * @date: 2020/5/31 17:06
 * @Description
 */
public class Account {

    private Integer id;
    private Integer uid;
    private Double Money;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public Integer getUid() {
        return uid;
    }

    public void setUid(Integer uid) {
        this.uid = uid;
    }

    public Double getMoney() {
        return Money;
    }

    public void setMoney(Double money) {
        Money = money;
    }

    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", uid=" + uid +
                ", Money=" + Money +
                '}';
    }
}
```



我们要查询的sql为,这是一对一查询，一个账户对应一个用户

```sql
SELECT 
 account.*,
 user.username,
 user.address
FROM
 account,
 user
WHERE account.uid = user.id
```

查询的结果为

![image-20200531171113200](D:\notes\notes\typora\mybatis\images\image-20200531171113200.png)

但是我们没有类可以装下这些属性，所以我们可以创建一个AccountUser类，他继承Account类

AccountUser.java

```java
package com.bean;

import java.io.Serializable;

/**
 * @ClassName: AccountUser
 * @author: Administrator
 * @date: 2020/5/31 17:12
 * @Description
 */
public class AccountUser extends Account implements Serializable {

    private String username ;
    private String address ;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return super.toString() +"AccountUser{" +
                "username='" + username + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}
```



IAccountDao.java

```java
public interface IAccountDao {

    /**
     *  查询所有账户，同时获取账户的所属用户名称以及它的地址信息
     * @return
     */
    List<AccountUser> findAll();
}
```



AccountDao.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.dao.IAccountDao">
    <select id="findAll" resultType="AccountUser">
        select a.*,u.username,u.address from account a,user u where a.uid =u.id;
    </select>
</mapper>
```



测试

```java
@Test
    public void findAll(){
        IAccountDao mapper = sqlSession.getMapper(IAccountDao.class);
        List<AccountUser> all = mapper.findAll();
        for(AccountUser accountUser:all){
            System.out.println(accountUser);
        }
    }
```





### 8.1.2 方式二

我们修改Account类，因为一个Account对应一个User，所以我们加入User属性

```java
package com.bean;

/**
 * @ClassName: Account
 * @author: Administrator
 * @date: 2020/5/31 17:06
 * @Description
 */
public class Account {

    private Integer id;
    private Integer uid;
    private Double Money;
    private User user ;

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public Integer getUid() {
        return uid;
    }

    public void setUid(Integer uid) {
        this.uid = uid;
    }

    public Double getMoney() {
        return Money;
    }

    public void setMoney(Double money) {
        Money = money;
    }

    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", uid=" + uid +
                ", Money=" + Money +
                ", user=" + user +
                '}';
    }
}
```



修改IAccountDao接口的方法,接收参数变为Account

```java
List<Account> findAll();
```



重新定义AccountDao.xml

```xml
<resultMap id="accountMap" type="account">
        <id property="id" column="aid" />
        <result property="uid" column="uid" />
        <result property="money" column="money" />
        <!-- 它是用于指定从表方的引用实体属性的 -->
        <association property="user" javaType="user">
            <id property="id" column="id" />
            <result property="username" column="username" />
            <result column="sex" property="sex"/>
            <result column="birthday" property="birthday"/>
            <result column="address" property="address"/>
        </association>
    </resultMap>
    
    <select id="findAll" resultMap="accountMap">
        select a.*,u.username,u.address from account a,user u where a.uid =u.id;
    </select>
```

测试

```java
@Test
    public void findAll(){
        IAccountDao mapper = sqlSession.getMapper(IAccountDao.class);
        List<Account> all = mapper.findAll();
        for(Account accountUser:all){
            System.out.println(accountUser);
        }
    }
```



结果

```xml
Account{id=null, uid=1, Money=1000.0, user=User{id=1, username='zhangsan', birthday=null, sex='null', address='123'}}
Account{id=null, uid=3, Money=1000.0, user=User{id=2, username='王五', birthday=null, sex='null', address='浙江'}}
Account{id=null, uid=4, Money=2000.0, user=User{id=3, username='赵柳', birthday=null, sex='null', address='江苏'}}
```



## 8.2 一对多

```java
需求：
	查询所有用户信息及用户关联的账户信息。
分析：
	用户信息和他的账户信息为一对多关系，并且查询过程中如果用户没有账户信息，此时也要将用户信息
查询出来，我们想到了左外连接查询比较合适。
```



sql语句

```sql
SELECT
u.*, acc.id id,
acc.uid,
 acc.money
FROM
user u
LEFT JOIN account acc ON u.id = acc.uid
```

查询结果

![image-20200531174824398](D:\notes\notes\typora\mybatis\images\image-20200531174824398.png)



1、更改User，增加List<Account> 属性

```java
package com.bean;

import java.util.Date;
import java.util.List;

/**
 * @ClassName: User
 * @author: Administrator
 * @date: 2020/5/28 13:10
 * @Description
 */
public class User {

    private Integer id ;
    private String username ;
    private Date birthday ;
    private String sex ;
    private String address ;
    private List<Account> accounts;

    public List<Account> getAccounts() {
        return accounts;
    }

    public void setAccounts(List<Account> accounts) {
        this.accounts = accounts;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", birthday=" + birthday +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                ", accounts=" + accounts +
                '}';
    }
}
```



更改UserDao.xml

**collection**

​	部分定义了用户关联的账户信息。表示关联查询结果集

property="accounts"

​	关联查询的结果集存储在 User 对象的上哪个属性。

ofType="account"

​	指定关联查询的结果集中的对象类型即List中的对象类型。此处可以使用别名，也可以使用全限定名。

```xml
<resultMap id="userMap" type="user">
        <id property="id" column="id" />
        <result property="username" column="username" />
        <result property="birthday" column="birthday" />
        <result property="sex" column="sex" />
        <result property="address" column="address" />
        <collection property="accounts" ofType="account">
            <id property="id" column="aid" />
            <result property="uid" column="uid" />
            <result property="money" column="money" />
        </collection>
    </resultMap>

    <select id="findAll" resultMap="userMap">
        select u.*,a.id as aid ,a.uid,a.money from user u left outer join account
          a on u.id =a.uid
    </select>
```



## 8.3 多对多

多对多的关系需要通过中间表来关联

这里我们通过用户和角色来测试多对多的关系



用户与角色多对多模型

![image-20200531182003278](D:\notes\notes\typora\mybatis\images\image-20200531182003278.png)



创表sql

```sql
DROP TABLE IF EXISTS `role`;

CREATE TABLE `role` (
  `ID` int(11) NOT NULL COMMENT '编号',
  `ROLE_NAME` varchar(30) default NULL COMMENT '角色名称',
  `ROLE_DESC` varchar(60) default NULL COMMENT '角色描述',
  PRIMARY KEY  (`ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;



insert  into `role`(`ID`,`ROLE_NAME`,`ROLE_DESC`) values (1,'院长','管理整个学院'),(2,'总裁','管理整个公司'),(3,'校长','管理整个学校');





DROP TABLE IF EXISTS `user_role`;

CREATE TABLE `user_role` (
  `UID` int(11) NOT NULL COMMENT '用户编号',
  `RID` int(11) NOT NULL COMMENT '角色编号',
  PRIMARY KEY  (`UID`,`RID`),
  KEY `FK_Reference_10` (`RID`),
  CONSTRAINT `FK_Reference_10` FOREIGN KEY (`RID`) REFERENCES `role` (`ID`),
  CONSTRAINT `FK_Reference_9` FOREIGN KEY (`UID`) REFERENCES `user` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

insert  into `user_role`(`UID`,`RID`) values (1,1),(3,1),(1,2);
```



业务sql要求及实现

```sql
需求：
实现查询所有对象并且加载它所分配的用户信息。
分析：
查询角色我们需要用到Role表，但角色分配的用户的信息我们并不能直接找到用户信息，而是要通过中
间表(USER_ROLE 表)才能关联到用户信息。
下面是实现的 SQL 语句：
SELECT
 r.*,u.id uid,
 u.username username,
 u.birthday birthday,
 u.sex sex,
 u.address address
FROM 
 ROLE r
INNER JOIN 
 USER_ROLE ur
ON ( r.id = ur.rid)
INNER JOIN
 USER u
ON (ur.uid = u.id);
```



创建角色实体类

```java
package com.bean;

import java.io.Serializable;
import java.util.List;

/**
 * @ClassName: Role
 * @author: Administrator
 * @date: 2020/5/31 18:24
 * @Description
 */
public class Role implements Serializable {

    private Integer id ;
    private String roleName ;
    private String roleDesc ;
    
    //一个角色对应多个用户
    private List<User> users ;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getRoleName() {
        return roleName;
    }

    public void setRoleName(String roleName) {
        this.roleName = roleName;
    }

    public String getRoleDesc() {
        return roleDesc;
    }

    public void setRoleDesc(String roleDesc) {
        this.roleDesc = roleDesc;
    }

    public List<User> getUsers() {
        return users;
    }

    public void setUsers(List<User> users) {
        this.users = users;
    }

    @Override
    public String toString() {
        return "Role{" +
                "id=" + id +
                ", roleName='" + roleName + '\'' +
                ", roleDesc='" + roleDesc + '\'' +
                ", users=" + users +
                '}';
    }
}
```



创建role的dao接口

```java
package com.dao;/**
 * @ClassName: IUserDao
 * @author: Administrator
 * @date: 2020/5/28 13:08
 * @Description
 */

import com.bean.Role;

import java.util.List;

/**
 * @author Administrator
 * @time 2020/5/28
 *
 */
public interface IRoleDao {


    //查找全部
    List<Role> findAll();

}
```



创建role的映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.dao.IRoleDao">

    <resultMap id="roleMap" type="role">
        <id property="id" column="ID" />
        <result property="roleName" column="ROLE_NAME" />
        <result property="roleDesc" column="ROLE_DESC" />
        <collection property="users" ofType="user">
            <id property="id" column="uid" />
            <result property="username" column="username" />
            <result property="birthday" column="birthday" />
            <result property="sex" column="sex" />
            <result property="address" column="address" />
        </collection>
    </resultMap>

    <select id="findAll" resultMap="roleMap">
        SELECT
 r.*,u.id uid,
 u.username username,
 u.birthday birthday,
 u.sex sex,
 u.address address
FROM
 ROLE r
INNER JOIN
 USER_ROLE ur
ON ( r.id = ur.rid)
INNER JOIN
 USER u
ON (ur.uid = u.id);
    </select>

</mapper>
```

测试

```java
@Test
    public void findAll(){
        IRoleDao mapper = sqlSession.getMapper(IRoleDao.class);
        List<Role> all = mapper.findAll();
        for(Role accountUser:all){
            System.out.println(accountUser);
        }
    }
```



结果

```xml
Role{id=1, roleName='院长', roleDesc='管理整个学院', users=[User{id=1, username='zhangsan', birthday=Sat Feb 01 00:00:00 CST 3919, sex='男', address='123', accounts=null}, User{id=3, username='王五', birthday=Sat May 16 00:00:00 CST 2020, sex='男', address='浙江', accounts=null}]}
Role{id=2, roleName='总裁', roleDesc='管理整个公司', users=[User{id=1, username='zhangsan', birthday=Sat Feb 01 00:00:00 CST 3919, sex='男', address='123', accounts=null}]}
```



# 九、mybatis的延迟加载

延迟加载：

- 在真正使用数据时才发起查询，不用的时候不查询。按需加载（懒加载）

立即加载：

- 不管用不用，只要一调用方法，马上发起查询



什么情况下用延迟加载，什么情况用立即加载

在对应的四种表关系中：一对多，多对一，一对一，多对多

- 一对多，多对多：通常情况下我们都是采用延迟加载。
- 多对一，一对一：通常情况下我们都是采用立即加载。

```xml
需求：
	查询账户(Account)信息并且关联查询用户(User)信息。如果先查询账户(Account)信息即可满足要
求，当我们需要查询用户(User)信息时再查询用户(User)信息。把对用户(User)信息的按需去查询就是延迟加
载。
mybatis第三天实现多表操作时，我们使用了resultMap来实现一对一，一对多，多对多关系的操作。主要
是通过 association、collection 实现一对一及一对多映射。association、collection 具备延迟加载功
能。
```

## 9.1、association的延迟加载

AccountDao.xml

**select**：填写我们要调用的 select 映射的 id 

**column** **：** 填写我们要传递给 select 映射的参数

```xml
<resultMap id="accountMap" type="account">
        <id property="id" column="id" />
        <result property="uid" column="uid" />
        <result property="money" column="money" />
        <!-- 它是用于指定从表方的引用实体属性的 -->
        <association property="user" javaType="user" select="com.dao.IUserDao.findUserById" column="uid">

        </association>
    </resultMap>
    
    <select id="findAll" resultMap="accountMap">
        select * from account
    </select>
```

UserDao.xml提供相应的方法

```xml
<select id="findUserById"  resultMap="userMap">
        select * from user where id=#{id}
    </select>
```

在mybatis主配置文件配置延迟加载策略

```xml
 <settings>
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="aggressiveLazyLoading" value="true"/>
    </settings>
```

测试

不涉及到User，就不会加载User

```java
@Test
    public void findAll(){
        IAccountDao mapper = sqlSession.getMapper(IAccountDao.class);
        List<Account> all = mapper.findAll();
    }
```

结果

只执行了一个sql语句

![image-20200531230411355](D:\notes\notes\typora\mybatis\images\image-20200531230411355.png)



```java
@Test
    public void findAll(){
        IAccountDao mapper = sqlSession.getMapper(IAccountDao.class);
        List<Account> all = mapper.findAll();
        for(Account accountUser:all){
            System.out.println(accountUser);
        }
    }
```

结果

当使用了User才会打印出来

![image-20200531230555679](D:\notes\notes\typora\mybatis\images\image-20200531230555679.png)





## 9.2 Collection的延迟加载

根据User信息查询Role信息

RoleDao.xml

```xml
 <select id="getRoleByUid" resultMap="roleMap">
        select * from role where id=#{uid}
    </select>
```

UserDao.xml

```xml
<resultMap id="userMap" type="user">
        <id property="id" column="id" />
        <result property="username" column="username" />
        <result property="birthday" column="birthday" />
        <result property="sex" column="sex" />
        <result property="address" column="address" />
        <collection property="accounts" ofType="account" select="com.dao.IRoleDao.getRoleByUid" column="id">
        </collection>
    </resultMap>

    <select id="findAll" resultMap="userMap">
        select * from user
    </select>
```

测试

```java
@Test
    public void findAll(){
        IUserDao mapper = sqlSession.getMapper(IUserDao.class);
        List<User> all = mapper.findAll();
        /*for(User accountUser:all){
            System.out.println(accountUser);
        }*/
    }
```



# 十、mybatis的缓存

```xml
Mybatis中的一级缓存和二级缓存
		一级缓存：
			它指的是Mybatis中SqlSession对象的缓存。
			当我们执行查询之后，查询的结果会同时存入到SqlSession为我们提供一块区域中。
			该区域的结构是一个Map。当我们再次查询同样的数据，mybatis会先去sqlsession中
			查询是否有，有的话直接拿出来用。
			当SqlSession对象消失时，mybatis的一级缓存也就消失了。
		
		二级缓存:
			它指的是Mybatis中SqlSessionFactory对象的缓存。由同一个SqlSessionFactory对象创建的SqlSession共享其缓存。
			二级缓存的使用步骤：
				第一步：让Mybatis框架支持二级缓存（在SqlMapConfig.xml中配置）
				第二步：让当前的映射文件支持二级缓存（在IUserDao.xml中配置）
				第三步：让当前的操作支持二级缓存（在select标签中配置）
```



```xml
Mybatis中的缓存
	什么是缓存
		存在于内存中的临时数据。
	为什么使用缓存
		减少和数据库的交互次数，提高执行效率。
	什么样的数据能使用缓存，什么样的数据不能使用
		适用于缓存：
			经常查询并且不经常改变的。
			数据的正确与否对最终结果影响不大的。
		不适用于缓存：
			经常改变的数据
			数据的正确与否对最终结果影响很大的。
			例如：商品的库存，银行的汇率，股市的牌价。
```





![image-20200601123651125](D:\notes\notes\typora\mybatis\images\image-20200601123651125.png)



## 10.1 一级缓存

一级缓存是SqlSession级别的，只要SqlSession没有flush或者close，它就存在



UserDao.xml

```xml
<select id="findUserById"  resultMap="userMap">
        select * from user where id=#{id}
    </select>
```

测试

```java
@Test
    public void findAll(){
        IUserDao mapper = sqlSession.getMapper(IUserDao.class);

        System.out.println(mapper.findUserById(1));

        System.out.println("--------------");

        System.out.println(mapper.findUserById(1));
    }
```

结果

只执行了一次sql，说明第二次的数据是从缓存中获取的

![image-20200601124523782](D:\notes\notes\typora\mybatis\images\image-20200601124523782.png)



如果数据库数据被修改了，mybatis怎么确保从缓存中获取的数据是实时的数据，而不是修改前的数据？

- 只要数据库的数据更新了(进行了增删改操作),mybatis的一级缓存就会刷新



测试

```java
@Test
    public void findAll(){
        IUserDao mapper = sqlSession.getMapper(IUserDao.class);

        System.out.println(mapper.findUserById(1));

        System.out.println("--------------");
        User user = new User();
        user.setId(1);
        user.setUsername("liuhaha");
        mapper.updateUser(user);

        System.out.println(mapper.findUserById(1));
    }
```



结果

可以看到，在更新数据库之后，下次操作还会从数据库中查询，而不会直接从缓存中获取

![image-20200601125156181](D:\notes\notes\typora\mybatis\images\image-20200601125156181.png)



**一级缓存的分析**

```xml
一级缓存是 SqlSession 范围的缓存，当调用 SqlSession 的修改，添加，删除，commit()，close()等方法时，一级缓存就会清空
```

![image-20200601125617391](D:\notes\notes\typora\mybatis\images\image-20200601125617391.png)



## 10.2 二级缓存

![image-20200601125750439](D:\notes\notes\typora\mybatis\images\image-20200601125750439.png)

```xml
首先开启 mybatis 的二级缓存。

sqlSession1 去查询用户信息，查询到用户信息会将查询数据存储到二级缓存中。

如果 SqlSession3 去执行相同 mapper 映射下 sql，执行 commit 提交，将会清空该 mapper 映射下的二
级缓存区域的数据。

sqlSession2 去查询与 sqlSession1 相同的用户信息，首先会去缓存中找是否存在数据，如果存在直接从
缓存中取出数据。
```



### 10.2.1 开启mybatis二级缓存

1、在主配置文件中配置

其实也可以不用配置(默认为true)

```xml
<settings>
       <setting name="cacheEnabled" value="true" />
    </settings>
```



2、在相应的映射文件配置

UserDao.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.dao.IUserDao">

    <!--开启二级缓存支持-->
    <cache />
```



3、在相应的statement设置useCache属性

```xml
<select id="findUserById"  resultMap="userMap" useCache="true">
        select * from user where id=#{id}
    </select>
```



测试

```java
@Test
    public void findAll(){
        SqlSession sqlSession1 = sqlSessionFactory.openSession();
        IUserDao mapper = sqlSession1.getMapper(IUserDao.class);
        mapper.findUserById(1);
        sqlSession1.close(); //关闭一级缓存

        System.out.println("-----------------------");

        SqlSession sqlSession2 = sqlSessionFactory.openSession();
        IUserDao mapper1 = sqlSession2.getMapper(IUserDao.class);
        mapper1.findUserById(1);
    }
```



结果

在一级缓存关闭之后，第二次查询还是从缓存中提取的，说明二级缓存以打开

![image-20200601131001305](D:\notes\notes\typora\mybatis\images\image-20200601131001305.png)



二级缓存使用注意事项

**使用的实体类必须实现序列化接口**

```java
public class User implements Serializable {
```



# 十一、mybatis注解开发

mybatis常用注解说明

```xml
@Insert:实现新增
@Update:实现更新
@Delete:实现删除
@Select:实现查询
@Result:实现结果集封装
@Results:可以与@Result 一起使用，封装多个结果集
@ResultMap:实现引用@Results 定义的封装
@One:实现一对一结果集封装
@Many:实现一对多结果集封装
@SelectProvider: 实现动态 SQL 映射
@CacheNamespace:实现注解二级缓存的使用
```



## 11.1、注解版简单的增删改查

IUserDao.java

```java
public interface IUserDao {


    //查找全部
    @Select({"select * from user"})
    @Results(id="userMap",value={
            @Result(id=true,property = "userId",column="id"),
            @Result(property = "username",column="username"),
            @Result(property = "userBirthday",column="birthday"),
            @Result(property = "userSex",column="sex"),
            @Result(property = "userAddress",column="address")
    })
    List<User> findAll();

    //根据id查询数据
    @Select({"select * from user where id=#{userId}"})
    @ResultMap({"userMap"})
    User findUserById(Integer id);

    //插入数据
    @Insert({"insert into user(username,birthday,sex,address) values(" +
            "#{username},#{userBirthday},#{userSex},#{userAddress})"})
    @SelectKey(keyColumn = "id" , keyProperty = "userId" , statement={"select last_insert_id()"} , before = false,resultType = Integer.class)
    void insertUser(User user);

    //更新数据
    @Update({"update user set username=#{username} where id=#{userId}"})
    void updateUser(User user);

    //删除数据
    @Delete({"delete from user where id=#{userId}"})
    void deleteUser(Integer id);
    
    /**
     * 根据用户名模糊查询
     * @param userName
     * @return
     */
     @Select("select * from user where username like #{username} ")
     List<User> getUserByName(String userName);
    
}
```

2、更改mybatis主配置文件

注册当前包下的所有dao接口

```xml
<mappers>
       <package name="com.dao" />
    </mappers>
```



## 11.2 复杂关系映射

复杂关系映射的注解说明

```xml
@Results 注解
代替的是标签<resultMap>
该注解中可以使用单个@Result 注解，也可以使用@Result 集合
@Results（{@Result（），@Result（）}）或@Results（@Result（））

@Resutl 注解
代替了 <id>标签和<result>标签
@Result 中 属性介绍：
	id 是否是主键字段
	column 数据库的列名
	property 需要装配的属性名
	one 需要使用的@One 注解（@Result（one=@One）（）））
	many 需要使用的@Many 注解（@Result（many=@many）（）））

@One 注解（一对一）
	代替了<assocation>标签，是多表查询的关键，在注解中用来指定子查询返回单一对象。
@One 注解属性介绍：
	select 指定用来多表查询的 sqlmapper
	fetchType 会覆盖全局的配置参数 lazyLoadingEnabled。。
使用格式：
	@Result(column=" ",property="",one=@One(select=""))

@Many 注解（多对一）
	代替了<Collection>标签,是是多表查询的关键，在注解中用来指定子查询返回对象集合。
注意：聚集元素用来处理“一对多”的关系。需要指定映射的 Java 实体类的属性，属性的 javaType
（一般为 ArrayList）但是注解中可以不定义；
使用格式：
	@Result(property="",column="",many=@Many(select=""))
```



### 11.2.1 使用注解实现一对一复杂关系映射及延迟加载

需求

```xml
需求：
	加载账户信息时并且加载该账户的用户信息，根据情况可实现延迟加载。（注解方式实现）
```

IUserDao.java

```java
//查找全部
    @Select({"select * from user"})
    @Results(id="userMap",value={
            @Result(id=true,property = "userId",column="id"),
            @Result(property = "username",column="username"),
            @Result(property = "userBirthday",column="birthday"),
            @Result(property = "userSex",column="sex"),
            @Result(property = "userAddress",column="address")
    })
    List<User> findAll();

    //根据id查询数据
    @Select({"select * from user where id=#{userId}"})
    @ResultMap({"userMap"})
    User findUserById(Integer id);
```

IAccountDao.java

```java
@Select("select * from account")
    @Results(id="accountMap",value = {
            @Result(id=true , property = "id" , column = "id"),
            @Result(property = "uid" , column = "uid"),
            @Result(property = "money" , column = "money"),
            @Result(property = "user" , column = "uid" ,one=@One(
                    select = "com.dao.IUserDao.findUserById", fetchType = FetchType.LAZY
            ))
    })
    List<Account> findAll();
```

测试

```java
@Test
    public void findAll(){
        IAccountDao mapper = sqlSession.getMapper(IAccountDao.class);
        List<Account> all = mapper.findAll();
        /*for(Account user :all){
            System.out.println(user);
        }*/
    }
```



### 11.2.2 使用注解实现一对多复杂关系映射及延迟加载

需求及分析

```xml
需求：
	查询用户信息时，也要查询他的账户列表。使用注解方式实现。
分析：
    一个用户具有多个账户信息，所以形成了用户(User)与账户(Account)之间的一对多关系
```

IAccountDao.java

```java
@Select("select * from account")
    @Results(id="accountMap",value = {
            @Result(id=true , property = "id" , column = "id"),
            @Result(property = "uid" , column = "uid"),
            @Result(property = "money" , column = "money"),
    })
    List<Account> findAll();


    @Select({"select * from account where uid=#{uid}"})
    @ResultMap("accountMap")
    List<Account> getAccountByUid(Integer uid);
```

IUserDao.java

```java
@Select({"select * from user"})
    @Results(id="userMap",value={
            @Result(id=true,property = "userId",column="id"),
            @Result(property = "username",column="username"),
            @Result(property = "userBirthday",column="birthday"),
            @Result(property = "userSex",column="sex"),
            @Result(property = "userAddress",column="address"),
            @Result(property = "accounts",column="id" , many = @Many(
                    select = "com.dao.IAccountDao.getAccountByUid" ,
                    fetchType = FetchType.LAZY
            ))
    })
    List<User> findAll();
```

测试

```java
@Test
    public void findAll(){
        IUserDao mapper = sqlSession.getMapper(IUserDao.class);
        List<User> all = mapper.findAll();
        /*for(User user :all){
            System.out.println(user);
        }*/
    }
```



### 11.2.3 mybatis基于注解的二级缓存

1、在mybatis主配置文件配置

```xml
<!-- 配置二级缓存 --> 
<settings>
	<!-- 开启二级缓存的支持 --> 
    <setting name="cacheEnabled" value="true"/>
</settings>
```

2、在持久层dao配置

```java
@CacheNamespace(blocking=true)//mybatis 基于注解方式实现配置二级缓存
public interface IUserDao {}
```

