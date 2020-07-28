> Filter：过滤器
>
> Listener：监听器
>
> Filter、Listener和Servlet是javaweb的三大组件。

# 概述

**监听器**是用于监听Web应用而实现了特定接口的Java类。监听器可以在事件发生前、后做一些有必要的处理。

  Servlet API提供了一系列的事件和事件监听接口。上层的servlet/JSP应用能够通过调用这些API进行事件驱动的开发。

  这里监听的所有事件都继承自java.util.EventObject对象。

　Servlet 中的监听器分类：

-   监听三个域对象(ServletContext、HttpSession、ServletRequest)的创建和销毁的三个监听器。
-   监听三个域对象的属性变更（添加、移除、替换）的三个监听器。
-   监听HttpSession中JavaBean的状态变换的监听（两个）。

---

**Filter**是拦截Request请求的对象： 在用户的请求访问资源前处理ServletRequest以及ServletResponse，它可以用于日志记录、解加密、Session检查、图像文件保护等。

# 一些概念

**context-param**：就是一些需要初始化的配置，放入context-param中，从而被监听器（这里特指org.springframework.web.context.ContextLoaderListener）监听，然后加载；

**监听器(listener)**：就是对项目起到监听的作用，它能感知到包括request(请求域)，session(会话域)和applicaiton(应用程序)的初始化和属性的变化；

**过滤器(filter)**：就是对请求起到过滤的作用，它在监听器之后，作用在servlet之前，对请求进行过滤；

**servlet**：就是对request和response进行处理的容器，它在filter之后执行，servlet其中的一部分就是controller层（标记为servlet_2），还包括渲染视图层(标记为servlet_3)和进入controller之前系统的一些处理部分(servlet_1)，另外我们把servlet开始的时刻标记为servlet_0，servlet结束的时刻标记为servlet_4。

**拦截器(interceptor)**：就是对请求和返回进行拦截，它作用在servlet的内部，具体来说有三个地方：

1）servlet_1和servlet_2之间，即请求还没有到controller层

2）servlet_2和servlet_3之间，即请求走出controller层次，还没有到渲染时图层

3）servlet_3和servlet_4之间，即结束视图渲染，但是还没有到servlet的结束

# Filter

Filter 中文的翻译是过滤器，是JavaWeb的三大组件之一。

Filter就像是一个一个哨卡，用户的请求需要经过Filter才能到达Servlet中进行处理。

可以定义多个过滤器。

