# CTE(Common Table Expressions) 简介

普通表表达式（CTE）是一个命名的临时结果集，它存在于单个语句的范围内，并且可以在该语句的后面被引用，可能是多次引用。

# 生成序列数字

```mysql
with recursive cte(n) as (select 0 union all select n + 1 from cte where n < 10)
select *
from cte;
```