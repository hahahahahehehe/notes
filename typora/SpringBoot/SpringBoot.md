# 一、SpringBoot入门

## **1、微服务**

 微服务:架构风格(服务微化)

- ​    一个应用应该是一组小型服务:可以通过http的方式相互通信

- ​    单体应用: ALL IN ONE

- ​    微服务:每个功能元素最终都是一个可独立替换和独立升级的软件单元

## 2、spring boot HelloWorld

- 创建一个maven工程(jar)
- 导入依赖

```java
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.9.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>


<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

- 编写主程序，启动spring boot应用(<span style="color:red;">主程序所在包必须时最外层,如主程序在com.demo包下，则controller和service必须在com.demo.controller和com.demo.service下,而不能在com.demo下</span>）

```java
package com.demo;


import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;


/**
* @SpringBootApplication 来标注一个主程序类,说明这是一个spring boot应用
*/
@SpringBootApplication
public class HelloWorldApplication {
    public static void main(String[] args) {


        //spring应用启动起来
        SpringApplication.run(HelloWorldApplication.class,args);
    }
}
```

- 编写相关conroller和service

```java
package com.demo.controller;


import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;


@Controller
public class HelloController {


    @RequestMapping("/hello")
    @ResponseBody
    public String hello(){
        return "hello world!";
    }
}
```

- 运行主程序
- 简化部署,将这个应用打成可执行jar，直接用java -jar命令执行

```java
<build>
    <plugins>


        <!--将应用打包成一个可执行jar-->
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

![](imgs\Image.png)

## 3、springboot原理探究

### 1、pom文件

- 父项目

```java
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.9.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

他的父项目是
<parent>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-dependencies</artifactId>
   <version>1.5.9.RELEASE</version>
   <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
他来管理spring boot 应用里面的所有依赖版本

spring boot的版本仲裁中心:
以后我们导入的依赖默认是不需要写版本(没有在dependencies里面的依赖还是要声明版本号的)
```

- 启动器

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

### 2、主程序类,主入口类

```java
/**
* @SpringBootApplication 来标注一个主程序类,说明这是一个spring boot应用
*/
@SpringBootApplication
public class HelloWorldApplication {
    public static void main(String[] args) {


        //spring应用启动起来
        SpringApplication.run(HelloWorldApplication.class,args);
    }
}
```

@SpringBootApplication ：springboot应用标注在某个类上说明这个类是springboot的主配置类,SpringBoot就应该运行这个类的main方法来启动SpringBoot应用

**@SpringBootApplication**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

@**SpringBootConfiguration**:SpringBoot的配置类，标注在某个类上，表示这是一个SpringBoot的配置类；

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {

}
```

@**Configuration**:配置类上来标注这个注解；配置类就是配置文件，只是以类的形式来注入使用，配置类也是容器的一个组件@Component

**@EnableAutoConfiguration**:开启自动配置功能；

- 我们需要的东西，SpringBoot都帮我们自动配置；**@EnableAutoConfiguration**告诉SpringBoot开启自动配置；这样自动配置才能生效；

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(EnableAutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```

