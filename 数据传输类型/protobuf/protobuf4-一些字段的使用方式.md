

# 注意

- 如果字段类型是 `message` 不能直接 `=`  进行赋值

  ```protobuf
  message Foo {
    optional Bar bar = 1;
  }
  message Bar {
    optional int32 i = 1;
  }
  
  oo = Foo()
  foo.bar = Bar()  # WRONG!
  ```

  

# 一、嵌套类型和重复类型

- 嵌套类型在`python` 之中等价于list。因此赋值时有两种方式
  - 第一种是 `add()` 一个对象，初始化这个对象就是赋值一个
  - 第二种是 `append` 一个对象 或者`extend` 一个`list`


# 二、Any 和 enum类型

- *Any 字段类型类似于泛型，使用 .Pack() 进行添加，使用 .Unpack() 类型进行解析*

- *enum 字段类型，使用 .Name(<int>)**类型进行查看***

  ```properties
  syntax = "proto3";
  import "google/protobuf/any.proto";  
  
  message User{
      int32 index = 1;
      string name = 2;
      repeated Phone content = 3;
      google.protobuf.Any Value = 4; # 泛型模板
  }
  
  message Phone{
      string phoneType = 1;
      int32 number = 2;
  }
  
  enum PhoneType{
      Telephone = 0;
      Home = 1;
      Work = 2;
  }
  
  message Remark{
    string note = 1;
  }
  
  ```

  

  ```python
  import user_pb2
  def test1():
    # 创建user实例
    user = user_pb2.User()
    # 填入user信息
    user.index = 1
    user.name = "User1"
  
    # 1、当 message 嵌套写入数据时，使用 add(),因为此时content的字段为repeated
    phone = user.content.add()
  
    pt = user_pb2.PhoneType  # enum
    phone.phoneType = pt.Name(0)  #  pt.Name(0):Telephone， 从enum中取出元素
    phone.number = 10000
    # user.content.append(phone) # 多添加一份
    print(user)
  
    # 给模板赋值
    info = user_pb2.Remark()
    info.note = "1234"
    mark = user.Value
    mark.Pack(info)
    print(info)
  
    out_b = user.SerializeToString()  # 序列化
    print(out_b)
  
  def test2():
    """测试enum"""
    en = PhoneType
    print(en.Name(1)) # Home
  ```


# Any

- 就是一个泛型模板，不需要在proto文件中确定类型



# 三、oneof

- oneof 中不能有map和repeated字段
- oneof 表示只能设置一个值
- [参考链接](https://developers.google.com/protocol-buffers/docs/proto3#oneof)

# 四、services

- 用于Rpc 调用字段

# 四、判断字段是否设置

- 如果字段的类型不是`message` 是不允许判断的

```python
syntax = "proto3";

message St {
  int32 a = 1;
  string b =2;

}

message Test {
  int32 a=1;
  St st = 2;
}

from test_proto_pb2 import Test

t = Test()
print(t.HasField("st")) # 允许
print(t.HasField("a"))  # 不允许
print(t.st.HasField("a")) # 不允许
```

