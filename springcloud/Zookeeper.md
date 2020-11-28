# 1. Zookeeper

![graphic](https://zsy0216.github.io/image/分布式架构/zookeeper/001.jpg)

## 1.1    简介

> ZooKeeper is a centralized service for maintaining configuration information, naming, providing distributed synchronization, and providing group services. All of these kinds of services are used in some form or another by distributed applications. Each time they are implemented there is a lot of work that goes into fixing the bugs and race conditions that are inevitable. Because of the difficulty of implementing these kinds of services, applications initially usually skimp on them, which make them brittle in the presence of change and difficult to manage. Even when done correctly, different implementations of these services lead to management complexity when the applications are deployed.

## 1.2    树形目录结构

![img](https://zsy0216.github.io/image/分布式架构/zookeeper/002.gif)

ZooKeeper使用树形结构管理数据。而且以“/”作为树形结构的根节点。树形结构中的每一个节点都称为“znode”。文件系统中的目录可以存放其他目录和文件，znode中可以存放其他znode，也可以对应一个具体的值。znode和它对应的值之间是键值对的关系。

每一个znode上同时还有一套状态信息，称为：stat。

## 1.3    异步通知机制

![img](https://zsy0216.github.io/image/分布式架构/zookeeper/003.gif)

在分布式项目中随着业务功能越来越多，具体的功能模块也会越来越多，一个大型的电商项目能达到几十个模块甚至更多。这么多业务模块的工程有可能需要共享一些信息，这些信息一旦发生变化，在各个相关模块工程中手动逐一修改会非常麻烦，甚至可能发生遗漏，严重的可能导致系统崩溃，造成经济损失。

使用ZooKeeper的通知机制后，各个模块工程在特定znode上设置Watcher（观察者）来监控当前节点上值的变化。一旦Watcher检测到了数据变化就会立即通知模块工程，从而自动实现“一处修改，处处生效”的效果。

## 1.4    leader-follower集群

![img](https://zsy0216.github.io/image/分布式架构/zookeeper/004.gif)

# 2. Zookeeper安装

## 2.1 环境准备——JDK

·Zookeeper需要在JVM虚拟机上运行，所以一定要保证有JDK支持。

1. 下载jdk-8u231-linux-x64.tar.gz到/opt目录

2. 解压到/opt目录下

   ```shell
   tar -zxvf /opt/jdk-8u231-linux-x64.tar.gz
   ```

3. 配置环境变量

   ```shell
   # JAVA_HOME 目录：/opt/jdk1.8.0_231
   vim /etc/profile
   
   # 最后加上
   JAVA_HOME=/opt/jdk1.8.0_231
   PATH=$JAVA_HOME/bin:$PATH
   export JAVA_HOME PATH
   # :wq 保存退出
   
   source /etc/profile
   ```

4. 验证

   ```shell
   echo $JAVA_HOME
   # /opt/jdk1.8.0_231
   echo $PATH
   # /opt/jdk1.8.0_231/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
   
   java -version
   # java version "1.8.0_231"
   # Java(TM) SE Runtime Environment (build 1.8.0_231-b11)
   # Java HotSpot(TM) 64-Bit Server VM (build 25.231-b11, mixed mode)
   ```

## 2.2 下载Zookeeper

下载zookeeper-3.4.9.tar.gz到/opt目录

## 2.3 解压

```shell
tar -zxvf /opt/zookeeper-3.4.9.tar.gz
```

## 2.4 准备配置文件

将解压完文件的配置文件名称改为zoo.cfg，这是Zookeeper的默认要求。

```shell
cp /opt/zookeeper-3.4.9/conf/zoo_sample.cfg /opt/zookeeper-3.4.9/conf/zoo.cfg
```

Zookeeper要求配置文件的文件名必须是：zoo.cfg

## 2.5 创建数据目录

```shell
mkdir /opt/zookeeper-3.4.9/data
```

## 2.6 在zoo.cfg中配置数据目录的位置

```shell
vim /opt/zookeeper-3.4.9/conf/zoo.cfg
dataDir=/opt/zookeeper-3.4.9/data
```

# 3. Zookeeper服务器端操作

## 3.1    zoo.cfg文件解读

**`tickTime`**
       通信心跳数,ZooKeeper服务器心跳时间，单位毫秒
       ZooKeeper使用的基本时间，服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个tickTime时间就会发送一个心跳，时间单位为毫秒。
       用于心跳机制，并且设置最小的session超时时间为两倍心跳时间(session的最小超时时间是2*tickTime)。
**`initLimit`**
       LF初始通信时限
       集群中的Follower跟随者服务器(F)与Leader领导者服务器(L)之间初始连接时能容忍的最多心跳数（tickTime的数量）。
       投票选举新Leader的初始化时间，Follower在启动过程中，会从Leader同步所有最新数据，然后确定自己能够对外服务的起始状态。
       Leader允许Follower在initLimit时间内完成这个工作。
**`syncLimit`**
       LF同步通信时限
       集群中Leader与Follower之间的最大响应时间单位，假如响应超过syncLimit * tickTime，Leader认为Follwer死掉，从服务器列表中删除Follwer。
       在运行过程中，Leader负责与ZooKeeper集群中所有机器进行通信，例如通过一些心跳检测机制，来检测机器的存活状态。
       如果L发出心跳包在syncLimit之后，还没有从F那收到响应，那么就认为这个F已经不在线了。
**`dataDir`**
       数据文件目录+数据持久化路径
       保存内存数据库快照信息的位置，如果没有其他说明，更新的事务日志也保存到数据库。
**`clientPort`**
       客户端连接端口

## 3.2 常用命令

### 服务器与客户端

启动服务器：`./zkServer.sh start`

停止服务器：`./zkServer.sh stop`

启动客户端：`./zkCli.sh`

退出客户端：[zk: localhost:2181(CONNECTED) 6]  `quit`

### ls

查看当前znode中所包含的内容

###  ls2

查看当前节点数据并能看到更新次数等数据

### stat

查看节点状态

### create

`create [-s] [-e] path data acl`

普通创建：不带有-s、-e参数

-s：含有序列

-e：临时（重启或者超时消失）

### set

设置节点的具体值

set 节点 value值

### get

获得节点的值

get节点

### delete

可以删除指定znode，当该znode拥有子znode时，必须先删除其所有子znode，否则操作将失败。

###  rmr

rmr命令可用于代替delete命令，rmr是一个递归删除命令，如果发生指定节点拥有子节点时，rmr命令会首先删除子节点。

## 3.3    Zookeeper节点类型

1. PERSISTENT-持久化目录节点

   客户端与zookeeper断开连接后，该节点依旧存在

2. PERSISTENT_SEQUENTIAL-持久化顺序编号目录节点

   `create -s`

   客户端与zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号

3. EPHEMERAL-临时目录节点

   `create -e`

   客户端与zookeeper断开连接后，该节点被删除

4. EPHEMERAL_SEQUENTIAL-临时顺序编号目录节点

   `create -s -e`

   客户端与zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号

## 3.4    Zookeeper节点状态

### 介绍

znode维护了一个stat结构，这个stat包含数据变化的版本号、访问控制列表变化、还有时间戳。版本号和时间戳一起，可让ZooKeeper验证缓存和协调更新。每次znode的数据发生了变化，版本号就增加。

例如：无论何时客户端检索数据，它也一起检索数据的版本号。并且当客户端执行更新或删除时，客户端必须提供他正在改变的znode的版本号。如果它提供的版本号和真实的数据版本号不一致，更新将会失败。

### 属性

czxid：引起这个znode创建的zxid，创建节点的事务的zxid（ZooKeeper Transaction Id）

ctime：znode被创建的毫秒数(从1970年开始)

mzxid：znode最后更新的zxid

mtime：znode最后修改的毫秒数(从1970年开始)

pZxid：znode最后更新的子节点zxid

cversion：znode子节点变化号，znode子节点修改次数

dataversion：znode数据变化号

aclVersion：znode访问控制列表的变化号

ephemeralOwner：如果是临时节点，这个是znode拥有者的session id。如果不是临时节点则是0。

dataLength：znode的数据长度

numChildren：znode子节点数量

## 3.5 四字命令

### 介绍

ZooKeeper支持某些特定的四字命令，他们大多是用来查询ZooKeeper服务的当前状态及相关信息的，使用时通过telnet或nc向ZooKeeper提交相应命令。

```shell
[root@right bin]# echo ruok | nc localhost 2181

imok[root@right bin]#
```

### nc命令

nc命令需要安装对应的程序才可以使用。

yum install -y nc

### 常用四字命令

ruok：测试服务是否处于正确状态。如果确实如此，那么服务返回“imok ”，否则不做任何响应

stat：输出关于性能和连接的客户端的列表

conf：输出相关服务配置的详细信息

cons：列出所有连接到服务器的客户端的完全的连接 /会话的详细信息。包括“接受 / 发送”的包数量、会话id 、操作延迟、最后的操作执行等等信息

dump：列出未经处理的会话和临时节点

envi：输出关于服务环境的详细信息（区别于conf命令）

reqs：列出未经处理的请求

wchs：列出服务器watch的详细信息

wchc：通过session列出服务器watch的详细信息，它的输出是一个与watch相关的会话的列表

wchp：通过路径列出服务器 watch的详细信息。它输出一个与 session相关的路径

# 4. Java客户端

代码示例：[Github-ZookeeperDemo](https://github.com/zsy0216/ZookeeperDemo)

## 4.1 依赖信息

```xml
<dependency>
    <groupId>com.101tec</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.10</version>
</dependency>
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.4.14</version>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

## 4.2 使用Junit测试类方法

- 创建Zookeeper对象：用于连接Zookeeper服务；
- Zookeeper构造器参数：
  - connectString：192.168.252.128:2181
  - sessionTimeOut：5000
  - Watcher对象：监控对象，数据有变化时调用方法返回通知

**修改节点数据：**

```java
public class ZkTest {
    // 连接Zookeeper服务的对象
    ZooKeeper zooKeeper;
    // 连接信息
    String connectString = "192.168.252.128:2181";
    // 连接超时时间 ms
    int sessionTimeOut = 5000;
	// Watcher对象用于检测节点是否变更，变更时调用对象内的方法异步通知
    Watcher watcher = new Watcher() {
        @Override
        public void process(WatchedEvent watchedEvent) {
        }
    };

    {
        try {
            // 在代码块中初始化Zookeeper对象
            zooKeeper = new ZooKeeper(connectString, sessionTimeOut, watcher);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Test
    public void testUpdateNodeData() throws KeeperException, InterruptedException {
        // 要操作的节点的路径
        String path = "/animal/cat";
        // 获取当前节点值
        byte[] resultByteArray = zooKeeper.getData(path, false, new Stat());
        // 将字节数组封装为字符串，输出
        String result = new String(resultByteArray);
        System.out.println(result);

        // 获取新值字符串对应的字节数组
        byte[] newValueByteArray = new String("mimi").getBytes();
        // 指定当前操作所基于的版本号，不确定可使用-1
        int version = -1;
        // 执行节点值的修改
        Stat stat = zooKeeper.setData(path, newValueByteArray, version);
        // 获取最新的版本号
        int newVersion = stat.getVersion();
        System.out.println("newVersion=" + newVersion);

        //获取最新的值，输出
        resultByteArray = zooKeeper.getData(path,false,new Stat());
        System.out.println(new String(resultByteArray));
        zooKeeper.close();
    }
}
```

代码示例：[Github-ZookeeperDemo](https://github.com/zsy0216/ZookeeperDemo)

# 5. 异步通知机制

## 5.1    工作机制介绍                                                 

> 客户端注册监听它关心的目录节点，当目录节点发生变化（数据改变、被删除、子目录节点增加删除）时，zookeeper会通知客户端。
>
> ZooKeeper支持Watch（观察）机制，客户端可以在每个znode结点上设置一个Watcher（观察者）。如果被观察服务端的znode结点有变更，那么Watcher就会被触发，这个Watcher所属的客户端将接收到一个通知包被告知结点已经发生变化，这就是把相应的事件通知给设置过Watcher的Client端。
>
> ZooKeeper里的所有读取操作：getData(),getChildren()和exists()都有设置Watch的选项。
>
> 
>
> 总结成一句话：**ZooKeeper的观察机制是一种异步回调的触发机制**。
>
> 
>
> 当数据有了变化时zkServer向客户端发送一个Watch通知，这是个一次性动作，触发一次就失效了。
>
> 如果想继续Watch的话，需要客户端重新设置Watcher。因此如果你得到了一个Watch事件，并且在将来继续得到节点变化通知，那么就必须另外设置一个新的Watcher继续观察。
>
> 节点有不同的改动方式。可以认为ZooKeeper维护两个观察列表：数据观察和子节点观察。getData()和exists()设置数据观察。getChildren()设置子节点观察。此外，还可以认为不同的返回数据有不同的观察。getData()和exists()返回节点的数据，而getChildren()返回子节点列表。所以，setData()将为znode触发数据观察。成功的create()将为新创建的节点触发数据观察，为其父节点触发子节点观察。成功的delete()将会为被删除的节点触发数据观察以及子节点观察（因为节点不能再有子节点了），为其父节点触发子节点观察。如果一个节点设置存在观察时尚未创建，并且在断开连接后执行节点创建以及删除操作，那么这个节点上设置的观察事件客户端接收不到，事件会丢失。

## 5.2 一次性通知

下面的代码测试继续在上面使用Java客户端连接Zookeeper的测试类中添加，共用测试类中声明的Zookeeper对象，新增测试方法测试一次性通知。

Watcher调用一次方法后会释放资源，所以只能进行一次性通知。

注意：异步通知机制要依赖于log4j的日志信息查看具体内容，这里需要导入log4j的配置文件。

```java
@Test
public void testNoticeOnce() throws KeeperException, InterruptedException {
    // 要操作的节点路径
    String path = "/animal/cat";
    Watcher watcher = new Watcher() {
        // 当前Watcher检测到节点值的变化，会调用process方法（异步通知）
        @Override
        public void process(WatchedEvent watchedEvent) {
            System.err.println("接收到了通知，值发生了修改！");
        }
    };
    // 修改前的值
    byte[] oldValue = zooKeeper.getData(path, watcher, new Stat());
    System.out.println("oldValue=" + oldValue);

    // 持续运行程序，等待Zookeeper修改值进行异步通知
    while (true){
        Thread.sleep(5000);
        System.err.println("当前方法要执行的业务逻辑");
    }
}
```

## 5.3 持续通知

下面的代码测试继续在上面使用Java客户端连接Zookeeper的测试类中添加，共用测试类中声明的Zookeeper对象，新增测试方法测试持续通知。

```java
@Test
public void testNoticeForever() throws KeeperException, InterruptedException {
    // 要操作的节点路径
    String path = "/animal/cat";
    getDataWithNotice(zooKeeper, path);

    // 持续运行程序，等待Zookeeper修改值进行异步通知
    while (true) {
        Thread.sleep(5000);
        System.err.println("当前方法要执行的业务逻辑");
    }
}

public void getDataWithNotice(ZooKeeper zooKeeper, String path) throws KeeperException, InterruptedException {
    byte[] resultByteArray = zooKeeper.getData(path, new Watcher() {
        @Override
        public void process(WatchedEvent watchedEvent) {
            // 以类似递归的方式调用getDataWithNotice()方法实现持续监控
            /**
                 * 这里不是真正的递归，这个process()方法是异步执行；
                 * 当getDataWithNotice()执行时，在创建完Watcher对象之后，继续执行到结束并释放资源
                 * 此时Watcher对象还在内存中，当接收到了修改时，再异步调用process()方法;
                 * 然后重新调用getDataWithNotice()方法，创建Watcher对象，实现持续监控。
                 * */
            try {
                System.out.println("*接收到了修改*");
                getDataWithNotice(zooKeeper, path);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }, new Stat());

    String result = new String(resultByteArray);
    System.out.println("当前节点值=" + result);
}
```

# 6. 集群

## 6.1  数据通信机制

同一台服务器上：IP地址一样，端口号必须得不一样

不同的服务器上：IP地址不一样，端口号可以用同一个



注意：启动完成后，会自动选举出leader/follower，leader和follower均可读可写，并进行数据的同步。

## 6.2    搭建步骤

### 第1步：创建集群所在目录

```shell
mkdir /opt/cluster-zk
```

### 第2步：重新解压tar包到集群目录

```shell
tar -zxvf /opt/zookeeper-3.4.9.tar.gz -C /opt/cluster-zk/
```

### 第3步：复制新的解压目录

```shell
cp -r /opt/cluster-zk/zookeeper-3.4.9/ /opt/cluster-zk/zkone
```

### 第4步：配置zkone

1. 创建zoo.cfg配置文件

   ```shell
   cp /opt/cluster-zk/zkone/conf/zoo_sample.cfg /opt/cluster-zk/zkone/conf/zoo.cfg
   ```

2. 创建数据目录

   ```shell
   mkdir /opt/cluster-zk/zkone/data
   ```

3. 在数据目录中创建编号文件

   ```shell
   vim /opt/cluster-zk/zkone/data/myid
   ```

4. 编辑编号文件，内容就是当前服务器实例的编号

   ```shell
   #myid
   1
   ```

5. 配置zoo.cfg

   ```shell
   vim /opt/cluster-zk/zkone/conf/zoo.cfg
   
   # zoo.cfg
   dataDir=/opt/cluster-zk/zkone/data
   clientPort=1000
   # 文件末尾追加
   server.1=127.0.0.1:1001:1002
   server.2=127.0.0.1:2001:2002
   server.3=127.0.0.1:3001:3002
   #格式解释：server.服务器实例编号=IP地址:数据通信端口号:选举端口号
   ```

### 第5步：配置zktwo

1. 把zktwo复制出来

   ```shell
   cp -r /opt/cluster-zk/zkone /opt/cluster-zk/zktwo
   ```

2. 修改myid文件中编号值

   ```shell
   vim /opt/cluster-zk/zktwo/data/myid
   
   #myid
   2
   ```

3. 修改zoo.cfg

   ```shell
   vim /opt/cluster-zk/zktwo/conf/zoo.cfg
   
   #zoo.cfg
   dataDir=/opt/cluster-zk/zktwo/data
   clientPort=2000
   ```

### 第6步：配置zkthree

1. 把zkthree复制出来

   ```shell
   cp -r /opt/cluster-zk/zktwo /opt/cluster-zk/zkthree
   ```

2. 修改myid文件中编号值

   ```shell
   vim /opt/cluster-zk/zkthree/data/myid
   
   #myid
   3
   ```

3. 修改zoo.cfg

   ```shell
   vim /opt/cluster-zk/zkthree/conf/zoo.cfg
   
   #zoo.cfg
   dataDir=/opt/cluster-zk/zkthree/data
   clientPort=3000
   ```

## 6.3 创建操作服务器的可执行脚本

### 6.3.1 创建启动服务器命令脚本文件

```shell
# 创建并编辑文件
vim /opt/cluster-zk/start.sh

#start.sh
/opt/cluster-zk/zkone/bin/zkServer.sh start
/opt/cluster-zk/zktwo/bin/zkServer.sh start
/opt/cluster-zk/zkthree/bin/zkServer.sh start
```

### 6.3.2 创建停止服务器命令脚本文件

```shell
# 创建并编辑文件
vim /opt/cluster-zk/stop.sh

#stop.sh
/opt/cluster-zk/zkone/bin/zkServer.sh stop
/opt/cluster-zk/zktwo/bin/zkServer.sh stop
/opt/cluster-zk/zkthree/bin/zkServer.sh stop
```

### 6.3.3 创建查看服务器状态命令脚本文件

```shell
# 创建并编辑文件
vim /opt/cluster-zk/status.sh

#status.sh
/opt/cluster-zk/zkone/bin/zkServer.sh status
/opt/cluster-zk/zktwo/bin/zkServer.sh status
/opt/cluster-zk/zkthree/bin/zkServer.sh status
```

### 6.3.4 给脚本文件设置可执行权限

```shell
chmod 755 /opt/cluster-zk/start.sh 
chmod 755 /opt/cluster-zk/stop.sh 
chmod 755 /opt/cluster-zk/status.sh 
```

### 6.3.5 执行脚本命令

```shell
/opt/cluster-zk/start.sh 
/opt/cluster-zk/stop.sh 
/opt/cluster-zk/status.sh 
# 启动命令执行完毕后查看状态，会自动选举出leader/follower
```

### 6.3.6 客户端登录

```shell
/opt/zookeeper-3.4.9/bin/zkCli.sh -server 127.0.0.1:1000
/opt/zookeeper-3.4.9/bin/zkCli.sh -server 127.0.0.1:2000
/opt/zookeeper-3.4.9/bin/zkCli.sh -server 127.0.0.1:3000
```

### 6.3.7 客户端的测试

此时zkone(127.0.0.1:1000)为leader，zktwo和zkthree为follower。

- 测试1：follower-zktwo中写入数据

  **follower可读可写，follower写的数据，其他follower(zkthree)和leader(zkone)都可以进行数据同步。**

- 测试2：follower-zktwo宕机

  `/opt/cluster-zk/zktwo/bin/zkServer.sh stop`

  **对其他follower(zkthree)和leader(zkone)无影响，数据同步正常。**

* 测试3：follower-zktwo重新启动

  `/opt/cluster-zk/zktwo/bin/zkServer.sh start`

  未自动连接时执行`connect 127.0.0.1:2000`

  **启动后，follower-zktwo自动进行数据同步，并自动设置自己为follower。**

* 测试4：leader-zkone宕机

  `/opt/cluster-zk/zkone/bin/zkServer.sh stop`

  **此时follower(zktwo,zkthree)连接正常，数据同步正常，并自动选举出新的leader。**

* 测试5：zk-one（曾经的leader）重新启动

  `/opt/cluster-zk/zkone/bin/zkServer.sh start`

  未自动连接时执行`connect 127.0.0.1:1000`

  **启动后数据同步正常，并自动设置自己为follower。**

## 6.4    集群中服务器数量

结论：一般来说，集群中服务器数量最好设置为单数。

原则：集群中有超过一半的服务器正常工程，则整个集群判断为正常工作。对外提供服务时大致满足预期。

推导：

共2实例：宕机1实例，剩下1=2/2，没有超过一半。死亡容忍度为0。

共3实例：宕机1实例，剩下2>3/2，超过一半。死亡容忍度为1。

共4实例：宕机1实例，剩下3>4/2，超过一半。死亡容忍度为1。

共5实例：宕机2实例，剩下3>5/2，超过一半。死亡容忍度为2。

共6实例：宕机2实例，剩下4>6/2，超过一半。死亡容忍度为2。