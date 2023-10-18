[参考链接](https://www.jb51.net/article/159652.htm#_label6)

- find() 返回的是所有对象迭代器

- find_one() 返回单一对象

- 实例

  ```python
  import pymongo
  
  # 创建连接
  client = pymongo.MongoClient('mongodb://172.25.8.145:7779/')
  
  # 链接数据库
  db = client.test_mongo  # 或者采用后面的方式 db = client['test_mongo']
  
  # 认证
  db.authenticate('y', 'didi1234')
  
  # 获取一个集合
  table = db.Book
  
  ret = table.find_one(
      {"name": "31"}
  )  # {'_id': ObjectId('62f6194dfff25bcff15ee957'), 'name': '31', 'type': 2, 'price': 1231}
  print(ret)
  
  ret = table.find()
  for r in ret:
    print(r)
  # {'_id': ObjectId('62f6194dfff25bcff15ee957'), 'name': '31', 'type': 2, 'price': 1231}
  # {'_id': ObjectId('62f6194dfff25bcff15ee957'), 'name': '31', 'type': 2, 'price': 1231}
  # {'_id': ObjectId('62f619af1a88ee67a26c21c3'), 'name': 'dasdsa', 'type': 9999, 'price': 321321}
  
  # 聚合查询
  pipeline = [{
      "$match": {
          "element_time": {
              "$lte": xxx
          },
          "region": "xxxx"
      }
  }, {
      "$group": {
          "_id": "$group_id"
      }
  }]
  data = table.aggregate(pipeline)
  ```
  