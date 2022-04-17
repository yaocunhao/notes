# 一、前言

- 程序员编写 Python代码和设计人员制作模板两项工作同时进行的效率是最高的，远胜于让一个人等待另一个人完成对某个既包含 Python又包含 HTML 的文件的编辑工作
- 基于这些原因，将页面的设计和Python的代码分离开会更干净简洁更容易维护。 我们可以使用 Django的 模板系统 (`Template System`)来实现这种模式

# 二、模板系统基本知识

```python
<html>
<head><title>Ordering notice</title></head>

<body>

<h1>Ordering notice</h1>

<p>Dear {{ person_name }},</p> #变量-> 如何指定变量的值？？

<p>Thanks for placing an order from {{ company }}. It's scheduled to
ship on {{ ship_date|date:"F j, Y" }}.</p> #们将变量ship_date传递给date过滤器，同时指定参数”F j,Y”。date过滤器根据参数进行格式输出。过滤器是用管道符(|)来调用的，

<p>Here are the items you've ordered:</p>

<ul>
{% for item in item_list %}
    <li>{{ item }}</li>
{% endfor %}
</ul>

{% if ordered_warranty %} #模板标签 标签(tag)定义比较明确，即：仅通知模板系统完成某些工作的标签。 if判断语句
    <p>Your warranty information will be included in the packaging.</p>
{% else %}
    <p>You didn't order a warranty, so you're on your own when
    the products inevitably stop working.</p>
{% endif %}

<p>Sincerely,<br />{{ company }}</p>

</body>
</html>
```

# 三、如何使用模板系统

## 3.1 使用展示

- 创建对象：`Template.Template`
- 传入变量：`template.Contextg`
- 调用模板方法：`render`

```python
>>> from django import template	# 导入模型
>>> t = template.Template('My name is {{ name }}.') #创建对象，双花括号包起来的是变量
>>> c = template.Context({'name': 'Adrian'})	 			#传入变量
>>> print t.render(c) # 调用方法，传入设置的值
My name is Adrian.

>>> c = template.Context({'name': 'Fred'})
>>> print t.render(c)
My name is Fred.
```

## 3.2 演示

```python
>>> from django.template import Template, Context

#我们把模板原始文本保存到变量raw_template。注意到我们使用了三个引号来 标识这些文本，因为这样可以包含多行。
>>> raw_template = """<p>Dear {{ person_name }},</p>
...
... <p>Thanks for placing an order from {{ company }}. It's scheduled to
... ship on {{ ship_date|date:"F j, Y" }}.</p>
...
... {% if ordered_warranty %}
... <p>Your warranty information will be included in the packaging.</p>
... {% else %}
... <p>You didn't order a warranty, so you're on your own when
... the products inevitably stop working.</p>
... {% endif %}
...
... <p>Sincerely,<br />{{ company }}</p>"""

#创建了一个模板对象t，把raw_template作为Template 类构造函数的参数
>>> t = Template(raw_template)

#从Python的标准库导入datetime模块，以后我们将会使用它
>>> import datetime

#们创建一个Context对象c。Context构造的参数是Python 字典数据类型。 在这里，我们指定参数person_name的值是'John Smith'， 参数company的值为‘Outdoor Equipment’，等等。
>>> c = Context({'person_name': 'John Smith',
...     'company': 'Outdoor Equipment',
...     'ship_date': datetime.date(2009, 4, 2),
...     'ordered_warranty': False})

#们在模板对象上调用render()方法，传递context参数给它。 这是返回渲染后的模板的方法，它会替换模板变量为真实的值和执行块标签。
>>> t.render(c)
u"<p>Dear John Smith,</p>\n\n<p>Thanks for placing an order from Outdoor
Equipment. It's scheduled to\nship on April 2, 2009.</p>\n\n\n<p>You
didn't order a warranty, so you're on your own when\nthe products
inevitably stop working.</p>\n\n\n<p>Sincerely,<br />Outdoor Equipment
</p>"
```

