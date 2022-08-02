# 一、概念

- 文档时MongoDB中数据的基本单位，由BSON一种计算机数据交换格式，类似于 JSON）格式的键/值对组成

# 二、插入文档

- 插入文档分为两种

  - 多功能接口，插入成功不提示
  - 单一功能接口，插入成功提示

- db.collection_name.insert(document)

  - 如果插入的主键存在(**也就是id如果存在**)，那么提示异常，并且不保存数据

  - insert **可以插入多个数据(即可以插入数组)**

    ```python
    # 文档的插入
    db.user.insert({name:"xiaohong",age:18},{name:"xiaoming",age:108}) 
    WriteResult({ "nInserted" : 1 })
    
    # 查看文档内容
    > db.user.find() 
    { "_id" : ObjectId("6260fdd62181824a4b81b673"), "name" : "xiaohong", "age" : 18 }
    
    # 文档中_id为主键 ID，它在每个文档中都是唯一的，在插入文档时，如果不指定 _id，MongoDB 则会为此文档自动分配一个唯一的 _id，当然也可以手动定义 _id 的值
    ```

- db.colletion_name.`insertOne()/insertMany()`

  - **只可以插入一条数据/传递一个文档数组插入多条数据**，并且提示更新的情况

    ```python
    # insertOne 只可以插入一条数据
    > db.user.insertOne({name:"陈浩南",age:118})
    {
    	"acknowledged" : true,
    	"insertedId" : ObjectId("626105a12181824a4b81b676") # 提示插入成功的Id
    }
    
    > db.user.find()
    { "_id" : ObjectId("6260fdd62181824a4b81b673"), "name" : "xiaohong", "age" : 18 }
    { "_id" : ObjectId("6260fe0b2181824a4b81b674"), "name" : "xiaohong", "age" : 18 }
    { "_id" : ObjectId("6260fe0b2181824a4b81b675"), "name" : "xiaoming", "age" : 108 }
    { "_id" : ObjectId("626105a12181824a4b81b676"), "name" : "陈浩南", "age" : 118 }
    
    
    # 插入文档数组
    > db.user.insertMany([{name:"shanji"},{name:"hongxing"}])
    {
    	"acknowledged" : true,
    	"insertedIds" : [
    		ObjectId("626106792181824a4b81b677"),
    		ObjectId("626106792181824a4b81b678")
    	]
    }
    
    > db.user.find()
    { "_id" : ObjectId("626106792181824a4b81b677"), "name" : "shanji" }
    { "_id" : ObjectId("626106792181824a4b81b678"), "name" : "hongxing" }
    ```

# 三、查询文档

## 3.1 基本查询操作

- db.`table_name`.find(query, projection)

  - table_name 表示集合的名字
  - query可选字段，使用查询操作符指定**查询条件**
  - projection可选字段，用投影操作符指定返回的键。查询时若要返回文档中所有键值，只需省略该参数即可

- `pretty()`方法

  - db.table_name.find(query, projection).pretty() 相当于一个函数，使得查询出来的结果，**格式更加优美**
  - **注意，使用在find后面可以，在其它指令后面不行**

- db.table_name.findOne(query,projection)

  - findOne() 方法仅能返回一个查询到的文档

  ```python
  > db.table.find({name:"tom11"}).pretty() # find查找不报错
  
  > db.table.findOne({name:"tom11"}).pretty() # findOne 查找是报错的
  uncaught exception: TypeError: db.table.findOne(...) is null :
  ```

## 3.2 条件查询

- MongoDB 中也支持类似关系型数据库中 where 字句的条件查询

- 语法为 `key 操作符 value`

  - 等于 `:`

    ```
    > db.table.find( {name:'tom'} )
    { "_id" : ObjectId("62cfb67b62b4befbb458bcb3"), "name" : "tom", "age" : 111, "sex" : "boy" }
    ```

  - 小于 `$lt`

    ```
    > db.table.find({age:{$lt:100}})
    { "_id" : ObjectId("62cfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
    { "_id" : ObjectId("62dfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
    ```

  - 小于或等于 `$lte:`

    ```
    > db.table.find({age:{$lte:44}})
    { "_id" : ObjectId("62cfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
    { "_id" : ObjectId("62dfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
    ```

    

  - 大于 `$gt`

    ```
    > db.table.find({age:{$gt:44}})
    { "_id" : ObjectId("62cfb67b62b4befbb458bcb3"), "name" : "tom", "age" : 111, "sex" : "boy" }
    ```

  - 大于或等于 `$gte`

    ```
    > db.table.find({age:{$gte:111}}).pretty()
    {
    	"_id" : ObjectId("62cfb67b62b4befbb458bcb3"),
    	"name" : "tom",
    	"age" : 111,
    	"sex" : "boy"
    }
    ```

  - 不等于 `$ne`

    ```python
    > db.table.find({age:{$ne:44}})
    { "_id" : ObjectId("62cfb67b62b4befbb458bcb3"), "name" : "tom", "age" : 111, "sex" : "boy" }
    { "_id" : ObjectId("62cfb72c62b4befbb458bcb5"), "info" : ObjectId("62cfb6d962b4befbb458bcb4") } # 没有age字段也显示出来了
    ```

  - 存在于数据之中的值 `$in [v1,v2...]`

    ```
    > db.table.find({age:{$in:[1,2,344,111]}})
    { "_id" : ObjectId("62cfb67b62b4befbb458bcb3"), "name" : "tom", "age" : 111, "sex" : "boy" }
    ```

  - 不在数组之中 `$nin [v1,v2...]`

    ```
    > db.table.find({age:{$nin:[1,2,344,111]}})
    { "_id" : ObjectId("62cfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
    { "_id" : ObjectId("62cfb72c62b4befbb458bcb5"), "info" : ObjectId("62cfb6d962b4befbb458bcb4") }
    { "_id" : ObjectId("62dfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
    ```

