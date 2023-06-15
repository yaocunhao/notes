- **[官方文档](http://docs.mongoengine.org/apireference.html)**
- [获取连接的数据库信息](https://stackoverflow.com/questions/47833179/mongoengine-how-to-get-database-name)
  - get_db()，获得的结果是一个`<class 'pymongo.database.Database'>`对象

- 索引的创建

  - [参考链接](https://stackoverflow.com/questions/45960493/how-to-create-index-for-referencefield-mongoengine)

  - Please be careful when creating indexes with MongoEngine. By default, the library always tries to create an index **every time you insert something**. Index creation in MongoDB is a blocking expensive operation that causes performance issues.You should always avoid it. My suggestion is to do the following

    ```python
    import mongoengine as me
    
    class MongoModel(me.Document):
    
        some_field = me.StringField()
        other_field = me.StringField()
    
        meta = {
            "auto_create_index": False, # 关闭自动创建索引
            "index_background": True,  # 索引再后台创建
            "indexes": [
                "other_field",
            ]
        }
    ```

- 查询太慢

  - [可能可DictField 有关](https://github.com/MongoEngine/mongoengine/issues/1230)
    - mongoEngine 内部一些转换太慢
  
- [提升效率的方法](https://devpress.csdn.net/mongodb/630487b67e6682346619ba30.html)

- 文档数据比engine类的成员多，是否能够读取？

  ```python
  def test12():
    """验证文档数据比engine类的成员多，是否能够读取？"""
    # 可以读取，但是多余的成员不会被读出来
    obj = IndexBase.objects.filter(num=21).first()
    print(obj.num, obj.task_id)
    # AttributeError: 'IndexBase' object has no attribute 'task_id'
   
  ```
  
- 如何获取pmongo

  ```python
  def test13():
    """获取pymongo"""
    obj = IndexBase._get_collection().find({"num": 21})
    # 得到的是 pymongo.cursor.Cursor 对象，需要循环处理
    print(obj[0]['num'])
    print(obj[0]['task_id'])
  ```

- 获取某一个文档的大小(不能在软件中运行,有误)

  ```shell
  在命令行中获取MongoDB某一个文档的大小，可以使用以下命令：
  1、进入mongo命令行
  2、权限认证
  3、执行下面的命令
  Object.bsonsize(db.collectionName.find({_id:ObjectId('文档ID')}))
  其中，collectionName是集合名称，ObjectId("文档ID")是要获取大小的文档的ID。该命令将返回以字节为单位的文档大小，包括文档元数据和数据。
  
  需要注意的是，该命令需要在MongoDB的安装路径下的bin目录中执行。如果没有将MongoDB的bin目录添加到系统的环境变量中，需要先切换到MongoDB的安装路径下的bin目录，然后再执行该命令。
  ```

- save

  - `save` 方法会监测当前对象有哪些值发生了变化(存在则更新，不存在则创建)。 如果是一个很大的嵌入式文档，MongoEngine这个监测的过程是非常慢的，因此如果能够此处数据为**新建**，那么可以调用`to_mongo`使用pymongo进行存储即可
  



