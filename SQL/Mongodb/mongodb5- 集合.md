# 一、集合的概念

- 集合是一组文档的组合，类似于关系型数据库（例如 MySQL）中的数据表
- 集合存在于数据库中，且没有固定的结构，您可以向集合中插入不同格式或类型的数据。



# 二、集合的创建

- db.createCollection(name, options)

  - name：要创建的集合名称

  - options：可选参数，指定有关内存大小及索引的选项

  - | 字段        | 类型 | 描述                                                         |
    | ----------- | ---- | ------------------------------------------------------------ |
    | capped      | 布尔 | （可选）如果为 true，则创建固定集合，固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档，注意：如果该值为 true，则必须指定 size 参数 |
    | autoIndexId | 布尔 | （可选）如为 true，则自动在 _id 字段创建索引，默认为 false，注意：MongoDB 3.2 之后不再支持该参数 |
    | size        | 数值 | （可选）为固定集合指定一个最大值，即字节数，如果 capped 为 true，则需要指定该字段 |
    | max         | 数值 | （可选）指定固定集合中包含文档的最大数量                     |

- show collections / show tables 查看数据库之中的集合

  ```python
  > use study
  switched to db study
  
  > show tables
  website
  ```

- 当向集合插入文档时，MongoDB会先检查固定集合的size字段，然后检查max字段

  ```python
  # 创建一个user 集合
  > db.createCollection('user')
  { "ok" : 1 }
  
  # 查看数据库中的集合
  > show tables
  user
  
  # 创建一个固定集合 mycol，整个集合空间大小为 102400 KB, 文档最大个数为 1000 个
  > db.createCollectino('mycol',{capped:true,size:1024,max:1000})
  > db.createCollection('mycol',{capped:true,autoIndexId:true,size:1024,max:1000})
  {
  	"ok" : 1
  }
  ```

- 通常我们不需要手动创建集合，在插入文档的时候，MongoDB会自动创建集合

  ```python
  > db.class.insert({name:"Jim"})
  WriteResult({ "nInserted" : 1 })
  > show tables;
  class         # 自动创建了这个集合
  mycol
  user
  website
  ```

# 三、集合的删除

- db.集合名字.drop()

  - 从数据库删除指定集合(完全删除)，不会留下所有相关联的索引

  - 不需要带参数

    ```python
    > show tables; # 删除前
    class
    mycol
    user
    website
    
    > db.website.drop(); # 删除操作
    true
    
    > show tables; # 删除后
    class
    mycol
    user
    ```

    