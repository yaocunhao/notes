# 一、基本介绍
- **直接**继承`Documents`的类一般用作**集合**的基类。继承"基类"的子类就是该集合中**文档**的基本数据结构

- **类的基本属性由meta来设置**，下面介绍一些常见的meta属性设置

- 默认情况下，基类的名称就是集合的名称(会被转换为snake_cace 的形态)。该基类的子类都会被存储于这个集合之中。可以通过meta字典之中的collection 字段来指定集合
  ``` python
  def test6():
    """测试指定基类的集合名称"""
    """测试成功，collection  就是指定集合的名称的字段"""
    class Base(Document):
      """测试修改集合的名称"""
      name = StringField()
      meta = {"collection": "base2"}
  
    Base(name='b_name').save()
    print('end')
  ```
  
- meta 的  allow_inheritance 默认为false不可以创建子类。这是因为当开启了继承模式，会添加一个隐藏字段`_cls`

- meta 的 `max_documents` 和 `max_size` 表示集合中允许存储的最大文档数量和集合的容量
  - 这两个数都应该是256的倍数
  - 如果设置了最大数量，没有设置最大容量，则默认为10MB
  
- meta 的 `indexs` 字段可以用于创建索引，在字段的前方 `+ -`符号表示索引的方向
  ``` python
  def test8():
    """通过meta创建索引"""
    """创建成功"""
    class Base3(Document):
      name = StringField()
      age = StringField()
      meta = {"collection": "base3", "indexes": ["name", "-age"]} # 注意使用list/tuple/单独的属性名称
    Base3(name="12344", age="456789").save()
  
    # db.base3.getIndexes() 查看索引
    # key:索引
    # name:索引名称
    [
      { v: 2, key: { _id: 1 }, name: '_id_' },
      { v: 2, key: { name: 1 }, name: 'name_1', background: false },
      { v: 2, key: { age: -1 }, name: 'age_-1', background: false }
    ]
  ```
  
- meta 的 auto_create_index 指定为false可以关闭自动索引的创建

- 默认情况下，如果allow_inheritance为True， _cls将被添加到每个索引(不包含列表)的开头
  -  可以在meta之中设定 index_cls为False来关闭
   ``` python
  
  db.post.getIndexes() # 默认两个索引
  [
    { v: 2, key: { _id: 1 }, name: '_id_' },
    { v: 2, key: { _cls: 1 }, name: '_cls_1', background: false }
  ]
  
  def test9():
  """关闭cls索引"""
  """关闭成功"""
  
    class Base4(Document):
      name = StringField()
      age = StringField()
      meta = {
          "collection": "base4",
          "allow_inheritance": True,
          "index_cls": False
      }
    Base4(name="12344", age="456789").save()
  
  # db.base4.getIndexes() cls索引被关闭
  # [ { v: 2, key: { _id: 1 }, name: '_id_' } ]
   ```

- 如果存储数据的时候，字段没有在Model之中进行声明，将会导致FieldDoesNotExist,可以在meta之中将strict设置为False来关闭这个错误

# 二、基本接口介绍

- objects: 访问时惰性创建的QuerySet对象，里面都是文档对象

## 2.1 增

- save()
  - 将文档保存到数据库中。如果文档已经存在，则更新它，否则将创建它。返回保存的对象实例

## 2.2 删

- delete(signal_kwargs=None, **write_concern)
  - 从数据库中删除文档
  - signal_kwargs：可选参数，字典类型，传递给信号调用
  - write_concern：待完成

- classmethod drop_collection()
  - 删除集合
  - 如果该文档没有集合引发异常，比如它是抽象的

```python
# Post类就是对应的集合对象，数据库在连接的时候已经选定了
# Post.objects 就是获取集合中的文档对象
def test10():
  """删除集合"""
  Post.drop_collection()

def test11():
  """删除文档"""
  for docs in Post.objects(title="MongoEngine Documentation"):
    docs.delete()
```

## 2.3 查

- 具体方法查看Querysert

## 2.4 改

- 查询得到对象，改变值后直接save

  ```python
  def test12():
    """修改文档"""
    objs = Post.objects.first()
    objs.title = '12311231'
    objs.save()
  ```

- modify(query=None, **update)

  - 原子性的更新操作

  - 如果文档已更新，则返回True;如果数据库中的文档与查询不匹配，则返回False

  - query: 匹配条件,如果没有匹配则不对当前文档对象进行任何的操作(相当于进行查询)

  - update：更新的字段和新的数据

    ```python
    def test14():
      """原子性更新操作"""
      objs = Post.objects.first()
      data = objs.modify({"title": "4444"},
                         title="11119999",
                         tags=['123', '456', '789'])
    ```

    

## 2.5 索引

- classmethod compare_indexes()
  -  将MongoEngine中定义的索引与数据库中现有的索引进行比较。返回任何丢失的/额外的索引
- classmethod create_index(keys, background=False, **kwargs)
  - 进行索引的创建
  - keys: 单独的key或者为一个list，可以使用`+-`来作为前缀
  - background： 允许在后台创建索引，默认为False
- classmethod ensure_index(key_or_list, background=False, **kwargs)
  - 创建索引，create_index 已弃用
- classmethod ensure_indexes()
  - 待定

## 2.6  其它接口

- Parameters
  - values: 文档的键和值的字典。它可能包含额外的保留关键字，例如" __auto_convert "
  - __auto_conver: 如果为True，每个字段会通过本身的to_python 方法转换为python类型
  - _create:指示这是一个全新的文档还是旧的已经存在的文档，默认为true

- cascade_save(**kwargs)
  - 递归地保存文档上的任何引用和泛型引用
  
- clean()
  -  钩子，用于在运行验证之前执行文档级数据清理(通常是验证或赋值)
  -  此方法引发的任何ValidationError都不会与特定字段关联
  -  它将与NON_FIELD_ERRORS定义的字段有特殊情况的关联
  
- classmethod from_json(json_data, created=False, **kwargs)
  - 将Json数据转换为文档形式
  - create： 如果为True，则认为文档是全新的; 如果为False且没有提供ID，则认为该文档是全新的;如果为False并提供了一个ID，则假定对象已经被持久化了(这将影响后续调用.save())
  
- get_text_score()
  - 从文本查询中获取文本得分
  
- classmethod list_indexes()
  - 列出应为Document集合创建的所有索引。它包括来自父类和子类的所有索引
  - 注意，它只返回索引的字段，而不返回索引的选项
  
- my_metaclass
  - alias of mongoengine.base.metaclasses.TopLevelDocumentMetaclass
  
- property pk
  - 获取主键
  
    ```python
      objs = Post.objects.first()
      print(objs.pk)
    ```
  
- classmethod register_delete_rule(document_cls, field_name, rule)
  - 此方法注册删除规则，以便在删除此对象时应用
  
- reload(*fields, **kwargs)
  - 从数据库重新加载所有属性
  - fields:要重新加载的字段列表
  - max_depth:深度解引用跟随