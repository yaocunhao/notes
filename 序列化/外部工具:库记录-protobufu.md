# 一、protobuf

[官网github](https://github.com/protocolbuffers/protobuf)

[下载安装](https://blog.csdn.net/sinat_38682860/article/details/108469592?ops_request_misc=&request_id=&biz_id=102&utm_term=pb%20%E6%95%B0%E6%8D%AE%E6%A0%BC%E5%BC%8F&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-108469592.142^v11^pc_search_result_control_group,157^v12^control&spm=1018.2226.3001.4187)

## 1.1 是什么

- protobuf是由Google开发的一套对数据结构进行序列化的方法，可用做通信协议，数据存储格式，等等

## 1.2  特点

- 是不限语言、不限平台、扩展性强
- [序列化](https://so.csdn.net/so/search?q=序列化&spm=1001.2101.3001.7020)后生成的代码体积更小
- 解析速度更快
- 抓包的数据比较难读。protobuf序列化后是一串二进制代码，如果没有对应的协议格式（即.proto文件），想要读懂它难如登天

## 1.3 [语法规则](https://blog.csdn.net/buknow/article/details/113894118?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164860452016780271924385%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164860452016780271924385&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-4-113894118.142^v5^pc_search_result_control_group,143^v6^control&utm_term=protubuf&spm=1018.2226.3001.4187)

- [字段的使用方式](https://blog.csdn.net/weixin_44534197/article/details/108757270?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165646687316782388072415%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165646687316782388072415&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-2-108757270-null-null.142^v24^huaweicloudv2,157^v15^new_3&utm_term=protoc+%E6%80%8E%E4%B9%88%E7%BB%99repeated%E5%AD%97%E6%AE%B5%E8%B5%8B%E5%80%BCH&spm=1018.2226.3001.4187)

- 标识符
  - message 标识一条消息
  - enum 标识一个枚举类型
  - 使用protobuf编译器将协议文件编译后，message和enum都会生成一个类
  
- 字段
  - required：**表示该字段必须有值，不能为空**，否则message被认为是未初始化的。如果试图build一个未初始化的message将会抛出RuntimeException。解析未初始化的message会抛出IOException(**proto3 已经移除**)
  - optional：**表示该段为可选值，可以不进行设置**。如果不设置，会设置一个默认值。可以设置一个默认值，否则使用系统默认值，数字类型默认为0；字符串类型默认为空字符串；逻辑类型默认为false；内部自定义的message，默认值通常是message的实例或原型(**proto3不可以设置默认值，并且改名为singular**)
  - repeated：表示该字段可以重复，**可等同于动态数组**
    - 注意：使用required字段一定要小心，因为该字段是永久性的。如果以后因为某种原因，想不用该字段，或者要将该字段改成optional或者repeated，那么使用旧接口读取新的协议时，如果发现没有该字段，他们会认为该字段是不完整的，会拒接接收该消息，或者直接丢弃
  - **简而言之：proto3中，只有repeated字段可以进行设置了**
  
- 字段类型

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

  - N 表示打包的字节并不是固定。而是根据数据的大小或者长度,例如int32，如果数值比较小，在0~127时，使用一个字节打包
  - 关于 fixed32 和int32的区别。fixed32的打包效率比int32的效率高，但是使用的空间一般比int32多。因此一个属于时间效率高，一个属于空间效率高。根据项目的实际情况，一般选择fixed32，如果遇到对传输数据量要求比较苛刻的环境，可以选择int32

- ## [proto3和proto2区别](https://blog.csdn.net/weixin_34294649/article/details/92170258)

  - 在第一行非空白非注释行，必须写：[syntax](https://so.csdn.net/so/search?q=syntax&spm=1001.2101.3001.7020) = “proto3”， 否则默认版本是proto2
  - 字段规则**移除了 “required”**，并把 “optional” 改名为 “singular”
  - “repeated”字段默认采用 packed 编码
    - 在 proto2 中，需要明确使用 [packed=true] 来为字段指定比较紧凑的 packed 编码方式

  - 移除了 default 选项
    - 在 proto2 中，可以使用 default 选项为某一字段指定默认值。在 proto3 中，字段的默认值只能根据字段类型由系统决定。也就是说，**默认值全部是约定好的，而不再提供指定默认值的语法**
    - 在字段被设置为默认值的时候，该字段**不会被序列化**。这样可以节省空间，提高效率。但这样就无法区分某字段是根本没赋值，还是赋值了默认值。这在 proto3 中问题不大，但在 proto2 中会有问题
      - 比如，在更新协议的时候使用 default 选项为某个字段指定了一个与原来不同的默认值，旧代码获取到的该字段的值会与新代码不一样

  - 枚举类型的第一个字段必须为 0，**这也是一个约定**
  - 移除了对分组的支持
    - 分组的功能完全可以用消息嵌套的方式来实现，并且更清晰。在 proto2 中已经把分组语法标注为『过期』了。这次也算清理垃圾了

  - 移除了对扩展的支持，新增了 Any 类型
  - 增加了 JSON 映射特性


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

  - **编译命令：protoc -I=$SRC_DIR --python_out=$DST_DIR $SRC_DIR/addressbook.prot**


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

  - 有关协议编译器为任何特定字段定义生成哪些成员的详细信息，请参见[Add有关Python generated code reference](https://developers.google.com/protocol-buffers/docs/reference/python-generated).



# 二、一些特性

## 2.1 标准消息类的方法

- 每个消息类还包含许多其他方法，可让您检查或操作整个消息，包括：

  - `IsInitialized()`: 检查是否所有必填字段都已设置

  - `__str__()`：返回消息的人类可读表示，对于调试特别有用（通常调用为`str(message)`or `print message`。）

  - `CopyFrom(other_msg)`: 用给定消息的值覆盖消息

  - `Clear()`: 将所有元素清除回空状态

  - 这些方法实现了`Message`接口。有关更多信息，请参阅[完整的 API 文档`Message`](https://googleapis.dev/python/protobuf/latest/google/protobuf/message.html#google.protobuf.message.Message)

    

## 2.2 解析和序列化

### 解析和序列化

- 最后，每个协议缓冲区类都有使用协议缓冲区[二进制格式](https://developers.google.com/protocol-buffers/docs/encoding)写入和读取您选择的类型的消息的方法。这些包括：

  - `SerializeToString()`: 序列化消息并将其作为字符串返回。请注意，字节是二进制的，而不是文本；我们只将`str`类型用作方便记录的容器

  - `ParseFromString(data)`: 从给定的字符串解析消息
  - 这些只是为解析和序列化提供的几个选项。同样，请参阅[`Message`API 参考](https://googleapis.dev/python/protobuf/latest/google/protobuf/message.html#google.protobuf.message.Message)以获取完整列表

## 2.3 扩展和兼容

- 在你发布使用你的协议缓冲区的代码之后，迟早你肯定会想要“改进”协议缓冲区的定义。如果您希望您的新缓冲区向后兼容，并且您的旧缓冲区向前兼容——您几乎肯定希望这样做——那么您需要遵循一些规则。在新版本的协议缓冲区中：

  - 您*不得*更改任何现有字段的标签号

  - 您*不得*添加或删除任何必填字段(required)

  - 您*可以*删除可选或重复的字段(optional)

  - 您*可以*添加新的可选字段或重复字段，但您必须使用新的标签号（即，从未在此协议缓冲区中使用过的标签号，即使已删除的字段也不使用）。（这些规则有[一些例外](https://developers.google.com/protocol-buffers/docs/proto#updating)，但很少使用）
  - 如果您遵循这些规则，旧代码将愉快地阅读新消息并忽略任何新字段。对于旧代码，已删除的可选字段将仅具有其默认值，而删除的重复字段将为空。新代码也将透明地读取旧消息。但是，请记住，旧消息中不会出现新的可选字段，因此您需要明确检查它们是否设置为，或者在文件`has_`中提供一个合理的默认值`.proto``[default = value]`标签号之后。如果未为可选元素指定默认值，则使用特定于类型的默认值：对于字符串，默认值为空字符串。对于布尔值，默认值为 false。对于数字类型，默认值为零。另请注意，如果您添加了一个新的重复字段，您的新代码将无法判断它是留空（通过新代码）还是根本没有设置（通过旧代码），因为它没有`has_`标志



## 2.4 更多高级用法

- 协议缓冲区的用途超越了简单的访问器和序列化。一定要探索[Python API 参考](https://googleapis.dev/python/protobuf/latest/)，看看你还能用它们做什么
- 协议消息类提供的一个关键特性是*反射*。您可以遍历消息的字段并操作它们的值，而无需针对任何特定的消息类型编写代码。使用反射的一种非常有用的方法是将协议消息与其他编码（例如 XML 或 JSON）相互转换。反射的更高级用途可能是发现相同类型的两条消息之间的差异，或者开发一种“协议消息的正则表达式”，您可以在其中编写与某些消息内容匹配的表达式。如果您发挥自己的想象力，则可以将 Protocol Buffers 应用于比您最初预期的范围更广的问题！
- 反射作为[`Message`接口](https://googleapis.dev/python/protobuf/latest/google/protobuf/message.html#google.protobuf.message.Message)的一部分提供





# 三、一些参考链接

- [_VarintBytes](https://www.jianshu.com/p/79a2d01a32db)
  - 序列化的一种方法

- [package](https://blog.csdn.net/u010900754/article/details/105984161/?ops_request_misc=&request_id=&biz_id=102&utm_term=protobuf%20%E6%96%87%E4%BB%B6%E4%B8%ADpackage%E7%9A%84%E4%BD%9C%E7%94%A8&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-105984161.142^v11^pc_search_result_control_group,157^v13^control&spm=1018.2226.3001.4187)
  - 一个包的标识

- [变量后面的数字](https://blog.csdn.net/weixin_39862716/article/details/111774831?ops_request_misc=&request_id=&biz_id=102&utm_term=protobuf%20%E5%8F%98%E9%87%8F%E5%90%8E%E9%9D%A2%E7%9A%84%E6%95%B0%E5%AD%97&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-111774831.142^v11^pc_search_result_control_group,157^v13^control&spm=1018.2226.3001.4187)
  - 作用仅仅是标识位置而已
- [中文参考](https://blog.csdn.net/wuxintdrh/article/details/113623777?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165444563416782391859180%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165444563416782391859180&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-113623777-null-null.142^v11^pc_search_result_control_group,157^v13^control&utm_term=repeated+%E5%AD%97%E6%AE%B5%E8%A2%AB%E7%A7%BB%E9%99%A4%E4%BA%86%EF%BC%9F&spm=1018.2226.3001.4187)
- [文档翻译](https://zingzheng.github.io/post/protocol-buffers-python-generated-code/)
- [文档翻译](https://zhuanlan.zhihu.com/p/451390348)
- [演示文档](https://android.googlesource.com/platform/external/protobuf-javalite/+/da247c697d4164fd5ec84f56050890e6469d6edb/python/google/protobuf/internal/well_known_types_test.py)

## 3.2 传输过程

- 根据proto生成文件
- 从生成的文件之中导入类
- 根据类定义对象，然后填充类的数据
- 对这个类进行序列化(可以根据选择是否写入文件之中)
- 读取的时候，定义对象，打开文件，读取内容进行反序列化即可

```python
# 进行写入
  def build_pb_raw(cls, pb_list, byte_prefix=True): # pb_list dict数据列表
    """Construct road protocol buffer raw content."""
    file_content_raw = b''
    for pb_data in pb_list: # 遍历数据列表
      if byte_prefix:
        serialized_pb_str_size = _VarintBytes(pb_data.ByteSize()) # 将整形转换成字节变量
        file_content_raw += (
            bytes(serialized_pb_str_size) + pb_data.SerializeToString()) # 将协议消息序列化为二进制字符串
      else:
        file_content_raw += pb_data.SerializeToString()
    return file_content_raw # 返回文件内容
  
@classmethod
  def download_proto(cls, filename, file_content_raw):
    """Generate the download file based on the file name
    and proto content passed in. """
    # 根据传入的文件名生成 proto内容

    response = HttpResponse(file_content_raw) # 传入内容
    response['Content-Type'] = 'text/plain' # 传输的类型
    response['Content-Length'] = str(len(file_content_raw)) #  长度
    response['Content-Disposition'] = 'attachment; filename="%s"' % filename
    return response


# 进行读取
from importlib import import_module
  mod = import_module("voy_foundation_protos.hdmap_protos.zone_pb2")
  zone = getattr(mod, 'Zone')()
  from google.protobuf.internal.decoder import _DecodeVarint32 # 源码中的方法，读取一个元素的值，并且返回长度
  with open(output_file,'rb') as f:
    buf = f.read()
    n = 0
    while n<len(buf):
      msg_len,new_pos=_DecodeVarint32(buf,n)
      n = new_pos
      msg_buf = buf[n:n+msg_len]
      n+=msg_len
      zone.ParseFromString(msg_buf)
      print(zone)

```



# 四、消息类方法

- [官方文档连接](https://googleapis.dev/python/protobuf/latest/google/protobuf/message.html)

## 4.1 异常模块

- 反序列化

  ```
   google.protobuf.message.DecodeError
  ```

- 序列化

  ```
  google.protobuf.message.EncodeError
  ```

- 基本错误类型

  ```python
  google.protobuf.message.Error
  ```

  

## 4.2 序列化模块

- `class google.protobuf.message Message`是根据protoc协议编译生成的类，该类的子类拥有以下方法，也就是说根据protoc文件生成的python文件中的对象(`根据message标识符生成的类`)也拥有这些方法

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

    

- `SerializeToString`(***kwargs*)

  - 序列化消息

- `SerializePartialToString`(***kwargs*)
  - 将协议消息序列化为二进制字符串。  此方法类似于SerializeToString，但不检查消息是否已初始化

