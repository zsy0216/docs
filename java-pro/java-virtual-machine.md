# JVM

> 学习地址：[解密 JVM](https://www.bilibili.com/video/BV1yE411Z7AP)

# 0.什么是JVM

Java Virtual Machine - Java 程序的运行环境（Java 二进制字节码的运行环境）

## 优点：

- 一次编写，处处运行
- 自动内存管理，垃圾回收机制
- 数据下标越界检查
- 多态

## 比较JVM JRE JDK

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608150422.png)

## 学习路线

![image-20200923152638699](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200923152638699.png)



# 1.内存模型

Java 内存区域：运行时数据区域

## 程序计数器

Program Counter Register 程序计数器（物理上是由寄存器来实现的）

![image-20200923153545221](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200923153545221.png)

Program Counter Register 程序计数器（物理上是由寄存器来实现的）

### 作用

用于保存 JVM 中下一条所要执行的指令的地址

### 特点

- 是线程私有的
  - CPU 会为每个线程分配时间片，当当前线程的时间片使用完以后，CPU 就会去执行另一个线程中的代码
  - 程序计数器是**每个线程**所**私有**的，当另一个线程的时间片用完，又返回来执行当前线程的代码时，通过程序计数器可以知道应该执行哪一句指令
- 不会存在内存溢出

## 虚拟机栈

![image-20200923162537085](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200923162537085.png)

### 定义

- 每个线程运行时所需要的内存，成为虚拟机栈；
- 每个栈由多个栈帧(Frame)组成，对应着每次方法调用时所占用的内存；
- 每个线程只能有一个活动栈帧，对应着当前正在执行的那个方法；

### 问题辨析

1. 垃圾回收是否涉及栈内存？

   **不需要**。因为虚拟机栈中是由一个个栈帧组成的，在方法执行完毕后，对应的栈帧就会被弹出栈。所以无需通过垃圾回收机制去回收内存。

2. 栈内存分配越大越好吗？

   不是。因为**物理内存是一定的**，栈内存越大，可以支持更多的递归调用，但是可执行的线程数就会越少。

3. 方法内的局部变量是否线程安全？

   - 如果方法内**局部变量没有逃离方法的作用范围**，则是**线程安全**的
   - 如果如果**局部变量引用了对象**，并**逃离了方法的作用范围**，则需要考虑线程安全问题

### 栈内存溢出

Java.lang.stackOverflowError 栈内存溢出

- 虚拟机栈中，**栈帧过多**（无限递归）
- 每个栈帧**所占用过大**

### 线程运行诊断

CPU 占用过高

- Linux 环境下运行某些程序的时候，可能导致CPU的占用过高，这时需要定位占用CPU过高的线程
  - **top** 命令，查看是哪个**进程**占用CPU过高
  - **ps H -eo pid, tid, %cpu | grep 刚才通过top查到的进程号** 通过ps命令进一步查看是哪个线程占用CPU过高
  - **jstack 进程id** 通过查看进程中的线程的nid，刚才通过ps命令看到的tid来**对比定位**，注意jstack查找出的线程id是**16进制的**，**需要转换** 

## 本地方法栈

![image-20200924103420021](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200924103420021.png)

一些带有 **native 关键字**的方法就是需要 JAVA 去调用本地的 C 或者 C++ 方法，因为JAVA有时候没法直接和操作系统底层交互，所以需要用到本地方法，这时使用的内存空间就是本地方法栈。

- 程序计数器、虚拟机栈、本地方法栈 都是线程私有的；

## 堆

![image-20200924103443097](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200924103443097.png)

### 定义

Heap 堆

- 通过 new 关键字创建的对象都会被放在堆内存；

特点

- 它是线程共享的，堆中对象都需要考虑线程安全的问题；
- 有垃圾回收机制；

### 堆内存溢出

`java.lang.OutofMemoryError ：java heap space.` 堆内存溢出

### 堆内存诊断

- jps：查看当前系统中有哪些 java 进程

- jmap：查看堆内存占用情况 `jmap - heap 进程id`

- jconsole：图形界面的，多功能的监测工具，可以连续监测

- jvirsalvm

## 方法区

### 定义

[JVM 规范-方法区定义](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5.4)

### 组成

![image-20200924111130383](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200924111130383.png)

![image-20200924111236667](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200924111236667.png)

### 方法区内存溢出

- 1.8以前会导致**永久代**内存溢出：java.lang.OutOfMemoryError: PermGen space
- 1.8以后会导致**元空间**内存溢出：java.lang.OutOfMemoryError: Metaspace

