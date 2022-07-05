[官方文档](https://developers.google.com/protocol-buffers/docs/proto3#simple)

# 保留字段

- 当需要更新proto协议时，不建议之间删除某些字段。如果有些程序使用了旧的协议格式来进行数据的读取，可能会导致数据确实等问题 ，通过`reserved`来标识某些字段，如果代码中使用了被标识的字段，编译器会报错处理

```protobuf
// 需要注意的是不要在同一个保留语句中混合使用字段编号和字段名

message Foo {
  reserved 2, 15, 9 to 11; # 使用字段编号
  reserved "foo", "bar"; # 使用字段名
}
```



#   引用定义好的类型

- 和类的使用方法类似

  ```protobuf
  import "myproject/other_protos.proto" //如果引用的类在其它文件之中
  
  message Result {
    string url = 1;
    string title = 2;
    repeated string snippets = 3;
  }
  
  message SearchResponse {
    repeated Result results = 1;
  }
  ```

  

# 包说明符号

- 可选，避免不同协议间的命名冲突冲突

  ```protobuf
  //在包中使用包说明符
  package foo.bar;
  message Open { ... }
  
  
  //在使用的过程之中， 可以通过包说明符找到对应的字段
  message Foo {
    ...
    foo.bar.Open open = 1;
    ...
  }
  ```

  

# 字段的使用方式

- 当message 嵌套写入数据时，使用add

- *repeated 字段类型,查看转义源码 default_value=[] 可知为 list 类型，可使用 append()添加字段*

  - 如果是嵌入类型则使用add()

    ```python
    import demo2_pb2
    
    info = demo2_pb2.Info()
    info.address = 'beijing'
    
    # userInfo 是嵌入类型，使用add来添加重复元素
    user_info1 = info.userInfo.add()
    user_info1.name='tom'
    user_info1.age = 18
    
    user_info2 = info.userInfo.add()
    user_info2.name = 'jim'
    user_info2.age = 22
    
    print(info)
    address: "beijing"
    userInfo {
      name: "tom"
      age: 18
    }
    userInfo {
      name: "jim"
      age: 22
    }
    ```

    

- *Any 字段类型类似于泛型，使用 .Pack() 进行添加，使用 .Unpack() 类型进行解析*

- *enum 字段类型，使用 .Name(<int>)类型进行查看*

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
  ```

  



# Any

- 就是一个泛型模板，不需要在proto文件中确定类型