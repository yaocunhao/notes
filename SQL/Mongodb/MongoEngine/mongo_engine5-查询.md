## 一、过滤查询

```python
# This will return a QuerySet that will only iterate over users whose
# 'country' field is set to 'uk'
uk_users = User.objects(country='uk')
```

## 二、查询运算符

| 运算符   | 含义                      |
| -------- | ------------------------- |
| ne       | 不等于                    |
| lt       | 小于于                    |
| lte      | 小于或等于                |
| gt       | 大于                      |
| gte      | 大于或等于                |
| not      | 否定                      |
| in       | 在列表之中                |
| nin      | 不在列表之中              |
| mod(x,y) | value%x = y, 对x取余得到y |
| all      | 所有的字段都存在          |
| size     | 字段数组的大小            |
| exists   | 字段是存在的              |



## 三、原始查询

## 3.1 演示

- `__raw__`：提供一个dict查询参数来进行一个原始的查询，**所谓的原始查询就是和命令行一样的模式**

  ```python
  Page.objects(__raw__={'tags': 'coding'})
  ```

- 示例

  ```python
  #  Q对象是待查询对象
  # __raw__ 标识提供一个原始查询
  
  def test6():
    """验证Q对象 和 __raw__"""
    query_dict = {}
    query_dict.update({"name": "123"})
    query_obj = Q(__raw__=query_dict)
    query_obj_2 = Q(__raw__={"age": {"$gte": 1}}) # 大于等于
    query_obj |= query_obj_2
    ret = Base.objects.filter(query_obj)
    print(ret.count()) # 打印获取的数量
  ```

## 3.2 原始查询和利用Queryset 查询的区别

```python
def test5():
  """验证查询函数，验证成功"""
  data = Base.objects.filter(age__lte=2) # 很明显这里不需要编写
  for d in data:
    print(d.age)

```

