## 595. 大的国家

### question

> 这里有张` World` 表
>

```
+-----------------+------------+------------+--------------+---------------+
| name            | continent  | area       | population   | gdp           |
+-----------------+------------+------------+--------------+---------------+
| Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |
| Albania         | Europe     | 28748      | 2831741      | 12960000      |
| Algeria         | Africa     | 2381741    | 37100000     | 188681000     |
| Andorra         | Europe     | 468        | 78115        | 3712000       |
| Angola          | Africa     | 1246700    | 20609294     | 100990000     |
+-----------------+------------+------------+--------------+---------------+
```

> 如果一个国家的面积超过300万平方公里，或者人口超过2500万，那么这个国家就是大国家。
>
> 编写一个SQL查询，输出表中所有大国家的名称、人口和面积。
>
> 例如，根据上表，我们应该输出:
>

```
+--------------+-------------+--------------+
| name         | population  | area         |
+--------------+-------------+--------------+
| Afghanistan  | 25500100    | 652230       |
| Algeria      | 37100000    | 2381741      |
+--------------+-------------+--------------+
```

### solution

```sql
select name, population, area 
from World 
where area > 3000000 or population > 25000000;
```

or 可能会导致索引失效，且 union 会更快

```sql
select name, population, area
from World 
where area > 3000000 
union
select name, population, area
from World 
where population > 25000000
```

## 175. 组合两个表

[175. 组合两个表](https://leetcode-cn.com/problems/combine-two-tables/)

### question

> 表1: `Person` 
>

```
+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
PersonId 是上表主键
```

> 表2: `Address`
>

```
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
AddressId 是上表主键
```

> 编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：
>

```
FirstName, LastName, City, State
```

### solution

1.根据条件，此题可根据外连接完成。

```sql
select p.FirstName,p.LastName,a.City,a.State
from Person p left join Address a
on p.PersonId = a.PersonId;
```

左连接：查询的是左表所有数据以及其交集部分。右表无数据补NULL