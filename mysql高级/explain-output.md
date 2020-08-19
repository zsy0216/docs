# Explain 性能分析

使用 EXPLAIN 关键字可以模拟优化器执行 SQL 查询语句，从而知道 MySQL 是如何处理你的 SQL 语句的。分析你的查询语句或是表结构的性能瓶颈。

用法：

```sql
EXPALIN SELECT * FROM t1;
```

Explain 执行后返回的信息：

![image-20200819094927588](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819094927588.png)

下面分别介绍其含义。

## id

select 查询的序列号,包含一组数字，表示查询中执行 select 子句或操作表的顺序。

1. id 相同，执行顺序由上至下

![image-20200819095028278](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819095028278.png)

2. id 不同，如果是子查询，id 的序号会递增，id 值越大优先级越高，越先被执行

   ![image-20200819095105830](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819095105830.png)

3. 有相同也有不同

   ![image-20200819095138677](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819095138677.png)

   id 如果相同，可以认为是一组，从上往下顺序执行；在所有组中，id 值越大，优先级越高，越先执行
   
   衍生 = DERIVED

关注点：id 号每个号码，表示一趟独立的查询。一个 sql 的查询趟数越少越好。

## select_type

select_type 代表查询的类型，主要是用于区别普通查询、联合查询、子查询等的复杂查询。

> https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_select_type

![image-20200819095557517](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819095557517.png) 

### SIMPLE

简单的 select 查询，查询中不包含子查询或者 UNION，即单表查询。

![image-20200819095848479](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819095848479.png)

###  PRIMARY

最外层的查询，查询中若包含任何复杂的子部分，最外层查询则被标记为 Primary

![image-20200819095941086](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819095941086.png)

### DERIVED

在 FROM 列表中包含的子查询被标记为 DERIVED(衍生)，MySQL 会递归执行这些子查询, 把结果放在临时表里。

### SUBQUERY

在 SELECT 或 WHERE 列表中包含了子查询。

![image-20200819100222561](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819100222561.png)

### DEPEDENT SUBQUERY

在 SELECT 或 WHERE 列表中包含了子查询, 子查询基于外层。

![image-20200819100300752](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819100300752.png)

都是 where 后面的条件，subquery 是单个值，dependent subquery 是一组值。

### UNCACHEABLE SUBQUERY 

无法使用缓存的子查询

![image-20200819100409467](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819100409467.png)

当使用了@@来引用系统变量的时候，不会使用缓存。

### UNION

若第二个 SELECT 出现在 UNION 之后，则被标记为 UNION；

若 UNION 包含在 FROM 子句的子查询中,外层 SELECT 将被标记为：DERIVED

![image-20200819100439822](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819100439822.png)

### UNION RESULT

从 UNION 表获取结果的SELECT

## table

显示这个数据是基于哪张表的。

## type

显示查询使用了哪种类型。是较为重要的一个指标，结果值从最好到最坏依次是：

```
system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index >ALL
```

一般来说，得保证查询至少达到 range 级别，最好能达到 ref。

### system

表只有一行记录（等于系统表），这是 const 类型的特列，平时不会出现，这个也可以忽略不计

### const

表示通过索引一次就找到了,const 用于比较 primary key 或者 unique 索引。因为只匹配一行数据所以很快。如将主键置于 where 列表中，MySQL 就能将该查询转换为一个常量。

![image-20200819103455497](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819103455497.png) 

### eq_ref

唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描。

![image-20200819103529511](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819103529511.png) 

### ref

非唯一性索引扫描，返回匹配某个单独值的所有行.本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而，它可能会找到多个符合条件的行，所以他应该属于查找和扫描的混合体。

没用索引前：

![image-20200819103615847](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819103615847.png) 

建立索引后：

![image-20200819103631367](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819103631367.png)

### range

只检索给定范围的行,使用一个索引来选择行。key 列显示使用了哪个索引一般就是在你的 where 语句中出现了 `between`、`<`、`>`、`in` 等的查询这种范围扫描索引扫描比全表扫描要好，因为它只需要开始于索引的某一点，而结束语另一点，不用扫描全部索引。

![image-20200819103734171](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819103734171.png)

![image-20200819103740850](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819103740850.png)

### index

Full Index Scan 全索引检索，index 和 all 的区别为 index 类型只遍历索引树，这通常比 all 快。因为索引文件通常比数据文件小。（也就是说，虽然 all 和 index 都是读全表，但 index 是从索引中读取的，而 all 是从硬盘中读取的）