- 这就是使用Django模板系统的基本规则：写模板，创建`Template` 对象，创建`Context`， 调用`render()`方法。

## 3.3 同一个模板，多个上下文

- 一旦有了模板对象，你就可以通过它渲染多个`context`

  ```python
  >>> from django.template import Template, Context
  >>> t = Template('Hello, {{ name }}') #创建对象
  >>> print t.render(Context({'name': 'John'}))#设置变量并且传入
  Hello, John
  
  >>> print t.render(Context({'name': 'Julie'}))
  Hello, Julie
  >>> print t.render(Context({'name': 'Pat'}))
  Hello, Pat
  ```

  

## 3.4 深度变量的查找

- 在 Django 模板中遍历复杂数据结构的关键是句点字符，比如，假设你要向模板传递一个 Python 字典。 要通过字典键访问该字典的值，可使用一个句点：

  ```python
  >>> from django.template import Template, Context
  
  >>> person = {'name': 'Sally', 'age': '43'}
  >>> t = Template('{{ person.name }} is {{ person.age }} years old.') # 通过字典访问其值
  >>> c = Context({'person': person})#传入字典
  >>> t.render(c)
  u'Sally is 43 years old.'
  ```

- 同样，也可以通过句点来访问对象的属性。 比方说， Python 的 `datetime.date`对象有`year`、`month`和`day` 几个属性，你同样可以在模板中使用句点来访问这些属性：

  ```python
  >>> from django.template import Template, Context
  >>> import datetime
  >>> d = datetime.date(1993, 5, 2)
  
  #访问对象属性
  >>> d.year
  1993
  >>> d.month
  5
  >>> d.day
  2
  
  >>> t = Template('The month is {{ date.month }} and the year is {{ date.year }}.')
  >>> c = Context({'date': d})#传入字典
  >>> t.render(c)
  u'The month is 5 and the year is 1993.'
  ```

- 这个例子使用了一个自定义的类，演示了通过实例变量加一点来访问它的属性，这个方法适用于任意的对象

  ```python
  >>> from django.template import Template, Context
  >>> class Person(object):
  ...     def __init__(self, first_name, last_name):
  ...         self.first_name, self.last_name = first_name, last_name
  
  >>> t = Template('Hello, {{ person.first_name }} {{ person.last_name }}.')
  >>> c = Context({'person': Person('John', 'Smith')}) #创建自定义的对象，并且用点去进行一个访问
  >>> t.render(c)
  u'Hello, John Smith.'
  ```

- 点语法也可以用来引用对象的**方法**。例如，每个 Python 字符串都有 `upper()`和`isdigit()`方法，你在模板中可以使用同样的句点语法来调用它们：**注意这里调用方法时并***没有\***使用圆括号，而且也无法给该方法传递参数；你只能调用不需参数的方法**

  ```python
  >>> from django.template import Template, Context
  
  >>> t = Template('{{ var }} -- {{ var.upper }} -- {{ var.isdigit }}') #创建模板对象，并且使用点来引用方法
  
  >>> t.render(Context({'var': 'hello'}))
  u'hello -- HELLO -- False'
  
  >>> t.render(Context({'var': '123'}))
  u'123 -- 123 -- True'
  ```

- 最后，句点也可用于访问列表索引，例如

  - **不允许使用负数列表索引。像`{{ items.-1 }}`这样的模板变量将会引发`TemplateSyntaxError`**

  ```python
  >>> from django.template import Template, Context
  
  >>> t = Template('Item 2 is {{ items.2 }}.') #使用下标进行访问
  >>> c = Context({'items': ['apples', 'bananas', 'carrots']})
  >>> t.render(c)
  u'Item 2 is carrots.'
  ```

# 四、方法调用行为

