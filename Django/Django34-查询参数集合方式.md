- 伪代码演示

  ```python
    # filter_mutil = {}，key为过滤的字段，value为过滤的值是一个list[]
    # 同一条件的模糊查询，也就是一个key对应多个值，所以需要使用Q对象之中的`|`运算
      for f_name, f_value in filter_mutil.items():
        if f_value:
          # 建立多个Q对象
          filter_fields = [Q(**{f_name: value}) for value in f_value]
          # 将Q对象传入做或运算
          filter_field = reduce(lambda x, y: x | y, filter_fields)
          model_objs = model_objs.filter(filter_field)
          
      # exact query
      model_objs = model_objs.filter(**filter_params).order_by(
        '-update_time')
  ```

  