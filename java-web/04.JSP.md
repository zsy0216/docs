> 找到一个详细的https://github.com/kickgod/Rear-End/tree/master/Java/JavaJSP

# 1    JSP简介

​	JSP全名为Java Server Pages，中文名叫java服务器页面，其根本是一个简化的Servlet设计，它是由Sun Microsystems公司倡导、许多公司参与一起建立的一种动态网页技术标准。JSP技术有点类似ASP技术，它是在传统的网页HTML（标准通用标记语言的子集）文件(.htm,.html)中插入Java程序段(Scriptlet)和JSP标记(tag)，从而形成JSP文件，后缀名为(*.jsp)。 用JSP开发的Web应用是跨平台的，既能在Linux下运行，也能在其他操作系统上运行。

## 1.1原理

​	浏览器发起请求，请求JSP，请求被Tomcat服务器接收，执行JSPServlet，将请求的JSP文件转义成为对应的java文件（即Servlet），然后执行转义好的java文件。

​	请求JSP时，服务器并不是直接访问JSP，而是访问转义后的Servlet。服务器并不对JSP文件操作。

## 1.2 jsp的路径问题

​	资源访问时，可以使用相对路径和绝对路径，也可以使用jsp自带的全局路径声明

```jsp
<%
String path = request.getContextPath();
String basePath = request.getScheme() + "://"
+ request.getServerName() + ":" + request.getServerPort()
+ path + "/";
%>
<base href="<%=basePath%>">
```

作用：

​	给资源前面添加项目路径,相当于： http://127.0.0.1:8080/虚拟项目名/

# 2    JSP页面元素指令

作用：用于配置JSP页面，导入资源文件；

格式：`<%@ 指令名称 属性名1=属性值1 属性名2=属性值2 ... %>`

## 2.1  page指令 

用于配置JSP页面

属性：

- contentType：

  - 设置响应体的MIME类型以及字符集；

  - 设置当前jsp页面的编码（只能是高级的IDE才能生效，低级工具需要使用pageEncoding属性来设置当前页面的字符集编码）

- pageEncoding：指定JSP页面的编码方式；

- buffer：缓冲区大小，out对象是输出流，需要使用缓冲区，默认值：8kb；

- language：指定JSP页面使用的脚本语言，默认值：java；

- import：声明转义的java文件要导入的包，不同的包使用逗号隔开，无默认值；

- errorPage：设置当前页面发生异常后，会自动跳转到指定的错误页面；

- isErrorPage：标识当前页面是否是错误页面；

  - true：是，可以使用内置对象exception
  - false：否，默认值。不可以使用内置对象exception

```jsp
<%@ page import="java.util.*, java.lang.*"%>
<%@ page contentType="text/html;charset=utf-8" language="java" %>
```

## 2.2   include指令

包含其他页面，复用jsp页面，用于导入页面的资源文件

- 语法:`<%@ include file=”top.jsp” %>`

inlcude动作：

​	语法：`<jsp:include page=”url” flush=”true|false”/>`

### JSP的静态引入

```jsp
<%@ include file="要引入的jsp文件的相对路径" %>
```

特点：

​	会将引入的jsp文件和当前jsp文件转义成一个java（Servlet）文件使用。在网页中也就显示了合并后的显示效果。

注意：

​	静态引入的jsp文件不会单独转义成java（Servlet）文件。

​	当前文件和静态引入的jsp文件中不能使用java代码块去声明同名的变量。

### JSP的动态引入

```jsp
<jsp:include page="要引入的jsp文件的相对路径"></jsp:include>
```

特点：

​	会将引入的jsp文件单独转义，在当前文件转义好的java文件中调用引入的jsp文件的转义文件。在网页中显示合并后的显示效果。

注意：

​	动态引入允许文件中声明同名变量。

## 2.3 taglib指令

用于导入资源，如标签库，JSTL。

- 语法: `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`
  - prefix：前缀，自定义；

# 3 JSP的注释

## 3.1 html的注释

只能注释html代码片段；

- `<!-- html注释 --> ` ，客户端可见，

会被转义，也会被发送，但是不会被浏览器执行

## 3.2 JSP的注释

可以注释所有；

- `<%-- html注释 --%>` 客户端不可见

