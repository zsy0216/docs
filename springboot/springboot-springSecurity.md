# SpringBoot整合SpringSecurity

SpringSecurity是spring系列的安全框架，直接看官网介绍：

> Spring Security is a powerful and highly customizable authentication and access-control framework. It is the de-facto standard for securing Spring-based applications.

- 认证：Authentication
- 授权：Authorization

下面进行简单的springboot和SpringSecurity的整合。

## 创建项目

创建项目时，选择了spring web，thymeleaf

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

### 准备页面

![](https://zsy0216.github.io/image/notes/20191216194541.png)

### 跳转控制

`RouterController.java`

```java
@Controller
public class RouterController {
    @RequestMapping({"/", "/index"})
    public String index() {
        return "index";
    }
    @RequestMapping("/toLogin")
    public String login() {
        return "views/login";
    }
    @RequestMapping("/level1/{id}")
    public String level1(@PathVariable("id") Integer id) {
        return "views/level1/" + id;
    }
    @RequestMapping("/level2/{id}")
    public String level2(@PathVariable("id") Integer id) {
        return "views/level2/" + id;
    }
    @RequestMapping("/level3/{id}")
    public String level3(@PathVariable("id") Integer id) {
        return "views/level3/" + id;
    }
}
```

运行项目测试跳转：

首页

![](https://zsy0216.github.io//image/notes/20191216210636.png)

点击会跳转对应的页面

## 整合SpringSecurity

加入依赖

```xml
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

根据 [官方文档](https://docs.spring.io/spring-security/site/docs/5.2.0.BUILD-SNAPSHOT/reference/htmlsingle/#hello-web-security-java-configuration) 新建一个config包，并创建SpringSecurity的配置类

`SpringSecurity.java`

```java
@Configuration
public class SpringSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 首页所有人可以访问，功能页只有对应权限的人才能访问
        // （请求授权规则）
        http.authorizeRequests()
                .antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("vip1")
                .antMatchers("/level2/**").hasRole("vip2")
                .antMatchers("/level3/**").hasRole("vip3");
    }
}
```

通过上面简单的配置我们添加了一些请求授权的规则，比如首页所有人都可以访问，而功能页只有拥有对应权限的人才能访问。

下面再尝试其他的一些配置：

### 没有权限跳到登录页

通过上面的配置，我们在没有登录时，或者没有相应的权限时去访问一些页面，会返回403没有权限的错误页面，现在设置没有权限时跳转到登录页

```java
http.formLogin();
```

这时，没有权限时会发起`/login`请求，而这个请求就是SpringSecurity已经封装好的登录请求，且有一个默认的登录页面。

 ![](https://zsy0216.github.io//image/notes/20191216213236.png)

### 配置认证信息

上面重写的 `configure()` 方法用于配置一些授权信息，现在重写一个用于配置认证信息的`configure()` 方法。

**注：下面简称授权方法和认证方法**

- `protected void configure(HttpSecurity http)` 是授权方法
- `protected void configure(AuthenticationManagerBuilder auth)`是认证方法

```java
// 认证
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    //auth.jdbcAuthentication()是根据数据库方式设置，下面的是通过内存方式；
    auth.inMemoryAuthentication()
        .withUser("tassel").password("123456").roles("vip2", "vip3")
        .and()
        .withUser("root").password("123456").roles("vip1", "vip2", "vip3")
        .and()
        .withUser("guest").password("123456").roles("vip1");
}
```

这样就添加了三个用户并分别设置了不同的权限，用户tassel拥有vip2和vip3的权限，root用户拥有最高权限，guest游客只有vip1的权限。

这是如果启动项目登录(这时访问的不是自己写的登录页面，而是SpringSecurity自带的那个登录)时会返回500错误页面，错误信息为`There is no PasswordEncoder mapped for the id "null"`，意思是说密码没有进行编码，也就是没有加密。

调整代码：

```java
// 认证
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    //auth.jdbcAuthentication()是根据数据库方式设置，下面的是通过内存方式；
    auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
        .withUser("tassel").password(new BCryptPasswordEncoder().encode("123456")).roles("vip2", "vip3")
        .and()
        .withUser("root").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1", "vip2", "vip3")
        .and()
        .withUser("guest").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1");
}
```

这时就可以登录成功了！而且不同的用户可以访问的功能页也是不同的。

从数据库读数据认证参考 [官方文档](https://docs.spring.io/spring-security/site/docs/5.2.0.BUILD-SNAPSHOT/reference/htmlsingle/#jc-authentication-jdbc) 

### 注销

在授权方法中添加：

```java
// 注销, 并跳转到首页
http.logout().logoutSuccessUrl("/");
// 关闭csrf功能，不关闭2.1.0以下版本会登出失败
http.csrf().disable();
```

这样就开启了注销，点进源码发现，注销的实现实际上请求了`/logout`请求，所以我们在页面上定义一个链接用于发起该请求完成注销功能。

```html
<header>
    index - <a th:href="@{/toLogin}">登录</a>
    ----<a th:href="@{/logout}">注销</a>
