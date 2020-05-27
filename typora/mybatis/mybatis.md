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
?xml version="1.0" encoding="UTF-8" ?>
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



