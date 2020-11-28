# 1. Dubbo介绍

代码示例：[Github](https://github.com/zsy0216/DubboDemo)

## 1.1 RPC

**Remote Procedure Call**：远程过程调用

![rpc](https://zsy0216.github.io/image/分布式架构/dubbo/1.png)

## 1.2 Dubbo架构

![dubbo](https://zsy0216.github.io/image/分布式架构/dubbo/2.png)

Subscribe 订阅；签署；赞成

Monitor 监听器；监控器

## 1.3 Dubbo在Zookeeper中注册信息的结构

![zookeeper](https://zsy0216.github.io/image/分布式架构/dubbo/3.png)

# 2. Dubbo使用

## 2.1 创建Common工程

> group id：com.joker.dubbo
>
> artifact id：dubbo-common
>
> package：jar

### 创建实体类

com.joker.dubbo.entity.Employee.java

```java
package com.joker.dubbo.entity;

import java.io.Serializable;

public class Employee implements Serializable {

    private static final long serialVersionUID = 1L;

    private Integer empId;
    private String empName;
    private Double salary;

    public Employee() {
    }

    public Employee(Integer empId, String empName, Double salary) {
        this.empId = empId;
        this.empName = empName;
        this.salary = salary;
    }
    // getter、setter略
}
```

### 创建远程调用的接口

package com.joker.dubbo.service.EmployeeRemoteService.java

```java
package com.joker.dubbo.service;

import com.joker.dubbo.entity.Employee;
import java.util.List;

public interface EmployeeRemoteService {
    List<Employee> getEmployeeByConditionRemote(Employee employee);
}
```



## 2.2 创建Provider工程

> group id：com.joker.dubbo
>
> artifact id：dubbo-provider
>
> package：war
>
> 依赖于：Common工程，使用其中的实体类和接口

### 依赖信息

pom.xml

```xml
<dependencies>
    <!-- 依赖于dubbo-common -->
    <dependency>
        <groupId>com.joker.dubbo</groupId>
        <artifactId>dubbo-common</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>cglib</groupId>
        <artifactId>cglib</artifactId>
        <version>2.2</version>
    </dependency>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.6.8</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>dubbo</artifactId>
        <version>2.5.5</version>
    </dependency>
    <dependency>
        <groupId>com.github.sgroschupf</groupId>
        <artifactId>zkclient</artifactId>
        <version>0.1</version>
    </dependency>
</dependencies>
```

### 配置log4j日志文件

log4j.properties

```properties
log4j.rootLogger=DEBUG,myConsole
log4j.appender.myConsole=org.apache.log4j.ConsoleAppender
log4j.appender.myConsole.Target=System.out
log4j.appender.myConsole.layout=org.apache.log4j.PatternLayout
log4j.appender.myConsole.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
```

### 配置web.xml

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
		  http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-dubbo.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    
</web-app>
```

### Spring配置文件中Dubbo的配置

spring-dubbo.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

    <!-- Dubbo配置：指定当前Web应用名称，便于在注册中心中注册 -->
    <dubbo:application name="dubbo-provider"/>

    <!-- Dubbo配置：指定注册中心的种类以及位置 -->
    <dubbo:registry protocol="zookeeper" address="192.168.252.128" port="2181"/>

    <!-- Dubbo配置：指定Dubbo的Provider本身监听的端口号 consumer访问时要使用这个端口号-->
    <dubbo:protocol name="dubbo" port="20880"/>

    <!-- 配置接口实现类的bean -->
    <bean id="employeeRemoteServiceImpl" class="com.joker.dubbo.service.impl.EmployeeRemoteServiceImpl"/>

    <!-- Dubbo配置：给远程方法接口指定实现类 -->
    <dubbo:service interface="com.joker.dubbo.service.EmployeeRemoteService" ref="employeeRemoteServiceImpl"/>

</beans>
```

### 创建远程调用接口的实现类

com.joker.dubbo.service.impl.EmployeeRemoteServiceImpl.java

```java
package com.joker.dubbo.service.impl;

import com.joker.dubbo.entity.Employee;
import com.joker.dubbo.service.EmployeeRemoteService;
import java.util.ArrayList;
import java.util.List;

/**
 * @PackageName:com.joker.dubbo.service.impl
 * @Date:2019/10/18 14:02
 * @Author: zsy
 */
public class EmployeeRemoteServiceImpl implements EmployeeRemoteService {
    @Override
    public List<Employee> getEmployeeByConditionRemote(Employee employee) {
        System.out.println("***provider***:" + employee);

        // 假数据用于数据测试
        List<Employee> empList = new ArrayList<>();
        empList.add(new Employee(111, "empName111", 111.11));
        empList.add(new Employee(222, "empName222", 222.22));
        empList.add(new Employee(333, "empName333", 333.33));
        return empList;
    }
}
```

### 注册简单测试

运行web项目，启动tomcat服务器，尝试连接Zookeeper，此时要确保服务器Zookeeper已启动

启动成功可以看到控制台日志打印心跳检查信息：

```shell
[2019-10-18 02:19:51,355] Artifact dubbo-provider:war exploded: Artifact is deployed successfully
[2019-10-18 02:19:51,355] Artifact dubbo-provider:war exploded: Deploy took 24,538 milliseconds
[DEBUG] 2019-10-18 14:20:01,235(31554) --> [RMI TCP Connection(192.168.252.128:2181)] org.apache.zookeeper.ClientCnxn$SendThread.readResponse(ClientCnxn.java:758): Got ping response for sessionid: 0x16ddd844d9a0000 after 2ms  
[DEBUG] 2019-10-18 14:20:11,241(41560) --> [RMI TCP Connection(192.168.252.128:2181)] org.apache.zookeeper.ClientCnxn$SendThread.readResponse(ClientCnxn.java:758): Got ping response for sessionid: 0x16ddd844d9a0000 after 2ms  
```

此时到服务器端启动Zookeeper客户端：

```shell
/opt/zookeeper3.4.9/bin/zkCli.sh

#启动后查看是否已经注册上provider的信息
[zk: localhost:2181(CONNECTED) 0] ls /
[animal, dubbo, zookeeper, fruits]
[zk: localhost:2181(CONNECTED) 1] ls /dubbo
[com.joker.dubbo.service.EmployeeRemoteService]
[zk: localhost:2181(CONNECTED) 2] ls /dubbo/com.joker.dubbo.service.EmployeeRemoteService
[configurators, providers]
[zk: localhost:2181(CONNECTED) 3] ls /dubbo/com.joker.dubbo.service.EmployeeRemoteService/providers
[dubbo%3A%2F%2F192.168.252.1%3A20880%2Fcom.joker.dubbo.service.EmployeeRemoteService%3Fanyhost%3Dtrue%26application%3Ddubbo-provider%26dubbo%3D2.5.5%26generic%3Dfalse%26interface%3Dcom.joker.dubbo.service.EmployeeRemoteService%26methods%3DgetEmployeeByConditionRemote%26pid%3D6364%26revision%3D1.0-SNAPSHOT%26side%3Dprovider%26timestamp%3D1571379571726]
[zk: localhost:2181(CONNECTED) 4] 
```

通过`ls /dubbo/com.joker.dubbo.service.EmployeeRemoteService/providers`我们看到下面注册的provider，通过Url解码得到内容：

```
dubbo://192.168.252.1:20880/com.joker.dubbo.service.EmployeeRemoteService?anyhost=true&application=dubbo-provider&dubbo=2.5.5&generic=false&interface=com.joker.dubbo.service.EmployeeRemoteService&methods=getEmployeeByConditionRemote&pid=6364&revision=1.0-SNAPSHOT&side=provider&timestamp=1571379571726
```

至此，证明Provider配置成功！

## 2.3 创建Consumer工程

> group id：com.joker.dubbo
>
> artifact id：dubbo-consumer
>
> package：war

### 依赖信息

pom.xml

```xml
<dependencies>
    <!-- 依赖于dubbo-common -->
    <dependency>
        <groupId>com.joker.dubbo</groupId>
        <artifactId>dubbo-common</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>cglib</groupId>
        <artifactId>cglib</artifactId>
        <version>2.2</version>
    </dependency>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.6.8</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>dubbo</artifactId>
        <version>2.5.5</version>
    </dependency>
    <dependency>
        <groupId>com.github.sgroschupf</groupId>
        <artifactId>zkclient</artifactId>
        <version>0.1</version>
    </dependency>
    <!-- spring-mvc -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>4.3.10.RELEASE</version>
    </dependency>
    <!-- jsp -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.1.3-b06</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

### 配置log4j日志配置文件

log4j.properties

```properties
log4j.rootLogger=DEBUG,myConsole
log4j.appender.myConsole=org.apache.log4j.ConsoleAppender
log4j.appender.myConsole.Target=System.out
log4j.appender.myConsole.layout=org.apache.log4j.PatternLayout
log4j.appender.myConsole.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
```

### 配置web.xml

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
		  http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">

    <servlet>
        <servlet-name>springDispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>springDispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

### 配置SpringMVC

spring-mvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- SpringMVC相关配置 -->
    <context:component-scan base-package="com.joker.dubbo.handler"/>
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    <mvc:annotation-driven/>
    <mvc:default-servlet-handler/>

    <!-- Dubbo配置：当前应用名称 -->
    <dubbo:application name="dubbo-consumer"/>

    <!-- Dubbo配置：指定Dubbo注册中心类型和位置 -->
    <dubbo:registry protocol="zookeeper" address="192.168.252.128" port="2181"/>

    <!-- Dubbo配置：引用Provider提供的可以远程调用的服务 -->
    <dubbo:reference id="employeeRemoteService" interface="com.joker.dubbo.service.EmployeeRemoteService"/>

</beans>
```

### 创建Handler类映射远程接口请求

com.joker.dubbo.handler.EmployeeHandler.java

```java
package com.joker.dubbo.handler;

import com.joker.dubbo.entity.Employee;
import com.joker.dubbo.service.EmployeeRemoteService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import java.util.List;

/**
 * @PackageName:com.joker.dubbo.handler
 * @Date:2019/10/18 14:51
 * @Author: zsy
 */
@Controller
public class EmployeeHandler {

    @Autowired
    EmployeeRemoteService employeeRemoteService;

    @RequestMapping("/get/emp/list")
    public String getEmpList() {
        Employee employee = new Employee(666, "empName666", 666.66);
        List<Employee> empList = employeeRemoteService.getEmployeeByConditionRemote(employee);
        for (Employee emp : empList) {
            System.out.println(emp);
        }
        return "success";
    }
}
```

### 创建返回成功页面

success.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
	<h1> Success !</h1>
</body>
</html>
```

### 注册简单测试

运行consumer的web项目，启动一个新的tomcat服务器，并设置不同的端口号；

同provider，启动完成后可以看到控制台日志打印心跳检查信息：

```shell
[DEBUG] 2019-10-18 16:21:14,947(31499) --> [RMI TCP Connection(192.168.252.128:2181)] org.apache.zookeeper.ClientCnxn$SendThread.readResponse(ClientCnxn.java:758): Got ping response for sessionid: 0x16ddd844d9a000b after 3ms  
[DEBUG] 2019-10-18 16:21:24,947(41499) --> [RMI TCP Connection(192.168.252.128:2181)] org.apache.zookeeper.ClientCnxn$SendThread.readResponse(ClientCnxn.java:758): Got ping response for sessionid: 0x16ddd844d9a000b after 1ms  
[DEBUG] 2019-10-18 16:21:34,949(51501) --> [RMI TCP Connection(192.168.252.128:2181)] org.apache.zookeeper.ClientCnxn$SendThread.readResponse(ClientCnxn.java:758): Got ping response for sessionid: 0x16ddd844d9a000b after 1ms  
```

此时到Zookeeper服务端查看服务注册信息：

```shell
[zk: localhost:2181(CONNECTED) 21] ls /dubbo/com.joker.dubbo.service.EmployeeRemoteService
[consumers, configurators, routers, providers]
[zk: localhost:2181(CONNECTED) 22]
```

这是可以看到Zookeeper注册中心不仅有providers，还有了新注册的consumers；

这时执行命令：`ls /dubbo/com.joker.dubbo.service.EmployeeRemoteService/consumers`

可以看到我们注册的consumer信息

```shell
[consumer%3A%2F%2F192.168.252.1%2Fcom.joker.dubbo.service.EmployeeRemoteService%3Fapplication%3Ddubbo-consumer%26category%3Dconsumers%26check%3Dfalse%26dubbo%3D2.5.5%26interface%3Dcom.joker.dubbo.service.EmployeeRemoteService%26methods%3DgetEmployeeByConditionRemote%26pid%3D13468%26revision%3D1.0-SNAPSHOT%26side%3Dconsumer%26timestamp%3D1571386846115]
```

通过URL解码可以看到：

```shell
consumer://192.168.252.1/com.joker.dubbo.service.EmployeeRemoteService?application=dubbo-consumer&category=consumers&check=false&dubbo=2.5.5&interface=com.joker.dubbo.service.EmployeeRemoteService&methods=getEmployeeByConditionRemote&pid=13468&revision=1.0-SNAPSHOT&side=consumer&timestamp=1571386846115
```

这里显示的是调用接口的消费者信息。

---

这时打开浏览器输出http://localhost:8081/consumer/get/emp/list  

注意：本项目中配置consumer的tomcat端口号为8081

可以看到成功页面返回！

同时看到tomcat运行控制台打印信息：

consumer端：

```shell
Employee{empId=111, empName='empName111', salary=111.11}
Employee{empId=222, empName='empName222', salary=222.22}
Employee{empId=333, empName='empName333', salary=333.33}
```

provider端

```shell
***provider***:Employee{empId=666, empName='empName666', salary=666.66}
```

证明远程方法调用成功，consumer调用了远程方法，并传递给provider一个Employee对象。

provider成功接收，并执行getEmployeeByConditionRemote()方法，返回一个Employee的List。

至此，consumer配置成功！

Dubbo+Zookeeper实现远程调用成功！

代码示例：[Github](https://github.com/zsy0216/DubboDemo)

 