</header>
```

### security整合thymeleaf

我们需要引入security-thymeleaf整合包

```xml
  <dependency>
      <groupId>org.thymeleaf.extras</groupId>
      <artifactId>thymeleaf-extras-springsecurity4</artifactId>
      <version>3.0.2.RELEASE</version>
</dependency>
```

修改首页：

1. 加入命名空间：

   ```html
   <html xmlns:th="http://www.thymeleaf.org" 
         xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity4">
   ```

2. 登录前显示登陆按钮，登录后显示注销

   ```html
    <!-- 如果未登录，显示登陆按钮 -->
   <div sec:authorize="!isAuthenticated()"> <!-- 判断用户是否已经认证 -->
       <a th:href="@{/toLogin}">登录</a>
   </div>
   <!-- 如果已经登陆，显示用户名+注销按钮 -->
   <div sec:authorize="isAuthenticated()">
       <a>
           用户名：<span sec:authentication="name"></span>
       </a>
       <a th:href="@{/logout}">注销</a>
   </div>
   ```

   ### 不同用户在首页显示不同内容

   我们想实现这样的效果：不同的用户登录后，由于权限不同，只显示其对应权限的信息。比如说，tassel用户显示level2和level3的内容，root用户显示所有内容，guest用户只显示level1的内容。

   修改：

   ```html
   <section sec:authorize="hasRole('vip1')">
       <a th:href="@{/level1/1}"> level1-1</a>
       <a th:href="@{/level1/2}"> level1-2</a>
       <a th:href="@{/level1/3}"> level1-3</a>
   </section>
   ```

   如这样设置，拥有vip角色的用户才会显示这样的内容，level2，level3类似。

   ### 记住我

   ```java
   // 记住我功能
   http.rememberMe();
   ```

   本质上是在客户端使用了cookie记录，默认保存两周的时间，

   ### 使用自己的登录页面

   登录表单请求：`/login`

   ```html
   <form th:action="@{/login}" method="post">
       <label for="username">用户名：</label>
       <input id="username" type="text" name="user"/>
       <label for="password">密码：</label>
       <input id="password" type="password" name="pwd"/>
       <input type="checkbox" name="remember"/> 记住我
       <input type="submit" value="登录"/>
   </form>
   ```

   授权方法：

   ```java
   http.formLogin().loginPage("/toLogin").loginProcessingUrl("/login")
           .usernameParameter("user").passwordParameter("pwd");
   ```

   - .loginPage()：去登录页面的请求；
   - .loginProcessingUrl()：登录请求；
   - .usernameParameter()：对应登录表单的用户名输入框的name属性；
   - .passwordParameter()：对应登录表单的密码输入框中的name属性；

   开启自定义登录页面的记住我功能

   ```java
   http.rememberMe().rememberMeParameter("remember");
   ```

   - .rememberMeParameter()：对应登录表单中的记住我多选框的name属性；