- 方法调用比其他类型的查找略为复杂一点。 以下是一些注意事项：

  - 在方法查找过程中，如果某方法**抛出一个异常**，除非该异常有一个 `silent_variable_failure`属性并且值为`True` ，否则的话它将被传播。如果异常被传播，模板里的指定变量会被置为空字符串，比如：

    ```
    >>> t = Template("My name is {{ person.first_name }}.") #定义模板对象
    
    >>> class PersonClass3: #自定义类
    ...     def first_name(self):
    ...         raise AssertionError, "foo" #抛出异常
    
    >>> p = PersonClass3()#定义对象
    
    >>> t.render(Context({"person": p}))
    Traceback (most recent call last):
    ...
    AssertionError: foo
    
    >>> class SilentAssertionError(AssertionError):
    ...     silent_variable_failure = True #设置为true
    >>> class PersonClass4:
    ...     def first_name(self):
    ...         raise SilentAssertionError #抛出异常
    
    >>> p = PersonClass4()
    >>> t.render(Context({"person": p}))
    u'My name is .'# 异常没有被传播
    ```

  - 仅在方法**无需传入参数时，其调用才有效**。 否则，系统将会转移到下一个查找类型（列表索引查找）

  - 显然，有些方法是有副作用的，好的情况下允许模板系统访问它们可能只是干件蠢事，坏的情况下甚至会引发安全漏洞

    - 例如，你的一个`BankAccount`对象有一个`delete()`方法。 如果某个模板中包含了像`{{ account.delete }}`这样的标签，其中`account` 又是`BankAccount` 的一个实例，请注意在这个模板载入时，`account`对象将被删除

    - 要防止这样的事情发生，必须设置该方法的`alters_data`函数属性：

      ```python
      def delete(self):
          # Delete the account
      delete.alters_data = True
      ```

    - 模板系统不会执行任何以该方式进行标记的方法。 接上面的例子，如果模板文件里包含了`{{ account.delete }}` ，对象又具有`delete()`方法，而且`delete()` 有`alters_data=True`这个属性，那么在模板载入时， `delete()`方法将不会被执行。 它将静静地错误退出。

# 五、如何处理无效变量

## 5.1 默认处理

-  默认情况下，如果一个变量不存在，模板系统会把它展示为空字符串，不做任何事情来表示失败

  ```python
  >>> from django.template import Template, Context
  >>> t = Template('Your name is {{ name }}.')
  >>> t.render(Context())
  u'Your name is .'
  >>> t.render(Context({'var': 'hello'}))
  u'Your name is .'
  >>> t.render(Context({'NAME': 'hello'}))
  u'Your name is .'
  >>> t.render(Context({'Name': 'hello'}))
  u'Your name is .'
  ```

  - 系统静悄悄地表示失败，而不是引发一个异常，因为这通常是人为错误造成的。 这种情况下，因为变量名有错误的状况或名称， 所有的查询都会失败。 现实世界中，对于一个web站点来说，如果仅仅因为一个小的模板语法错误而造成无法访问，这是不可接受的。

## 5.2 玩一玩上下文对象

- 多数时间，你可以通过传递一个完全填充(`full populated`)的字典给 `Context()`来初始化上下文(`Context`) 。 但是初始化以后，你也可以使用标准的Python字典语法(`syntax`)向`上下文(Context)` 对象添加或者删除条目:

  ```python
  >>> from django.template import Context
  >>> c = Context({"foo": "bar"})
  >>> c['foo']
  'bar'
  >>> del c['foo']
  >>> c['foo']
  Traceback (most recent call last):
    ...
  KeyError: 'foo'
  >>> c['newvariable'] = 'hello'
  >>> c['newvariable']
  'hello'
  ```

# 六、 基本的模板标签和过滤器

- 像我们以前提到过的，模板系统带有内置的标签和过滤器

##  6.1 **if/else** 

- `{% if %}`标签检查(evaluate)一个变量，如果这个变量为真（即，变量存在，非空，不是布尔值假），系统会显示在 `{% if %}` 和 `{% endif %}`之间的任何内容，**else不是必须的** 例如：

  ```python
  {% if today_is_weekend %}
      <p>Welcome to the weekend!</p>
  {% endif %}
  ```

