# 一、组建注册

## 1、配置文件形式的spring应用

pom.xml

```xml
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.3.13.RELEASE</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.springframework/spring-aspects -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>4.3.13.RELEASE</version>
    </dependency>


    <!-- https://mvnrepository.com/artifact/org.springframework/spring-tx -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>4.3.13.RELEASE</version>
    </dependency>
```



Person.java

```java
package com.demo.entity;

public class Person {

    private String name;
    private Integer age;

    public Person() {
    }

    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
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

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd">

    <context:property-placeholder location="classpath:person.properties"/>
    <!-- 包扫描、只要标注了@Controller、@Service、@Repository，@Component等注解的，都将他们放到spring容器中 -->
    <!-- <context:component-scan base-package="com.demo" use-default-filters="false"></context:component-scan> -->
    <bean id="person" class="com.demo.entiry.Person" scope="prototype" >
        <property name="age" value="${age}"></property>
        <property name="name" value="zhangsan"></property>
    </bean>

    <!-- 开启基于注解版的切面功能 -->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>

    <!-- <tx:annotation-driven/> -->

</beans>
```

调用bean

```java
package com.demo;

import com.demo.entity.Person;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class test {

    @Test
    public void test01(){
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Person person = (Person) context.getBean("person");
        System.out.println(person);
    }
}
```



## 2、注解形式的spring应用

1、配置类(相当于配置文件beans.xml)

```java
package com.demo.configuration;

import com.demo.entity.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration//表示这个类是一个配置类(相当于之前的配置文件)
@ComponentScan("com.demo")//扫描com.demo底下的所有包(可以将带@Controller @Service @Component @Repository等注解的类放入容器)
public class Config {

    //@Bean//相当于配置文件的bean标签，返回类型Person相当于class的值,方法名相当于id值
    @Bean(name="person2") //可以通过name属性更改bean的名称(此时方法名虽然是person，但是bean的名称为person2)
    public Person person(){
        return new Person("liuhaha",12);
    }
}
```

2、Controller

```java
package com.demo.controller;

import org.springframework.stereotype.Controller;

@Controller
public class PersonController {
}
```

3、service

```java
package com.demo.service.impl;

import org.springframework.stereotype.Service;

@Service
public class PersonServiceImpl {
}
```

4、测试类

```java
@org.junit.Test
    public void test02(){
        ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
        String[] names = context.getBeanDefinitionNames();
        for(String name:names){
            System.out.println(name);
        }
    }
```

5、运行结果

```java
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalRequiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
config
personController
personServiceImpl
person
```

### 2.1 excludeFilters

表示要排除哪些组件，默认按照FilterType.ANNOTATION(注解)的方式排除

**后面有的版本会把@ComponentScan.Filter写成ComponentScan.Filter，记得要改回来，不然后面写参数会有问题**

```java
@Configuration
@ComponentScan(value = "com.demo",excludeFilters = {
        @ComponentScan.Filter(value = {Controller.class})
})//excludeFilters表示要排除哪些组件，默认按照FilterType.ANNOTATION(注解)的方式排除
public class Config {

    @Bean(name="person2")
    public Person person(){
        return new Person("liuhaha",12);
    }
}
```

**可以从结果看到@Controller注解这个组件并没有放进容器**

```java
config
org.springframework.context.annotation.ConfigurationClassPostProcessor.importAwareProcessor
org.springframework.context.annotation.ConfigurationClassPostProcessor.enhancedConfigurationProcessor
personService
person2
```

### 2.2 includeFilters

表示只包含哪些组件，但是要将useDefaultFilters设置为false,他默认是true,表示扫描全部组件

**自定义的includeFilters组件一定要加上useDefaultFilters=false，不然之后默认扫描除了带@Controller，@Service等标记的以外的类，具体自己debug查看**