## 3.3 JSP脚本注释

只能注释jsp脚本段；

​	//单行注释

​	/**/多行注释

# 4.JSP的脚本

## 4.1 JSP的局部代码块

在JSP页面中执行的java代码，在service方法中；

特点：

​	局部代码块中声明的java代码会被原样转义到jsp对应的Servlet文件中的_JspService方法中，代码块中声明的变量都是局部变量。

缺点：

​	使用局部代码块在jsp中进行逻辑判断，书写麻烦，阅读困难。

开发：

​	Servlet进行请求逻辑处理，使用jsp进行页面展现。

语法：

```jsp
<% 
	//java代码 
	...
%> 
```

## 4.2 JSP的全局代码块

定义的java代码，在jsp转换后的java类中的成员位置；

```jsp
<%!
    //全局代码
%>
```

特点：

​	声明的java 代码作为全局代码转义到对应的Servlet类中。

注意：

​	全局代码块声明的代码需要在局部代码块中调用。

## 4.3 JSP的脚本段

输出内容到页面上；

```jsp
<%= 变量名或方法名 %>
```

特点：

​	帮助我们快速的获取变量或者是方法的返回值，作为数据响应给浏览器。



# 5 JSP内置对象

## 5.1   什么是内置对象

jsp文件在转义成其对应的Servlet文件的时候自动生成的并声明的对象，在jsp页面直接使用即可。

注意：内置对象在jsp页面中，不能在全局代码块中使用。

**常见的内置对象及其真实类型：**

| 变量名      | 真实类型            | 作用                                                |
| ----------- | :------------------ | :-------------------------------------------------- |
| pageContext | PageContext         | 域对象：当前页面共享数据，还可以获取其他8个内置对象 |
| request     | HttpServletRequest  | 域对象：一次请求访问的多个资源（转发）              |
| session     | HttpSession         | 域对象：一次会话的多次请求间                        |
| application | ServletContext      | 域对象：所有用户间共享数据                          |
| response    | HttpServletResponse | 响应对象                                            |
| page        | Object              | 当前页面(Servlet)对象，this                         |
| out         | JspWriter           | 输出对象，数据输出到页面上                          |
| config      | ServletConfig       | Servlet的配置对象                                   |
| exception   | Throwable           | 异常对象                                            |

## 5.2   out对象

​	字符输出流对象，可以将数据输出到页面上，jsp内部使用。

​	带有缓冲区的响应对象，效率高于response对象。

​	out对象是JspWriter类的实例，是向客户端输出内容常用的对象。

常用方法：

* void println() 向客户端打印字符串
* void clear() 清除缓冲区的内容，如果在flush之后调用会抛出异常
* void clearBuffer() 清除缓冲区内容，且在flush之后调用不会抛出异常
* void flush() 将缓冲区的内容输出到客户端
* int getRemaining() 返回缓冲区还剩多少可用
* int getBufferSize() 返回缓冲区以字节数的大小，如不设缓冲区则为0
* boolean isAutoFlush() 返回缓冲区满时，是自动清空还是抛出异常
* void close() 关闭输出流

## 5.3   request对象

​	封存当前请求数据的对象，由Tomcat服务器创建。一次请求内有效

​	客户端的请求信息被封装在reque对象中，通过它才能了解到客户的需求，然后做出响应。它是HttpServletRequest类的实现。request对象具有请求域，即完成客户端的请求之前，该对象一直有效。

常用方法：

* String getParameter(String name) 返回name指定参数的参数值

* String[] getPatameterValues(String name) 返回包含参数name的所有值的数组

* void setAttribute(String,Object) 存储此请求中的属性

* object getAttribute(String name) 返回指定属性的属性值

* String getContentType() 得到请求体的MIME类型

* String getProtocol() 返回请求用的协议类型及版本号

* String getServerName() 返回接受请求的服务器主机号

* Int getServerPort() 返回服务器接收此请求所用的端口号

* String getCharacterEncoding() 返回字符编码方式

* void setCharacterEncoding() 设置请求的字符编码方式

* int getContentLength() 返回请求体的长度

* String getRemoteAddr() 返回发送此请求的客户端IP地址

* String getRealPath(String path) 返回一虚拟路径的真实路径