- `{% if %}` 标签接受 `and` ， `or` 或者 `not` 关键字来对多个变量做判断 ，或者对变量取反（`not` )，例如： 例如：

  ```python
  {% if athlete_list and coach_list %}
      Both athletes and coaches are available.
  {% endif %}
  
  {% if not athlete_list %}
      There are no athletes.
  {% endif %}
  
  {% if athlete_list or coach_list %}
      There are some athletes or some coaches.
  {% endif %}
  
  {% if not athlete_list or coach_list %}
      There are no athletes or there are some coaches.
  {% endif %}
  
  {% if athlete_list and not coach_list %}
      There are some athletes and absolutely no coaches.
  {% endif %}
  ```

- `{% if %}`标签不允许在同一个标签中同时使用 `and` 和 `or` ，因为逻辑上可能模糊的

- 系统不支持用圆括号来组合比较操作。 如果你确实需要用到圆括号来组合表达你的逻辑式，考虑将它移到模板之外处理，然后以模板变量的形式传入结果吧。 或者，仅仅用嵌套的`{% if %}`标签替换

- 多次使用同一个逻辑操作符是没有问题的，但是我们不能把不同的操作符组合起来

- 并没有 `{% elif %}` 标签， 请使用嵌套的`{% if %}` 标签来达成同样的效果

- 一定要用 `{% endif %}` 关闭每一个 `{% if %}` 标签

## 6.2 for

- `{% for %}`允许我们在一个序列上迭代。 与Python的 for 语句的情形类似，循环语法是`for X in Y` ，Y是要迭代的序列而X是在每一个特定的循环中使用的变量名称。 每一次循环中，模板系统会渲染在`{% for %}`和`{% endfor %}` 之间的所有内容
- 在每个`{% for %}`循环里有一个称为`forloop` 的模板变量。这个变量有一些提示循环进度信息的属性
  - `forloop.counter`总是一个表示当前循环的执行次数的整数计数器。 这个计数器是从1开始的，所以在第一次循环时 `forloop.counter` 将会被设置为1
  - `forloop.revcounter0` 类似于 `forloop.revcounter` ，但它以0做为结束索引。 在第一次执行循环时，该变量会被置为序列的项的个数减1。
  - `forloop.first` 是一个布尔值，如果该迭代是第一次执行，那么它被置为`True`在下面的情形中这个变量是很有用的
  - `forloop.last` 是一个布尔值；在最后一次执行循环时被置为`True`。 一个常见的用法是在一系列的链接之间放置管道符（`|`）
  - `forloop.parentloop` 是一个指向当前循环的上一级循环的 forloop 对象的引用（在嵌套循环的情况下）
  - `forloop` 变量仅仅能够在循环中使用。 在模板解析器碰到`{% endfor %}`标签后，`forloop`就不可访问了

## 6.3 **ifequal/ifnotequal** 

- `{% ifequal %}`标签比较两个值，当他们相等时，显示在`{% ifequal %}`和`{% endifequal %}`之中所有的值

## 6.4 注释

- 如果要实现多行注释，可以使用`{% comment %}` 模板标签，就像这样：

  ```
  {% comment %}
  This is a
  multi-line comment.
  {% endcomment %}
  ```

## 6.5 在视图中使用模板

- 导入Temptlate、Context模块

  ```python
  def GetTime(request):
      now = datetime.datetime.now() #获取时间
      t = Template("<html><body>It is now {{time}}.</body></html>") #create obj
      html = t.render(Context({'time': now})) # 添加变量
      return HttpResponse(html) # 返回渲染好的模板
  ```

  - 但是，模板仍然嵌入在Python代码里，并未真正的实现数据与表现的分离。让我们将模板置于一个单独的文件中，并且让视图加载该文件来解决此问题

