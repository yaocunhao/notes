# 一、正则表达式

```python
from flask import Flask
from .url import r1, r2
# 导入BasseConverter类，作为所有自定义转换器的父类
from werkzeug.routing import BaseConverter

app = Flask(__name__)

# 定义自己的转换器，继承于转换器父类
class RegexConver(BaseConverter):
  def __init__(self, url_map, regex):
    super().__init__(map=url_map)
    self.regex = regex

# 将自定义的转换器添加到flask的应用中
app.url_map.converters['regex'] = RegexConver


# 初始化转换器名称
app.url_map.converters['regex'] = RegexConver  # 路由匹配

# 进行蓝图注册
app.register_blueprint(r1)
app.register_blueprint(r2)

```

# 二、层层递进关系

