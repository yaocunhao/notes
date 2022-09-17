- [参考链接](https://blog.csdn.net/xujin0/article/details/97372499?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165952004816782350897531%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165952004816782350897531&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-97372499-null-null.142^v39^pc_rank_34_2&utm_term=add_url_rule&spm=1018.2226.3001.4187)

# 一、路由使用的两种方式

## 1.1  使用介绍

- 装饰器使用

  ```python
  from flask import Flask
  
  app = Flask(__name__)  # __name__ 当前文件的名称
  
  @app.route('/test') # 路由必须以/开始
  def test():
    return 'hello word!!!!'
  ```

- 源码函数调用

  ```python
  from flask import Flask
  
  app = Flask(__name__)  # __name__ 当前文件的名称
  
  def test():
    return 'hello word!!!!'
  
  app.add_url_rule('/test', view_func=test) # 直接源码函数调用
  ```

## 1.2  源码分析 

- 在flask中，我们知道给一个函数添加url的时候，只需要使用装饰器@app.route('<url>')装饰对应的函数就可以了。为什么这个装饰器就可以给函数视图 添加url规则呢？查看app.route()源码发现，这个装饰器在里面调用的另外一个方法self.add_url_rule，这里的self就是app这个实例对象

- **methods**：`add_url_rule`还可以传入一个`methods`参数，用来指定这个函数对应的访问规制，如post,get请求等，默认是`get`请求，并且只允许`get`请求。当我们需要改变请求方式的时候，我们就可以传入这个参数了

  ```python
  import typing as t
  
  
  def add_url_rule(
      self,
      rule: str,  # 绑定的函数
      endpoint: t.Optional[str] = None,  # 端口名称，默认和view_func 一样
      view_func: t.Optional[t.Callable] = None,  # 视图函数
      provide_automatic_options: t.Optional[bool] = None, # 添加可选方法，忽略即可
      **options: t.Any, # methods = ['Post','get']
  ) -> None:
    pass
  
  
  
  # 使用装饰器进行路由
  # 本质也是使用 add_url_rule
  def route(rule, **options):
    def decorator(f):
      endpoint = options.pop("endpoint", None)
      add_url_rule(rule, endpoint, f, **options)
      return f
  
    return decorator
  ```

  

# 二、类视图

## 2.1 标准类视图

- 标准类视图是继承自`flask.views.View`

- 在子类中必须实现`dispatch_request`方法，这个方法就是相当于视图函数，所有的逻辑操作我们都要放在这个里面完成。也必须的返回一个值

- 类视图和函数视图基本都是一样的，只是我们在做添加`url`规则的时候不一样而已

- methods = [] 提供http方法名称

  ```python
  from flask import Flask, views
  app = Flask(__name__)
  
  class ProfileView(views.View): # 对View进行继承
    methods = ['GET', 'POST'] # 提供http方法的名称
    
    def dispatch_request(self): # 在子类之中实现 dispatch_request，这个方法相当于视图函数
      return '个人中心页面'
  
  
   # 添加路由规则 
  app.add_url_rule('/profile/', endpoint='profile',
                   view_func=ProfileView.as_view('profile')) # as_view 起一个名字
  
  ```

  - as_view里面传入的参数 含义
    - 类视图都会继承自View类，并且都要重写`dispatch_request`方法，那么flask怎么知道我们绑定的是哪一个类的`dispatch_request`方法呢？就是通过这个参数来指定的
    - 相当于给dispatch_request方法起一个名字。没有写endpoint参数的时候，那么endpoint的值就会是这个函数的名称
    - 通过url_for进行反转的时候也是用的这个值。如果指定了endpoint的值，url_for就会使用endpoint的值，这个赋予的函数名称对于我们来说没有什么太大的用处了，但是flask内部还是很有用处的
      

## 2.2 基于调度方法的视图

- 在flask中，还提供了另外一种类视图`flask.views.MethodView`，对每个HTTP的请求方法执行不同的函数

- 这种方法可以不提供methods

  ```python
  from flask import Flask, views
  app = Flask(__name__)
  
  class LoginView(views.MethodView):
    def get(self): # get 方法执行这个函数
      data = {'data': 'get'}
      return data
  
    def post(self): # post 方法执行这个函数
      data = {'data': 'post'}
      return data
  
    
  app.add_url_rule('/login/', view_func=LoginView.as_view('login'))
  ```



# 三、视图使用装饰器

- 假设一个登录验证的装饰器

  ```python
  from flask import Flask,views,request,redirect,url_for
  from functools import wraps
  
  app = Flask(__name__)
  
  def login_require(fn):
      @wraps(fn)
      def wrapper(*args,**kwargs):
          username = request.args.get('username')
          if username and username == 'xxx':
              return fn(*args,**kwargs)
          else:
              return redirect( url_for('login') )
      return wrapper
  
  ```

- 函数视图使用装饰器

  - **装饰器必须放在路由下面**

  ```python
  @app.route('/login/')
  def login():
  	return 'login page'
  
  @app.route('/profile/')
  @login_require		# 使用我们写的login_require装饰器 这个装饰器必须放在@app.route()装饰器的下面
  def profile():
  	return '个人中心'
  ```

- 类视图使用装饰器

  - 将装饰器用decorators 列表装起来
  - 后者直接装饰器dispatch_request 这个函数

  ```python
  class ProfileView(views.View):
  	# 将这个视图所有的装饰器用decorators这个列表装起来
  	decorators = [login_require]
  
  	def dispatch_request(self):
  		return '个人中心页面'
  
  # 当然也可以直接装饰这个函数
  class ProfileView(views.View):
  	@login_require	
  	def dispatch_request(self):
  		return '个人中心页面'
  
  app.add_url_rule('/profile/',view_func=Profile.as_view('profile'))
  
  ```

- views.MethodView类 使用装饰器

  - 直接对相应的请求进行装饰
  - 重写dispatch_request方法，然后进行装饰
  - 使用decorators属性(也就是使用这个列表)

  ```python
  # 第一种，直接对相应的请求进行装饰
  class ProfileView(views.MethodView):
  	@login_require
  	def get(self):
  		return 'profile from get method'
  
  	def post(self):
  		return 'profile from post method'
  
  # 第二种，重写dispatch_request方法，然后进行装饰
  class ProfileView(views.MethodView):
  	@login_require
  	def dispatch_request(self,*args,**kwargs):
  		return super().dispatch_request(*args,**kwargs)
  		
  	def get(self):
  		return 'profile from get method'
  
  	def post(self):
  		return 'profile from post method'
  
  # 第三中，使用decorators属性
  class ProfileView(views.MethodView):
  	decorators = [login_require]
  		
  	def get(self):
  		return 'profile from get method'
  
  	def post(self):
  		return 'profile from post method'
  
  app.add_url_rule('/profile/',view_func=Profile.as_view('profile'))
  ```



# 四、Url 的构建

## 4.1 Url变量规则

- 通过向规则参数添加变量部分，可以动态构建URL。此变量部分标记为**<variable-name>** 。它作为关键字参数传递给与规则相关联的函数

  ```python
  from flask import Flask
  app = Flask(__name__)
  
  @app.route('/hello/<name>')
  def hello_name(name):
     return 'Hello %s!' % name
  
  if __name__ == '__main__':
     app.run(debug = True)
  
  #  http:// localhost:5000/hello/123  ， 变量为123
  #  输入内容 Hello  123!
      
  ```

- 除了默认字符串变量部分之外，还可以使用以下转换器构建规则：

  | 序号 |  转换器   |           描述           |
  | :--: | :-------: | :----------------------: |
  |  1   |  **int**  |         接受整数         |
  |  2   | **float** |        对于浮点值        |
  |  3   | **path ** | 接受用作目录分隔符的斜杠 |

  ```python
  from flask import Flask
  app = Flask(__name__)
  
  @app.route('/blog/<int:postID>')
  def show_blog(postID):
     return 'Blog Number %d' % postID
  
  @app.route('/rev/<float:revNo>')
  def revision(revNo):
     return 'Revision Number %f' % revNo
  
  if __name__ == '__main__':
     app.run()
  ```

- 标准的路由定义方式

  - 路由中结尾`/`使得访问时url的结尾是否带`/`都可以进行访问，这样更加的标准

  ```python
  @app.route('/flask')
  
  @app.route('/flask/')
  ```

  - 当使用POST方法时，遇到过必须要`/`的情况，因此在设置的时候，添加关键字 `strict_slashes=False`[参考链接](https://zhuanlan.zhihu.com/p/107383595)