- 你可能首先考虑把模板保存在文件系统的某个位置并用 Python 内建的文件操作函数来读取文件内容。假设文件保存在 `/home/djangouser/templates/mytemplate.html` 中的话，代码就会像下面这样：

  ```python
  from django.template import Template, Context
  from django.http import HttpResponse
  import datetime
  
  def current_datetime(request):
      now = datetime.datetime.now()
      # Simple way of using templates from the filesystem.
      # This is BAD because it doesn't account for missing files!
      fp = open('/home/djangouser/templates/mytemplate.html')
      t = Template(fp.read()) #读取模板文件的内容作为参数传入Template之中
      fp.close()
      html = t.render(Context({'current_date': now}))
      return HttpResponse(html)
  ```

  - 然而，基于以下几个原因，该方法还算不上简洁：
    - 它没有对文件丢失的情况做出处理。 如果文件`mytemplate.html` 不存在或者不可读，`open()`函数调用将会引发`IOError`异常。
    - 这里对模板文件的位置进行了硬编码。 如果你在每个视图函数都用该技术，就要不断复制这些模板的位置。 更不用说还要带来大量的输入工作！
    - 它包含了大量令人生厌的重复代码。 与其在每次加载模板时都调用 `open()` 、`fp.read()` 和 `fp.close()` ，还不如做出更佳选择。
    - 为了解决这些问题，我们采用了模板自加载跟模板目录的技巧.

## 6.6 模板加载

- 为了减少模板加载调用过程及模板本身的冗余代码，Django提供了一种使用方便且功能强大的 API ，用于从磁盘中加载模板
- 要使用此模板加载API，首先你必须将模板的保存位置告诉框架。设置的保存文件就是我们前一章节讲述ROOT_URLCONF配置的时候提到的`settings.py`

- setting.py之中，该设置告诉 Django 的模板加载机制在哪里查找模板。选择一个目录用于存放模板并将其添加到 `TEMPLATE_DIRS` 

  ```python
  # 该设置告诉 Django 的模板加载机制在哪里查找模板。
    
    # 添加模板的路径
    TEMPLATES
          'DIRS': ['/Users/didi/django/mysite/templates'],
          'APP_DIRS': False,  # false去DIRS中找，否则默认的
  
  ```

- 修改视图代码，让它使用 Django 模板加载功能而不是对模板路径硬编码

  ```python
  def GetTime(request):
      now = datetime.datetime.now()
      t = get_template('time.html')  # 以模板名称为参数，在文件系统中找出模块的位置，打开文件并返回一个编译好的 Template 对象。
      html = t.render({'noWtime': now}) #注意以及弃用了Context,采用字典方式进行传参
      return HttpResponse(html)
  ```

## 6.6 **render_to_response()**

- 我们已经告诉你如何载入一个模板文件，然后用 Context渲染它，最后返回这个处理好的`HttpResponse`对象给用户。 我们已经优化了方案，使用 `get_template()` 方法代替繁杂的用代码来处理模板及其路径的工作。但这仍然需要一定量的时间来敲出这些简化的代码。这是一个普遍存在的重复苦力劳动。D**jango为此提供了一个捷径，让你一次性地载入某个模板文件，渲染它，然后将此作为 `HttpResponse`返回**

  ```python
  def GetTime(request):
          now = datetime.datetime.now()
          return render_to_response('time.html', {'noWtime': now}) #自动加载模板文件，并且进行渲染
  ```

  - 然进行 `now` 计算，但模板加载、上下文创建、模板解析和`HttpResponse` 创建工作均在对`render_to_response()`的调用中完成了。 由于 `render_to_response()` 返回 `HttpResponse` 对象，因此我们仅需在视图中`return`该值。

  - ​	`render_to_response()` 的第一个参数必须是要使用的模板名称。 如果要给定第二个参数，那么该参数必须是为该模板创建 Context 时所使用的字典。如果不提供第二个参数， `render_to_response()` 使用一个空字典

## 6.7 include 标签

- 该标签允许在（模板中）包含其它的模板的内容。标签的参数是所要包含的模板名称，可以是一个变量，也可以是用单/双引号硬编码的字符串。 每当在多个模板中出现相同的代码时，就应该考虑是否要使用`{% include %}`来减少重复

- 下面这两个例子都包含了 `nav.html` 模板。这两个例子是等价的，它们证明单/双引号都是允许的。

  ```python
  {% include 'nav.html' %}
  {% include "nav.html" %}
  ```

