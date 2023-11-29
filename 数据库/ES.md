# ES常用命令

ES数据层级如下：

```
Elasticsearch -> Indices -> Types -> Documents -> Fields
类比关系型数据库:
Relational DB -> Databases -> Tables -> Rows -> Columns
```



## 查看所有索引状态

```
GET _cat/indices?v
```



## 删除索引

```
# 删除索引结构，数据一同删除
DELETE index_name

# 只删除数据，不删除索引结构
POST index_name/_delete_by_query
{
	"query": {
		"match_all": {}
	}
}
```



## 索引别名

```
# 查询某一个索引别名
GET index_name/_alias

# 查询所有索引的别名
GET /_aliases

# 创建索引，并将别名指向它（alias_name是别名，index_name是索引名称）
PUT /index_name
PUT /index_name/_alias/alias_name
```



## 查询结果中各个参数的含义

查询结果中各个字段的含义：

1. **took**：此次检索耗费的时间，单位是毫秒
2. **timed_out**：是否超出规定的检索时间
3. **_shards**：显示各分片查询是否成功。索引被分成多个分片，查询请求会被分发到所有主分片（primary shard）或者主分片对应的备份分片（replica shard）
4. **hits**：命中的文档情况，有以下几个参数
   * **total：**符合条件的文档总数，即命中数
   * **max_score：** Lucene底层对检索到的文档的相关度的评分,相关度越高,说明越匹配,score的值也就越高
   * **hits：**命中所有document的详细数据