### 运行时常量池

二进制字节码的组成：类的基本信息、常量池、类的方法定义（包含了虚拟机指令）

- 常量池，就是一张表，虚拟机指令根据这张常量表找到要执行的类名、方法名、参数类型、字面量等信息；
- Class 文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息是常量池，用于存放编译期生成的各种字面量和符号引用，类加载后会被存放到方法区的运行时常量池；
- 运行时常量池，**常量池是 *.class 文件中的**，当该类被加载，它的常量池信息就会放入运行时常量
  池，并把里面的符号地址变为真实地址；

### StringTable

串池 HashTable 结构

#### 特性

- 常量池中的字符串仅是符号，第一次用到时才变为对象；
- 利用串池的机制，来避免重复创建字符串对象；
- 字符串变量拼接的原理是 StringBuilder(1.8)；
  - StringBuilder().append(String).append(String).toString();
  - toString 方法实际上用了 new String(char[], int, int) 创建了新的对象；
- 字符串常量拼接的原理是编译期优化；
  - 在编译期间就已经对常量进行运算出结果；
- 可以使用 intern 方法，主动将串池中还没有的字符串对象放入串池；
  - 1.8  将这个字符串对象尝试放入串池，如果有则并不会放入，如果没有则放入串池， 会把串
    池中的对象返回；
  - 1.6  将这个字符串对象尝试放入串池，如果有则并不会放入，如果没有会把此对象复制一份，
    放入串池， 会把串池中的对象返回；

**注意**：无论是串池还是堆里面的字符串，都是对象

#### 位置

由方法区的组成可以看出

- 1.8 串池位于堆空间；
- 1.6 串池位于永久代空间；

#### 垃圾回收

StringTable在内存紧张时，会发生垃圾回收

#### 性能调优

- 因为 StringTable 是由 HashTable 实现的，所以可以 **适当增加 HashTable 桶的个数** ，来减少字符串放入串池所需要的时间

  ```powershell
  -XX:StringTableSize=xxxxCopy
  ```

- 考虑是否需要将字符串对象入池

- 可以通过 intern 方法减少重复入池

## 直接内存

- 属于操作系统，常见于NIO操作时，**用于数据缓冲区**
- 分配回收成本较高，但读写性能高
- 不受JVM内存回收管理

### 文件读写流程

普通 IO

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608150715.png)

使用 DirectBuffer 直接内存

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608150736.png)

直接内存是操作系统和 Java 代码都可以访问的一块区域，无需将代码从系统内存复制到 Java 堆内存，从而提高了效率。

### 释放原理

- 使用了 Unsafe 对象完成直接内存的分配回收，并且回收需要主动调用 freeMemory 方法；
- ByteBuffer  的实现类内部，使用了 Cleaner （虚引用）来监测 ByteBuffer 对象，一旦 ByteBuffer 对象被垃圾回收，那么就会由 ReferenceHandler 线程通过 Cleaner 的 clean 方法调用 freeMemory 来释放直接内存；

# 2.垃圾回收

## 如何判断对象可以被回收？

### 引用计数法

对对象的引用进行计数，当引用计数为 0 时，对该对象进行回收。

弊端：对于循环引用的情况，导致无法回收。

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608150750.png)

### 可达性分析算法

- JVM 中的垃圾回收器通过**可达性分析**来探索所有存活的对象
- 扫描堆中的对象，看能否沿着 GC Root 对象为起点的引用链找到该对象，如果**找不到，则表示可以回收**
- 可以作为 GC Root 的对象
  - 虚拟机栈（栈帧中的本地变量表）中引用的对象。　
  - 方法区中类静态属性引用的对象
  - 方法区中常量引用的对象
  - 本地方法栈中 JNI（即一般说的 Native 方法）引用的对象

### 四种引用

1. 强引用
- 只有所有 GC Roots 对象都不通过【强引用】引用该对象，该对象才能被垃圾回收；
2. 软引用（SoftReference）
  - 仅有软引用引用该对象时，在垃圾回收后，内存仍不足时会再次出发垃圾回收，回收软引用对象；
  - 可以配合引用队列来释放软引用自身内存；
3. 弱引用（WeakReference）
  - 仅有弱引用引用该对象时，在垃圾回收时，无论内存是否充足，都会回收弱引用对象；
  - 可以配合引用队列来释放弱引用自身内存；
4. 虚引用（PhantomReference）
  - 必须配合引用队列使用，主要配合 ByteBuffer 使用，被引用对象回收时，会将虚引用入队，由Reference Handler 线程调用虚引用相关方法释放直接内存
