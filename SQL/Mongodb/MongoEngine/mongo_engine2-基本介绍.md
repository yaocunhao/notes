# 一、基本使用过程

- 通过connect 进行数据库的连接

  ```shell
  connect('mon_test',
          host="",
          port=21111,
          username='',
          password="",
          authentication_source="admin")
  ```

- 通过继承`Document`类定义数据结构

  - 直接继承了Document 则会以该类的名称创建一个集合
  -  通过设置 `meta = {'allow_inheritance': True} `表示这是一个被允许继承的基类

  ```python
  # 定义mongodb基本的数据结构
  class User(Document):
    email = StringField(required=True)
    first_name = StringField(max_length=50)
    last_name = StringField(max_length=50)
    
  # 基类
  class Post(Document):
    title = StringField(max_length=120, required=True)
    author = ReferenceField(User)  # 表示对作者这个表进行引用
    tags = ListField(StringField(
        max_length=30))  #  因为tags可能非常小，并且有多条，因此不再建立独立的集合，而是使用list列表的方式存储起来
    meta = {'allow_inheritance': True}  # 允许被继承
  
  
  # 评论表
  class TextPost(Post):
    content = StringField()
  
  # 图像表
  class ImagePost(Post):
    image_path = StringField()
  
  # 链接表
  class LinkPost(Post):
    link_url = StringField()
  
  # 如上代码，，最终的mongo数据库之中会创建 user 和 post两个数据库
  ```

- 通过`save()`数据库插入数据

  ```python
  def test():
    """向数据库进行数据的添加"""
    # 创建用户
    ross = User(email='ross@example.com', first_name='Ross',
                last_name='Lawley').save()  # 和Django的ORM的使用方式差不多
    john = User(email="cc", first_name="Jshn", last_name="Dava").save()
  
    # 创建post,并且插入到数据库之中
    post1 = TextPost(title='Fun with MongoEngine', author=john)
    post1.content = 'Took a look at MongoEngine today, looks pretty cool.'
    post1.tags = ['mongodb', 'mongoengine']
    post1.save()
  
    post2 = LinkPost(title='MongoEngine Documentation', author=ross)
    post2.link_url = 'http://docs.mongoengine.com/'
    post2.tags = ['mongoengine']
    post2.save()
  ```

- 通过`object` 访问数据

  - 每个继承了`Document`的类都有一个object属性，得到`Queryset`对象，和Django 的ORM一样，这是一个惰性( lazily queries)查找集合

  - object 的三种输出方式

    ```python
      # 1 根据基类输出所有的
      #for post in Post.objects:
      #  print(post.title)
    
      # 2 指定子对象
      # for text in TextPost.objects:
      #   print(text.content)
    
      # 3 进行属性的判断再输出
      for post in Post.objects:
        print(post.title)
        print('=' * len(post.title))
    
        if isinstance(post, TextPost):  # 判断类型输出对应的内容
          print(post.content)
    
        if isinstance(post, LinkPost):
          print('Link: {}'.format(post.link_url))
    
    ```

  - Queryset 的多种方式

    ```python
    # 比如常见的根据某个字段进行过滤
    def test3():
      """查询的方式"""
      for post in Post.objects(tags='mongodb'):
        print(post.title)
    ```

    

  