- 和在 `get_template()` 中一样， 对模板的文件名进行判断时会在所调取的模板名称之前加上来自 TEMPLATE_DIRS 的模板目录。 所包含的模板执行时的`context`和包含它们的模板是一样的。 举例说，考虑下面两个模板文件：

  ```python
  # mypage.html
  
  <html>
  <body>
  {% include "includes/nav.html" %}
  <h1>{{ title }}</h1>
  </body>
  </html>
  
  # includes/nav.html
  
  <div id="nav">
      You are in: {{ current_section }} #使用Context进行渲染，会保存在这里，即使是在外层进行使用
  </div>
  ```

  - 如果`{% include %}`标签指定的模板没找到，Django将会在下面两个处理方法中选择一个：
    - 如果 DEBUG 设置为 True ，你将会在 Django 错误信息页面看到 `TemplateDoesNotExist` 异常。
    - 如果 DEBUG 设置为 False ，该标签不会引发错误信息，在标签位置不显示任何东西。

## 6.8模板继承

- 使用继承的一种常见方式是下面的三层法：
- 创建 `base.html` 模板，在其中定义站点的主要外观感受。 这些都是不常修改甚至从不修改的部分。
- 为网站的每个区域创建 `base_SECTION.html` 模板(例如, `base_photos.html` 和 `base_forum.html` )。这些模板对 `base.html` 进行拓展，并包含区域特定的风格与设计。
- 为每种类型的页面创建独立的模板，例如论坛页面或者图片库。 这些模板拓展相应的区域模板。

- **以下是使用模板继承的一些诀窍：**

  - 如果在模板中使用 `{% extends %}` ，必须保证其为模板中的**第一个模板标记**。否则，模板继承将不起作用。

  - 一般来说**，基础模板中的 `{% block %}` 标签越多越好**。记住，子模板不必定义父模板中所有的代码块，因此你可以用合理的缺省值对一些代码块进行填充，然后只对子模板所需的代码块进行（重）定义。俗话说，钩子越多越好。

  - 如果发觉自己在多个模板之间拷贝代码，你应该考虑将该代码段放置到父模板的某个 `{% block %}` 中。

  - 如果你需要访问父模板中的块的内容，使用 `{{ block.super }}`这个标签吧，这一个魔法变量将会表现出父模板中的内容。如果只想在上级代码块基础上添加内容，而不是全部重载，该变量就显得非常有用了。

  - **不允许在同一个模板中定义多个同名的 `{% block %}`** 。 存在这样的限制是因为`block` 标签的工作方式是双向的。也就是说，`block`标签不仅挖了一个要填的坑，也定义了在父模板中这个坑所填充的内容。如果模板中出现了两个相同名称的 `{% block %}` 标签，父模板将无从得知要使用哪个块的内容。

  - **`{% extends %}` 对所传入模板名称使用的加载方法和 `get_template()` 相同。也就是说，会将模板名称被添加到 TEMPLATE_DIRS 设置之后。**

  - 多数情况下，`{% extends %}` 的参数应该是字符串，但是如果直到运行时方能确定父模板名，这个参数也可以是个变量。 这使得你能够实现一些很酷的动态功能。

    ```python
    #-----基础模板
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN">
    <html lang="en">
    <head>
        <title>{% block title %}{% endblock %}</title> #标签为title,继承后同样标签的中间就显示自定义内容
    </head>
    <body>
        <h1>My helpful timestamp site</h1>
        {% block content %}{% endblock %} #标签为contex
        {% block footer %}
        <hr>
        <p>Thanks for visiting my site.</p>
        {% endblock %}
    </body>
    </html>
    
    # ---进行继承
    {% extends "base.html" %} #继承该模板
    
    {% block title %}计算未来的时间{% endblock %} #实际是在base模板的标签处进行添加内容
    
    {% block content %}
    <p>当前时间：{{ now}},延时后：{{ time }},延时了{{ offset}}.</p> #content标签处添加内容
    {% endblock %}
    ```

    