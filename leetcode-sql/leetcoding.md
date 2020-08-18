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

- 暴力解决

```sql
select name, population, area 
from World 
where area > 3000000 or population > 25000000;
```

- or 可能会导致索引失效，且 union 会更快

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

根据条件，此题可根据外连接完成。

```sql
select p.FirstName,p.LastName,a.City,a.State
from Person p left join Address a
on p.PersonId = a.PersonId;
```

左连接：查询的是左表所有数据以及其交集部分。右表无数据补NULL

## 176. 第二高的薪水

[176. 第二高的薪水](https://leetcode-cn.com/problems/second-highest-salary/) 

### question

> 编写一个 SQL 查询，获取 `Employee` 表中第二高的薪水（Salary） 。

```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

> 例如上述 `Employee` 表，SQL查询应该返回 `200` 作为第二高的薪水。如果不存在第二高的薪水，那么查询应返回 `null`。

```
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

### solution

- 先排除最高的工资的所有信息
- 再查出剩下最高的工资信息
- IFNULL: 如果只有一条记录则会返回NULL

```sql
select IFNULL(max(Salary), NULL) as SecondHighestSalary
from Employee
where Salary < (select max(Salary) from Employee)
```

- 将不同的薪资按降序排序，然后使用 `LIMIT` 子句获得第二高的薪资。

```sql
SELECT
    IFNULL(
      (SELECT DISTINCT Salary
       FROM Employee
       ORDER BY Salary DESC
        LIMIT 1 OFFSET 1),
    NULL) AS SecondHighestSalary
```

## 181. 超过经理收入的员工

[181. 超过经理收入的员工](https://leetcode-cn.com/problems/employees-earning-more-than-their-managers/) 

### question

> `Employee` 表包含所有员工，他们的经理也属于员工。每个员工都有一个 Id，此外还有一列对应员工的经理的 Id。

```
+----+-------+--------+-----------+
| Id | Name  | Salary | ManagerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |
+----+-------+--------+-----------+
```

> 给定 `Employee` 表，编写一个 SQL 查询，该查询可以获取收入超过他们经理的员工的姓名。在上面的表格中，Joe 是唯一一个收入超过他的经理的员工。

```
+----------+
| Employee |
+----------+
| Joe      |
+----------+
```

### solution

- 通过自连接找到a表经理ID等于b表ID 且 a表工资 大于 b表工资

```sql
select a.name as Employee
from Employee as a join Employee as b
on a.ManagerId = b.Id and a.Salary > b.Salary;
```

```sql
# Write your MySQL query statement below
select a.name as Employee
from Employee as a, Employee as b
where a.ManagerId = b.Id 
and a.Salary > b.Salary; 
```





