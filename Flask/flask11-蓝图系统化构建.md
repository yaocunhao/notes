# 一、正则表达式和命名路由

- flask 正则表达式的使用需要**定义自己的转换器**，并且在flask项目运行的时候进行**初始化**

- 使用正则表达式的格式为：regex("这里面填写表达式")

- flask之中动态命名路由的方式为<动态变量名称> 或者 <regex("这里面填写表达式")：动态变量名称>

  - Django之中则为 (?P<动态变量名称>(正则表达式))

  ```python
  from flask import Blueprint, Flask, render_template
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
  
  # 蓝图创建
  test = Blueprint('test', __name__, url_prefix='/test')
  
  def example(uid: str):
    if len(uid) == 0:
      print("uid is none!!")
    else:
      print("uid is not None!!!")
      print("uid:", uid, len(uid))
  
    return "uid: %s" % uid
  
  # 添加路由
  # 使用正则表达式
  test.add_url_rule('<regex("(\w+)?"):uid>', view_func=example, methods=['GET'])
  
  # 将蓝图注册到App程序之中
  app.register_blueprint(test)
  
  # _____________命名路由_____________________
  # 如下代码之中uid和slug就是动态变量，其中uid是前面的正则表达式的动态变量，slug为-后面的内容
  # 测试url: http://127.0.0.1:5000/test/add-foo/
  def example2(uid: str, slug):
    print(uid, slug)
    return "uid: %s" % uid
  test.add_url_rule('/<regex("(add|delete)?"):uid>-<slug>/',view_func=example2,methods=['GET'])
  
  ```



# 二、如何构建一个项目

- 创建蓝图对象

- 给蓝图对象添加路由(该路由调用那个函数)

- 进行蓝图的注册

  ```python
  # 文件1： 填写视图函数(相当于Django中的view，只是这里直接进行逻辑处理了，没有modle文件)
  class LoginView(views.MethodView):
  
    def get(self, url_get):  # get 方法执行这个函数
      print(url_get)
      print(str(url_get))
      if str(url_get) == "123":
        data = {'data': 'get'}
        return data
      else:
        return {'a': 1, 'b': 2}
  
    def post(self, url_post):  # post 方法执行这个函数
      if url_post == 'pp':
        data = {'data': 'post'}
        return data
      else:
        return '4344444'
      
  # 文件2：进行路由，相当于url文件
  from .route_test import LoginView, T2
  from flask import Blueprint
  
  # 蓝图创建
  r1 = Blueprint('test_1', __name__,
                 url_prefix='/test1')  # param1: 蓝图名称， param2:有助于定位，param3: 前缀
  # 路由添加
  r1.add_url_rule('/test1_url/<url_get>',
                  view_func=LoginView.as_view('tet'),
                  methods=[
                      'GET',
                  ])
  r1.add_url_rule('/test1_url/<url_post>',
                  view_func=LoginView.as_view('tet2'),
                  methods=[
                      'POST',
                  ])
  
  
  #  文件3： 进行蓝图的注册，需要在flask程序启动的时候进行初始化
  from flask import Flask
  from .url import r1, r2 # 导入蓝图
  # 导入BasseConverter类，作为所有自定义转换器的父类
  from werkzeug.routing import BaseConverter
  
  app = Flask(__name__)
  
  # 定义自己的转换器，继承于转换器父类
  class RegexConver(BaseConverter):
    def __init__(self, url_map, regex):
      super().__init__(map=url_map)
      self.regex = regex
  # 初始化转换器名称
  app.url_map.converters['regex'] = RegexConver  # 路由匹配
  
  
  # 进行蓝图注册
  app.register_blueprint(r1)
  app.register_blueprint(r2)
  
  ```

  