```java
package com.demo.configuration;

import com.demo.entity.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.stereotype.Controller;

@Configuration
@ComponentScan(value = "com.demo",useDefaultFilters = false,includeFilters = {
        @ComponentScan.Filter(value = {Controller.class})
})//includeFilters表示只包含哪些组件，但是要将useDefaultFilters设置为false,他默认是true,表示扫描全部组件
public class Config {

    @Bean(name="person2")
    public Person person(){
        return new Person("liuhaha",12);
    }
}
```

可以看到@service的组件并没有放入容器

```java
config
org.springframework.context.annotation.ConfigurationClassPostProcessor.importAwareProcessor
org.springframework.context.annotation.ConfigurationClassPostProcessor.enhancedConfigurationProcessor
personController
person2
```

### 2.3 自定义FilterType

@ComponentScan源码

可以看到includeFilters和excludeFilters的参数类型都是Filter类型

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
public @interface ComponentScan {
    Filter[] includeFilters() default {};
    Filter[] excludeFilters() default {};
    boolean useDefaultFilters() default true;
}
```

@Filter源码

@Filter的默认类型是FilterType.ANNOTATION，表示以注解的方式过滤

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({})
@interface Filter {
    FilterType type() default FilterType.ANNOTATION;
    Class<?>[] value() default {};
}
```

FilterType源码

FilterType是一个枚举类型

```java
public enum FilterType {

   /**
    * Filter candidates marked with a given annotation.
    * @see org.springframework.core.type.filter.AnnotationTypeFilter
    */
   ANNOTATION,

   /**
    * Filter candidates assignable to a given type.
    * @see org.springframework.core.type.filter.AssignableTypeFilter
    */
   ASSIGNABLE_TYPE,

   /**
    * Filter candidates matching a given AspectJ type pattern expression.
    * @see org.springframework.core.type.filter.AspectJTypeFilter
    */
   ASPECTJ,

   /**
    * Filter candidates matching a given regex pattern.
    * @see org.springframework.core.type.filter.RegexPatternTypeFilter
    */
   REGEX,

   /** Filter candidates using a given custom
    * {@link org.springframework.core.type.filter.TypeFilter} implementation.
    */
   CUSTOM

}
```

FilterType.ANNOTATION表示以注解类型过滤，具体用法参考上面的代码有例子

FilterType.ASSIGNABLE_TYPE表示以所给的类的类型过滤

```java
@Configuration
@ComponentScan(value = "com.demo",useDefaultFilters = false,includeFilters = {
        @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE,value = {Controller.class})
})//includeFilters表示只包含哪些组件，但是要将useDefaultFilters设置为false,他默认是true,表示扫描全部组件
public class Config {

    @Bean(name="person2")
    public Person person(){
        return new Person("liuhaha",12);
    }
}
```

FilterType.CUSTOM表示自定义过滤条件

通过FilterType的源码可以看出，自定义的过滤类型需要实现TypeFilter

```java
public enum FilterType {
   /** Filter candidates using a given custom
    * {@link org.springframework.core.type.filter.TypeFilter} implementation.
    */
   CUSTOM
}
```

自定义的过滤规则需要实现TypeFilter的match方法，通过返回的true的false来确定是否过滤

这个过滤过则是如果className包含Controller这个字符串的话就过滤

```java
package com.demo.customfilter;

import org.springframework.core.io.Resource;
import org.springframework.core.type.AnnotationMetadata;
import org.springframework.core.type.ClassMetadata;
import org.springframework.core.type.classreading.MetadataReader;
import org.springframework.core.type.classreading.MetadataReaderFactory;
import org.springframework.core.type.filter.TypeFilter;

import java.io.IOException;

public class MyFilter implements TypeFilter {

    /**
     * metadataReader 读取到当前正在扫描的类的信息
     * metadataReaderFactory 可以获取到其他任何类的信息
     * @param metadataReader
     * @param metadataReaderFactory
     * @return
     * @throws IOException
     */

    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {

        //获取当前类的注解的信息
        AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();

        //获取当前正在扫描的类的信息(例如：内部的方法，实现的接口)
        ClassMetadata classMetadata = metadataReader.getClassMetadata();

        //获取当前扫描的类的资源信息(例如：路径等)
        Resource resource = metadataReader.getResource();

        //获取当前扫描类的类名
        String className = classMetadata.getClassName();

        System.out.println("---->"+className);

        if(className.contains("Controller")){
            return true;
        }
        return false;
    }
}
```