* String getContextPath() 返回上下文路径

## 5.4   response对象

​	响应对象，用来响应请求处理结果给浏览器的对象。设置响应头，重定向。

​	response对象包含了响应客户请求的有关信息，但在JSP中很少直接使用到它。它是HttpServletResponse类的实例。response对象具有页面作用域，即访问一个页面时，该页面内的response对象只能对这次访问有效，其他页面的response对象对当前页面无效。

常用方法：

* String getCharacterEncoding() 返回响应用的是哪种字符编码

* void setContentType(String type) 设置响应的MIME类型

* PrintWriter getWriter() 返回可以向客户端输出字符的一个对象（主意比较：PrintWriter与内置out对象的区别，response输出的内容会先于out，与位置无关。）

* sendRedirect(java.lang.String location) 重新定向客户端的请求

## 5.5   session对象

​	用来存储用户的不同请求的共享数据，作用域为一次会话。

​	session就是一次会话，保存在服务器内存中的，每个session对象对应一个用户。

常用方法：

* long getCreationTime() ：返回session 创建时间

* public String getId() ：返回session创建时JSP引擎为它设的唯一ID号

* public void setAttribute(String name,Object value) ：使用指定名称将对象绑定到此会话

* public Object getAttribute(String name) ：返回此会话中的指定名称绑定在一起的对象，如果没有对象绑定在该名称下，则返回null

* String[] getValueNames() ：返回一个包含此session中所有可用属性的数组

* int getMaxInactiveInterval() ：返回两次请求间隔多长时间此session被取消（单位秒）

## 5.6   application对象

​	也就是ServletContext对象，一个项目只有一个。存储用户共享数据的对象，以及完成其他操作。项目内有效。

​	application对象实现了用户之间数据的共享，可存放全局变量。它开始于服务器的启动，终止于服务器的关闭。在用户的前后连接或不同用户之间的连接中，都可以对application对象的同一属性进行操作。在任何地方对application对象属性的操作，都将影响到其他用户对此的访问。Application对象是ServletContext类的实例。

常用方法

*  public void setAttribute(String name,Object value) 使用指定名称将对象绑定到此会话 

*  public Object getAttribute(String name) 返回与此会话中的指定名称绑定在一起的对象，如果没有对象绑定在该名称下，则返回null

*  Enumeration getAttributeNames() 返回所有可用属性名的枚举

*  String getServerInfo() 返回JSP（servlet）引擎名及版本号

## 5.7   page 对象

​	page对象就是指向当前JSP页面本身，相当于java中的this指针，它是java.lang.Object类的实例。

常用方法：

*  class getClass() 返回此Object的类

*  int hashCode() 返回此Object 的hash码

*  boolean equals(Object obj) 判断此Object是否和指定的Object对象相等

*  void copy(Object obj) 把此Object拷贝到指定的Object对象中

*  Object clone() 克隆此Object对象

*  String toString() 把此Object对象转换成String类的对象

*  void notify() 唤醒一个等待的线程

*  void notifyAll() 唤醒所有等待的线程

*  void wait(int timeout) 使一个线程处于等待直到timeout结束或被唤醒

*  void wait()  使一个线程处于等待直到被唤醒

## 5.8   pageContext

​	页面上下文对象，封存了其他内置对象。封存了当前jsp的运行信息。每个jsp文件单独拥有一个pageContext对象。作用域为当前页面。

​	pageContext对象提供了对JSP页面内所有的对象及名字空间的访问，它可以访问到本页所在的session，也可以取本页面所在的application的某一属性值。相当于页面中所有功能的集大成者。pageContext对象的本类名也叫pageContext。

常用方法：

*  JspWriter getOut() 返回当前客户端响应被使用的JspWriter流（out）

*  HttpSession getSession() 返回当前页中的HttpSession对象（session）

*  Object getPage() 返回当前页的Object对象（page）

*  ServletRequest getRequest() 返回当前页的ServletRequest对象（request）

*  ServletResponse getResponse() 返回当前页的ServletResponse对象（response）

*  void setAttribute(String name,Object attribute) 设置属性及属性值

*  object getAttribute(String name,int scope) 在指定范围内取属性的值

*  int getAttributeScope(String name) 返回某属性的作用范围

