# 一、protobuf

[官网github](https://github.com/protocolbuffers/protobuf)

## 1.1 是什么

- protobuf是由Google开发的一套对[数据结构](https://so.csdn.net/so/search?q=数据结构&spm=1001.2101.3001.7020)进行序列化的方法，可用做通信协议，数据存储格式，等等

## 1.2  特点

- 是不限语言、不限平台、扩展性强
- [序列化](https://so.csdn.net/so/search?q=序列化&spm=1001.2101.3001.7020)后生成的代码体积更小
- 解析速度更快
- 抓包的数据比较难读。protobuf序列化后是一串二进制代码，如果没有对应的协议格式（即.proto文件），想要读懂它难如登天

## 1.3 语法规则[语法规则](https://blog.csdn.net/buknow/article/details/113894118?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164860452016780271924385%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164860452016780271924385&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-4-113894118.142^v5^pc_search_result_control_group,143^v6^control&utm_term=protubuf&spm=1018.2226.3001.4187)

- 标识符
  - message 标识一条消息
  - enum 标识一个枚举类型
  - 使用protobuf编译器将协议文件编译后，message和enum都会生成一个类
- 字段
  - required：**表示该字段必须有值，不能为空**，否则message被认为是未初始化的。如果试图build一个未初始化的message将会抛出RuntimeException。解析未初始化的message会抛出IOException
  - optional：**表示该段为可选值，可以不进行设置**。如果不设置，会设置一个默认值。可以设置一个默认值，否则使用系统默认值，数字类型默认为0；字符串类型默认为空字符串；逻辑类型默认为false；内部自定义的message，默认值通常是message的实例或原型
  - repeated：表示该字段可以重复，可等同于动态数组
  - 注意：使用required字段一定要小心，因为该字段是永久性的。如果以后因为某种原因，想不用该字段，或者要将该字段改成optional或者repeated，那么使用旧接口读取新的协议时，如果发现没有该字段，他们会认为该字段是不完整的，会拒接接收该消息，或者直接丢弃
- 字段类型

- [proto3和proto2区别](https://blog.csdn.net/weixin_34294649/article/details/92170258)

## 1.4 python使用示例

[python官方文档](https://developers.google.com/protocol-buffers/docs/pythontutorial#where-to-find-the-example-code)

- 定义协议格式

  ```protobuf
  syntax = "proto2";
  
  package tutorial; //python 包的声明了
  
  message Person {
    optional string name = 1;
    optional int32 id = 2;
    optional string email = 3;
  
    enum PhoneType {
      MOBILE = 0;
      HOME = 1;
      WORK = 2;
    }
  
    message PhoneNumber {
      optional string number = 1;
      optional PhoneType type = 2 [default = HOME];
    }
  
    repeated PhoneNumber phones = 4;
  }
  
  message AddressBook {
    repeated Person people = 1;
  }
  ```

- 编译proto协议文件

  ```c++
  //protoc -I=$SRC_DIR --python_out=$DST_DIR $SRC_DIR/addressbook.proto
  //SRC_DIR 指定源目录，不指定则是当前目录
  //DST_DIR 生成的目标目录，不指定当前目录
  //因为需要生成python类，所以提供--python_out选项，其它的语言也应该提供类似的选项
  //最后的参数为编译的文件名
  
  // -I 源文件目录
  // --python)out 生成python文件的目录，语言不同该指定参数不同
  // 最后编译的protoc文件所在
  // protoc -I=./ --python_out=./ ./addressbook.proto  -> 生成python文件
  ```

- 协议缓冲区API

  - 与生成 Java 和 C++ 协议缓冲区代码不同，Python 协议缓冲区编译器不会直接为您生成数据访问代码。相反（如您所见`addressbook_pb2.py`），它为您的所有消息、枚举和字段以及一些神秘的空类（每种消息类型一个）生成**特殊描述符**：

    ```python
    class Person(message.Message):
      __metaclass__ = reflection.GeneratedProtocolMessageType
    
      class PhoneNumber(message.Message):
        __metaclass__ = reflection.GeneratedProtocolMessageType
        DESCRIPTOR = _PERSON_PHONENUMBER
      DESCRIPTOR = _PERSON
    
    class AddressBook(message.Message):
      __metaclass__ = reflection.GeneratedProtocolMessageType
      DESCRIPTOR = _ADDRESSBOOK
    ```

  ## 1.4 工作流程

![image-20220330171950290](../../Library/Application Support/typora-user-images/image-20220330171950290.png)