在配置类上配置这个自定义过滤器，因为用的是includeFilters ，所以PersonController会被加入容器

```java
package com.demo.configuration;

import com.demo.customfilter.MyFilter;
import com.demo.entity.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

@Configuration
@ComponentScan(value = "com.demo",useDefaultFilters = false,includeFilters = {
        @ComponentScan.Filter(type = FilterType.CUSTOM,value = {MyFilter.class})
})//includeFilters表示只包含哪些组件，但是要将useDefaultFilters设置为false,他默认是true,表示扫描全部组件
public class Config {

    @Bean(name="person2")
    public Person person(){
        return new Person("liuhaha",12);
    }
}
```

```java
@Test
public void test01(){
    ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);

    //获取bean容器中所有的bean的名字
    String[] names = context.getBeanDefinitionNames();
    for (String name:names){
        System.out.println(name);
    }

    System.out.println("----------------------");
}
```

结果

只有personController被加进了容器，service没有加进去

```java
config
org.springframework.context.annotation.ConfigurationClassPostProcessor.importAwareProcessor
org.springframework.context.annotation.ConfigurationClassPostProcessor.enhancedConfigurationProcessor
personController
person2
```

## 3、@scope设置组件的作用域

配置类

```java
package com.demo.configuration;

import com.demo.entity.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Config {

    @Bean(name="person")
    public Person person(){
        return new Person("liuhaha",12);
    }
}
```

测试类

```java
@Test
public void test01(){
    ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
    Object person = context.getBean("person");
    Object person1 = context.getBean("person");
    System.out.println(person==person1);

}
```

结果

可以看出spring默认的作用域是单例模式

```java
十一月 11, 2019 9:02:28 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@6a6824be: startup date [Mon Nov 11 21:02:28 CST 2019]; root of context hierarchy
true
```

**而且在单例模式下，bean是在ioc容器创建的时候就开始创建的**

配置类

```java
package com.demo.configuration;

import com.demo.entity.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Config {

    @Bean(name="person")
    public Person person(){
        System.out.println("创建了person对象");
        return new Person("liuhaha",12);
    }
}
```

测试类

```java
@Test
public void test01(){
    ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
}
```

结果

```java
十一月 11, 2019 9:04:58 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@6a6824be: startup date [Mon Nov 11 21:04:58 CST 2019]; root of context hierarchy
创建了person对象
```

### 3.1 可以通过@Scope注解来控制作用域

@scope源码

**可以看到value默认是ConfigurableBeanFactory.SCOPE_SINGLETON(就是sington)**

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Scope {

   /**
    * Specifies the scope to use for the annotated component/bean.
    * @see ConfigurableBeanFactory#SCOPE_SINGLETON
    * @see ConfigurableBeanFactory#SCOPE_PROTOTYPE
    * @see org.springframework.web.context.WebApplicationContext#SCOPE_REQUEST
    * @see org.springframework.web.context.WebApplicationContext#SCOPE_SESSION
    */
   String value() default ConfigurableBeanFactory.SCOPE_SINGLETON;
}
```

**@Scope("prototype")将组件设置问多例模式**

```java
package com.demo.configuration;

        import com.demo.entity.Person;
        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        import org.springframework.context.annotation.Scope;

@Configuration
public class Config {

    @Scope("prototype")
    @Bean(name="person")
    public Person person(){
        System.out.println("创建了person对象");
        return new Person("liuhaha",12);
    }
}
```

测试类

```java
@Test
public void test01(){
    ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
}
```

结果

**可以发现，在初始化ioc容器时，person的对象并没有创建**

```java
十一月 11, 2019 9:11:44 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@6a6824be: startup date [Mon Nov 11 21:11:44 CST 2019]; root of context hierarchy

