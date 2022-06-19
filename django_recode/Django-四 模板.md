# 一、什么是模板

- 在 Django 中，模板是可以根据字典数据动态变化的，并且能够根据视图中传递的字典数据动态生成相应的 HTML 网页。Django 中使用 Template 来表示模板，Template 对象定义在 django/template/base.py 文件中，它的构造函数如下所示：

  ```python
  def __init__(self,template_string,origin=None,name=None,engine=None)
  ```

  - 它只有一个必填的参数：字符串表示的模板代码

# 二、模板的作用

- 程序员编写 Python代码和设计人员制作模板两项工作同时进行的效率是最高的，远胜于让一个人等待另一个人完成对某个既包含 Python又包含 HTML 的文件的编辑工作
- 基于这些原因，将页面的设计和Python的代码分离开会更干净简洁更容易维护。 我们可以使用 Django的 模板系统 (`Template System`)来实现这种模式

# 三、创建和设置模板

- 在项目目录下创建templates，在setting.py之中进行设置

  ```python
  EMPLATES = [
      {
          # Django默认设置，指定了要是用的模板引擎的 Python 路径；
          'BACKEND': 'django.template.backends.django.DjangoTemplates',
          'DIRS': [os.path.join(BASE_DIR, 'templates')],  # 指定模板文件的存放路径,可以是一个或者多个。模板引擎将按照列表中定义的顺序查找模板文件；
          'APP_DIRS': True,  # 搜索APP里面的所有templates目录
          'OPTIONS': {  # 指定额外的选项，不同的模板引擎有着不同的可选参数
              'context_processors': [  # context_processors 用于配置模板上下文处理器
                  'django.template.context_processors.debug',
                  'django.template.context_processors.request',
                  'django.contrib.auth.context_processors.auth',
                  'django.contrib.messages.context_processors.messages',
              ],
          },
      },
  ]
  ```

  

# 四、模板的加载与响应方式

## 4.1 使用方式

- 通过 loader 获取模板,通过 HttpResponse 进行响应

  ```python
  from django.template import loader
  
  # 1.通过loader加载模板
  t = loader.get_template("模板文件名")
  
  # 2.将t转换成HTML字符串
  html = t.render(字典数据) #给模板之中对应的变量传入值，也就是通过view函数可变化的传入所需要的值
  
  # 3.用响应对象将转换的字符串内容返回给浏览器
  return HttpResponse(html)
  ```

- 使用 render 方法直接加载并响应模板

  - 上述过程步骤还是很多，可以直接使用render接口，**将模板名字和字典数据变量直接传入返回响应即可**

  ```python
  from django.shortcuts import render
  
  return render(request,'模板文件名', 字典数据)
  ```

## 4.2演示

- 在index.vires之中创建视图函数

  ```python
  from django.http import HttpResponse
  
  # 方式一
  from django.template import loader  # 导入loader方法
  # 方式二
  from django.shortcuts import render  # 导入render 方法
  
  
  def test_html(request):
      t = loader.get_template('test.html')
      html = t.render({'name': 'Django'})  # 以字典形式传递数据并生成html
      return HttpResponse(html)  # 以 HttpResponse方式响应html
  
  
  def test_html(request):
      return render(request, 'test.html', {'name': 'Django'})  # 根据字典数据生成动态模板
  
    
  # 当方法1和2同时存在时，会优先响应方法2 (有待验证)
  ```

- 在 templates 目录下创建 test.html 文件

  ```python
  #这个name就是一个变量，可以通过视图函数的处理逻辑来传入
  #并且这个前端处理页面可以交给前端去做，我们只需要调用传入对应的值即可
  #这就是解耦
  <p style="font-size:50px;color:red">{{ name }} 
  ```

- 在项目根目录的urls.py文件之中添加路由关系

  ```python
  urlpatterns = [ path('test/',views.test_html), ]
  path('test/', views.test_html),
  ```

  ![image-20220320234434542](../../Library/Application Support/typora-user-images/image-20220320234434542.png)

## 4.3 render方法详解

