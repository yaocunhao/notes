[参考链接](https://blog.csdn.net/qq_18948359/article/details/88777066?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-88777066-blog-45012717.t0_searchtargeting_v1&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-88777066-blog-45012717.t0_searchtargeting_v1&utm_relevant_index=2)

# 一、基本介绍

- MongoDB 中的聚合操作用来处理数据并返回计算结果，聚合操作可以将多个文档中的值组合在一起，并可对数据执行各种操作，以返回单个结果

- 您可以使用 MongoDB 中的 aggregate() 方法来执行聚合操作，其语法格式如下：

  `db.collection_name.aggregate(aggregate_operation)`

# 二、聚合表达式

- 语法规范
  - ```
    { $group: { _id: <expression>, <field1>: { <accumulator1> : <expression1> }, ... } }
    ```
    
    - _id 必须存在。可以为null(为null就是所有的元素一起)。其余的计算字段是可选的，并使用 `<accumulator> `运算符计算
    - $group：分组、按照_id对应的元素进行分组，然后采用后面的聚合表达式
  
- 下表中展示了一些聚合表达式：

  | 表达式    | 描述                                         | 实例                                                         |
  | --------- | -------------------------------------------- | ------------------------------------------------------------ |
  | $sum      | 计算总和                                     | db.mycol.aggregate([{$group : {_id : "$author", num_tutorial : {$sum : "$likes"}}}]) |
  | $avg      | 计算平均值                                   | db.mycol.aggregate([{$group : {_id : "$author", num_tutorial : {$avg : "$likes"}}}]) |
  | $min      | 获取集合中所有文档对应值得最小值             | db.mycol.aggregate([{$group : {_id : "$author", num_tutorial : {$min : "$likes"}}}]) |
  | $max      | 获取集合中所有文档对应值得最大值             | db.mycol.aggregate([{$group : {_id : "$author", num_tutorial : {$max : "$likes"}}}]) |
  | $push     | 在结果文档中插入值到一个数组中               | db.mycol.aggregate([{$group : {_id : "$author", url : {$push: "$url"}}}]) |
  | $addToSet | 在结果文档中插入值到一个数组中，但不创建副本 | db.mycol.aggregate([{$group : {_id : "$author", url : {$addToSet : "$url"}}}]) |
  | $first    | 根据资源文档的排序获取第一个文档数据         | db.mycol.aggregate([{$group : {_id : "$author", first_url : {$first : "$url"}}}]) |
  | $last     | 根据资源文档的排序获取最后一个文档数据       | db.mycol.aggregate([{$group : {_id : "$author", last_url : {$last : "$url"}}}]) |

  

- 使用演示

  ```python
  # 数据
  db.Book.find()
  { "_id" : ObjectId("62f6194dfff25bcff15ee957"), "name" : "31", "type" : 2, "price" : 1231 }
  { "_id" : ObjectId("62f619af1a88ee67a26c21c3"), "name" : "dasdsa", "type" : 9999, "price" : 321321 }
  { "_id" : ObjectId("62f9ebccb2d9dc81e744886f"), "name" : "dasdsa", "type" : 3232, "price" : 10 }
  { "_id" : ObjectId("62f9ed55b2d9dc81e7448870"), "name" : 31, "type" : 9999, "price" : 10 }
  
  # 如果只进行聚合不进行后续操作，则每组返回一个
  > db.Book.aggregate({$group:{_id:"$type"}})
  { "_id" : 2 }
  { "_id" : 9999 }
  { "_id" : 3232 }
  
  # 按照名字计算 price总和, _id就是名字
  > db.Book.aggregate({$group : {_id : "$name", price_sum : {$sum : "$price"}}})
  { "_id" : "31", "price_sum" : 1231 }
  { "_id" : "dasdsa", "price_sum" : 321331 }
  { "_id" : 31, "price_sum" : 10 }
  
  
  # _id 为null，则计算所有的
  > db.Book.aggregate({$group : {_id : null, price_sum : {$sum : "$price"}}})
  { "_id" : null, "price_sum" : 322572 }
  ```

# 三、管道

![image-20220815153159620](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202208151531968.png)

- 在 UNIX 命令中，<font color=yellow>管道意味着可以将某些操作的输出结果作为下一个命令的参数</font>，以此类推。MongoDB 中同样也支持管道，即 MongoDB 会在一个管道处理完毕后将结果传递给下一个管道处理，而且管道操作是可以重复的。

  下面介绍了**聚合框架**中几个常用的操作：

  - $project：用于从集合中选择要输出的字段；
    - `_id字段是无法过滤的`
  - $match：用于过滤数据，只输出符合条件的文档，可以减少作为下一阶段输入的文档数量；
  - $group：对集合中的文档进行分组，可用于统计结果；
  - $sort：将输入文档进行排序后输出；
  - $skip：在聚合管道中跳过指定数量的文档，并返回余下的文档；
  - $limit：用来限制 MongoDB 聚合管道返回的文档数量；
  - $unwind：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值
  - $facet：根据条件，分类一次性查出想要的数据和数据的总和

- 示例

  ```python
  # project， 字段后面为1表示输出
  > db.Book.aggregate({$project:{name:1, price:1}})
  { "_id" : ObjectId("62f6194dfff25bcff15ee957"), "name" : "31", "price" : 1231 }
  { "_id" : ObjectId("62f619af1a88ee67a26c21c3"), "name" : "dasdsa", "price" : 321321 }
  { "_id" : ObjectId("62f9ebccb2d9dc81e744886f"), "name" : "dasdsa", "price" : 10 }
  { "_id" : ObjectId("62f9ed55b2d9dc81e7448870"), "name" : 31, "price" : 10 }
  
  # 多重管道连接， match 过滤结果
  # aggregate 之中采用[]包含元素的方式进行管道连接
  > db.Book.aggregate([{$project:{name:1, price:1}}, {$match:{price:10}}])
  { "_id" : ObjectId("62f9ebccb2d9dc81e744886f"), "name" : "dasdsa", "price" : 10 }
  { "_id" : ObjectId("62f9ed55b2d9dc81e7448870"), "name" : 31, "price" : 10 }
  
  # facet 分类一次性查出想要的数据
  > db.Book.aggregate({$facet:{"all_data_size":[{$count:"total"}]}})
  { "all_data_size" : [ { "total" : 4 } ] }
  
  # facet 分类查询，每个分类的参数都需要用[]来进行隔离
  # metadata: 统计文档数量
  # data：显示每个文档之中的name字段、
  > db.Book.aggregate({$facet: {"metadata": [{$count: "total"}],'data': [{$project:{name:1} }]}}).pretty()
  {
  	"metadata" : [
  		{
  			"total" : 4
  		}
  	],
  	"data" : [
  		{
  			"_id" : ObjectId("62f6194dfff25bcff15ee957"),
  			"name" : "31"
  		},
  		{
  			"_id" : ObjectId("62f619af1a88ee67a26c21c3"),
  			"name" : "dasdsa"
  		},
  		{
  			"_id" : ObjectId("62f9ebccb2d9dc81e744886f"),
  			"name" : "dasdsa"
  		},
  		{
  			"_id" : ObjectId("62f9ed55b2d9dc81e7448870"),
  			"name" : 31
  		}
  	]
  }
  ```

  

