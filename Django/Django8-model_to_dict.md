- 函数声明

  ```python
  from django.forms.models import model_to_dict
  ：def model_to_dict(instance, fields=None, exclude=None):
  ```

  

- 字段含义
  - instance是对象实例
  - fields是指定需要哪些字段
  - exclude是指定排除哪些字段，exclude比fields优先级高