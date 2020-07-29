问题探究：

- 静态资源
- 首页
- 模板引擎：thyemleaf

# 静态资源

源码：`WebMvcAutoConfiguration.class` 

```java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    // 如果修改了配置，默认的资源映射就会失效
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
    } else {
        Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
        CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
        // 通过webjars的方式引入静态资源，可以通过maven坐标的方式导入
        // 根目录下的/webjars/就对应了导入jar包中的/META-INF/resources/webjars/
        // 以此访问静态资源，可以进webjars官网选择导入，比如jquery等
        if (!registry.hasMappingForPattern("/webjars/**")) {
            this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }
        
        String staticPathPattern = this.mvcProperties.getStaticPathPattern();
        //第二种方式,点对应的变量或方法可以通过XxxProperties类查看相关默认配置和修改默认配置的方法
        //staticPathPattern 默认是/** 可以通过spring.mvc.static-path-pattern配置
        //对应的目录为：staticLocations：
        //classpath:/META-INF/resources/", "classpath:/resources/", "classpath:/static/", "classpath:/public/"
        if (!registry.hasMappingForPattern(staticPathPattern)) {
            this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }
    }
}
```

上面代码的注释提供了两种不同的方式引入静态资源，第二种常用；

## 结论

**默认配置：**

静态资源路径映射：/**

静态资源目录：

- `classpath:/META-INF/resources/` 
- `classpath:/resources/` 
- `classpath:/static/` 
- `classpath:/public/` 

**修改默认配置**

静态资源路径映射：`spring.mvc.static-path-pattern=` 

静态资源目录： `spring.resources.static-locations=` 

- 这里修改静态资源路径映射的时候要注意，这里要写`/demo/**` 只写`/demo/` 是无法匹配的。

# 首页定制

源码：`WebMvcAutoConfiguration.java` 

```java
@Bean
public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext, FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
    
    WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(new TemplateAvailabilityProviders(applicationContext), applicationContext, this.getWelcomePage(), this.mvcProperties.getStaticPathPattern());
    welcomePageHandlerMapping.setInterceptors(this.getInterceptors(mvcConversionService, mvcResourceUrlProvider));
    return welcomePageHandlerMapping;
}

private Optional<Resource> getWelcomePage() {
    String[] locations = WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations());
    return Arrays.stream(locations).map(this::getIndexHtml).filter(this::isReadable).findFirst();
}

private Resource getIndexHtml(String location) {
    return this.resourceLoader.getResource(location + "index.html");
}
```

也可以看到，首页名字要定义为index.html，并且要放到对应的那几个静态资源目录。

# Thymeleaf

## 使用

引用：通过[SpringBoot官网](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/html/using-spring-boot.html#using-boot-starter)提供的starter来引入[Thymeleaf](https://github.com/spring-projects/spring-boot/blob/v2.2.2.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-thymeleaf/pom.xml)

```xml
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
</dependency>
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-java8time</artifactId>
</dependency>
```

使用：将html文件放到`classpath:/templates/` 下即可，

然后根据thymeleaf的默认配置 就可以实现类似springmvc视图解析器的效果：

`Thymeleaf.java`  

```java
public static final String DEFAULT_PREFIX = "classpath:/templates/";
public static final String DEFAULT_SUFFIX = ".html";
```

之后在Controller中定义的请求处理的方法就可以按照这个规则找到视图。

## 语法

https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.pdf 第17页



# 装配扩展SpringMVC

[官方文档介绍](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-auto-configuration)

根据官网介绍：

> If you want to keep Spring Boot MVC features and you want to add additional [MVC configuration](https://docs.spring.io/spring/docs/5.2.2.RELEASE/spring-framework-reference/web.html#mvc) (interceptors, formatters, view controllers, and other features), you can add your own `@Configuration` class of type `WebMvcConfigurer` but **without** `@EnableWebMvc`. 

我们只需要自定义一个类属于`WebMvcConfigurer`类型，加上`@Configuration`注解，且不加`@EnableWebMvc`注解就可以了。

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        // 视图文件要在classpath:/templates/ 下
        registry.addViewController("/my-config").setViewName("myConfig");
    }
}
```

![](https://zsy0216.github.io/image/notes/20191213154814.png)