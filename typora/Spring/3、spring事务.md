# 一、声明式事务

## 1、环境搭建

1）、导入相关依赖         

​			数据源、数据库驱动、Spring-jdbc模块      

2）、配置数据源、JdbcTemplate(spring提供简化数据库操作的工具 )操作数据库      



## 2、使用注解版事务步骤

1）、给方法上标注上@Transactional表示当前方法是一个事务方法     

2）、@EnableTransactionManagement开启基于注解的事务管理功能      

3）、配置事务管理器来控制事务           

​			@Bean           

​			public PlatformTransactionManager transactionManager(DataSource dataSource){



MainConfigOfTx

<span style="color:red">@EnableTransactionManagement：表示开启事务管理器</span>

<span style="color:red">public PlatformTransactionManager transactionManager 表示注册一个事务管理器</span>

```java
package com.demo.configuration;


import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.sql.DataSource;

/**
 * 声明式事务
 *
 * 环境搭建：
 *      1、导入相关依赖
 *          数据源、数据库驱动、Spring-jdbc模块
 *      2、配置数据源、JdbcTemplate(spring提供简化数据库操作的工具 )操作数据库
 *      3、给方法上标注上@Transactional表示当前方法是一个事务方法
 *      4、@EnableTransactionManagement开启基于注解的事务管理功能
 *      5、配置事务管理器来控制事务
 *           @Bean
 *           public PlatformTransactionManager transactionManager(DataSource dataSource){
 *
 */
@ComponentScan({"com.demo.dao","com.demo.service"})
@Configuration
@EnableTransactionManagement
public class MainConfigOfTx {

    @Bean
    public DataSource dataSource() throws Exception {
        ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();
        comboPooledDataSource.setUser("root");
        comboPooledDataSource.setPassword("root");
        comboPooledDataSource.setDriverClass("com.mysql.cj.jdbc.Driver");
        comboPooledDataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test?serverTimezone=GMT%2B8");
        return comboPooledDataSource;
    }

    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource){
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
        return jdbcTemplate;
    }

    //注册事务管理器在容器中
    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource){
        return new DataSourceTransactionManager(dataSource);
    }
}
```



UserDao

```java
@Repository
public class UserDao {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    public void insert(){
        String sql = "insert into tbl_user(username,age) VALUES(?,?)";

        String username = UUID.randomUUID().toString().substring(0, 5);
        jdbcTemplate.update(sql,username,19);
    }
}
```



UserService

<span style="color:red">在方法上配置了@Transactional表示这个方法是一个事务方法,若方法发生了异常，则回滚</span>

```java
@Service
public class UserService {

    @Autowired
    private UserDao userDao;

    @Transactional
    public void insert(){
        userDao.insert();
        System.out.println("insert success...");
        int i = 10/0;
    }
}
```



测试类

```java
public class TxTest {

    ApplicationContext context = new AnnotationConfigApplicationContext(MainConfigOfTx.class);

    @Test
    public void test(){
        UserService bean = context.getBean(UserService.class);
        bean.insert();

    }
}
```



# 二、@EnableTransactionManagement



1）、@EnableTransactionManagement会利用TransactionManagementConfigurationSelector给容器中导入组件

导入两个组件

​		AutoProxyRegistrar

​		ProxyTransactionManagementConfiguration

2）、AutoProxyRegistrar给容器中注册了一个InfrastructureAdvisorAutoProxyCreator组件

​				InfrastructureAdvisorAutoProxyCreator ？

​						利用后置处理器机制在对象创建以后，包装对象，返回一个代理对象(增强器),代理对象执行方法利用拦截器链进行调用

3）、ProxyTransactionManagementConfiguration做了什么？

​				1、给容器中注册事务增强器

​							1）、事务增强器需要用到事务注解的信息，AnnotationTransactionAttributeSource解析事务注解

​							2）、事务拦截器：

​											TransactionInterceptor：保存了事务属性信息，事务管理器，

​											它是一个MethodInterceptor，在目标方法执行的时候：

​													执行拦截器链：

​													事务拦截器：

​															1）、先获取事务的相关属性

​															2）、再获取PlatformTransactionManager,如果事先没有添加指定任何transactionManager,最终会从容器中按照类型获取一个PlatformTransactionManager

​															3)、执行目标方法，如果异常，获取事务管理器，利用事务管理回滚操作；如果正常，利用事务管理器，提交事务

​					