- AND 条件语句

  - MongoDB 的 find() 方法可以**传入多个键（key），每个键以逗号隔开**，类似于常规 SQL 语句中的 AND 条件语句

    ```python
    > db.table.find({age:{$lt:111}})
    { "_id" : ObjectId("62cfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
    { "_id" : ObjectId("62dfb6d962b4befbb458bcb4"), "name" : "tt", "age" : 44 }
    { "_id" : ObjectId("62cfc0c6cbad880a37984d39"), "age" : 44, "name" : "qq" }
    
    > db.table.find({$and:[{age:{$lt:111}},{name:"qq"}]})
    { "_id" : ObjectId("62cfc0c6cbad880a37984d39"), "age" : 44, "name" : "qq" }
    
    # 省略and关键字
    > db.table.find({age:{$lt:111}},{name:"tt"})
    { "_id" : ObjectId("62cfb6d962b4befbb458bcb4"), "name" : "tt" }
    ```

- or条件语句

  - OR 条件语句需要使用 $or 关键字

    ```
    > db.table.find({'age':111})
    { "_id" : ObjectId("62cfb67b62b4befbb458bcb3"), "name" : "tom", "age" : 111, "sex" : "boy" }
    
    > db.table.find({$or:[{'age':111},{name:"qq"}]})
    { "_id" : ObjectId("62cfb67b62b4befbb458bcb3"), "name" : "tom", "age" : 111, "sex" : "boy" }
    { "_id" : ObjectId("62cfc0c6cbad880a37984d39"), "age" : 44, "name" : "qq" }
    ```

- and 和 or 联合使用

  ```python
  # (name 不为tt） 并且 （name为tom或者qq)
  > db.table.find({name:{$ne:"tt"} , $or:[{name:"tom"},{name:"qq"}]})
  { "_id" : ObjectId("62cfb67b62b4befbb458bcb3"), "name" : "tom", "age" : 111, "sex" : "boy" }
  { "_id" : ObjectId("62cfc0c6cbad880a37984d39"), "age" : 44, "name" : "qq" }
  ```



# 四、更新文档

- update 基本语法

  - `db.collection_name.update( <query>,  <update>,{ upsert: <boolean>,    multi: <boolean>,writeConcern: <document>  })`

    - query 表示查询条件

    - update 表示更新操作符(如 $set、 等等)

    - upsert 可选参数，默认false，表示当更新记录不存在时，是否进行数据的插入(**默认false不插入**)

    - multi 可选参数，默认false，当查找出来多条数据时，表示更新一条数据还是全部数据(**默认只更新第一条**)

    - writeConcern：可选参数，用来定义抛出异常的级别

      ```python
      # 查看
      > db.table.find({name:'123'})
      { "_id" : ObjectId("62cfcba1c739ad279ca426bf"), "name" : "123" }
      { "_id" : ObjectId("62cfcba6c739ad279ca426c0"), "name" : "123" }
      
      # 更新
      > db.table.update({name:'123'},{$set:{age:100000}})
      
      # 没有设置multi，只更新一条数据
      > db.table.find({name:'123'})
      { "_id" : ObjectId("62cfcba1c739ad279ca426bf"), "name" : "123", "age" : 100000 }
      { "_id" : ObjectId("62cfcba6c739ad279ca426c0"), "name" : "123" }
      
      # 设置multi,更新多条数据
      > db.table.update({name:"123"},{$set:{food2:"beef2"}},{multi:true})
      WriteResult({ "nMatched" : 2, "nUpserted" : 0, "nModified" : 2 })
      > db.table.find({name:"123"})
      { "_id" : ObjectId("62cfcba1c739ad279ca426bf"), "name" : "123", "age" : 100000, "sex" : "girl", "food" : "beef", "food2" : "beef2" }
      { "_id" : ObjectId("62cfcba6c739ad279ca426c0"), "name" : "123", "food" : "beef", "food2" : "beef2" }
      ```

- save

  - 根据传入的id进行判断，如果存在则用新的数据进行替换，否则插入一条新的(**本质上就是覆盖式的写入**)

    ```python
    # 查看原有的数据
    > db.table.find({name:"123"})
    { "_id" : ObjectId("62cfcba1c739ad279ca426bf"), "name" : "123", "age" : 100000, "sex" : "girl", "food" : "beef", "food2" : "beef2" }
    { "_id" : ObjectId("62cfcba6c739ad279ca426c0"), "name" : "123", "food" : "beef", "food2" : "beef2" }
    
    # save 更新写入
    > db.table.save({"_id" : ObjectId("62cfcba1c739ad279ca426bf"),"year":"2022"})
    WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
    
    # 查找
    > db.table.find({year:"2022"})
    { "_id" : ObjectId("62cfcba1c739ad279ca426bf"), "year" : "2022" }
    ```

    

# 五、删除文档

- 基本语法

  - `db.collection_name.remove(
      <query>,                        表示删除条件
      {
        justOne: <boolean>,           默认false删除所有匹配的文档
        writeConcern: <document>      可选参数，定义抛出异常的级别
      }
    )`

  - **如果没有指定条件，表示删除该集合之中所有的文档（一般是会有保护设置的）**

    ```python
    > db.table.find({name:'123'})
    { "_id" : ObjectId("62cfcba6c739ad279ca426c0"), "name" : "123", "food" : "beef", "food2" : "beef2" }
    
    > db.table.remove({"name":"123"}) # 进行删除
    WriteResult({ "nRemoved" : 1 })
    
    > db.table.find({"name":"123"})
    ```

    