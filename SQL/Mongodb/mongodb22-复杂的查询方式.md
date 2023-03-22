- [字典对象的查询](https://stackoverflow.com/questions/39018389/mongodb-find-key-on-nested-object-key-json)

  ```sql
  {
  "id": "123456",
  "makes": {
      "abc": {
          "att1": 4,
          "att2": "fffff",
          "att3": 46
          },
      "fgh": {
          "att1": 8,
          "att2": "ggggg",
          "att3": 6
      },
      "rty": {
          "att1": 3,
          "att2": "hhhh",
          "att3": 4
          },
      "iop": {
          "att1": 4,
          "att2": "llll",
          "att3": 3
          }
  }
  
  // 查询方式
  db.myCollection.find( { "makes.fgh" : { $exists : true } })
  
  ```

- 查询示例

  ```sql
  def test8():
    """测试使用dict字段，看是否能够使用key 进行查询"""
    # data = Base.objects.filter(like__exact={
    #   "sport": ["pingpong", "basketball"],
    #   "eat": ["fruit", "beef"]
    # })
    data = Base.objects(
      # like字段为字典， 下面判断是key为sport&&对应的值存在[列表之中]
      # && swming 字段不能存在
      __raw__={
        "like.sport": {
          "$exists": True,
          "$in": ['s2']
        },
        "like.swming": {
          "$exists": False
        }
      })
  
    for d in data:
      print(d.age, d.name, d.like)
  ```

  