Process finished with exit code 0
```

测试类

```java
@Test
public void test01(){
    ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
    Object person = context.getBean("person");
    Object person1 = context.getBean("person");
    System.out.println(person==person1);

}
```

结果

**在获取bean的时候创建了person对象，而且获取几次就创建了几次**

```java
十一月 11, 2019 9:13:28 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@6a6824be: startup date [Mon Nov 11 21:13:28 CST 2019]; root of context hierarchy
创建了person对象
创建了person对象
false

Process finished with exit code 0
```

### 3.2 懒加载模式  @lazy

**懒加载是针对单实例的模式而言的，在上面说到过，在sington的模式下，容器的对象是在ioc容器初始化的时候创建的，懒加载模式是可以延迟对象的创建，让他在第一次获取bean的时候创建**

配置类

```java
package com.demo.configuration;
import com.demo.entity.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Config {
    @Lazy
    @Bean(name="person")
    public Person person(){
        System.out.println("创建了person对象");
        return new Person("liuhaha",12);
    }
}
```

测试类

```java
@Test
public void test01(){
    ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
}
```

结果

**可以看到在懒加载的模式下，初始化ioc的时候，对象并没有被创建**

```java
十一月 11, 2019 9:20:37 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@6a6824be: startup date [Mon Nov 11 21:20:37 CST 2019]; root of context hierarchy

Process finished with exit code 0
```

测试类

```java
@Test
public void test01(){
    ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
    Object person = context.getBean("person");
    Object person1 = context.getBean("person");
    System.out.println(person==person1);
}
```

结果

**可以看到在获取bean的时候，对象创建了，而且只创建了一次**

```java
十一月 11, 2019 9:23:38 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@6a6824be: startup date [Mon Nov 11 21:23:38 CST 2019]; root of context hierarchy
创建了person对象
true
Process finished with exit code 0
```

## 4、@conditional 按照条件注册bean

**默认情况下person01和person02都会被注册出来，但是加了@Conditional注解之后，只有当@Conditional的条件成立的情况下才会注册**

自定义条件，当前环境如果是windows就注册person01,是linux就注册person02



首先查看@Conditional注解源码，发现value的值是一个实现了Condition接口的数组，所以我们自定义条件时，得先创建一个Condition的实现类

```java
package org.springframework.context.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
public @interface Conditional {

   /**
    * All {@link Condition}s that must {@linkplain Condition#matches match}
    * in order for the component to be registered.
    */
   Class<? extends Condition>[] value();

}
```

condition实现类

```java
package com.demo.condition;

import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.env.Environment;
import org.springframework.core.type.AnnotatedTypeMetadata;

/**
 * 判断是否是window系统
 */
public class WindowCondition implements Condition {

    /**
     * ConditionContext 判断条件能使用的上下文(环境)
     * AnnotatedTypeMetadata 注释信息
     * @param context
     * @param metadata
     * @return
     */
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {

        //1、能获取的ioc容器使用的beanfactory
        ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();

        //2、获取类加载器
        ClassLoader classLoader = context.getClassLoader();

        //3、获取当前环境信息
        Environment environment = context.getEnvironment();

        //4、能获取的bean定义的注册类
        BeanDefinitionRegistry registry = context.getRegistry();

        //获取当前的操作系统是windows还是linux
        String property = environment.getProperty("os.name");
        if(property.toLowerCase().contains("window")){
            return true;
        }
        return false;
    }
}
```

```java
package com.demo.condition;

import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.env.Environment;
import org.springframework.core.type.AnnotatedTypeMetadata;

public class LinuxCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        Environment environment = context.getEnvironment();

        //获取当前的操作系统是windows还是linux
        String property = environment.getProperty("os.name");
        if(property.toLowerCase().contains("linux")){
            return true;
        }
        return false;
    }
}
```

配置类

```java
package com.demo.configuration;

