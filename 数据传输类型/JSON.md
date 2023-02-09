# 一、语法

[参考链接](https://www.runoob.com/json/js-json-objects.html)

## 1.1 语法规则

- 数据在名称/值对中
- 数据由逗号分隔
- {} 保存对象
- [] 保存数组，数组可以包含多个对象

## 1.2 名称/值对

- 语法格式

  - key:value

    ```json
    "name":"value"
    ```

  - json的key必须加上引号

## 1.3 JSON的值类型

- 数字（整数或浮点数）
- 字符串（在双引号中）
- 逻辑值（true 或 false）
- 数组（在中括号中）
- 对象（在大括号中）
- null

## 1.4 总结

- JSON 的格式为用 {}表示对象,[]表示数组，两者又可以相互包含
- 在{} 之中用 key:value 对来表示对应的值
- key 必须为字符串(也就是key必须带引号)，value则必须为JSON的值类型
- `键值对` 之间必须用`,`相隔开来

# 二、python 之中使用JSON

## 2.1 常用方法介绍

- [参考链接](https://blog.csdn.net/qq_46092061/article/details/119912882?ops_request_misc=&request_id=&biz_id=102&utm_term=python%20%E4%B8%AD%E4%BD%BF%E7%94%A8JSON&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-119912882.142^v7^control,157^v4^control&spm=1018.2226.3001.4187)

- **python**在使用**json**这个模块前，首先要导入**json**库：`import json`
  - json.dumps()	将 Python **对象编码成 JSON 字符串**
  - json.loads()	将已编码的 JSON **字符串解码为 Python 对象**
  - json.dump()	将Python内置类型序列化为json对象后写入文件
  - json.load()	读取文件中json形式的字符串元素转化为Python类型
  - 不带s的是序列化到文件或者从文件[反序列化](https://so.csdn.net/so/search?q=反序列化&spm=1001.2101.3001.7020)，带s的都是内存操作不涉及持久化

## 2.2 使用演示

### 2.3.1 内存中序列化和反序列化

- dumps

  - 在内存中将Python对象编码成JSON字符串

  ```python
  import json
  
  data = {"name": "tom", "age": 18}
  # 将python对象转换成json字符串
  print(type(data), data)
  ret = json.dumps(data)
  print(type(ret), ret)
  
  # <class 'dict'> {'name': 'tom', 'age': 18}
  # <class 'str'> {"name": "tom", "age": 18}
  ```

  - 可以清晰的看到dict 类型变成了str类型，**也就是说由对象变成了字符串**

- loads

  - 在内存中将JSON字符串解码为python对象

  - 编码过程中，python中的list和tuple都被转化成json的数组，而解码后**，json的数组最终被转化成python的list的，无论是原来是list还是tuple**

    ```python
    import json
    
    l_data = [{"name": "tom", "age": 18}, {"name": "tom2", "age": 182}]
    t_data2 = ({"name": "tom", "age": 18}, {"name": "tom2", "age": 182})
    
    # 序列化成Json数据
    l_j = json.dumps(l_data)
    t_j = json.dumps(t_data2)
    print(type(l_j), type(t_j)) # <class 'str'> <class 'str'>
    
    # 将json数据反序列化成python对象
    l_data = json.loads(l_j)
    t_data2 = json.loads(t_j)
    print(type(l_data), l_data, "\n", type(t_data2), t_data2)
    
    # 可见不管是列表还是元组，序列化后再反序列化都会编程列表
    #<class 'list'> [{'name': 'tom', 'age': 18}, {'name': 'tom2', 'age': 182}] 
    # <class 'list'> [{'name': 'tom', 'age': 18}, {'name': 'tom2', 'age': 182}]
    ```

  - loads 读取的是字符串类型

    ```python
    import json
    
    # 文件内容
    # [
    #     {
    #         "1": 1234,
    #         "2": 2345,
    #         "3": 3456
    #     },
    #     {
    #         "name": "Jim"
    #     }
    # ]
    
    dic = [{1: 1234, 2: 2345, 3: 3456}, {"name": "Jim"}]
    with open("json_test.txt", "r+", encoding="utf-8") as f:
      print(json.loads(f.read())) # load的茶君入
      f.seek(0) # 将文件游标挪动到文件的开头位置
      print(json.loads(f.read()))
    
    # 输入内容
    # [{'1': 1234, '2': 2345, '3': 3456}, {'name': 'Jim'}]
    # [{'1': 1234, '2': 2345, '3': 3456}, {'name': 'Jim'}]
    ```

### 2.3.2 文件中序列化和反序列化

- dump

  - 将python**内置类型**序列化为json对象后写入文件

    ```python
    import json
    
    l_data = [{"name": "tom", "age": 18}, {"name": "tom2", "age": 182}]
    t_data2 = ({"name": "tom", "age": 18}, {"name": "tom2", "age": 182})
    
    with open('json_test.txt', 'w+') as f:
      json.dump(l_data, f)
      json.dump(t_data2, f)
      
    # json_test.txt 文件之中
    [{"name": "tom", "age": 18}, {"name": "tom2", "age": 182}][{"name": "tom", "age": 18}, {"name": "tom2", "age": 182}]
    ```

- load

  - 读取文件中的json形式字符串，并且转换成python类型

    ```python
    with open('json_test.txt', 'r+') as f:
      result = json.load(f)
      print(type(result), "\n", result)
    
      # <class 'dict'> 
      # {'l1': [{'name': 'tom', 'age': 18}, {'name': 'tom2', 'age': 182}], 
      # 'l2': [{'name': 'tom', 'age': 18}, {'name': 'tom2', 'age': 182}]}
    
    # json文件内容
    # 一个对象、对象里面两个 名称/值对
    # 两个值对都是数组
    # 数组里面又有两个对象
    {
    "l1":[{"name": "tom", "age": 18}, {"name": "tom2", "age": 182}],
    "l2":[{"name": "tom", "age": 18}, {"name": "tom2", "age": 182}]
    }
    ```

# 三、JSON参数解析

## 3.1 序列化函数参数解析

- [官方文档](https://docs.python.org/3/library/json.html#json.dumps)

### 3.1.1 参数介绍

- dumps 序列化到内存，参数解析

  ```python
  dumps(obj,skipkeys=False, ensure_ascii=True, check_circular=True,
          allow_nan=True, cls=None, indent=None, separators=None,
          default=None, sort_keys=False, **kw):
  ```

  - obj:序列化对象
  - skipkeys: 如果为True的话，则只能是字典对象，否则会TypeError错误, 默认False
  - ensure_ascii: 确定是否为ASCII编码
  - check_circular: 循环类型检查，如果为True的话
  - allow_nan: 确定是否为允许的值
  - **indent**: 会以美观的方式来打印，呈现，实现缩进
  - separators: 对象分隔符，默认为`,`
  - encoding: 编码方式，默认为utf-8
  - sort_keys: 如果是字典对象，选择True的话，会按照键的ASCII码来排序
  - **default:**默认只能序列化内置类型

- dump

  - 对于dump来说，只是多了一个fp参数

  - 简单说就是**dump**需要一个类似文件指针的参数（并不是真正的指针，可以称之为文件对象），与文件操作相结合，即先将**Python**文件对象转化为**json**字符串再保存在文件中

    ```python
    dump(obj, fp, skipkeys=False, ensure_ascii=True, check_circular=True,
            allow_nan=True, cls=None, indent=None, separators=None,
            default=None, sort_keys=False, **kw)
    ```

### 3.1.2 使用演示

- 使用indent 进行格式化保存

  ```python
  import json
  
  dic = {1: 1234, 2: 2345, 3: 3456}
  with open("json_test.txt", "w+", encoding="utf-8") as f:
    json.dump(dic, f, indent=4)
  
  # 未使用ident 
  {"1": 1234, "2": 2345, "3": 3456}
  
  # 使用后 -> indent 是的JSON的每个值对都占用一行 && 前面缩进4个空格
  {
      "1": 1234,
      "2": 2345,
      "3": 3456
  }
  
  # 对list同样适用
  import json
  
  dic = [{1: 1234, 2: 2345, 3: 3456}, {"name": "Jim"}]
  with open("json_test.txt", "w+", encoding="utf-8") as f:
    json.dump(dic, f, indent=4)
  
  # 文件内容
   [
      {
          "1": 1234,
          "2": 2345,
          "3": 3456
      },
      {
          "name": "Jim"
      }
  ]  
  ```

- default 序列化自定义对象

  - 通过编写转换函数，告诉JSON应该怎么序列化自定义类型

    ```python
    import json
    
    
    class Person:
      def __init__(self, name, age, sex):
        self.name = name
        self.age = age
        self.sex = sex
    
    
    # 定义一个对象
    p = Person("jim", 222, "girl")
    
    
    # 转换函数
    def python_to_json(obj):
      return {
        "name": obj.name,
        "age": obj.age,
        "sex": obj.sex
      }
    
    
    # 序列化
    # 语义就是在转换的时候调用转换函数，也就是说变成一个dict 进行存储
    with open("json_test.txt", "w", encoding="utf-8") as f:
      json.dump(p, f, indent=4, default=python_to_json)
    
      
    # 序列化结果
    {
        "name": "jim",
        "age": 222,
        "sex": "girl"
    }
    ```

## 3.2 反序列化参数解析

- **JSON反序列化为类对象或者类的实例**，使用的是**loads**()方法中的**object_hook**参数

  ```python
  import json
  
  class Person:
    def __init__(self, name, age, sex):
      self.name = name
      self.age = age
      self.sex = sex
  
  
  # 转换函数
  def json_to_python(obj):
    return Person(obj['name'], obj['age'], obj['sex'])
  
  
  # 定义个JSON字符串
  json_string = '{"name":"Jim","age":22,"sex":"boy"}'
  
  # 语义
  # 在转换的时候，先将JSON字符串转变成dict对象，然后再从中提取值，定义一个Person对象
  obj = json.loads(json_string, object_hook=json_to_python)
  
  # 说明了obj是一个对象
  print(obj)  # <__main__.Person object at 0x7fe2d5635ba8>
  print(obj.name)  # Jim
  
  ```

# 四、从文件中读取多行JSON文件

- JSON只能读取一个文档对象，如果读取多行(如下所示)就会报错

  ```json
  [1, 2, 3, 4, 5][6, 7, 8, 9, 11]
  ```

- 解决办法

  - 用 {} 将其合成一个JSON对象

    ```json
    {"l1":[1, 2, 3, 4, 5],"l2":[6, 7, 8, 9, 11]}
    ```

  - 一行行读取

    ```python
    import json
    
    with open("json_test.txt", "r+", encoding="utf-8") as f:
      for line in f.readlines():
        line = line.strip() # 删除前后空格
        if len(line) != 0 : # 空行也会导致读取JSON失败
          data = json.loads(line)
          print(data)
          
    # [1, 2, 3, 4, 5]
    # [6, 7, 8, 9, 11]
    ```

    

