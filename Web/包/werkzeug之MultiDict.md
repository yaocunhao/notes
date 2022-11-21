- 一个字典的子类，实现了字典的所有标准用法

-  不同之处在于，这个字典是一键多值的，将所有键的值保存为一个列表，但是使用标准字典的访问方法只会返回一个值

- 如果想要返回所有的值，正确的方法是使用 getlist

- 初始化构造

  - 可以通过`(key, value)`的元组，字典，或者MultiDict 进行构造

    ```python
    
    def test2():
      """构造函数"""
      # 使用元组进行构造
      d1 = MultiDict([('a', 1), ('a', 2), ('b', 3), ('b', 4)])
    
      # 使用字典进行构造
      d2 = MultiDict({'a': 1, 'b': 2})
    
      # 使用本身对象进行构造
      d3 = MultiDict(d2)
    
      print(d1.getlist('b'))
      print(d2.getlist('b'))
      print(d3.getlist('b'))
      # [3, 4]
      # [2]
      # [2]
    ```

- 示例

  ```python
  d = MultiDict()
  d['a'] = 1
  d.add('a', 2)
  print(d)  # MultiDict([('a', 1), ('a', 2)])
  print(d['a'])  # 1
  print(d.getlist('a')) # [1, 2]
  ```