import com.demo.condition.LinuxCondition;
import com.demo.condition.WindowCondition;
import com.demo.entity.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Conditional;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Config {

    @Bean
    @Conditional({WindowCondition.class})
    public Person person01(){
        return new Person("liuhaha",11);
    }

    @Bean
    @Conditional({LinuxCondition.class})
    public Person person02(){
        return new Person("wahaha",12);
    }
}
```

测试类

```java
@Test
public void test01(){
    ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);

    //获取指定类型的对象名称
    String[] beanNamesForType = context.getBeanNamesForType(Person.class);
    for(String beanName:beanNamesForType){
        System.out.println(beanName);
    }

    //获取指定类型的Map值
    Map<String, Person> beansOfType = context.getBeansOfType(Person.class);
    System.out.println(beansOfType);

    //获取当前环境(查看是window系统，还是linux系统)
    Environment environment = context.getEnvironment();
    String property = environment.getProperty("os.name");
    System.out.println(property);

}
```

结果

**只有person01被创建出来了，即@Conditional生效**

```java
十一月 11, 2019 10:14:28 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@6a6824be: startup date [Mon Nov 11 22:14:28 CST 2019]; root of context hierarchy
person01
{person01=Person{name='liuhaha', age=11}}
Windows 10

Process finished with exit code 0
```

## 5、@import

给容器中导入组件

1. 包扫描+组件标注注解(@service/@Controller/@Repository/@Component)
2. @Bean	导入第三方包里的组件
3. @Import	快速给容器导入一个组件
   1. @Import(要导入到容器的组件);容器就会自动注册这个组件，id默认是全类名
   2. ImportSelector:返回需要导入组件的全类名数组
   3. ImportBeanDefinitionRegistrar：手动注册Bean到容器中
   4. FactoryBean注册·Bean

### 5.1、@import	快速给容器中导入一个组件

Color

```java
package com.demo.entity;

public class Color {
}
```

Size

```java
package com.demo.entity;

public class Size {
}
```

配置类

```java
package com.demo.configuration;

import com.demo.entity.Color;
import com.demo.entity.Person;
import com.demo.entity.Size;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import({Color.class, Size.class})
public class Config {

    @Bean
    public Person person01(){
        return new Person("liuhaha",11);
    }

    @Bean
    public Person person02(){
        return new Person("wahaha",12);
    }
}
```

测试类

```java
ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);

@Test
    public void test01(){
    print();

}

public void print(){
    String[] beanDefinitionNames = context.getBeanDefinitionNames();
    for(String name:beanDefinitionNames){
        System.out.println(name);
    }
}
```

结果

**Color和Size都放进了容器中，bean的名字是全类名(包名加上类名)**

```java
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
config
com.demo.entity.Color
com.demo.entity.Size
person01
person02
```

### 5.2 ImportSelector给容器中导入组件

1、新建一个类，实现ImportSelector接口，重写selectImports方法，方法返回的全类名数组就是要加载到容器的组件

MyImportSelector 

```java
package com.demo.importselector;

import org.springframework.context.annotation.ImportSelector;
import org.springframework.core.type.AnnotationMetadata;

public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        return new String[]{"com.demo.entity.Blue","com.demo.entity.Red"};
    }
}
```

Blue

```java
package com.demo.entity;
public class Blue {
}
```

Red

```java
package com.demo.entity;
public class Red {
}
```

配置类

此时@Import导入MyImportSelector.class并不是将MyImportSelector放入容器中，因为MyImportSelector实现了ImportSelector 接口，所以是将selectImports的返回值的全类名数组放入容器中

```java
package com.demo.configuration;

import com.demo.entity.Color;
import com.demo.entity.Person;
import com.demo.entity.Size;
import com.demo.importselector.MyImportSelector;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import({Color.class, Size.class, MyImportSelector.class})
public class Config {

    @Bean
    public Person person01(){
        return new Person("liuhaha",11);
    }

    @Bean
    public Person person02(){
        return new Person("wahaha",12);
    }
}
```

测试类

```java
ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);

