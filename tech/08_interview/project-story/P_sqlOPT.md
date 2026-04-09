# 怎么优化SQL？

我一般先通过 EXPLAIN 分析 SQL 执行计划，重点关注 type、key、rows 和 Extra 字段。
如果发现 type=ALL，说明全表扫描，会优先考虑建立索引。
如果出现 Using filesort 或 Using temporary，会考虑为 ORDER BY 或 GROUP BY 字段建立索引。
如果出现 Using join buffer，说明 JOIN 没有使用索引，需要为 JOIN 字段建立索引。
同时也会优化 SQL 写法，比如避免 SELECT *、避免函数操作索引字段、使用覆盖索引等。