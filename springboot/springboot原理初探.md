# Springboot原理

## 自动配置：
### pom.xml

- spring-boot-dependencies：核心依赖在父工程中，这里定义了很多 springboot 所依赖的 jar 包和版本；

### 启动器

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
```

- 启动器就是springboot的启动场景；
- 比如：spring-boot-starter-web 就是web相关的启动场景，它会帮我们自动导入 web 环境所有的依赖。这里集成了springmvc。
- springboot会把所有的功能场景，变成一个个的启动器。
- 我们需要什么功能场景，只需要导入相应的启动器（starter）即可。

### 主启动类

```java
@SpringBootApplication
public class Springboot01HelloworldApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot01HelloworldApplication.class, args);
    }
}
```

注解：

- SpringbootConfiguration：springboot的配置
	
- Configuration：spring的配置类
	  
	- Component：说明是一个spring组件
	
- EnableAutoConfiguration：自动配置

  - AutoConfigurationPackage：自动配置包

    - Import(AutoConfigurationPackages.Register.class)：

  - Import(AutoConfigurationImportSelector.class)：自动导入包的核心

    类-AutoConfigurationImportSelector：选择了什么东西？

    - getAutoConfigurationEntry()：获得自动配置的实体 

    - getCandidateConfigurations()：获取候选的配置 

      ```java
      // 标注了 EnableAutoConfiguration 注解的类
      protected Class getSpringFactoriesLoaderFactoryClass(){
          return EnableAutoConfiguration.clss;
      }
      ```
      这里也就是找到主启动类，自动加载主启动类所有的配置

      - loadFactoryNames()：根据名字加载所有的配置

        ```java
        public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
            String factoryTypeName = factoryType.getName();
            return (List)loadSpringFactories(classLoader).getOrDefault(factoryTypeName, Collections.emptyList());
        }
        ```

      - loadSpringFactories()：

        ```java
        加载项目资源：classLoader.getResources("META-INF/spring.factories")
        加载系统资源：ClassLoader.getSystemResources("META-INF/spring.factories")
        从这些资源中遍历所有的nextElement(自动配置)，遍历完成之后，封装为Properties供我们使用
        ```

        `META-INF/spring.factories` 文件位置：

        `spring-boot-autoconfigure-2.2.2.RELEASE.jar → META-INF → spring.factories`

        ![](https://zsy0216.github.io//image/notes/20191211184109.png)

        所有的自动配置都声明在这个文件中。

  结论：springboot所有的自动配置都是在主启动类启动的时候扫描(`spring.factories`)并加载的，所有的自动配置类都在`spring.factories` 文件中，至于是否生效，要判断这些自动配置类上面的判断条件的注解是否满足，即类似 `@ConditionalOnXxx` 这样的注解。而想要生效，就要导入对应的`starter`启动器，有了相应的启动器，自动配置才会自动装配成功。



1. springboot在启动的时候，从类路径下`META-INF/spring.factories` 中获取 `@EnableAutoConfiguration` 指定的值；
2. 将这些值作为自动配置类导入容器，自动配置类就会生效，帮我们进行自动配置；（以前我们配置的东西，现在springboot已经帮我们做了）
3. 整个JavaEE的整体解决方案和自动配置的东西都在 `spring-boot-autoconfigure-2.2.2.RELEASE.jar` 这个包下；
4. 它会把所有需要导入的组件以全类名的方式返回，这些组件就会被添加到容器中；
5. 文件中存在很多 `XxxAutoConfiguration` 类(@Bean)，就是这些类给容器中导入了这个场景需要的所有组件，并自动配置@Configuration；
6. 有了自动配置类，就免去了我们手动编写配置文件注入功能的工作。

### run方法

执行run方法来启动springboot项目，即开启了一个服务。

**分析：SpringApplication的实例化和run方法的执行。**

SpringApplication这个类主要做了以下四件事情：

1. 推断应用的类型是普通的项目还是Web项目
2. 查找并加载所有可用初始化器 ， 设置到initializers属性中
3. 找出所有的应用程序监听器，设置到listeners属性中
4. 推断并设置main方法的定义类，找到运行的主类

查看构造器

```java
 public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
     this.sources = new LinkedHashSet();
     this.bannerMode = Mode.CONSOLE;
     this.logStartupInfo = true;
     this.addCommandLineProperties = true;
     this.addConversionService = true;
     this.headless = true;
     this.registerShutdownHook = true;
     this.additionalProfiles = new HashSet();
     this.isCustomEnvironment = false;
     this.lazyInitialization = false;
     this.resourceLoader = resourceLoader;
     Assert.notNull(primarySources, "PrimarySources must not be null");
     this.primarySources = new LinkedHashSet(Arrays.asList(primarySources));
     this.webApplicationType = WebApplicationType.deduceFromClasspath();
     this.setInitializers(this.getSpringFactoriesInstances(ApplicationContextInitializer.class));
     this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class));
     this.mainApplicationClass = this.deduceMainApplicationClass();
 }
```

run()方法：

![](https://zsy0216.github.io///image/notes/20191211201358.png)