@Test
    public void test01(){
    print();

}
public void print(){
    String[] beanDefinitionNames = context.getBeanDefinitionNames();
    for(String name:beanDefinitionNames){
        System.out.println(name);
    }
}
```

结果

**Blue和Red全都放进了容器中**

```java
config
com.demo.entity.Color
com.demo.entity.Size
com.demo.entity.Blue
com.demo.entity.Red
person01
person02
```

### 5.3 ImportBeanDefinitionRegistrar

1、新建一个类实现ImportBeanDefinitionRegistrar接口，重写registerBeanDefinitions方法，在方法中可以注册新的组件

MyBeanDefinition

类中表示如果容器中存在com.demo.entity.Blue和com.demo.entity.Red组件，就注册一个名为rain的组件

```java
package com.demo.importselector;

import com.demo.entity.Rain;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.beans.factory.support.RootBeanDefinition;
import org.springframework.context.annotation.ImportBeanDefinitionRegistrar;
import org.springframework.core.type.AnnotationMetadata;

    public class MyBeanDefinition implements ImportBeanDefinitionRegistrar {

    /**
     * importingClassMetadata   当前类的注解信息
     * BeanDefinitionRegistry   BeanDefinition注册类；
     *      把所有需要添加到容器中的Bean；调用BeanDefinitionRegistry.registerBeanDefinitions手工注册进来
     *
     * @param importingClassMetadata
     * @param registry
     */
    @Override
    public void registerBeanDefinitions(Annotatietadata importingClassMetadata, BeanDefinitionRegistry registry) {onM
        boolean b = registry.containsBeanDefinition("com.demo.entity.Blue");
        boolean b1 = registry.containsBeanDefinition("com.demo.entity.Red");
        if(b && b1){

            //指定注册Bean定义信息（Bean的类型及作用域等）
            RootBeanDefinition rootBeanDefinition = new RootBeanDefinition(Rain.class);

            //注册一个Bean,指定Bean的名称
            registry.registerBeanDefinition("rain",rootBeanDefinition);
        }
    }
}
```

Rain

```java
package com.demo.entity;
public class Rain {
}
```

配置类

@Import导入的MyBeanDefinition类因为实现了ImportBeanDefinitionRegistrar接口，所以会生成registerBeanDefinitions注册的组件，而不会将MyBeanDefinition 注册到容器中

```java
package com.demo.configuration;

import com.demo.entity.Color;
import com.demo.entity.Person;
import com.demo.entity.Size;
import com.demo.importselector.MyBeanDefinition;
import com.demo.importselector.MyImportSelector;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import({Color.class, Size.class, MyImportSelector.class, MyBeanDefinition.class})
public class Config {

    @Bean
    public Person person01(){
        return new Person("liuhaha",11);
    }

    @Bean
    public Person person02(){
        return new Person("wahaha",12);
    }
}
```

### 5.4 FactoryBean

1、通过实现FactoryBean接口来注册Bean

1. getObject(): 注册Bean的方法
2. getObjectType(): 获取Bean的类型
3. isSingleton(): 是否使用单实例

```java
package com.demo.factory;

import com.demo.entity.Kid;
import org.springframework.beans.factory.FactoryBean;

public class MyFactoryBean implements FactoryBean<Kid> {

    @Override
    public Kid getObject() throws Exception {
        System.out.println("new Kid()...");
        return new Kid();
    }

    @Override
    public Class<?> getObjectType() {
        return Kid.class;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }
}
```

配置类

```java
package com.demo.configuration;

import com.demo.entity.Person;
import com.demo.factory.MyFactoryBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Config {

    @Bean
    public Person person01(){
        return new Person("liuhaha",11);
    }

    @Bean
    public Person person02(){
        return new Person("wahaha",12);
    }

    @Bean
    public MyFactoryBean myFactoryBean(){
        return new MyFactoryBean();
    }
}
```

测试类

```java
ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);

@Test
    public void test01(){
    print();
    Object myFactoryBean = context.getBean("myFactoryBean");
    System.out.println(myFactoryBean.getClass());
}
```

结果

虽然bean的名称显示是myFactoryBean，但是实际上可以看出bean是com.demo.entity.Kid

```java
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
config
person01
person02
myFactoryBean
new Kid()...
class com.demo.entity.Kid
```

# 二、生命周期

## 1、@Bean指定的初始化的销毁方法

