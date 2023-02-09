- [官方文档连接](https://googleapis.dev/python/protobuf/latest/google/protobuf/message.html)

# 一、异常模块

- 反序列化异常

  ```
   google.protobuf.message.DecodeError
  ```

- 序列化异常

  ```
  google.protobuf.message.EncodeError
  ```

- 基本错误类型异常

  ```python
  google.protobuf.message.Error
  ```

  

# 二、序列化模块

## 2.1 基本介绍

- `class google.protobuf.message` 为proto协议的"base"类，其它的子模块是在该模块的基础上进行开发的，也就是说，根据proto生成的对象拥有`2.2`基本API接口， 其它的子模块则添加了一些新的功能(比如新增的json映射系列)

## 2.2 基本API 接口

- ByteSize()

  - 返回序列化消息的大小

- Clear()

  - 清除在消息中设置的所有数据，即将当前对象设置的消息全部去除

- `ClearField`(*field_name*)

  - 将指定消息的内容复制到当前消息中**。  该方法清除当前消息，然后使用MergeFrom合并指定的消息**

    ```python
    user_info.ClearField('name') # 清除给定字段的内容
    ```

- `CopyFrom`(*other_msg*)

  - 将指定消息拷贝到当前消息之中

    ```python
    user_info.CopyFrom(user_info2) # 进行内容拷贝,传入的参数也必须是message类型的对象
    ```

- `IsInitialized`()

  - 检查消息是否初始化完成，**即是否给对象成员进行赋值完成**

- `ListFields`()

  - 返回当前字段内容的元组列表

    ```python
    user_info = demo_pb2.UserInfo()
    user_info.name = 'tom'
    user_info.age = 11
    print(user_info.ListFields())
    
    # result  [(<google.protobuf.pyext._message.FieldDescriptor object at 0x7fa764190160>, 'tom'), (<google.protobuf.pyext._message.FieldDescriptor object at 0x7fa764190278>, 11)]
    ```

- `MergeFrom`(*other_msg*)

  - 将指定消息的内容合并到当前消息中。  此方法将指定消息的内容合并到当前消息中。在指定消息中设置的单一字段将覆盖当前消息中的相应字段。重复字段被追加。递归合并单个子消息和组

- `MergeFromString`(*serialized*)

  - 将序列化的协议缓冲区数据合并到此消息中。  当我们发现序列化中已经存在的字段时:  如果它是一个“重复”字段，则将其追加到列表的末尾。  否则，如果它是一个标量，我们覆盖我们的字段。  否则，(它是非重复组合)，我们递归地合并到现有的组合中

- `ParseFromString`(*serialized*)

  - 反序列化，类似于MergeFromString()，只不过我们先清除对象

  - 参数：可以传入从文件读取的参数

    ```python
    # Read the existing address book.
    f = open("./address.txt", "rb") # 打开文件
    address_book.ParseFromString(f.read()) # 读取文件，并且进行反序列化，反序列化后得到的是一个字典
    f.close()
    ```

  - **注意：**这里是序列化成字符串，因此当将一个0(int类型)序列化成字符串时，然后再序列化回来会导致当前字段的丢失，**因为`0`被序列化成了空串，因此不会进行显示。同理False也会被序列化成空串**

- 序列化消息

  - `SerializeToString`(***kwargs*)
  - `SerializePartialToString`(***kwargs*)
  - 将协议消息序列化为**二进制字符串**。  SerializeToString会检查消息是否已经初始化，SerializePartialToString不检查消息是否已初始化
  - 由于proto3不再支持`required`字段，所以是否初始化对于Proto3来说是没有意义的




# 三、序列化和返序列化接口使用解析

- 普通序列化接口(SerializeToString、SerializePartialToString) 是将pb对象序列化成二进制字符串。因此采用这种序列化的方式必须通过`ParseFromString` 来进行反序列化

- `text_format`、`json_format`则可以直接序列化成`text`或`json`文本

- text_fromat 的使用

  ```python
  def test2():
    """进行序列化测试"""
    sex = Sex
    person = Person()
    person.age = 11
    person.sex = sex.gg
    with open('./proto.log', 'w') as f:
      # 进行序列化，设置编码为utf8
      f.write(text_format.MessageToString(person, as_utf8=True))
  
   """进行反序列化"""    
  # 需要注意的是读取的文件必须是符合要求的string文件
  con = text_format.Parse(f.read(), Person())
  ```

- json_format的使用

  ```python
  # con: 为pb对象
  # 需要注意的是pb对象必须是符合Json格式的
  json_format.MessageToJson(con) 
  ```

  
