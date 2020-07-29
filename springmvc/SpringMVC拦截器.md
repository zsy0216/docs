# 拦截器的介绍

interceptor中文翻译为拦截器。

> Spring MVC中的拦截器（Interceptor）类似于Servlet中的过滤器（Filter），它主要用于拦截用户请求并作相应的处理。例如通过拦截器可以进行权限验证、记录请求信息的日志、判断用户是否登录等。

要使用Spring MVC中的拦截器，就需要对拦截器类进行定义和配置。通常拦截器类可以通过两种方式来定义。

1. 通过实现HandlerInterceptor接口，或继承HandlerInterceptor接口的实现类（如HandlerInterceptorAdapter）来定义。

2. 通过实现WebRequestInterceptor接口，或继承WebRequestInterceptor接口的实现类来定义。

# 实现拦截器

这里示范通过实现HandlerInterceptor接口的方式来实现拦截器：

```java
package com.learn.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Date;

public class TestInteceptor implements HandlerInterceptor {

    /**
     * 在业务处理器处理请求之前被调用
     * 如果返回false
     * 从当前的拦截器往回执行所有拦截器的afterCompletion(),再退出拦截器链
     * 如果返回true
     * 执行下一个拦截器,直到所有的拦截器都执行完毕
     * 再执行被拦截的Controller
     * 然后进入拦截器链,
     * 从最后一个拦截器往回执行所有的postHandle()
     * 接着再从最后一个拦截器往回执行所有的afterCompletion()
     */
    @Override
    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
        System.out.println("preHandle(), 在访问controller方法之前被调用");
        return true;
    }

    /**
     * 在业务处理器处理请求执行完成后,生成视图之前执行的动作
     * 可在modelAndView中加入数据，比如当前时间
     */
    @Override
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle(), 在访问Controller之后，访问视图之前被调用,这里可以注入一个时间到modelAndView中，用于后续视图显示");
        modelAndView.addObject("date", "由拦截器生成的时间:" + new Date());
    }

    /**
     * 在DispatcherServlet完全处理完请求后被调用,可用于清理资源等
     * <p>
     * 当有拦截器抛出异常时,会从当前拦截器往回执行所有的拦截器的afterCompletion()
     */
    @Override
    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
        System.out.println("afterCompletion(), 在访问视图之后被调用");
    }
}
```

# 配置拦截器

需要在springmvc.xml配置文件中对拦截器拦截的请求进行配置。

如果要拦截其他路径：
/** 拦截所有
/category/** 拦截/category路径下的所有；

`springmvc.xml`

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/test"/>
        <bean class="com.learn.interceptor.TestInteceptor"/>
    </mvc:interceptor>
    <!-- 当设置多个拦截器时，先按顺序调用 preHandle 方法，然后逆序调用每个拦截器的 postHandle 和 afterCompletion  方法 -->
</mvc:interceptors>
```

# 修改测试的视图

该视图是[创建springmvc的maven项目](https://github.com/zsy0216/LearnNotes/blob/master/SpringMVC/%E5%88%9B%E5%BB%BAspringmvc%E7%9A%84maven%E9%A1%B9%E7%9B%AE.md)时的测试视图，文件路径为：`/WEB-INF/views/test/mvc.jsp`;

该拦截器拦截的请求就是跳转到该页面的那个请求，即

```java
@RequestMapping("/test")
public String testMvc() {
    return "test/mvc";
}
```

`mvc.jsp`

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>testMvc</title>
</head>
<body>
<h1>test mvc success.</h1>

<p>${date}</p>
</body>
</html>

```

# 测试

运行项目后在浏览器地址栏输入对应test的访问地址，这里是：`http://localhost:8080/sm/test`,sm是我的虚拟项目名，含义是springmvc。

![](https://raw.githubusercontent.com/zsy0216/image/master/notes/20191208215105.png)

![](https://raw.githubusercontent.com/zsy0216/image/master/notes/20191208215143.png)