*  void farward(String relativeUrlPath) 使当前页面重导到另一页面

*  void include(String relativeUrlPath) 在当前位置包含另一文件

## 5.9   config对象

​	也就是ServletConfig，主要用来获取web.xml中的配置数据，完成一些初始化数据的读取。

​	config对象是在一个Servlet初始化时，JSP引擎向它传递信息用的，此信息包括Servlet初始化时所要用到的参数（通过属性名和属性值构成）以及服务器的有关信息（通过传递一个ServletContext对象）。

常用方法：

*  ServletContext getServletContext() 返回含有服务器相关信息的ServletContext对象

*  String getInitParameter(String name) 返回初始化参数的值

*  Enumeration getInitParameterNames() 返回Servlet初始化所需所有参数的枚举

## 5.10  exception对象

​	使用此对象，需要在page指令中开启该属性，把isErrorPage设为true。

​	exception对象是一个异常对象，当一个页面在运行过程中发生了异常，就产生这个对象。如果一个JSP页面要应用此对象，就必须把isErrorPage设为true，否则无法编译。他实际上是java.lang.Throwable的对象。

常用方法：

*  String getMessage() 返回描述异常的信息
*  String toString() 返回关于异常的简短描述消息
*  void printStackTrace() 显示异常及其栈轨迹
*  Throwable FillInStackTrace() 重写异常的执行栈轨迹

## 5.11 四个作用域对象

| 域对象      | 作用域                                                       |
| ----------- | ------------------------------------------------------------ |
| pageContext | 当前页面，解决在当前页面内的数据共享问题。获取其他内置对象。 |
| request     | 一次请求，一个请求的Servlet的数据共享；通过请求转发，将数据流转给下一个Servlet。 |
| session     | 一次会话，一个用户的不同请求的数据共享；将数据从一次请求流转给其他请求。 |
| application | 项目内，不同用户的数据共享问题。将数据从一个用户流转其他用户。 |

作用：数据流转。

# 7    Javabean

## 7.1   简介

​	Javabeans就是符合某种特定的规范的java类，使用javabeans的好处是解决代码重复编写，减少代码冗余，功能区分明确，提高了代码的维护性。

## 7.2   设计原则

​      公有类，无参的公有构造方法，属性私有，getter和setter方法

## 7.3   Jsp动作

​	JSP动作元素（action elements），动作元素为请求处理阶段提供信息。动作元素遵循XML元素的语法，有一个包含元素名的开始标签，可以有属性、可选的内容、与开始标签匹配的结束标签。

1. 第一类与存取JavaBean有关

   <jsp:useBean> <jsp:setProperty> <jsp:getProperty>

2. 第二类是JSP1.2就开始有的基本元素，包括6个动作元素

   <jsp:include> <jsp:forward> <jsp:param> <jsp:plugin> <jsp:params> <jsp:fallback>

3. 第三类是JSP2.0新增加的元素，主要与JSP Document有关，包括6个元素

   <jsp:root> <jsp:declaration> <jsp:scriptlet> <jsp:expression> <jsp:text> <jsp:output>

4. 第四类是JSP2.0新增加的动作元素，主要用来动态生成XML元素标签的值，包括3个动作

   <jsp:attribute> <jsp:body> <jsp:element>

5. 第五类是JSP2.0新增的动作元素，主要是用在Tag File中，包括2个元素

   <jsp:invoke> <jsp:dobody>

## 7.4   Javabean的作用域范围

* page ：仅在当前页面有效

* request ：可以通过HTTPRequest.getAttribute()方法取得JavaBean对象

* session ：可以通过HTTPSession.getAttribute()方法取得JavaBean对象

* application ：可以通过application.getAttribute()方法取得JavaBean对象

# 8 页面转发（forward标签）

```jsp
<jsp:forward page="要转发的jsp文件的相对路径"></jsp:forward>
```

特点：

- 一次请求，地址栏信息不改变。

注意：

- 在转发标签的两个标签中间除了写<jsp:param name="str" value="aaa" />子标签外，写其他任意字符都会报错。
- <jsp:param name="str" value="aaa" />，name属性为附带的数据的键名，value为附带的数据内容。
- 注意：会将数据以？的形式拼接在转发路径的后面。

