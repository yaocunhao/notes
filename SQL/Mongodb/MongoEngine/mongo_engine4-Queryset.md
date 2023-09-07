# 注意事项

- 查询风格变幻
  - filter的查询风格是和django一样的
  - `__raw__`的查询风格是和mongo shell 一样的

# 一、常用接口

- `delete`**(write_concern=None**,_from_doc_delete=False**,** *cascade_refs=None*)

  - 删除接口，返回已删除的数量

- `def filter(self, *q_objs, **query)`

  - q_objs：class Q 对象

  - query： django风格的查询关键字参数

  - **默认使用`,`进行 `&` 查询， 或查询请使用Q对象**


  ```python
  def test5():
    """验证查询函数，验证成功"""
    data = Base.objects.filter(age__gte=4,name__exact="123")
    for d in data:
      print(d.age)
  ```

- `get`**(****q_objs***,** ***query***)**

  - 检索引发MultipleObjectsReturned或DocumentName的匹配对象。multipleobjects如果**多个结果**和DoesNotExist或DocumentName返回异常。如果没有发现结果，则不返回

- `in_bulk`**(***object_ids***)**

  - 根据文档的id检索一组文档
  - ObjectId的列表或元组
  - 返回值为dict，id为键，wend子类为值

- `insert`**(***doc_or_docs***,** *load_bulk=True***,** *write_concern=None***,** *signal_kwargs=None***)**

  - 批量插入文档
  - doc_or_docs:要插入的文档或文档列表
  - load_bulk: 如果True返回文档实例的列表
  
- 批量删除

  ```python
  def test23():
    """
    测试数据批量删除
    结论： 通过queryset 进行批量删除速度要快上很多
    """
    
    class LinkPost(Post):
      link_id = me.SequenceField(required=True)
  
  
    # 数据准备
    save_data_list = []
    for i in range(1000):
      link_post = LinkPost(link_id=i)
      save_data_list.append(link_post)
    result = LinkPost.objects.insert(save_data_list)
  
    # # 一个个删除
    # time_s = time.time()
    # data_list = LinkPost.objects.filter(link_id__lt=100)
    # for data in data_list:
    #   data.delete()
    # time_e = time.time()
    # print(time_e - time_s) # 0.642568826675415
  
    # 批量删除
    time_s = time.time()
    data_list = LinkPost.objects.filter(link_id__lt=100)
    data_list.delete()
    time_e = time.time()
    print(time_e - time_s)  # 0.030241966247558594
  
  ```
  
  

# 二、其它接口

- `average`**(**field**)**：对指定字段求平均值

- `batch_size`**(**size**)**：限制单个批处理中返回的文档数量

- `clear_cls_query()`：清除默认的“_cls”查询

- `clone()`：创建当前queryset副本

- `count`**(**with_limit_and_skip=False**)**：统计查询选选中的元素

  - with_limit_and_skip：在获取计数时，考虑应用于此游标的任何limit()或skip()

  ```python
  def test2():
    """验证查询的count接口"""
    num = Base.objects(name='456').count() # 这里统计的就是name为'456'的所有数量
    print(num)
  ```

- `create`**(**kwargs**)**：创建新的对象，返回返回保存对象的实例

- `distinct`**(**field**)**：为给定字段返回列表

  ```python
  def test4():
    """验证distinct：返回给定字段的列表"""
    data = Base.objects.distinct('age')
    print(data) # [1, 2, 3, 4]
  ```

- `exclude`**(****fields***)： 要排除的字段，并且可以进行链接

  ```python
  post = BlogPost.objects.exclude('title').exclude('author.name')
  ```

- `fields`**(***_only_called=False***,** ***kwargs***)**

  - 操控如何加载字段，1表示加载，0表示不加载

    ```python
    posts = BlogPost.objects(…).fields(author=1, title=1)
    ```

- `from_json`**(***json_data***)**

  - 将json数据转换为未保存的对象

- `hint`**(***index=None***)**

  - 告诉mongodb使用正确的索引。如果对应的索引不存在，则提示将不起任何作用。应用于此游标的最后一个提示优先于所有其他提示

- `insert`**(***doc_or_docs***,** *load_bulk=True***,** *write_concern=None***,** *signal_kwargs=None***)**

  - 批量插入文档
  - doc_or_docs:要插入的文档或文档列表
  - load_bulk: 如果True返回文档实例的列表。默认情况下返回文档实例，将load_bulk设置为False只返回objectid
