# 一、标识符(消息/协议 类型)

- message 标识一条消息
- enum 标识一个枚举类型
- 使用protobuf编译器将**协议文件编译后**，message和enum都会生成一个**类**

# 二、字段类型(消息/协议 字段)

## 2.1  指定字段类型

- [字段类型的使用方式](https://blog.csdn.net/weixin_44534197/article/details/108757270?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165646687316782388072415%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165646687316782388072415&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-2-108757270-null-null.142^v24^huaweicloudv2,157^v15^new_3&utm_term=protoc+%E6%80%8E%E4%B9%88%E7%BB%99repeated%E5%AD%97%E6%AE%B5%E8%B5%8B%E5%80%BCH&spm=1018.2226.3001.4187)
- **proto3中，只有repeated字段可以进行设置，也就是说该字段可以出现多次**

- required
  - **表示该字段必须有值，不能为空**，否则message被认为是未初始化的。如果试图build一个未初始化的message将会抛出RuntimeException。解析未初始化的message会抛出IOException(**proto3 已经移除**)
- optional
  - **表示该段为可选值，可以不进行设置**。如果不设置，会设置一个默认值。可以设置一个默认值，否则使用系统默认值，数字类型默认为0；字符串类型默认为空字符串；逻辑类型默认为false；内部自定义的message，默认值通常是message的实例或原型(**proto3不可以设置默认值，并且改名为singular**)
- repeated
  - 表示该字段可以重复，**可等同于动态数组**
  - 注意：使用required字段一定要小心，因为该字段是永久性的。如果以后因为某种原因，想不用该字段，或者要将该字段改成optional或者repeated，那么使用旧接口读取新的协议时，如果发现没有该字段，他们会认为该字段是不完整的，会拒接接收该消息，或者直接丢弃

## 2.2 标量字段类型

- N 表示打包的字节并不是固定。而是根据数据的大小或者长度,例如int32，如果数值比较小，在0~127时，使用一个字节打包
- 关于 fixed32 和int32的区别。fixed32的打包效率比int32的效率高，但是使用的空间一般比int32多。因此一个属于时间效率高，一个属于空间效率高。根据项目的实际情况，一般选择fixed32，如果遇到对传输数据量要求比较苛刻的环境，可以选择int32

| protobuf 数据类型 | 描述                                   | 打包      | C++语言映射      |
| ----------------- | -------------------------------------- | --------- | ---------------- |
| bool              | 布尔类型                               | 1字节     | bool             |
| double            | 64位浮点数                             | N         | double           |
| float             | 32为浮点数                             | N         | float            |
| int32             | 32位整数、                             | N         | int              |
| uin32             | 无符号32位整数                         | N         | unsigned int     |
| int64             | 64位整数                               | N         | __int64          |
| uint64            | 64为无符号整                           | N         | unsigned __int64 |
| sint32            | 32位整数，处理负数效率更高             | N         | int32            |
| sing64            | 64位整数 处理负数效率更高              | N         | __int64          |
| fixed32           | 32位无符号整数                         | 4         | unsigned int32   |
| fixed64           | 64位无符号整数                         | 8         | unsigned __int64 |
| sfixed32          | 32位整数、能以更高的效率处理负数       | 4         | unsigned int32   |
| sfixed64          | 64为整数                               | 8         | unsigned __int64 |
| string            | 只能处理 ASCII字符                     | N         | std::string      |
| bytes             | 用于处理多字节的语言字符、如中文       | N         | std::string      |
| enum              | 可以包含一个用户自定义的枚举类型uint32 | N(uint32) | enum             |
| message           | 可以包含一个用户自定义的消息类型       | N         | object of class  |

# 三、编译使用演示


## 3.1 python使用示例

- [python官方文档](https://developers.google.com/protocol-buffers/docs/pythontutorial#where-to-find-the-example-code)

- 定义协议格式

  ```protobuf
  syntax = "proto2";
  
  package tutorial; //python 包声明， 在其它文件导入该文件的某个消息类时需要带上这个标识符
  
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

  - **编译命令：protoc -I=$SRC_DIR --python_out=$DST_DIR $SRC_DIR/addressbook.prot**

    ```protobuf
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

  - 有关协议编译器为任何特定字段定义生成哪些成员的详细信息，请参见[Add有关Python generated code reference](https://developers.google.com/protocol-buffers/docs/reference/python-generated).



# 