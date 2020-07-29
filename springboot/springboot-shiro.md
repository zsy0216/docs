# SpringBoot整合Shiro

# 导入依赖

```xml
<dependency>    
    <groupId>org.apache.shiro</groupId>    
    <artifactId>shiro-spring</artifactId>    
    <version>1.4.1</version>
</dependency>
```

# 配置

创建config子包，并创建 shiro 的配置类。

`ShiroConfig.java` 

```java
@Configuration
public class ShiroConfig {
    // ShiroFilterFactoryBean
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("securityManager") DefaultWebSecurityManager securityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        // 关联 DefaultWebSecurityManager
        bean.setSecurityManager(securityManager);
        return bean;
    }
    // DefaultWebSecurityManager
    @Bean(name = "securityManager")
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        // 关联 realm
        securityManager.setRealm(userRealm);
        return securityManager;
    }
    // 创建 Realm 对象, 需要自定义Realm类型的类
    @Bean
    public UserRealm userRealm(){
        return new UserRealm();
    }
}
```

创建Realm类

`UserRealm.java` 

```java
public class UserRealm extends AuthorizingRealm {
    // 授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }
    //认证 
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        return null;
    }
}
```

