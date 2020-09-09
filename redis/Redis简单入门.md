## 概念

- redis是一款高性能的NOSQL（Not Only SQL）系列的非关系型数据库。
- 数据间没有关联关系；
- 数据存储在内存中；

## 下载安装

下载地址：

- linux版本：
  - 官网：https://redis.io
  - 中文网：https://www.redis.net.cn
- windows版本：
  - github地址：https://github.com/MicrosoftArchive/redis/releases

下载完成后直接解压即可。

- redis.window.conf：配置文件
- redis-cli.exe ：redis的客户端
- redis-server.exe ：redis的服务器端

## redis的数据结构

redis存储的是 key-value  格式的数据，其中key都是字符串，value有下面5中不同的数据结构：

value的数据结构：

- 字符串类型 String
- 哈希类型 hash ：map格式
- 列表类型 list：linkedList格式，可重复，无序
- 集合类型 set ：hashset格式，不可重复，无序
- 有序集合类型 sortedset ：不可重复，有序

## 命令操作

[redis教程](https://www.redis.net.cn/tutorial/3508.html)

### 1. 字符串类型 String

- 存储：`set key value`
- 获取：`get key`
- 删除：`del key`

### 2. 哈希类型 hash ：map格式

- 存储：`hset key field value`
- 获取：`hget key field`
  - 获取所有：`hgetall key`
- 删除：`hdel key field`

### 3. 列表类型 list：linkedList格式

- 存储：
  - `lpush key value`：将元素加入到列表左边
  - `rpush key value`：将元素加入到列表右边
- 获取：`lrange key start end`：范围获取
- 删除 ：
  - `lpop key`：删除列表最左边的元素，并将元素返回
  - `rpop key`：删除列表最右边的元素，并将元素返回

### 4. 集合类型 set 

- 存储：`sadd key value`
- 获取：`smembers key`：获取set集合中的所有元素
- 删除：`srem key value`：删除set集合中的某个元素

### 5. 有序集合类型 sortedset

- 存储：`zadd key score value`  //根据score（double类型）排序
- 获取：`zrange key start end [with scores]`
- 删除：`zrem key value`

### 通用命令：

- `keys *`：查询所有的键
- `type key`：获取键对应的value的值
- `del key`：删除指定的key-value

## 持久化操作

redis 是一个内存数据库，当redis服务器重启，或电脑重启，数据就会丢失，我们可以将redis内存中的数据持久化保存到硬盘的文件中。

### redis持久化机制：

1. RDB：默认方式，不需要进行配置，默认就是使用这种机制。

   在一定的时间间隔内，检测key的变化情况，然后持久化数据。

   1. 编辑redis.windows.conf文件；

      ```shell
      #   after 900 sec (15 min) if at least 1 key changed
      save 900 1
      #   after 300 sec (5 min) if at least 10 keys changed
      save 300 10
      #   after 60 sec if at least 10000 keys changed
      save 60 10000
      ```

   2. **命令行**重新启动redis服务器，并指定配置文件名称；

      ```shell
      redis-server.exe redis.windows.conf
      ```

2. AOF：日志记录的方式，可以记录每一条命令的操作。可以在每一次命令操作后，持久化数据。

   - 编辑redis.windows.conf文件

     appendonly no（关闭aof） -->  appendonly yes（开启aof）

     ```shell
     # appendfsync always ： 每一次操作都进行持久化
     appendfsync everysec ： 每隔一秒进行一次持久化
     # appendfsync no	 ： 不进行持久化
     ```

## 使用Java客户端操作redis：Jedis

- Jedis：一款java操作redis数据库的工具。

### 使用步骤：

1. 导入jedis的jar包；
2. 获取连接；
3. 操作；
4. 关闭连接；

```java
//1. 获取连接
Jedis jedis = new Jedis("localhost", 6379);
//2. 操作
jedis.set("username", "zhangsan");
//3. 关闭连接
jedis.close();
```

- Jedis操作各种redis中的数据结构的方法和redis的命令操作一致。

## Jedis连接池

JedisPool

使用：

1. 创建JedisPool连接池对象；
2. 调用方法 getResource()方法获取jedis连接；

```java
 //0. 创建一个配置对象
JedisPoolConfig config = new JedisPoolConfig();
config.setMaxTotal(50);
config.setMinIdle(10);

//1. 创建Jedis连接池对象
//JedisPool jedisPool = new JedisPool();
JedisPool jedisPool = new JedisPool(config, "localhost", 6379);

//2. 获取连接
Jedis jedis = jedisPool.getResource();

//3. 使用
jedis.set("hehe", "haha");

//4. 关闭 归还到连接池中
jedis.close();
```



