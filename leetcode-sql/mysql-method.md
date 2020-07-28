# 条件判断函数

## IF(expr, v1, v2)

```sql
IF(expr, v1, v2)
--- 如果表达式 expr 为 true 返回 v1，否则返回 v2
```

## IFNULL(v1, v2)

```sql
IFNULL(v1, v2)
--- 如果 v1 为 NULL 返回 v2， 否则返回 v1
```

## CASE...WHEN...THEN END

```sql
CASE expr
WHEN v1 THEN r1
[WHEN v2 THEN r2]
[ELSE rn]
END
--- 类似 switch case
```

