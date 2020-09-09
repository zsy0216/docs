# Redis 简单入门

## 概念

- Redis 是一款基于键值对的高性能的 NoSQL（Not Only SQL）系列的非关系型数据库。
- 数据间没有关联关系；
- 数据存储在内存中，所以它的读写性能十分惊人。                                                                                                 同时，Redis 还可以将内存中的数据以快照或日志的形式保存到硬盘上，以保证数据的安全性。
- Redis 典型的应用场景包括：缓存、排行榜、计数器、社交网络、消息队列等。

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

## Redis 的数据结构

redis 存储的是 key-value  格式的数据，其中 key 都是字符串，value 有下面 5 种不同的数据结构：

value 的数据结构：

- 字符串类型 string
- 哈希类型 hash ：map 格式
- 列表类型 list：linkedList 格式，可重复，无序
- 集合类型 set ：hashset 格式，不可重复，无序
- 有序集合类型 sorted set ：不可重复，有序

## 命令操作

[redis教程](https://www.redis.net.cn/tutorial/3508.html)

### 1. 字符串类型 String

- 存储：`set key value` 
- 获取：`get key` 
- 删除：`del key` 
- 加减值：`incr/decr key` 

```shell
set test:count 1 # OK
get test:count   # "1"
incr test:count  # (integer) 2
dect test:count  # (integer) 1
```

### 2. 哈希类型 hash ：map格式

- 存储：`hset key field value` 
- 获取：`hget key field ` 
  - 获取所有：`hgetall key` 
- 删除：`hdel key field` 

```shell
hset test:user id 1  # (integer) 1
hset test:user username zhangsan  # (integer) 1
hget test:user id  # "1"
hget test:user username  # "zhangsan"
```

### 3. 列表类型 list：linkedList格式

- 存储：
  - `lpush key value`：将元素加入到列表左边
  - `rpush key value`：将元素加入到列表右边
- 统计：`llen key` ：列表元素个数
- 获取：`lrange key start end`：范围获取  `lindex key index`:  获取指定索引的值
- 删除 ：
  - `lpop key`：删除列表最左边的元素，并将元素返回
  - `rpop key`：删除列表最右边的元素，并将元素返回

```shell
lpush test:ids 101 102 103  # (integer) 3
llen test:ids  # (integer) 3
lindex test:ids 0  # "103"
lindex test:ids 2  # "101"
lrange test:ids 0 2  # 1) "103" 2) "102" 3) "101" 
```

### 4. 集合类型 set 

- 存储：`sadd key value` 
- 统计：`scard key`  
- 获取：`smembers key`：获取set集合中的所有元素
- 删除：`srem key value`：删除set集合中的某个元素
- 随机获取值后删除：`spop ket [count]` 

```shell
sadd test:teacher aaa bbb ccc ddd eee  # (integer) 5
scard test:teacher  # (integer) 5
spop test:teacher   # "eee"
spop test:teacher 2  # 1) "ddd" 2) "aaa"
smembers test:teacher  # 1) "ccc" 2) "bbb"
```

### 5. 有序集合类型 sortedset

- 存储：`zadd key score value`  //根据 score（double类型）排序
- 统计：`zcard key`  
- 查询某个值的 score ：`zscord key member`  
- 查询某个值的排名：`zrank key member`  
- 范围获取：`zrange key start end [with scores]` 
- 删除：`zrem key value` 

```shell
zadd test:students 10 aaa 20 bbb 30 ccc 40 ddd 50 eee  # (integer) 5
zcard test:students   # (integer) 5
zscord test:students ccc   # "30"
zrank test:students ccc  # (integer) 2
zrange test:students 0 2  # 1) "aaa" 2) "bbb" 3) "ccc"
```

## 全局命令：

- `keys *`：查询所有的键（key）

  `````shell
  keys test*  # 可使用通配符，列出以 test 开头的所有 key
  `````

- `type key`：获取键对应的 value 的值

  ```shell
  type test:user  # hash
  ```

- `exists key` ：查看某个 key 是否存在 （1/0）

- `del key`：删除指定的 key-value

- `expire key seconds` ：设置某个 key 多少秒之后过期

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



