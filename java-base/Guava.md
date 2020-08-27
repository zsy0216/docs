> 参考： [Guava API](https://guava.dev/releases/snapshot-jre/api/docs/) | [Google Guava官方教程(中文版)](http://ifeve.com/google-guava/)

# Guava

## Strings

### 连接器 Joiner

```java
/**
 * 字符串的连接
 * @param separator 连接符
 * @param first
 * @param second
 * @param rest
 * @return
 */
public static String strJoiner(String separator, Object first, Object second, Object... rest) {
    // Joiner joiner = Joiner.on(separator).useForNull("null");
    Joiner joiner = Joiner.on(separator).skipNulls();
    return joiner.join(first, second, rest);
}
```

- 使用 静态方法 on(String) 构造 Joiner 对象；
  - useForNull(String) 方法可以替换连接中的 Null 对象；
  - skipNulls() 方法直接忽略 NULL值；
- 使用 join(Object... rest) 方法进行连接；

**示例：** 

```java
String m = "Hello";
String n = "World";

System.out.println(StringUtils.strJoiner(";", m, null, n)); 
// Hello;World or Hello;null;World
```

> **Warning: joiner instances are always immutable**; a configuration method such as `useForNull` has no effect on the instance it is invoked on! You must store and use the new joiner instance returned by the method. This makes joiners thread-safe, and safe to store as `static final` constants.

> 警告：`joiner`  实例总是不可变的；用来定义 `joiner` 目标语义的配置方法总会返回一个新的 `joiner` 实例。这使得joiner实例都是线程安全的，你可以将其定义为 `static final` 常量。

### 如何处理 null 值？

```java
Joiner joiner = Joiner.on(separator).useForNull("null");
Joiner joiner = Joiner.on(separator).skipNulls();
```

- `usrForNull()` 方法 使用参数字符替代 `null` 值；
- `skipNulls()` 方法 直接忽略 `null` 值；