# springboot的配置文件

springboot使用一个全局的配置文件，位置在 `resources` 目录下，且有一个确定的名字。

- application.yaml(官方推荐)

  语法结构：key: value  ()

- application.properties

  语法结构：key=value

配置文件的作用：修改springboot自动配置的默认值。

# 注入配置文件

在需要注入的类名上加上 `@ConfigurationProperties` 注解，使用方法：

```java
/*
	@ConfigurationProperties作用：
		将配置文件中配置的每一个属性的值，映射到这个组件中；
		告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定
		参数 prefix = “person” : 将配置文件中的person下面的所有属性一一对应

	只有这个组件是容器中的组件，才能使用容器提供的@ConfigurationProperties功能
*/
@Component //注册bean
@ConfigurationProperties(prefix = "person")
public class Person {
}
```

## yaml配置文件中可配置项的来源

由上面的注解可以知道，yaml中配置的内容其实也是根据自动配置类上 `@ConfigurationProperties` 注解中的prefix值来确定的。

(自动配置类可参考上一篇springboot原理初探中主启动类部分)

```java
@EnableConfigurationProperties({XxxProperties.class}) //也有可能在内部类上面指定
public class XxxAutoConfiguration(){
}
```

- `XxxAutoConfiguration`：自动配置类；

- `@EnableConfigurationProperties({XxxProperties.classs})`：开启配置属性类，大括号的参数就是注入配置文件的类；

- springboot通过yaml中的配置注入到自动配置类中，进而完成对自动配置中默认值的修改。

这就是自动配置的原理。

**精髓：**

**1）、SpringBoot启动会加载大量的自动配置类**

**2）、我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中；**

**3）、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件存在在其中，我们就不需要再手动配置了）**

**4）、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可；**

**xxxxAutoConfigurartion：自动配置类；**给容器中添加组件

**xxxxProperties:封装配置文件中相关属性；**

## 配置文件的位置

除了上述两种默认配置文件位置以外，spring官网还介绍了其他几个可以加载到配置文件的位置：

官方介绍： [spring-boot-config](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/html/spring-boot-features.html#boot-features-external-config-application-property-files) 

```txt	
A /config subdirectory of the current directory
The current directory
A classpath /config package
The classpath root (default)
```

1. `file:./config/`  ：在项目根目录下的config目录下
2. `file:./`  ：在项目根目录下
3. `classpath:/config/` ：在类路径下的config目录下
4. `classpath:/` ：在类路径下

其中，默认加载顺序也按照上述步骤，也就是说以file:./config/的配置文件优先级最高，没有的话再加载下面的。

## 通过debug配置启动查看匹配到哪些自动配置

在配置文件中配置：

```yaml
debug: true
```

运行项目

# YAML介绍

YAML是 "YAML Ain't a Markup Language" （YAML不是一种标记语言）的递归缩写。

在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）

YAML A Markup Language ：是一个标记语言
YAML isnot Markup Language ：不是一个标记语言

## 语法

```yml
# 普通key-value
name: tassel

# 对象
person:
  name: tassel
  age: 18

# 行内写法
person: {name: tassel, age: 18}

# 数组
pets:
  - cat
  - dog
  - pig
  
pets: [cat, dog, pig]
```

# 多环境切换

## 方式一：多配置文件

我们在主配置文件编写的时候，文件名可以是 application-{profile}.properties/yml ， 用来指定多个环境版本；

例如：`application-test.properties` 代表测试环境配置  `application-dev.properties`  代表开发环境配置

但是Springboot并不会直接启动这些配置文件，它默认使用application.properties主配置文件；

我们需要通过一个配置来选择需要激活的环境；

```properties
#比如在配置文件中指定使用dev环境，我们可以通过设置不同的端口号进行测试；
#我们启动SpringBoot，就可以看到已经切换到dev下的配置了；
spring.profiles.active=dev
```

## 方式二：yaml的多文档块

```yaml
server:
  port: 8081
#选择要激活那个环境块
spring:
  profiles:
    active: prod

---
server:
  port: 8083
#配置环境的名称
spring:
  profiles: dev

---
server:
  port: 8084
spring:
  profiles: prod  #配置环境的名称
```

**注意：如果yml和properties同时都配置了端口，并且没有激活其他环境 ， 默认会使用properties配置文件的！**