

# 四、字段的使用方式

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