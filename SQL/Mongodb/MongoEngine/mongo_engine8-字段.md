- *class*`mongoengine.fields.``SequenceField`**(***collection_name=None***,** *db_alias=None***,** *sequence_name=None***,** ***value_decorator**=None***,** **args***,** ***kwargs***)**

  - mongo 内部有一个计数器，每次会默认生成这个数字，如果外部进行了填充，并不会影响计数器进行叠加

    ```python
    # 此处x就是计数器，返回的值就是在原有计数器上加上100
    def add_func(x):
      print(x)
      if x<1000:
        return x+100
      
    class LinkPost(Post):
      link_url = StringField()
      link_id = me.SequenceField(required=True, value_decorator=func)
      
    def test18():
      """测试value_decorator 字段"""
    	
      # 假设link_id 填写为1，下次不填写，根据计数器自动生成也是1，也就是外部填充的值，并不会对计数器产生影响
      link = LinkPost(link_id="1")
      link.save()
    ```

  - value_decorator 只对默认的计数器有效，在查询的时候value_decorator也会生效，<font color=yellow>所以尽量不要使用该装饰函数</font>
  
    

