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

- <font color=yellow>查询太慢</font>

  - [可能可DictField 有关](https://github.com/MongoEngine/mongoengine/issues/1230)
    - mongoEngine 内部一些转换太慢
    - mongoengine 在查询的时候，会调用非常多次数的`to_python` 来对dict 进行转换。 当对象非常大时，这里想要变快最好是采用 pymongo 的方法
  
- [提升插入效率的方法](https://devpress.csdn.net/mongodb/630487b67e6682346619ba30.html)

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

  - `save` 方法会监测当前对象有哪些值发生了变化(存在则更新，不存在则创建)。 如果是一个很大的嵌入式文档，MongoEngine这个监测的过程是非常慢的，因此如果能够确定此处数据为**新建**，那么可以调用`to_mongo`将数据转换成pymongo需要的`SON格式`使用pymongo进行存储即可
  - [mongoengine 没有提供从 `Document` 直接转成 dict 的方法，但 mongoengine 底层由 pymongo 实现，所以 `Document` 对象可以使用 `to_mongo` 方法转成 pymongo 支持的对象 `bson.son.SON`。 而 `SON` 对象使用 `to_dict` 方法可以得到 dict 对象](https://blog.perillaroc.wang/post/2018/2018-09-26-mongoengine-document-convert-to-python-dict/)

- [数据批量处理](https://www.cnpython.com/qa/1664291)
- [EmbeddedDocumentField 和 ReferenceField的区别](https://www.oomake.com/question/4974118)
  
  - `EmbeddedDocumentField`只是父文档的路径，如`DictField`，并存储在mongo中包含父文档的一条记录中。 要保存`EmbeddedDocument`，只需保存父文档
  - Reference 还需要保存子文档。<font color=yellow > 也就是说Reference不能将子文档变成json存储在父文档之中</font>
  
- `STRICT` 的作用
  
  ```python
  def test16():
    """
    测试STRICT 的作用
    如下所示， 当从json 传入数据进行初始化对象存储之时，如果
    多了字段&STRICT=True, 那么sex字段会被抛弃, 如果STRICT 为False
    那么会抛出异常
    """
  
    class Base2(Document):
      name = StringField()
      age = IntField()
      STRICT = True
  
    data = {"name": '123', "age": 456, 'sex': 123}
    data = json.dumps(data)
    Base2.from_json(data).save()
  
  ```
  
  
