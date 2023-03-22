# 一、杂记

- [参考链接](https://blog.csdn.net/weixin_44685869/article/details/105402339?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166753162516782390563560%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166753162516782390563560&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-105402339-null-null.142^v63^wechat,201^v3^control_1,213^v1^control&utm_term=Django%20%E6%83%B0%E6%80%A7%E6%9F%A5%E8%AF%A2&spm=1018.2226.3001.4187)
- Django 的查询集和过滤并不会进行数据库的搜索。只有当进行**读取动作时**才会进行数据库的查找
- 使用同一个查询集，第一次使用时会发生数据库的查询，然后Django会把结果缓存下来，再次使用这个查询集时会使用缓存的数据，减少了数据库的查询次数。更改查询集也就是会需要重新进行查询
- 常见的读取动作有：循环，判断，读取某一个属性，跳跃式切片(arrat[:3:10]



# 二、测试代码

```python
def test3(request):
  """
  测试惰性查找：
   得到查询集或者过滤器不会执行数据库查找动作
   判断是否存在会进行查找动作
   进行for循环会执行查询动作(就是将查询集中的SQL命令执行),只是执行一次查询,并不是循环多少次，就执行了多少次
   也就是说过滤一次且读取一次就是进行了数据库查询
   使用同一个查询集，第一次使用时会发生数据库的查询,然后Django会把结果缓存下来,
   再次使用这个查询集时会使用缓存的数据，减少了数据库的查询次数
  """

  objs = Book.objects.all()
  l = []
	
  # 第一段代码
  for obj in objs:
    print(obj.id)
  for obj in objs: # 上面已经进行了一次查询动作，因此不会再查询
    print(obj.id)  
  
  # 这里会进行多次查询，因为每次都是用的新的查询集
  # for i in range(1, 7):
  #   obj = objs.filter(id__exact=i) # 这里每次使用的都是新的查询集
  #   if obj.last():
  #     l.append(obj)
      
	
  # 这里也会进行多次查询，因为在用小标读取某一个属性时，就是生成一个新的查询集合
  # 如果不注释第一段代码不会进行多次查询，这是因为第一段代码的时候已经查询过了
  # 如果注释第一段代码这里将进行两次数据库的查询
  print(objs[0].id)
  print(objs[1].id)
  return HttpResponse("success!!!")

```

