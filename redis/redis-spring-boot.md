# Spring 整合 Redis

## 1. 导入相关依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

## 2. 写入相关配置

```yaml
# RedisProperties.java
spring:
  redis:
    database: 11   # 使用 redis 中的哪个数据库
    host: localhost  # 连接地址
    port: 6379  # 连接端口号
```

## 3. redis 配置类

把默认配置中泛型 <Object, Object> 改为 <String, Object>，并设置 key-value 的序列化方式。

```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);

        // 设置 key 的序列化方式
        template.setKeySerializer(RedisSerializer.string());
        // 设置普通 value 的序列化方式
        template.setValueSerializer(RedisSerializer.json());
        // 设置 hash key 的序列化方式
        template.setHashKeySerializer(RedisSerializer.string());
        // 设置 hash value 的序列化方式
        template.setHashValueSerializer(RedisSerializer.json());

        template.afterPropertiesSet();
        return template;
    }
}
```

## 4. 简单测试

```java
@Resource
RedisTemplate redisTemplate;

@Test
public void testStrings() {
    String redisKey = "test:count";
    redisTemplate.opsForValue().set(redisKey, 1);
    System.out.println(redisTemplate.opsForValue().get(redisKey));  // 1
    System.out.println(redisTemplate.opsForValue().increment(redisKey));  // 2
    System.out.println(redisTemplate.opsForValue().decrement(redisKey));  // 1
}

@Test
public void testHashes() {
    String redisKey = "test:user";
    redisTemplate.opsForHash().put(redisKey, "id", 1);
    redisTemplate.opsForHash().put(redisKey, "username", "zhangsan");
    System.out.println(redisTemplate.opsForHash().get(redisKey, "id"));  // 1
    System.out.println(redisTemplate.opsForHash().get(redisKey, "username"));  // zs
}

@Test
public void testLists() {
    String redisKey = "test:ids";
    redisTemplate.opsForList().leftPush(redisKey, 101);
    redisTemplate.opsForList().leftPush(redisKey, 102);
    redisTemplate.opsForList().leftPush(redisKey, 103);
    System.out.println(redisTemplate.opsForList().size(redisKey));  // 3
    System.out.println(redisTemplate.opsForList().index(redisKey, 0));  // 103
    System.out.println(redisTemplate.opsForList().range(redisKey, 0, 2));//[103,102,101]
    System.out.println(redisTemplate.opsForList().leftPop(redisKey));  // 103  
    System.out.println(redisTemplate.opsForList().leftPop(redisKey));  // 102
    System.out.println(redisTemplate.opsForList().leftPop(redisKey));  // 101
}

@Test
public void testSets() {
	String redisKey = "test:teachers";
	redisTemplate.opsForSet().add(redisKey, "刘备", "关羽", "张飞", "赵云", "诸葛亮");
	System.out.println(redisTemplate.opsForSet().size(redisKey)); // 5
	System.out.println(redisTemplate.opsForSet().pop(redisKey));  // 刘备
	System.out.println(redisTemplate.opsForSet().members(redisKey)); // [,,,,]
}

@Test
public void testSortedSets() {
	String redisKey = "test:students";
	redisTemplate.opsForZSet().add(redisKey, "唐僧", 80);
	redisTemplate.opsForZSet().add(redisKey, "悟空", 90);
	redisTemplate.opsForZSet().add(redisKey, "八戒", 50);
	redisTemplate.opsForZSet().add(redisKey, "沙僧", 70);
	redisTemplate.opsForZSet().add(redisKey, "白龙马", 60);
	System.out.println(redisTemplate.opsForZSet().zCard(redisKey));  // 5
	System.out.println(redisTemplate.opsForZSet().score(redisKey, "八戒"));  // 50.0
	System.out.println(redisTemplate.opsForZSet().reverseRank(redisKey, "八戒"));// 4
	System.out.println(redisTemplate.opsForZSet().reverseRange(redisKey, 0, 2));// [,,,]
}
```

RedisTemplate 操作各种数据结构的 API：

- string ：opsForValue()

- hash：opsForHash()

- list：opsForList()
- set：opsForSet()
- zSet：opsForZSet()
- 删除：delete(key)
- 判断是否有某 key：hasKey(key)
- 设置过期：expire(key, timeout, unit)



> 问题：每次对同一个 key 操作时，都要重复的指定该 key。

**绑定 key 以多次使用** 

```java
@Test
public void testBoundOperations() {
    String redisKey = "test:count";
    BoundValueOperations operations = redisTemplate.boundValueOps(redisKey);
    operations.increment();
}
```

