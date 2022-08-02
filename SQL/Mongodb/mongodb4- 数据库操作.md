# 一、数据库操作

## 1.1 创建数据库

- use `database_name`
  - 存在切换到数据库
  - 不存在创建新的数据库
  - **注意**
    - 创建的数据库并不会直接显示，需要插入一些数据才会显示
    - 默认的数据库为test，如果没有`use`选择数据库，默认创建的集合会在test数据库之中
    - 集合只有在内容插入的时候才会真正的创建(**也就是说创建集合之后插入文档才算真正的创建**)

## 1.2 查看数据库

- db
  - 查看当前选择的数据库

- show dbs 

  - 查看当前所有所有的数据库

    ```python
    > db.website.insert({name:'tom',age:'18'}) # 先插入数据，才会显示新创建的数据库
    WriteResult({ "nInserted" : 1 }) 
    
    > show dbs
    admin   0.000GB
    config  0.000GB
    local   0.000GB
    study   0.000GB
    test    0.000GB
    ```

## 1.3 删除数据库

- db.dropDatabase()
  - 删除的是当前的数据库

# 二、集合操作

## 2.1 创建集合

- MongoDB 中的集合是一组文档的组合，类似于关系型数据库（例如 MySQL）中的数据表。集合存在于数据库中，且没有固定的结构，您可以向集合中插入不同格式或类型的数据
- 集合类似于表，文档类似于数据行

- 集合创建命令 `db.createCollection(name, options)`

  - 参数说明如下：

    - name: 要创建的集合名称；

    - options: 可选参数, 指定有关内存大小及索引的选项，可选值如下表所示

      | 字段        | 类型 | 描述                                                         |
      | ----------- | ---- | ------------------------------------------------------------ |
      | capped      | 布尔 | （可选）如果为 true，则创建固定集合，**固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档**，注意：如果该值为 true，则必须指定 size 参数.**指定是否为固定集合** |
      | autoIndexId | 布尔 | （可选）如为 true，则自动在 _id 字段创建索引，默认为 false，注意：**MongoDB 3.2 之后不再支持该参数** |
      | size        | 数值 | （可选）**为固定集合指定一个最大值**，即字节数，如果 capped 为 true，则需要指定该字段 |
      | max         | 数值 | （可选）指定固定集合中**包含文档的最大数量**                 |

    - 当我们在集合中插入文档时，MongoDB 会首先检查固定集合的 size 字段，然后检查 max 字段

- 虽然 MongoDB 中提供了单独的创建集合的方法，但通常我们不需要手动创建集合，因为当您在插入文档时，MongoDB 会自动创建集合

- 操作示例

  ```sql
  > db # 查看当前所在数据库
  biancheng 
  
  > db.createCollection("name") # 创建集合name
  { "ok" : 1 }
  
  > show tables # 查看集合
  name
  
  > db.jihe.insert({name:'tom',age:18}) # 插入的时候自动创建集合
  WriteResult({ "nInserted" : 1 })
  > show tables
  jihe # 自动创建的集合
  name
  
  # 创建集合，并且指定大小为100kb，文档最大个数为200
  > db.createCollection("my_mongo",{capped:true,size:100,max:200})
  { "ok" : 1 }
  > show tables
  jihe
  my_mongo
  name
  
  ```

  