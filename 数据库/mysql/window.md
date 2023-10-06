# 窗口函数简介

https://dev.mysql.com/doc/refman/8.0/en/window-functions.html

窗口函数用于分组排序后计算聚合函数，但不减少行数

```f(...) over (partition by xxx order by yyy )```

# 聚合函数

https://dev.mysql.com/doc/refman/8.0/en/window-function-descriptions.html

- min/max 等普通聚合函数
- ROW_NUMBER() 行号
- RANK() 排名，有间隔
- DENSE_RANK() 排名，没有间隔
- PERCENT_RANK() 排名百分比
- CUME_DIST() 累计分布
- FIRST_VALUE(expr) / LAST_VALUE(expr)
- LAG(expr [, N[, default]]) / LEAD(expr [, N[, default]]) 当前行的 前n 或 后n 行的值
- NTH_VALUE(expr, N) 第 n 行的值
- NTILE(N) 将所有行分为 n 个部分，返回当前行所在的位置
