# 一、投影

## 1.1 基本概念

- MongoDB 中的投影即查询指定的字段，而不是直接查询文档的全部字段
- 在使用 find() 方法时，如果不设置其中的第二个参数，那么在查询时将返回文档中的所有字段

- 如果仅需要设置第二个参数，而不需要设置第一个参数的话，则需要在第一个参数的位置添加一个空的花括号`{}`作为占位符，例如：`db.collection_name.find({}, {_id:1})`
- 第二个参数之中，字段后面的值为1的时候，表示显示该字段，为0的时候表示不显示

## 1.2 使用演示

```python
> db.table.find({age:{$lt:1111}})
{ "_id" : ObjectId("62cfb67b62b4befbb458bcb3"), "name" : "tom", "age" : 111, "sex" : "boy" }
{ "_id" : ObjectId("62cfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
{ "_id" : ObjectId("62dfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
{ "_id" : ObjectId("62cfc0c6cbad880a37984d39"), "age" : 44, "name" : "qq" }
{ "_id" : "62cfc0c6cbad880a37984d39", "age" : 44, "name" : "qq", "nama_name" : "qq_qq" }
> db.table.find({age:{$lt:111}})

> db.table.find({age:{$lt:111}},{"name":1,"age":1})
{ "_id" : ObjectId("62cfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
{ "_id" : ObjectId("62dfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
{ "_id" : ObjectId("62cfc0c6cbad880a37984d39"), "age" : 44, "name" : "qq" }
{ "_id" : "62cfc0c6cbad880a37984d39", "age" : 44, "name" : "qq" }


> db.table.find({age:{$lt:111}},{"name":0,"age":0}) # 为0：name和age都不显示
{ "_id" : ObjectId("62cfb6d962b4befbb458bcb4") }
{ "_id" : ObjectId("62dfb6d962b4befbb458bcb4") }
{ "_id" : ObjectId("62cfc0c6cbad880a37984d39") }
{ "_id" : "62cfc0c6cbad880a37984d39", "nama_name" : "qq_qq" }
```



# 二、limit方法

- 设置限制查询的条数

# 三、skip方法

- 设置跳过的文档数

  ```python
  # 显示所有的文档
  > db.table.find()
  { "_id" : ObjectId("62cfb67b62b4befbb458bcb3"), "name" : "tom", "age" : 111, "sex" : "boy" }
  { "_id" : ObjectId("62cfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
  { "_id" : ObjectId("62cfb72c62b4befbb458bcb5"), "info" : ObjectId("62cfb6d962b4befbb458bcb4") }
  { "_id" : ObjectId("62dfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
  { "_id" : ObjectId("62cfc0c6cbad880a37984d39"), "age" : 44, "name" : "qq" }
  { "_id" : ObjectId("62cfc507cbad880a37984d3a"), "name" : "cc", "age" : "44" }
  { "_id" : "62cfc0c6cbad880a37984d39", "age" : 44, "name" : "qq", "nama_name" : "qq_qq" }
  { "_id" : "62cfb67b62b4befbb458bcb3", "name" : "tom_tom" }
  { "_id" : ObjectId("62cfcba1c739ad279ca426bf"), "year" : "2022" }
  
  # 显示开始的两条
  > db.table.find().limit(2)
  { "_id" : ObjectId("62cfb67b62b4befbb458bcb3"), "name" : "tom", "age" : 111, "sex" : "boy" }
  { "_id" : ObjectId("62cfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
  
  
  # 跳过开始两条
  > db.table.find().skip(2)
  { "_id" : ObjectId("62cfb72c62b4befbb458bcb5"), "info" : ObjectId("62cfb6d962b4befbb458bcb4") }
  { "_id" : ObjectId("62dfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
  { "_id" : ObjectId("62cfc0c6cbad880a37984d39"), "age" : 44, "name" : "qq" }
  { "_id" : ObjectId("62cfc507cbad880a37984d3a"), "name" : "cc", "age" : "44" }
  { "_id" : "62cfc0c6cbad880a37984d39", "age" : 44, "name" : "qq", "nama_name" : "qq_qq" }
  { "_id" : "62cfb67b62b4befbb458bcb3", "name" : "tom_tom" }
  { "_id" : ObjectId("62cfcba1c739ad279ca426bf"), "year" : "2022" }
  ```

  

