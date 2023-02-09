# 一、前言

- protubuf对int类型的0值处理不是很优雅，因此在设计pb文件的时候，得额外的注意这个问题

# 二、实例演示

- pb文件

  ```properties
  // 主要用来测试 0 值问题
  syntax = "proto3"; // 指定使用3版本
  package pt3; // 包的标识
  
  enum Type {
    SOFT=0;
    HARD=1;
  }
  
  message TestMessage {
    string name = 1;
    Type type = 2;
  }
  
  ```

- 演示代码

  ```python
  """测试protoc之中的零值问题"""
  from pt_3_pb2 import Type, TestMessage
  from google.protobuf import json_format, text_format
  
  
  def test1():
      """如果不设0值，转化为字典也会默认待一个0值"""
      test_obj = TestMessage()
      test_obj.name = '123'
      test_obj.type = 0
  
      # including_default_value_fields:
      # 如果为True，会序列化所有字段，主要的是默认字段(比如int类型，默认为0)。 带来的问题是：int如果没有设置为0，也会被设置成0
      # 如果为False，只会序列化非空字段。 带来的问题是：就算设置了0值，也不会序列化
      message = json_format.MessageToDict(test_obj,
                                          including_default_value_fields=True)
      print(type(message), message)
  
  
  test1()
  ```

  