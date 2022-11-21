# 一、排序

- 要在 MongoDB 中对查询到的文档进行排序，您可以使用 sort() 方法，该方法的语法格式如下：`db.collection_name.find().sort({key:1})`
- 其中 key 用来定义要根据那个字段进行排序，后面的值 1 则表示以升序进行排序，若要以降序进行排序则需要将其设置为 -1
- 如果在使用 sort() 方法时未指定排序的选项，那么 sort() 方法将默认按 _id 的升序显示文档

# 二、索引

## 2.1 创建索引

- 语法

  ```
  db.collection_name.createIndex(keys, options)
  ```

- keys：由键/值对组成，其中键用来定义要创建索引的字段，值用来定义创建索引的顺序，1 表示按升序创建索引，-1 表示按降序来创建索引；

- options：可选参数，其中包含一组控制索引创建的选项，可选值如下表所示

  | 参数               | 类型          | 描述                                                         |
  | ------------------ | ------------- | ------------------------------------------------------------ |
  | background         | Boolean       | 可选参数，当值为 true 时，表示在后台构建索引，避免在创建索引的过程阻塞其它数据库操作，默认值为 false |
  | unique             | Boolean       | 创建唯一索引，当值为 true 时表示创建唯一索引，以避免重复数据的插入，默认为 false |
  | name               | string        | 索引的名称。如果未指定，MongoDB 将通过连接索引的字段名和排序顺序生成一个索引名称 |
  | dropDups           | Boolean       | 在建立唯一索引时是否删除重复记录，设置为 true 则表示创建唯一索引，默认值为 false，3.0 版本之后废弃 |
  | sparse             | Boolean       | 对文档中不存在的字段数据不启用索引，这个参数需要特别注意，如果设置为 true 的话，则在索引字段中不会查询出不包含对应字段的文档。默认值为 false |
  | expireAfterSeconds | integer       | 指定一个以秒为单位的数值，完成 TTL 设定，设定集合的生存时间  |
  | v                  | index version | 索引的版本号，默认的索引版本取决于 mongod 创建索引时运行的版本 |
  | weights            | document      | 索引权重值，数值在 1 到 99999 之间，表示该索引相对于其他索引字段的得分权重 |
  | default_language   | string        | 对于文本索引，该语言用于确定停用词列表以及词干分析器和令牌生成器的规则，默认为英语 |
  | language_override  | string        | 对于文本索引，指定文档中包含要替代默认语言的语言的字段名称，默认值为 language |

## 2.2 删除索引

- dropIndex

  ```
  db.collection_name.dropIndex(index)
  ```

  - index 用来指定要删除的索引，可以是索引名称`key`的形式，也可以是`{key:1}`的形式

- dropindexes()

  - 与 dropIndex() 方法不同 dropIndexes() 方法可以同时删除集合中的多个索引

    ```
    db.collection_name.dropIndexes()
    ```

  - 需要注意在使用该方法的时不提供任何参数

## 3.3 获取集合中的所有索引

- getindexes()
  - 获取集合中的所有索引，同样不需要任何参数

## 3.4 演示

```shell
use('my_mongo')
db.mt.createIndex({"age":1}) // 按照升序来给age这个字段创建索引
db.mt.createIndex({"name":1}) // 按照升序来给age这个字段创建索引
db.mt.getIndexes() // 获取所有索引
db.mt.dropIndexes() //删除所有的索引
db.mt.getIndexes() // 获取所有索引，最后只剩下_id 这个主键索引
```

