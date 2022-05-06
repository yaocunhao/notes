# 一、创建数据库

- use `database_name`
  - 存在切换到数据库
  - 不存在创建新的数据库
  - **注意**
    - 创建的数据库并不会直接显示，需要插入一些数据才会显示
    - 默认的数据库为test，如果没有`use`选择数据库，默认创建的集合会在test数据库之中
    - 集合只有在内容插入的时候才会真正的创建(**也就是说创建集合之后插入文档才算真正的创建**)

# 二、查看数据库

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

# 三、删除数据库

- db.dropDatabase()
  - 删除的是当前的数据库