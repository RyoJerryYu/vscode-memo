# PostgreSQL 中 JSONB 的储存与索引

https://scalegrid.io/blog/using-jsonb-in-postgresql-how-to-effectively-store-index-json-data-in-postgresql/

## 为什么要用 PostgreSQL 来储存 JSON ？

1. 半结构数据
2. 多层次组织
3. 额外数据源同步

## JSON 与 JSONB 有何不同

1. JSON 会保存原格式（缩进）与 key 的顺序
2. JSON 会保留冲突的 key
3. JSONB 更快

## 什么情况下不要用

PG 会保存各个列的值分布统计信息，但 JSONB 的列不会。join 时会有些差异。参考： [When To Avoid JSONB In A PostgreSQL Schema.](https://heap.io/blog/engineering/when-to-avoid-jsonb-in-a-postgresql-schema)

JSONB 需要为每一行储存 JSON key 的名字，占用空间大

## 数据结构

JSONB 和 MongoDB 的 BSON 本质上很相似，都是用多级树形结构来储存 JSON

JSONB 与 TOAST ， 额外储存空间？

## JSONB 操作符、函数 略
https://www.postgresql.org/docs/9.5/functions-json.html

## JSONB 索引

三种类型： GIN ， BTREE ， HASH

### GIN

主要处理复合值，查多个值 （包含于等），可用 expression index 作用于内部结构
开启 pathops 选项可减小索引大小，但不支持范围查询

### B-Tree

GIN 索引不能支持 =, <, >, <=, >= 等操作符，但作用于路径的 B-Tree 索引支持

### Hash



## JSON PATH


