# 一、嵌入式文档

- `classmongoengine.EmbeddedDocument(*args, **kwargs)`

-  不存储在自己的集合中的Document
- EmbeddedDocuments应该通过EmbeddedDocumentField字段类型作为文档上的字段使用

# 二、嵌入式文档字段

- **EmbeddedDocumentField**

- 演示

  ```python
  from mongoengine import *
  
  connect('mon_test',
          host="",
          port=,
          username='',
          password="",
          authentication_source="admin")
  
  
  # 被嵌入的文档
  class C1(EmbeddedDocument):
    name = StringField()
  meta = {"collection": "em_1"} # 设置集合名称
  
  
  class C2(Document):
    age = IntField()
    name = EmbeddedDocumentField(C1)
  
  def test1():
    """测试嵌入式文档的存储方式"""
    c1 = C1(name="ccc")
    c2 = C2(age=1, name=c1).save()
    
    # db.c2.find()
    # { "_id" : ObjectId("637e0296bcf764aec0eedd96"), "age" : 1, "name" : { "name" : "ccc" } }
  
    # test1 测试结果：
      # 没有创建 c1集合，创建了c2集合
  
  
  def test2():
    """测试如何读取"""
    c2 = C2.objects.first()
    print(c2.name)  # C1 object
    print(c2.name.name)  # ccc
  
  def test3():
    """测试是否可以传入被嵌入的对象"""
    # 结论： 不可以，被嵌入对象是不可以被保存的  
    c1 = C1(name="ccc")
    c1.save()
    c2 = C2(age=1, name=c1)
    c2.save()
    print(c2.name)
  ```




# 三、外键

- 定义字段：`ReferenceField`
- 这个字段似于传统orm中的外键字段，在保存时自动转换为引用，在加载时取消引用。



# 四、外键和嵌入式文档的区别

- 嵌入式文档,应该通过EmbeddedDocumentField字段类型作为文档上的字段使用
- 被嵌入的子文档，必须继承  EmbeddedDocument
- ReferenceField 子文档也需要进行保存，因此不能直接使用json

```python

def test14():
  """嵌入式文档的查询方式"""

  class Form(EmbeddedDocument):
    age = IntField()

  # 嵌入式文档,应该通过EmbeddedDocumentField字段类型作为文档上的字段使用.
  #被嵌入的子文档，必须继承  EmbeddedDocument
  # ReferenceField 子文档也需要进行保存，因此不能直接使用json
  class Structure(EmbeddedDocument):
    form = EmbeddedDocumentField(Form, required=True)
    str_age = IntField()

    @property
    def short(self):
      return {'form': self.form}

  class Record(Document):
    # 引用
    form = EmbeddedDocumentField(Form, required=True)
    structure = EmbeddedDocumentField(Structure)

  # # 准备数据, 注意这里的嵌入式文档需要将数据转换成json格式
  # data = {
  #     "form": {
  #         "age": 9999
  #     },
  #     "structure": {
  #         "form": {
  #             "age": 9999
  #         },
  #         "str_age": 9999
  #     }
  # }
  # data = json.dumps(data)

  # record = Record.from_json(json_data=data)
  # record.save()

  # 根据record 之中的structure的form进行查询
  data = Record.objects(__raw__={"structure.form.age": {"$in": [9999]}})
  for d in data:
    print(d.form.age)
    print(d.structure.form.age)
```

