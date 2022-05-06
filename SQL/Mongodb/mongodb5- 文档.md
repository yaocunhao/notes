# 一、概念

- 文档时MongoDB中数据的基本单位，由BSON一种计算机数据交换格式，类似于 JSON）格式的键/值对组成

# 二、插入文档

- db.collection_name.insert(document)
  - 如果插入的主键存在，那么提示异常，并且不保存数据
  - inert **可以插入多个数据(即可以插入数组)**
- db.colletion_name.insertOne()/replaceOne()
  - 之间id存在则更新数据，不存在则插入数据
  - **只可以插入一条数据**
- db.colletion_name.find()
  - 查看集合中的文档

# 三、演示

- insert 演示

  ```python
  # 文档的插入
  db.user.insert({name:"xiaohong",age:18},{name:"xiaoming",age:108}) 
  WriteResult({ "nInserted" : 1 })
  
  # 查看文档内容
  > db.user.find() 
  { "_id" : ObjectId("6260fdd62181824a4b81b673"), "name" : "xiaohong", "age" : 18 }
  
  # 文档中_id为主键 ID，它在每个文档中都是唯一的，在插入文档时，如果不指定 _id，MongoDB 则会为此文档自动分配一个唯一的 _id，当然也可以手动定义 _id 的值
  ```

- insertOne 演示

  ```python
  # insertOne 只可以插入一条数据
  > db.user.insertOne({name:"陈浩南",age:118})
  {
  	"acknowledged" : true,
  	"insertedId" : ObjectId("626105a12181824a4b81b676")
  }
  
  > db.user.find()
  { "_id" : ObjectId("6260fdd62181824a4b81b673"), "name" : "xiaohong", "age" : 18 }
  { "_id" : ObjectId("6260fe0b2181824a4b81b674"), "name" : "xiaohong", "age" : 18 }
  { "_id" : ObjectId("6260fe0b2181824a4b81b675"), "name" : "xiaoming", "age" : 108 }
  { "_id" : ObjectId("626105a12181824a4b81b676"), "name" : "陈浩南", "age" : 118 }
  ```

- insertMany()

  - 可以插入多个文档，在使用的时候传递一个文档数据即可

    ```python
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

    