## 内连接

平时使用的多表查询用where 消除无用数据就属于一种内连接，称为隐式内连接                                                              

`select sth from a, b where a.id = b.id` 

【显式内连接】是使用 `inner join` 关键字来说明的连接方式（inner可省略），查询出来的数据是两表中交集的部分。

```sql
select sth from a join b on a.column = b.column
```

内连接条件前面要用 on

## 外连接

平时我们说的左连接和右连接都属于外连接

#### 左外连接：

* 语法：select 字段列表 from 表1 left [outer] join 表2 on 条件；

* 查询的是左表所有数据以及其交集部分。

* 例子：

  ```mysql
  -- 查询所有员工信息，如果员工有部门，则查询部门名称，没有部门，则不显示部门名称
  SELECT 	t1.*,t2.`name` FROM emp t1 LEFT JOIN dept t2 ON t1.`dept_id` = t2.`id`;
  ```

#### 右外连接：

* 语法：select 字段列表 from 表1 right [outer] join 表2 on 条件；

* 查询的是右表所有数据以及其交集部分。

* 例子：

  ```mysql
  SELECT 	* FROM dept t2 RIGHT JOIN emp t1 ON t1.`dept_id` = t2.`id`;
  ```

### 

## sql 连接图示

![1.jpg](https://gitee.com/zsy0216/typora-image/raw/master/typora/ad3df1c4ecc7d2dbe85f92cdde8ec9a731fdd20dc4c5629ecb372b21de26c682-1.jpg)