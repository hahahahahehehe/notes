# 一、dubbo配置文件实例

**本次实例是服务提供方UserService提供服务给服务消费方OderService消费**



## 1、导入相关jar

​		1）、dubbo的jar

​		2）、操作zookeeper的jar，2.6以下的使用zkClient的jar，2.6以上的使用curator-framework操作zookeeper

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>dubbo</artifactId>
    <version>2.6.2</version>
</dependency>
<!-- 注册中心使用的是zookeeper，引入操作zookeeper的客户端端 -->
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-framework</artifactId>
    <version>2.12.0</version>
</dependency>
```

## 2、服务提供方

1）、编写相关的bean

UserAddress.java

```java
package com.maven.dubbo.bean;

import java.io.Serializable;

/**
 * 用户地址
 * @author lfy
 *
 */
public class UserAddress implements Serializable {
	
	private Integer id;
    private String userAddress; //用户地址
    private String userId; //用户id
    private String consignee; //收货人
    private String phoneNum; //电话号码
    private String isDefault; //是否为默认地址    Y-是     N-否
    
    public UserAddress() {
		super();
		// TODO Auto-generated constructor stub
	}
    
	public UserAddress(Integer id, String userAddress, String userId, String consignee, String phoneNum,
			String isDefault) {
		super();
		this.id = id;
		this.userAddress = userAddress;
		this.userId = userId;
		this.consignee = consignee;
		this.phoneNum = phoneNum;
		this.isDefault = isDefault;
	}
	
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getUserAddress() {
		return userAddress;
	}
	public void setUserAddress(String userAddress) {
		this.userAddress = userAddress;
	}
	public String getUserId() {
		return userId;
	}
	public void setUserId(String userId) {
		this.userId = userId;
	}
	public String getConsignee() {
		return consignee;
	}
	public void setConsignee(String consignee) {
		this.consignee = consignee;
	}
	public String getPhoneNum() {
		return phoneNum;
	}
	public void setPhoneNum(String phoneNum) {
		this.phoneNum = phoneNum;
	}
	public String getIsDefault() {
		return isDefault;
	}
	public void setIsDefault(String isDefault) {
		this.isDefault = isDefault;
	}
}
```

2）、编写接口

UserService.java

```java
package com.maven.dubbo.service;


import com.maven.dubbo.bean.UserAddress;

import java.util.List;

/**
 * 用户服务
 * @author lfy
 *
 */
public interface UserService {
	
	/**
	 * 按照用户id返回所有的收货地址
	 * @param userId
	 * @return
	 */
	public List<UserAddress> getUserAddressList(String userId);
}
```

3）、编写实现类UserServiceImpl(真正暴露的服务)

```java
package com.maven.demo.service.impl;

import com.maven.dubbo.bean.UserAddress;
import com.maven.dubbo.service.UserService;

import java.util.Arrays;
import java.util.List;

public class UserServiceImpl implements UserService {

	@Override
	public List<UserAddress> getUserAddressList(String userId) {
		System.out.println("UserServiceImpl.....old...");
		// TODO Auto-generated method stub
		UserAddress address1 = new UserAddress(1, "北京市昌平区宏福科技园综合楼3层", "1", "李老师", "010-56253825", "Y");
		UserAddress address2 = new UserAddress(2, "深圳市宝安区西部硅谷大厦B座3层（深圳分校）", "1", "王老师", "010-56253825", "N");
		/*try {
			Thread.sleep(4000);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}*/
		return Arrays.asList(address1,address2);
	}

}
```

4）、编写配置文件(provider.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd
		http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

    <!-- 1、指定当前服务/应用的名字（同样的服务名字相同，不要和别的服务同名） -->
    <dubbo:application name="dubbo-provider"></dubbo:application>

    <!-- 2、指定注册中心的位置 -->
    <!-- <dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry> -->
    <dubbo:registry protocol="zookeeper" address="127.0.0.1:2181"></dubbo:registry>

    <!-- 3、指定通信规则（通信协议？通信端口） -->
    <dubbo:protocol name="dubbo" port="20882"></dubbo:protocol>

    <!-- 4、暴露服务   ref：指向服务的真正的实现对象 -->
    <dubbo:service interface="com.maven.dubbo.service.UserService"
                   ref="userServiceImpl01" timeout="1000" version="1.0.0">
        <dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method>
    </dubbo:service>

    <!--统一设置服务提供方的规则  -->
    <dubbo:provider timeout="1000"></dubbo:provider>


    <!-- 服务的实现 -->
    <bean id="userServiceImpl01" class="com.maven.demo.service.impl.UserServiceImpl"></bean>


    <!--<dubbo:service interface="com.atguigu.gmall.service.UserService"
                   ref="userServiceImpl02" timeout="1000" version="2.0.0">
        <dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method>
    </dubbo:service>
    <bean id="userServiceImpl02" class="com.atguigu.gmall.service.impl.UserServiceImpl2"></bean>-->

    <!-- 连接监控中心 -->
    <!--<dubbo:monitor protocol="registry"></dubbo:monitor>-->

</beans>
```