- renbder 方法的作用是结合一个给定的模板和一个给定的字典，并返回一个渲染后的 HttpResponse 对象。通俗的讲就是把字典格式的内容, 加载进 templates 目录中定义的 HTML 文件, 最终通过浏览器渲染呈现

- rebder() 方法的完整参数格式如下所示：

  ```python
  render(request, template_name, context=None, content_type=None, status=None, using=None)
  
  
  以下每个参数的含义如下所示：
  request: 是一个固定参数，用于生成响应的请求对象；
  template_name: templates 中定义的文件, 要注意路径名. 比如 'templates\appname\index.html', 参数就要写‘appname\index.html’；
  context: 要传入文件中用于渲染呈现的数据, 默认是字典格式；
  content_type: 生成的文档要使用的媒体格式类型。默认为 DEFAULT_CONTENT_TYPE 设置的值；
  status: http 的响应代码,默认是 200；
  using: 用于加载模板使用的模板引擎的名称。
  ```

- 常见 content_type 格式

  ```python
  text/html ： HTML 格式
  text/plain ：纯文本格式
  text/xml ： XML 格式
  
  image/gif ：gif 图片格式
  image/jpeg ：jpg 图片格式
  image/png：png 图片格式
  
  application/xhtml+xml ：XHTML 格式
  application/xml： XML 数据格式
  application/atom+xml ：Atom XML 聚合格式
  application/json： JSON 数据格式
  application/pdf：pdf 格式
  application/msword ：Word 文档格式
  application/octet-stream ： 二进制流数据（如常见的文件下载）
  application/x-www-form-urlencoded ：form 表单数据被编码为 key/value 格式发送到服务器（表单默认的提交数据的格式）。
  
  multipart/form-data ： 需要在表单中进行文件上传时，就需要使用该格式
  ```

# 五、模板语法

## 5.1 概述

- 我们知道，模板是文本文件，比如我们常见的有 HTML、CSV、TXT 等
- Django 模板语言的语法主要分为 变量 、标签、过滤器、解释

## 5.2 模板变量

- 上面用到的name就是模板变量。jango 模板引擎通过 `context_processors`这个上下文处理器来完成字典提供的值（vaule）与模板变量之间的替换

- 在视图函数中**必须将变量封装到字典中**才允许传递到模板上

- **return** render(request, 'xxx.html', locals())

  - locals() 返回当前函数作用域内**全部局部变量形成的字典**

    ```python
     
       def test():
        v1 = 200
        v2 = "1234"
        dic = locals()
    
        print(dic)
    
    test() # {'v2': '1234', 'v1': 200}
    ```

- 模板变量传值

  - 模板变量的传值是有四种场景的

    - 索引查询：name.key 此时传入的是一个列表
    - 字典查询：name.key
    - 属性或方法查询：对象.方法
    - 函数调用：函数名
    - 注意：在模板中访问对象方法的时候，方法调用不需要加括号，而且只能够调用不带参数的方法；如果不希望自定义的方法被模板调用可以使用 alters_data=Ture 属性，放在方法的结束位置即可

  - 演示代码

    ```python
    # 视图函数
    def test(request):
        value = {} #空字典，模板以字典的形式进行传参
        value['name'] = 'django'
        value['list'] = ['测试模板','第二次测试']
        value['dict'] = {'v1':100,'v2':200}
        value['func'] = test_func
        value['obj']  = obj()
        return render(request,'test_html.html',value)
    
    def test_func():
        return 'i am here!!'
    
    class obj:
        def func(self):
            return 'obj func'
        #func.alters_data=True #不让obj()方法被模板调用
        
    # 模板
    //字典查询
    <p> 普通变量{{ name }}</p>
    //索引查询
    <p> 列表的值{{ list.1 }}</p>
    //自动遍历字典
    <p> 字典的值{{ dict }}  <p>
    //字典查询
    <p> 字典查询的值是{{dict.v2}}  </p>
    //函数方法调用
    <p> 函数fuction：{{ func }}</p>
    //类方法调用
    <p> 类实例化对象：{{obj.func}} </p>
    ```

    ![image-20220321094352767](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192305018.png)