5. 终结器引用（FinalReference）
  - 无需手动编码，但其内部配合引用队列使用，在垃圾回收时，终结器引用入队（被引用对象暂时没有被回收），再由 Finalizer 线程通过终结器引用找到被引用对象并调用它的 finalize方法，第二次 GC 时才能回收被引用对象

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608150800.png)

## 垃圾回收算法

### 标记-清除算法（Mark Sweep）

- 速度较快
- 会造成垃圾碎片

![image-20201011100903497](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20201011100903497.png)

定义：标记清除算法顾名思义，是指在虚拟机执行垃圾回收的过程中，先采用标记算法确定可回收对象，然后垃圾收集器根据标识清除相应的内容，给堆内存腾出相应的空间

- 这里的腾出内存空间并不是将内存空间的字节清零，而是记录下这段内存的起始结束地址，下次分配内存的时候，会直接覆盖这段内存；

缺点：容易产生大量的内存碎片，可能无法满足大对象的内存分配，一旦导致无法分配对象，那就会导致 JVM 启动 GC，一旦启动 GC，我们的应用程序就会暂停，这就导致应用的响应速度变慢；

### 标记-整理算法（Mark Compact）

- 速度慢
- 没有垃圾碎片

![image-20201011191821129](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20201011191821129.png)

定义：标记-整理会将不被 GC Root 引用的对象回收，清除其占用的内存空间。然后整理剩余的对象，可以有效避免因内存碎片而导致的问题，但是因为整体需要消耗一定的时间，所以效率较低。

### 复制算法（Copy）

- 不会有内存碎片
- 需要占用双倍内存空间

![image-20201011195537512](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20201011195537512.png)

将内存分为相同大小的两个区域，FROM 和 TO（TO 区域为空）。现将被 GC Root 引用的对象（不会被垃圾回收）从 FROM 放入 TO 中，再回收 FROM 中剩余的不被 GC Root 引用的将要被回收的对象。然后交换 FROM 和 TO。这样也可以避免内存碎片的问题，但是会占用双倍的内存空间。

## 分代回收

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608150931.png)

### **回收流程**

新创建的对象都放在了新生代的伊甸园中

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608150939.png)

当伊甸园中的内存不足时，就会进行一次垃圾回收，这时的回收叫做 **Minor** **GC**

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608150946.png)

Minor GC 会将伊甸园和幸存区 FROM 存活的对象先复制到 幸存区 TO 中， 并让其寿命加 1，再交换两个幸存区

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608150955.png)

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608151002.png)

再次创建对象，若新生代的伊甸园又满了，则会再次触发 Minor GC（会触发 stop the world， 暂停其他用户线程，只让垃圾回收线程工作），这时不仅会回收伊甸园中的垃圾，还会回收幸存区中的垃圾，再将活跃对象复制到幸存区 TO 中。回收以后会交换两个幸存区，并让幸存区中的对象寿命加 1

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608151010.png)

如果幸存区中的对象的**寿命超过某个阈值**（最大为15，4bit），就会被**放入老年代**中

![img](https://gitee.com/zsy0216/typora-image/raw/master/typora/20200608151018.png)

如果新生代老年代中的内存都满了，就会先触发 Minor Gc，再触发 **Full GC**，扫描**新生代和老年代中**所有不再使用的对象并回收

- 对象首先分配在新生代的伊甸园区；
- 新生代空间不足时，触发 minor gc，伊甸园和 from 存活的对象使用 copy 算法复制到 to 中，存活的对象年龄 +1 并交换 from 和 to 区域；
- minor gc 会引发 stop the world，暂停其他用户的线程，优先执行垃圾回收的线程，等垃圾回收结束后，用户线程才恢复运行；
- 当对象寿命超过阈值时，会晋升至老年代，最大寿命是 15 次（4bit）；
- 当老年代空间不足，会先尝试触发 minor gc，如果空间仍不足，那么触发 full gc，STW（stop the world）的时间更长；

### GC 分析

- 大对象处理策略

  当遇到一个**较大的对象**时，就算新生代的**伊甸园**为空，也**无法容纳该对象**时，会将该对象**直接晋升为老年代**

- 线程内存溢出

  某个线程的内存溢出了而抛异常（out of memory），不会让其他的线程结束运行

  这是因为当一个线程**抛出OOM异常后**，**它所占据的内存资源会全部被释放掉**，从而不会影响其他线程的运行，**进程依然正常**

## 垃圾回收器

### 串行

### 吞吐量优先

### 响应时间优先