5）、编写启动类

```java
package com.maven.demo;

import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.io.IOException;

/**
 * Hello world!
 *
 */
public class App 
{
    public static void main( String[] args ) throws IOException {

        ClassPathXmlApplicationContext ioc = new ClassPathXmlApplicationContext("provider.xml");
        ioc.start();
        System.out.println("服务已启动...");
        System.in.read();  //将服务停在光标处，输入任意字符退出
    }
}
```



## 3、服务消费方

1）、编写bean(和服务方传过来的bean要一样)

UserAddress

```java
package com.maven.dubbo.bean;

import java.io.Serializable;

/**
 * 用户地址
 * @author lfy
 *
 */
public class UserAddress implements Serializable {
	
	private Integer id;
    private String userAddress; //用户地址
    private String userId; //用户id
    private String consignee; //收货人
    private String phoneNum; //电话号码
    private String isDefault; //是否为默认地址    Y-是     N-否
    
    public UserAddress() {
		super();
		// TODO Auto-generated constructor stub
	}
    
	public UserAddress(Integer id, String userAddress, String userId, String consignee, String phoneNum,
			String isDefault) {
		super();
		this.id = id;
		this.userAddress = userAddress;
		this.userId = userId;
		this.consignee = consignee;
		this.phoneNum = phoneNum;
		this.isDefault = isDefault;
	}
	
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getUserAddress() {
		return userAddress;
	}
	public void setUserAddress(String userAddress) {
		this.userAddress = userAddress;
	}
	public String getUserId() {
		return userId;
	}
	public void setUserId(String userId) {
		this.userId = userId;
	}
	public String getConsignee() {
		return consignee;
	}
	public void setConsignee(String consignee) {
		this.consignee = consignee;
	}
	public String getPhoneNum() {
		return phoneNum;
	}
	public void setPhoneNum(String phoneNum) {
		this.phoneNum = phoneNum;
	}
	public String getIsDefault() {
		return isDefault;
	}
	public void setIsDefault(String isDefault) {
		this.isDefault = isDefault;
	}
}
```

2）、编写接收服务的接口，此接口必须和服务暴露接口要一模一样，即内容和包名都要一模一样,也需要在和服务提供者同一个包下com.maven.dubbo.service

UserService

```java
package com.maven.dubbo.service;


import com.maven.dubbo.bean.UserAddress;

import java.util.List;

/**
 * 用户服务
 * @author lfy
 *
 */
public interface UserService {
	
	/**
	 * 按照用户id返回所有的收货地址
	 * @param userId
	 * @return
	 */
	public List<UserAddress> getUserAddressList(String userId);
}
```

3）、编写消费者的接口和实现类

```java
package com.maven.dubbo.service;


import com.maven.dubbo.bean.UserAddress;

import java.util.List;

public interface OrderService {
	
	/**
	 * 初始化订单
	 * @param userId
	 */
	public List<UserAddress> initOrder(String userId);

}
```

```java
package com.maven.dubbo.service.impl;

import java.util.List;

import com.maven.dubbo.bean.UserAddress;
import com.maven.dubbo.service.OrderService;
import com.maven.dubbo.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

/**
 * 1、将服务提供者注册到注册中心（暴露服务）
 * 		1）、导入dubbo依赖（2.6.2）\操作zookeeper的客户端(curator)
 * 		2）、配置服务提供者
 * 
 * 2、让服务消费者去注册中心订阅服务提供者的服务地址
 * @author lfy
 *
 */
@Service
public class OrderServiceImpl implements OrderService {

	@Autowired
	UserService userService;
	@Override
	public List<UserAddress> initOrder(String userId) {
		// TODO Auto-generated method stub
		System.out.println("用户id："+userId);
		//1、查询用户的收货地址
		List<UserAddress> addressList = userService.getUserAddressList(userId);
		for (UserAddress userAddress : addressList) {
			System.out.println(userAddress.getUserAddress());
		}
		return addressList;
	}
}
```



4）、编写配置文件

consumer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd
		http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
    <context:component-scan base-package="com.maven.dubbo.service.impl"></context:component-scan>


    <dubbo:application name="dubbo-consumer"></dubbo:application>

    <dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry>

    <!--  配置本地存根-->

    <!--声明需要调用的远程服务的接口；生成远程服务代理  -->
    <!--
        1）、精确优先 (方法级优先，接口级次之，全局配置再次之)
        2）、消费者设置优先(如果级别一样，则消费方优先，提供方次之)
    -->
    <!-- timeout="0" 默认是1000ms-->
    <!-- retries="":重试次数，不包含第一次调用，0代表不重试-->
    <!-- 幂等（设置重试次数）【查询、删除、修改】、非幂等（不能设置重试次数）【新增】 -->
    <dubbo:reference interface="com.maven.dubbo.service.UserService"
                     id="userService" timeout="5000" retries="3" version="*">
        <!-- <dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method> -->
    </dubbo:reference>

    <!-- 配置当前消费者的统一规则：所有的服务都不检查 -->
    <!--<dubbo:consumer check="false" timeout="5000"></dubbo:consumer>

    <dubbo:monitor protocol="registry"></dubbo:monitor>-->
    <!-- <dubbo:monitor address="127.0.0.1:7070"></dubbo:monitor> -->
