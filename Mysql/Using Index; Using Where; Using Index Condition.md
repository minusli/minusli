# （InnoDB）Using Index; Using Where; Using Index Condition

- 回表：通过数据地址查询具体数据内容，这里的地址不是id，或者内存指针
- 索引：主键索引or二级索引（聚簇索引or非聚簇索引）

#### Using Index:
    索引覆盖，无需回表
#### Using Index; Using where:
    索引覆盖，无需回表，但需要在索引通过where条件过滤结果
#### Using index condition:
    二级索引确定数据范围，然后通过ICP机制回表部分字段
#### Using Index Condition; Using where:
    二级索引确定数据范围，然后通过ICP机制回表部分字段，同时使用where条件进行过滤
#### Using where:
    使用where条件进行过滤