在JSP页面中执行的表达式：

- 语法：    <%=表达式%>  

- 注意：表达式不以分号结束。

# 9 EL表达式

## 概念：

Expression Language 表达式语言；

## 作用：

替换和简化jsp页面中java代码的编写；

## 语法：

${表达式}

注意：jsp默认支持EL表达式；如果要忽略el表达式，

- 可以设置page指令中的属性`isELIgnored="true"`

- 也可以使用转义字符：`\${表达式}` 只忽略当前el表达式；

## 使用：

### 运算：

- 算术运算符：+  -  *  /(div)  %(mod)
- 比较运算符：>  <  >=  <=  ==  !=
- 逻辑运算符：&&(and)   ||(or)   !(not)
- 空运算符：empty  ${empty str/list/array}  :arrow_right:可以用于判断字符串、集合、数组对象是否为null且长度是否为0;

### 获取值：

1. el表达式只能从域对象中获取值

2. 语法：

   1. ${域名称.键名}：从指定域中获取指定键名的值

      域名称对应域对象

      | 域名称           | 域对象                      |
      | ---------------- | --------------------------- |
      | pageScope        | pageContext                 |
      | requestScope     | request                     |
      | sessionScope     | session                     |
      | applicationScope | application(ServletContext) |

   2. $(键名)：表示依次从最小的域中查找是否有该键对应的值，直到找到为止；

3. 获取对象、List集合、Map集合的值：

   1. 对象：${域名称.键名.属性名}，本质上调用对象的getter方法；

   2. List集合：${域名称.键名[索引]}；
   3. Map集合：${域名称.键名["key名称"]}或${域名称.键名.key名称}

## 隐式对象：

el表达式中有11个隐式对象；

- pageContext：

1. 获取jsp其他八个内置对象；

   如：${pageContext.request}，

   之后可以动态获取虚拟目录：${pageContext.request.contextPath}

# 10 JSTL标签

概念：JavaServer Pages Tag Library 	JSP标准标签库

​		是由Apache组织提供的开源的免费的jsp标签；

作用：用于简化和替换jsp页面上的java代码；

## 使用步骤：

1. 导入jstl相关jar包
2. 引入标签库：taglib指令：`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`
3. 使用标签  `<c:标签> </c:标签>`

## 常用的JSTL标签

- if ：相当于java代码的if语句

  `<c:if test=""> 标签体 </c:if>`

  - test 是必须属性，接收boolean表达式，如果为true则显示标签体内容，否则不显示；
  - c:if标签没有else情况，可以定义多个c:if实现；

- choose： 相当于java代码的switch语句，也可以作为java的if...else使用

  数字编号对应星期几：

  ```jsp
  <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
  <%
  	request.setAttribute("number", 3);
  %>
  
  <c:choose>
      <c:when test="${number == 1}"> 星期一 </c:when>
      <c:when test="${number == 2}"> 星期二 </c:when>
      <c:when test="${number == 3}"> 星期三 </c:when>
      <c:when test="${number == 4}"> 星期四 </c:when>
      <c:when test="${number == 5}"> 星期五 </c:when>
      <c:when test="${number == 6}"> 星期六 </c:when>
      <c:when test="${number == 7}"> 星期日 </c:when>
      <c:otherwise> 数字输入有误 </c:otherwise>
  </c:choose>
  ```

- forEach： 相当于java代码的for语句

  **普通for循环：**

  属性：

  - begin：开始值
  - end：结束值
  - var：临时变量
  - step：步长
  - varStatus：循环状态对象，常用语遍历容器
    - index：遍历容器时容器中元素的索引，从0开始
    - count：循环次数，从1开始

  ```jsp
  <c:forEach begin="1" end="10" var="i" step="1">
  	${i}  <!-- 12345678910 -->
  </c:forEach>
  ```

  **foreach遍历容器：**

  属性：

  - items：容器对象
  - var：容器中元素的临时变量
  - varStatus：循环状态对象
    - index：遍历容器时容器中元素的索引，从0开始
    - count：循环次数，从1开始

  ```jsp
  <c:forEach items="${list}" var="str" varStatus="s">
      ${s.index} ${s.count} ${str}
  </c:forEach>
  ```

  