</beans>
```



5）、编写启动类

```java
package com.maven.dubbo;

import com.maven.dubbo.service.OrderService;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.io.IOException;

/**
 * Hello world!
 *
 */
public class App 
{
    public static void main( String[] args ) throws IOException {

        ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("consumer.xml");

        OrderService orderService = applicationContext.getBean(OrderService.class);

        orderService.initOrder("1");
        System.out.println("调用完成....");
        System.in.read();
    }
}

```



## 4、改良

因为provider和consumer都需要写一些公共的接口，当提供的接口很多事，两边重复写会很麻烦，所以我们可以把公共类单独放倒一个项目，然后导入那个项目就行

1、新建一个项目放公共类，将bean和service接口都放进去

![image-20200117133947208](D:\notes\notes\typora\dubbo\images\image-20200117133947208.png)



2、在provider和consumer都导入这个jar

provider-pom.xml

```xml
<dependencies>
    <dependency>
        <groupId>com.maven.dubbo</groupId>
        <artifactId>gmallinterface</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>

    <!-- 引入dubbo -->
    <!-- https://mvnrepository.com/artifact/com.alibaba/dubbo -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>dubbo</artifactId>
        <version>2.6.2</version>
    </dependency>
    <!-- 注册中心使用的是zookeeper，引入操作zookeeper的客户端端 -->
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>2.12.0</version>
    </dependency>
</dependencies>
```

同理consumer.xml也引入这个jar，就能统一公用接口了



# 二、springboot整合dubbo

## 1、公共接口应用

​			gmallinterface   见上

## 2、服务提供者

1）、导入相关依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.maven.dubbo</groupId>
    <artifactId>springboot-dubbo-provider</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot-dubbo-provider</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>com.maven.dubbo</groupId>
            <artifactId>gmallinterface</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>0.2.0</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

2）、编写需要暴露的服务UserServiceImpl

此处用到的@Service是dubbo的注解，用来暴露服务的，并不是spring的注解

```java
package com.maven.dubbo.service.impl;

import com.alibaba.dubbo.config.annotation.Service;
import com.maven.dubbo.bean.UserAddress;
import com.maven.dubbo.service.UserService;
import org.springframework.stereotype.Component;

import java.util.Arrays;
import java.util.List;

@Service
@Component
public class UserServiceImpl implements UserService {
    @Override
    public List<UserAddress> getUserAddressList(String userId) {
        UserAddress address1 = new UserAddress(1, "北京市昌平区宏福科技园综合楼3层", "1", "李老师", "010-56253825", "Y");
        UserAddress address2 = new UserAddress(2, "深圳市宝安区西部硅谷大厦B座3层（深圳分校）", "1", "王老师", "010-56253825", "N");
        return Arrays.asList(address1,address2);
    }
}
```



3）、配置dubbo(application.properties)

```prope
dubbo.application.name=springboot-dubbo-provider
dubbo.registry.address=zookeeper://127.0.0.1:2181
dubbo.protocol.name=dubbo
dubbo.protocol.port=20880
```



4）、启动类

@EnableDubbo 用于开启基于注解的dubbo服务

```java
package com.maven.dubbo;

import com.alibaba.dubbo.config.spring.context.annotation.DubboComponentScan;
import com.alibaba.dubbo.config.spring.context.annotation.EnableDubbo;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@EnableDubbo
@DubboComponentScan({"com.maven.dubbo.service.impl"})
public class SpringbootDubboProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootDubboProviderApplication.class, args);
    }
}
```



## 3、服务消费者

1）、导入jar

​		导入的jar和provider一样，见provider的pom.xml



2）、编写消费类OrderServiceImpl

@Reference 是dubbo中调用远程服务的注解

```java
package com.maven.dubbo.service.impl;

import com.alibaba.dubbo.config.annotation.Reference;
import com.maven.dubbo.bean.UserAddress;
import com.maven.dubbo.service.OrderService;
import com.maven.dubbo.service.UserService;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class OrderServiceImpl implements OrderService {

    @Reference
    private UserService userService;

    @Override
    public List<UserAddress> initOrder(String userId) {
        List<UserAddress> userAddressList = userService.getUserAddressList(userId);
        return userAddressList;
    }
}
```



3）、配置文件(application.properties)

```prop
dubbo.application.name=springboot-dubbo-consumer
dubbo.registry.address=zookeeper://127.0.0.1:2181
server.servlet.context-path=/dubbo
```



4）、启动类

```java
package com.maven.dubbo;

import com.alibaba.dubbo.config.spring.context.annotation.DubboComponentScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringbootDubboConsumerApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootDubboConsumerApplication.class, args);
    }
}
```

