# 创建maven项目

1. 选择file->new->project

2. 选择maven

   ![image-20191202172233225](https://zsy0216.github.io/image/notes/springmvc/image-20191202172233225.png)

3. 根据提示完成创建。

# 添加pom依赖

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>4.3.14.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>4.3.14.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.2</version>
    </dependency>

    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>
</dependencies>
```

# 配置web.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         id="WebApp_ID" version="3.0">
    <display-name>Archetype Created Web Application</display-name>
    <!-- 配置springmvc的前端控制器 -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <welcome-file-list>
        <welcome-file>/myIndex.jsp</welcome-file>
    </welcome-file-list>

</web-app>
```



# 配置springmvc.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">

     <!-- 配置springmvc的基本控制器，并开启了对json数据格式的支持 -->
    <!-- mvc注解驱动：可以代替注解映射器和适配器的配置
         mvc注解驱动默认加载了很多的参数绑定，比如默认加载json转换解析器
         如果使用该注解驱动，就不用配置注解映射器和适配器  -->
    <mvc:annotation-driven/>

    <!-- 只需要扫描 com.demo 下的 @Controller，@Service, @Componet, 并注册成bean -->
    <context:component-scan base-package="com.learn" use-default-filters="false">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <!--<context:include-filter type="annotation" expression="org.springframework.stereotype.Service" />-->
        <!--<context:include-filter type="annotation" expression="org.springframework.stereotype.Component" />-->
    </context:component-scan>

    <!-- 视图解析器: 定义JSP文件的位置 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- 处理静态资源 -->
    <mvc:default-servlet-handler/>

    <!-- 将地址重定向："/"是访问的path，"/demoCtrl/index"是重定向后的path：
    定义无需Controller的url<->view直接映射-->
    <mvc:view-controller path="/" view-name="redirect:/demoCtrl/index"/>
</beans>
```

# 测试代码

目录结构：

![image-20191202175344542](https://zsy0216.github.io/image/notes/springmvc/image2.png)

TestController.java

```java
package com.learn;


import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class TestController {

    @RequestMapping("/test")
    public String testMvc(){
        return "test/mvc";
    }
}
```

mvc.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>testMvc</title>
</head>
<body>
<h1>test mvc success.</h1>
</body>
</html>
```



# 配置tomcat

点击打开配置窗口

![image-20191202175808281](https://zsy0216.github.io/image/notes/springmvc/image3.png)

点击+号，找到Tomcat，选择local

![image-20191202182202234](https://zsy0216.github.io/image/notes/springmvc/image4.png)

点击Server中的Configure配置本地tomcat服务器版本

![image-20191202175808281](https://zsy0216.github.io/image/notes/springmvc/image5.png)

点击Deployment下的+号里面的Artifact。

![image-20191202180208073](https://zsy0216.github.io/image/notes/springmvc/image7.png)

如图选择，点击ok。

![image-20191202180208073](https://zsy0216.github.io/image/notes/springmvc/image8.png)

之后在deployment下面配置项目路径。

apply， ok配置完成

# 启动

最后， 点击绿三角运行tomcat服务器

![image-20191202180208073](https://zsy0216.github.io/image/notes/springmvc/image-9.png)

出现以下界面表示完成springmvc的配置

![](https://zsy0216.github.io/image/notes/springmvc/image10.png)

然后输入继续在地址栏输入controller中定义的映射地址：

![image-20191202180208073](https://zsy0216.github.io/image/notes/springmvc/image11.png)