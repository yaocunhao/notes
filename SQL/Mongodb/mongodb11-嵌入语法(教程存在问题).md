# 一、基本概念

- 当文档中需要引用来自不同集合数据的情况下(规范化数据模型)，我们可以使用 MongoDB 中的 DBRefs

- 语法

  ```shell
  { $ref : value, $id : value, $db : value }
  $ref：嵌入的集合；
  $id：嵌入的文档id；
  $db：可选字段，集合所在数据库名称；
  value：表示各个字段所对应的值。
  ```

- 演示

  ```shell
  #创建被引用表和引用表
  use('my_mongo')
  db.mt.insert({name:"zhangsan",age:2,sex:"boy"})
  db.mt.insert(
    {
      name:"lisi",
      age:1231,
      sex:"gril",
      "othre":{
        $ref:"mt",
        $id:"6374d000f02c24f1fb642fdf",
        $db:"my_mongo"
      }
    }
  )
  # 得到结果
  
  # a.被引用表
  { "_id" : ObjectId("6374d000f02c24f1fb642fdf"), "name" : "zhangsan", "age" : 2, "sex" : "boy" }
  
  # b.引用表
  { "_id" : ObjectId("6374d08f573c8ceb83ef65f7"), "name" : "lisi", "age" : 1231, "sex" : "gril", "othre" : DBRef("mt", "6374d000f02c24f1fb642fdf", "my_mongo") }
  ```

  

