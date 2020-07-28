# 数组

数组是一种重要的数据结构，对于java来说，数组用来存储固定大小的同类型元素。

# 声明数组变量

在java中，数组变量和其他的变量一样，在使用前都必须声明。

声明格式：

```java
double[] myArray; 
```

# 数组初始化

方式一：

```java
// 分配长度为4个 int 类型的内存空间，并分别赋初值为1,2,3,4
int[] array = new int[]{1,2,3,4};
```

方式二：

```java
// 方式一的简写
int[] array = {1,2,3,4};
```

方式三：

```java
// 分配长度为4个int类型的内存空间，初始值都为0
// 相当于 int[] array = new int[4]{0,0,0,0};
int[] array = new int[4];
```

如方式三所示，当在数组初始化时，没有特定指定其初值，java根据其数据类型赋予其默认的初始值。

下面介绍不同数据类型数组对应的初始值（一般规律是数值类型的数组默认值为0，引用类型数组默认值为null）。

数组类型 默认初始值   byte 0  short 0  int 0  long 0  char 编码为0的字符  String（引用类型） null  float 0.0  double 0.0  boolean false

| 数组类型 | 默认初始值 |
| -------- | ---------- |
| byte     | 0          |
| short    | 0          |
| int      | 0          |
| long     | 0          |
| float    | 0.0        |
| double   | 0.0     |
| char     | 编码为0的字符     |
| boolean  | false     |