出现 index 是 sql 使用了索引但是**没有通过索引进行过滤**，一般是使用了**覆盖索引**或者是**利用索引进行了排序分组。**

![image-20200819103814141](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819103814141.png) 

### all

Full Table Scan，将遍历全表以找到匹配的行。

![image-20200819105133183](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819105133183.png)

### index_merge

在查询过程中需要多个索引组合使用，通常出现在有 or 的关键字的 sql 中。

![image-20200819105324913](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819105324913.png)

### ref_or_null

对于某个字段既需要关联条件，也需要 null 值得情况下。查询优化器会选择用 ref_or_null 连接查询。

![image-20200819105338018](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819105338018.png)

### index_subquery

利用索引来关联子查询，不再全表扫描。

![image-20200819105400023](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819105400023.png)

![image-20200819105406067](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819105406067.png)

![image-20200819105411233](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819105411233.png)

### unique_subquery

该联接类型类似于 index_subquery。 子查询中的唯一索引。

![image-20200819105430186](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819105430186.png)

## possible_keys

显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段上若存在索引，则该索引将被列出， **但不一**
**定被查询实际使用。**

## key

实际使用的索引。如果为 NULL，则没有使用索引。查询中若使用了覆盖索引，则该索引仅出现在 key 列表中

## key_len

表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度。在不损失精度的前提下，长度越短越好。

ken_len 显示的值为索引字段的最大可能长度，并非实际使用长度，即key_len 是根据表定义计算而得，不是通过表内检索出的。

key_len 字段能够帮你检查是否充分的利用上了索引。ken_len 越长，说明索引使用的越充分。

![image-20200819111317105](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819111317105.png)

![image-20200819111324818](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819111324818.png)

如何计算：

①先看索引上字段的类型+长度比如 int=4 ; varchar(20) =20 ; char(20) =20
②如果是 varchar 或者 char 这种字符串字段，视字符集要乘不同的值，比如 utf-8 要乘 3,GBK 要乘 2，
③varchar 这种动态字符串要加 2 个字节
④允许为空的字段要加 1 个字节
第一组：key_len=age 的字节长度+name 的字节长度=4+1 + ( 20*3+2)=5+62=67
第二组：key_len=age 的字节长度=4+1=5

![image-20200819111346619](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819111346619.png)

## ref

显示索引的哪一列被使用了，如果可能的话，是一个常数。哪些列或常量被用于查找索引列上的值。

![image-20200819111417493](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819111417493.png)

## rows

rows 列显示 MySQL 认为它执行查询时必须检查的行数。越少越好！

![image-20200819111429182](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819111429182.png)

## Extra

其他的额外重要的信息。

### Using filesort

说明 mysql 会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取。MySQL 中无法利用索引
完成的排序操作称为“文件排序”。

- 出现 filesort 的情况：

  ![image-20200819135833465](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819135833465.png)

- 优化后，不再出现 filesort 的情况：

  ![image-20200819135850760](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819135850760.png)

查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度。

###  Using temporary

使了用临时表保存中间结果, MySQL 在对查询结果排序时使用临时表。常见于排序 order by 和分组查询 group
by。

- 优化前：

  ![image-20200819140058925](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819140058925.png)

- 优化后：

  ![image-20200819140113916](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819140113916.png)

###  Using index

Using index 代表表示相应的 select 操作中使用了覆盖索引(Covering Index)，避免访问了表的数据行，效率不错！如果同时出现 using where，表明索引被用来执行索引键值的查找;如果没有同时出现 using where，表明索引只是用来读取数据而非利用索引执行查找。

利用索引进行了排序或分组。

![image-20200819141648393](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819141648393.png)

###  Using where

表明使用了 where 过滤。

### Using join buffer

使用了连接缓存。

![image-20200819140212049](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819140212049.png)

### impossible where

where 子句的值总是 false，不能用来获取任何元组。

![image-20200819140243679](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819140243679.png)

### select tables optimized away

在没有 GROUPBY 子句的情况下，基于索引优化 MIN/MAX 操作或者对于 MyISAM 存储引擎优化 COUNT(*)操
作，不必等到执行阶段再进行计算，查询执行计划生成的阶段即完成优化。

- 在 innodb 中：

  ![image-20200819140304500](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819140304500.png)

- 在 Myisam 中：

  ![image-20200819140313372](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200819140313372.png)

### distinct

优化 distinct 操作，在找到第一匹配的元组后即停止找同样值的操作。