@**AutoConfigurationPackage**:自动配置包

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {
```

**@Import(AutoConfigurationPackages.Registrar.class):**

- Spring的底层注解@Import,给容器导入一个组件,导入组件由AutoConfigurationPackages.Registrar.class将<span style="color:red;">主配置类(**@SpringBootApplication**标注的类)所在包及下面所有子包中的所有组件都扫描到spring容器中</span>；

![](imgs\1.png)

**@Import(EnableAutoConfigurationImportSelector.class)**给容器导入组件

**EnableAutoConfigurationImportSelector**：导入哪些组件的选择器,将所需要的组件以全类名的形式返回,这些组件会被添加到容器中,会给容器中导入非常多的自动配置类(xxxAutoConfiguration)；就是给容器中导入这个场景需要的所有组件，并配置好这些组件(EnableAutoConfigurationImportSelector-AutoConfigurationImportSelector)

![](imgs\2.png)

![](imgs\3.png)

有了自动配置类，可以免去手动配置和注入功能组件等工作

getCandidateConfigurations(annotationMetadata,attributes)--SpringFactoriesLoader.loadFactoryNames

![](imgs\4.png)

SpringFactoriesLoader.loadFactoryNames(EnableAutoConfiguration.class,ClassLoader)

springBoot在启动时从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值，将这些值作为自动配置类导入容器，自动配置生效，进行自动配置

![](imgs\5.png)

# 二、SpringBoot配置

## 1、配置文件

springBoot使用一个全局配置文件，配置文件名是固定的；

- application.properties
- application.yml

配合文件的作用：修改SpringBoot自动配置的默认值；

yaml:配置例子

```yaml
server:
	port: 8081
```

xml:

```xml
<server>
	<port>8081</port>
</server>
```

## 2、yaml语法

### 1、基本语法

k:(空格)v		表示一对键值对<span style="color:red;">(空格必须有)</span>

以空格的缩进控制层级关系；只要左对齐的一列数据，都是一个层级的

```yaml
server:
	port: 8081
	path: /hello
```

<span style="color:red;">属性和值是大小写敏感；</span>

### 2、值的写法

#### 1、字面量：普通的值(数字，字符串，布尔)

k: v		字面直接来写；

​	字符串默认不加上<span style="color:red;">单引号</span>和<span style="color:red;">双引号</span>

​	"":双引号；	不会转义字符串里面的特殊字符；字符串会作为本身想表达的意思

​		name: "zs \n lisi"	输出:zs 换行 lisi

![](imgs\6.png)

​	‘’：单引号	会转义特殊字符，特殊字符作为一个普通的字符串变量

​		name: 'zs \n lisi'	输出:zs \n lisi

![](imgs\7.png)

#### 2、对象、Map(属性和值)(键值对)；

k: v	在下一行来写对象属性和值的关系；注意缩进

对象还是k: v的方式

```yaml
friends:
	lastName: zs
	age: 20
```

行内写法

```yaml
friends: {lastName: zs,age: 20}
```

#### 3、数组(List	Set)

用- 值表示数组的一个元素

```yaml
pets:
	- cat
	- dog
	- pig
```

行内写法

```yaml
pets: [cat,dog,pig]
```

#### 4、使用例子,通过@ConfigurationProperties注解获取属性值

application.yaml

```yaml
person:
  lastName: 'zs \n lisi'
  age: 20
  boss: true
  birth: 2019/12/12
  maps:
    a: aa
    b: bb
  lists:
    - c
    - d
    - e
  dog:
    name: ww
    age: 2
```

如果使用application.properties，则形式为

```properties
person.last-name=张三
person.age=20
person.birth=2019/12/12
person.boss=true
person.dog.name=ww
person.dog.age=2
person.lists=a,b,c
person.maps.k1=d
person.maps.k2=f
```



Person.java

```java
package com.demo.bean;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.List;
import java.util.Map;

/**
 *@ConfigurationProperties 将配置文件中配置每一个属性的值都映射到这个组件，将这个类的属性和配置文件相关配置进行绑定
 * prefix = "person"    配置文件中哪个下面的属性进行一一映射
 * 只有容器中的组件才能使用@ConfigurationProperties功能,所以需要加上@Component
 * @ConfigurationProperties(prefix = "person")默认从全局配置文件中获取值
 */
@Component
@ConfigurationProperties(prefix = "person")
public class Person {

    private String lastName;
    private Integer age;
    private Boolean boss;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;

    @Override
    public String toString() {
        return "Person{" +
                "lastName='" + lastName + '\'' +
                ", age=" + age +
                ", boss=" + boss +
                ", birth=" + birth +
                ", maps=" + maps +
                ", lists=" + lists +
                '}';
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Boolean getBoss() {
        return boss;
    }

    public void setBoss(Boolean boss) {
        this.boss = boss;
    }

    public Date getBirth() {
        return birth;
    }

    public void setBirth(Date birth) {
        this.birth = birth;
    }

    public Map<String, Object> getMaps() {
        return maps;
    }

    public void setMaps(Map<String, Object> maps) {
        this.maps = maps;
    }

    public List<Object> getLists() {
        return lists;
    }

    public void setLists(List<Object> lists) {
        this.lists = lists;
    }

    public Dog getDog() {
        return dog;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }
}

```

Dog.java

```java
package com.demo.bean;

public class Dog {

    private String name;
    private Integer age;

    @Override
    public String toString() {
        return "Dog{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}

```

ApplicationTests.java

```java
package com.demo;

import com.demo.bean.Person;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class ApplicationTests {

    @Autowired
    private Person person;

    @Test
    public void contextLoads() {
        System.out.println(person);
    }

}
```

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.maven.demo</groupId>
    <artifactId>spring-boot-01-helloworld</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--导入配置文件处理器，配置文件绑定就会有提示-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>

       <!-- springboot测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>

            <!--将应用打包成一个可执行jar-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

#### 5、@ConfigurationProperties和@Value的区别

|                       | @ConfigurationProperties | @Value     |
| --------------------- | ------------------------ | ---------- |
| 功能                  | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定(松散语法)    | 支持                     | 不支持     |
| spel                  | 不支持                   | 支持       |
| JSR303校验            | 支持                     | 不支持     |
| 复杂类型封装(map ...) | 支持                     | 不支持     |

JSR303校验可以看到@ConfigurationProperties支持@Validated,而  @Value不支持

<span style="color:red;">若涉及到复杂类型,如map等，就需要用  @ConfigurationProperties</span>

![](imgs\10.png)

![](imgs\11.png)







通过**@Value**获取值

```java
package com.demo.bean;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.List;
import java.util.Map;


@Component
//@ConfigurationProperties(prefix = "person")
public class Person {

    @Value("${person.last-name}")
    private String lastName;

    @Value("#{11*2}")
    private Integer age;

    @Value("true")
    private Boolean boss;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;

    @Override
    public String toString() {
        return "Person{" +
                "lastName='" + lastName + '\'' +
                ", age=" + age +
                ", boss=" + boss +
                ", birth=" + birth +
                ", maps=" + maps +
                ", lists=" + lists +
                ", dog=" + dog +
                '}';
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Boolean getBoss() {
        return boss;
    }

    public void setBoss(Boolean boss) {
        this.boss = boss;
    }

    public Date getBirth() {
        return birth;
    }

    public void setBirth(Date birth) {
        this.birth = birth;
    }

    public Map<String, Object> getMaps() {
        return maps;
    }

    public void setMaps(Map<String, Object> maps) {
        this.maps = maps;
    }

    public List<Object> getLists() {
        return lists;
    }

    public void setLists(List<Object> lists) {
        this.lists = lists;
    }

    public Dog getDog() {
        return dog;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }
}

```

application.properties

```java
person.last-name=张三
person.age=20
person.birth=2019/12/12
person.boss=true
person.dog.name=ww
person.dog.age=2
person.lists=a,b,c
person.maps.k1=d
person.maps.k2=f
```

![](imgs\8.png)

#### 6、@PropertySource和@ImportResource

<span style="color:red">@ConfigurationProperties(prefix = "person")默认从全局配置文件中获取值</span>,若要获取其他的properties文件属性，则需要配合@PropertySource

```java
package com.demo.bean;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.List;
import java.util.Map;


@Component
//@Validated
@ConfigurationProperties(prefix = "person")
@PropertySource(value = {"classpath:person.properties"})
public class Person {

    //@Value("${person.last-name}")
    //@Email
    private String lastName;

    //@Value("#{11*2}")
    private Integer age;

    //@Value("true")
    private Boolean boss;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;

    @Override
    public String toString() {
        return "Person{" +
                "lastName='" + lastName + '\'' +
                ", age=" + age +
                ", boss=" + boss +
                ", birth=" + birth +
                ", maps=" + maps +
                ", lists=" + lists +
                ", dog=" + dog +
                '}';
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Boolean getBoss() {
        return boss;
    }

    public void setBoss(Boolean boss) {
        this.boss = boss;
    }

    public Date getBirth() {
        return birth;
    }

    public void setBirth(Date birth) {
        this.birth = birth;
    }

    public Map<String, Object> getMaps() {
        return maps;
    }

    public void setMaps(Map<String, Object> maps) {
        this.maps = maps;
    }

    public List<Object> getLists() {
        return lists;
    }

    public void setLists(List<Object> lists) {
        this.lists = lists;
    }

    public Dog getDog() {
        return dog;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }
}
```

@ImportResource：导入spring的配置文件，让配置文件内容生效；

springboot里面没有spring的配置文件，我们自己编写的配置文件也不能自动识别，想让spring的配置文件生效，加载进来；需要将**@ImportResource**标注在一个配置类上

beans.xml（增加一个bean）发现加载不到bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="helloService" class="com.demo.service.HelloService"></bean>
</beans>
```

![](imgs\12.png)

加上**@ImportResource**注解之后

![](imgs\13.png)

返回成功

![](imgs\14.png)

#### 7、**@Configuration**和**@Bean**

然而在springboot中不推荐编写spring配置文件(beans.xml)来添加组件;

springBoot推荐给容器中添加组件的方式是使用全注解的方式，使用配置类来代替配置文件

配置类写法

```java
package com.demo.config;

import com.demo.service.HelloService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @Configuration:指明当前类是一个配置类；就是代替之前的spring配置文件
 * 在配置文件中使用<bean></bean>标签添加组件  这里使用@Bean注解添加组件
 */

@Configuration
public class BeanConfig {

    //将方法的返回值添加到容器中，容器中组件默认的id名就是方法名
    @Bean
    public HelloService helloService(){
        return new HelloService();
    }
}
```

返回结果能成功找到这个组件

![](imgs\15.png)



## 3、配置文件占位符

- 随机数

```java
${random.value}、${random.int}、${random.long}

${random.int(10)}、${random.int[1024,65536]}
```

- 占位符获取之前配置的值，如果没有可以用:指定默认的值

```java
person.last-name=张三${random.uuid}
person.age=${random.int}
person.birth=2019/12/12
person.boss=true
person.dog.name=${person.last-name:haha}_dog
person.dog.age=2
person.lists=a,b,c
person.maps.k1=d
person.maps.k2=f
```

## 4、profile(做多环境支持的)

### 1、多profile文件

我们在主配置文件编写的时候,文件名可以是 application-{profile}.properties，默认使用application.properties配置

可以看到使用了application.properties的配置

![](imgs\16.png)

在application.properties通过**spring.profiles.active=dev**指定执行配置文件application-dev.properties

![](imgs\17.png)

### 2、yml多文档块方式

yml中以<span style="color:red;">---</span>来划分多文档,默认执行第一份

application.yml

```yml
server:
  port: 8083

---

server:
  port: 8084

spring:
  profiles: dev
---
server:
  port: 8085

spring:
  profiles: prod
```

![](imgs\18.png)

通过

```yaml
spring:
  profiles:
    active: dev
```

切换环境

![](imgs\19.png)

### 3、激活指定profile方式

1、在配置文件中指定spring.profiles.active=dev

2、命令行：

​	java -jar *.jar --spring.profiles.active=dev;

​	可以直接在测试的时候配置传入命令行参数

![](imgs\20.png)

3、虚拟机参数：

​	-Dspring.profiles.active=dev

![](imgs\21.png)

## 5、配置文件加载位置

springboot会扫描以下位置的application.properties或者application.yml文件作为springboot的配置文件

- file:./config/
- file:./
- classpath:/config/
- classpath:/

以上是按照**优先级从高到低**的顺序，所有位置的文件都会被加载，**高优先级配置会覆盖低优先级配置**

- file:./config/   项目下的config文件夹

![](imgs\22.png)



- file:./   当前项目下

![](imgs\23.png)

- classpath:/config/    类路径下的config

![](imgs\24.png)

- classpath:/   类路径下

![](imgs\25.png)

我们还可以通过spring.config.location来改变默认配置文件位置

项目打包好后，我们可以使用命令行参数的形式，启东项目的时候指定配置文件的新位置，指定配置文件和默认配置文件一起起作用，形成互补配置

```pr
java -jar *.jar --spring.config.location=c:/a.properties
```

## 5、外部配置的加载顺序

springboot也可以通过以下位置加载配置

[参考官方文档](https://docs.spring.io/spring-boot/docs/2.1.7.RELEASE/reference/html/boot-features-external-config.html)

- <span style="color:red;">命令行参数</span>
  - java -jar *.jar --server.port=8081 --server.comtext-path=/hello
- <span style="color:red;">jar包外部的application-{frofile}.properties或application.yml(带spring.profile配置文件)</span>
- <span style="color:red;">jar包内部的application-{frofile}.properties或application.yml(带spring.profile配置文件)</span>
- <span style="color:red;">jar包外部的application.properties或application.yml(不带spring.profile配置文件)</span>
- <span style="color:red;">jar包内部的application.properties或application.yml(不带spring.profile配置文件)</span>

## 6、自动配置原理

application.properties能写什么，自动配置原理；

[配置文件属性参照文档](https://docs.spring.io/spring-boot/docs/2.1.7.RELEASE/reference/html/common-application-properties.html)

### **1、自动配置原理**

- springboot启动时加载主配置类，开启了自动配置功能@EnableAutoConfiguration

- @EnableAutoConfiguration的作用

  - 利用EnableAutoConfigurationImportSelector给容器导入一些组件
  - 可以查看selectImports()方法内容:
  - List<String> configurations = getCandidateConfigurations(annotationMetadata,0.attributes);获取候选的配置

     ```java
  SpringFactoriesLoader.loadFactoryNames()
  扫描jar包类路径下META-INF/spring.factories
  把扫到的这些文件内容包装成properties对象
  从properties中获取到EnableAutoConfiguration.class类(类名)对应的值，然后把他们添加到容器中
     ```

  将类路径下META-INF/spring.factories里面配置的所有EnableAutoConfiguration的值加入到容器中；

  ```properties
  # Auto Configure
  org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
  org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
  org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
  org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
  org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
  org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
  org.springframework.boot.autoconfigure.cloud.CloudAutoConfiguration,\
  org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
  org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
  org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
  org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
  org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.ldap.LdapDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\
  org.springframework.boot.autoconfigure.elasticsearch.jest.JestAutoConfiguration,\
  org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration,\
  org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration,\
  org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration,\
  org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration,\
  org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration,\
  org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration,\
  org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration,\
  org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration,\
  org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration,\
  org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
  org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\
  org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration,\
  org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration,\
  org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration,\
  org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration,\
  org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration,\
  org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration,\
  org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration,\
  org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration,\
  org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration,\
  org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration,\
  org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration,\
  org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration,\
  org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration,\
  org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration,\
  org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration,\
  org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration,\
  org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration,\
  org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration,\
  org.springframework.boot.autoconfigure.mobile.DeviceResolverAutoConfiguration,\
  org.springframework.boot.autoconfigure.mobile.DeviceDelegatingViewResolverAutoConfiguration,\
  org.springframework.boot.autoconfigure.mobile.SitePreferenceAutoConfiguration,\
  org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration,\
  org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\
  org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration,\
  org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
  org.springframework.boot.autoconfigure.reactor.ReactorAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.SecurityAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.SecurityFilterAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.FallbackWebSecurityAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.oauth2.OAuth2AutoConfiguration,\
  org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\
  org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\
  org.springframework.boot.autoconfigure.social.SocialWebAutoConfiguration,\
  org.springframework.boot.autoconfigure.social.FacebookAutoConfiguration,\
  org.springframework.boot.autoconfigure.social.LinkedInAutoConfiguration,\
  org.springframework.boot.autoconfigure.social.TwitterAutoConfiguration,\
  org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration,\
  org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration,\
  org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration,\
  org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration,\
  org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.DispatcherServletAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.EmbeddedServletContainerAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.ErrorMvcAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.HttpEncodingAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.HttpMessageConvertersAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.MultipartAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.ServerPropertiesAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.WebClientAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.WebMvcAutoConfiguration,\
  org.springframework.boot.autoconfigure.websocket.WebSocketAutoConfiguration,\
  org.springframework.boot.autoconfigure.websocket.WebSocketMessagingAutoConfiguration,\
  org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration
  ```

  每一个这样的xxxAutoConfiguration类都是容器中的一个组件，都加入到了容器中；用他们作自动配置

- 每一个自动配置类都进行自动配置功能
- 以**HttpEncodingAutoConfiguration**为例解释自动配置原理

```java
@Configuration //表示这是一个配置类，和以前编写的配置文件一样，也可以给容器中添加组件
@EnableConfigurationProperties(HttpEncodingProperties.class)//启动指定类的ConfigurationProperties功能；将配置文件中对应的值和HttpEncodingProperties绑定起来；并把HttpEncodingProperties加入到ioc容器中

@ConditionalOnWebApplication//spring底层@Conditional注解，根据不同的条件，如果满足指定的条件，整个配置类中的配置就会生效；	判断当前应用是否是web应用，如果是，当前配置类生效

@ConditionalOnClass(CharacterEncodingFilter.class)//判断当前项目中有没有这个类，CharacterEncodingFilter；springMvc进行乱码解决过滤器

@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)//判断配置文件是否存在某个配置，spring.http.encoding.enabled;如果不存在，判断也是成立的
//即使我们配置文件中不配置spring.http.encoding.enabled=true,也是默认生成的
public class HttpEncodingAutoConfiguration {
    
    //它已经和springboot的配置文件映射了
    private final HttpEncodingProperties properties;
    
    //在只有一个有参构造器的情况下，参数值就会从容器中去拿
    public HttpEncodingAutoConfiguration(HttpEncodingProperties properties) {
		this.properties = properties;
	}
    
    
    @Bean//给容器中添加一个组件，这个组件中的某些值需要从properties中获取
	@ConditionalOnMissingBean(CharacterEncodingFilter.class)
	public CharacterEncodingFilter characterEncodingFilter() {
		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
		filter.setEncoding(this.properties.getCharset().name());
		filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
		filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
		return filter;
	}
```

根据当前不同的条件判断你，决定这个配置类是否生效

一旦这个配置类生效；这个配置类就会给容器中添加各种组件；这些组件的属性是从对应的properties文件中获取的，这些类里面的每一个属性又是和配置文件绑定的

- 所有配置文件中能配置的属性都是在xxxProperties类中封装着；配置文件中能配置什么就可以参照某个功能对应的这个属性类

```java
@ConfigurationProperties(prefix = "spring.http.encoding") //从配置文件中获取指定的值求和bean的属性进行绑定
public class HttpEncodingProperties {
```

精髓：

- springboot启动会加载大量的自动配置类
- 我们看我们需要的功能有没有springboot默认写好的自动配置类
- 我们看这些自动配置类中到底配置了哪些组件；(只要有我们要用的组件，我们就不需要再来自动配置了)
- 给容器中自动配置类添加组件的时候，会从properties类中获取某些属性，我们就可以在配置文件中指定这些属性的值

xxxAutoConfiguration：自动配置类；

给容器中添加组件

xxxProperties：封装配置文件中相关属性



### 2、细节

- @Conditional派生注解(spring注解版原生的@Conditional作用)
- 作用：必须是@Conditional指定的条件成立，才给容器中添加组件配置类里面；

| @Conditional扩展注解            | 作用(判断是否满足当前指定条件)                   |
| :------------------------------ | :----------------------------------------------- |
| @ConditionalOnjava              | 系统的java版本是否满足要求                       |
| @ConditionalOnBean              | 容器中存在指定的bean                             |
| @ConditionalOnMissingBean       | 容器中不存在指定的bean                           |
| @ConditionalOnExpression        | 满足SpEL表达式指定                               |
| @ConditionalOnClass             | 系统中有指定的类                                 |
| @ConditionalOnMissingClass      | 系统中没有指定的类                               |
| @ConditionalOnSingleCandidate   | 系统中只有一个指定的bean，或者这个bean是首选bean |
| @ConditionalOnProperty          | 系统中指定的属性是否有指定的值                   |
| @ConditionalOnResource          | 类路径下是否存在指定的资源文件                   |
| @ConditionalOnWebApplication    | 当前是web环境                                    |
| @ConditionalOnNotWebApplication | 当前不是web环境                                  |
| @ConditionalOnjndi              | JNDI存在指定项                                   |

**自动配置类必须在一定的条件下才能生效**

我们怎么知道哪些自动配置类生效？

我们可以通过启用debug=true属性，来让控制台打印出自动配置包高，这样我们就很方便的知道哪些自动配置生效

application.properties

```properties
debug=true
server.port=8081
```

console

```java
Positive matches:（生效的）
-----------------

   DispatcherServletAutoConfiguration matched:
      - @ConditionalOnClass found required class 'org.springframework.web.servlet.DispatcherServlet'; @ConditionalOnMissingClass did not find unwanted class (OnClassCondition)
      - @ConditionalOnWebApplication (required) found StandardServletEnvironment (OnWebApplicationCondition)
          

Negative matches:（不生效的）
-----------------

   ActiveMQAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required classes 'javax.jms.ConnectionFactory', 'org.apache.activemq.ActiveMQConnectionFactory' (OnClassCondition)


```

# 三、springboot与日志

## 1、日志框架

| 日志门面(日志抽象层)      | 日志实现            |
| ------------------------- | ------------------- |
| JCL、SLF4J、jboss-logging | log4j、JUL、logback |

左边选一个门面(抽象层)、右边来一个实现

日志门面：SLF4J

日志实现：logback

**springboot选用的是slf4j和logback**

## 2、SLF4J的使用



aaa

