# 一、从Request对象中获取数据

## 1.1 URL相关信息

- `HttpRequest`对象包含当前请求URL的一些信息：

- | 属性/方法                 | 说明                                                  | 举例                                                         |
  | :------------------------ | :---------------------------------------------------- | :----------------------------------------------------------- |
  | `request.path`            | 除域名以外的请求路径，以正斜杠开头                    | `"/hello/"`                                                  |
  | `request.get_host()`      | 主机名（比如，通常所说的域名）                        | `"127.0.0.1:8000"` or "[www.example.com](http://example.com/)" |
  | `request.get_full_path()` | 请求路径，可能包含查询字符串                          | `"/hello/?print=true"`                                       |
  | `request.is_secure()`     | 如果通过HTTPS访问，则此方法返回`True`， 否则返回False | `True` 或者 `False`                                          |

- 在view函数里，**要始终用这个属性或方法来得到URL，而不要手动输入**。 这会使得代码更加灵活，以便在其它地方重用。 下面是一个简单的例子：

  ```python
  # BAD!
  def current_url_view_bad(request):
      return HttpResponse("Welcome to the page at /current/") # 不要手动输入
  
  # GOOD
  def current_url_view_good(request):
      return HttpResponse("Welcome to the page at %s" % request.path) # 调用获取路径
  ```

## 1.2 **request**其它信息

- `request.META`是一个Python字典，包含了所有本次HTTP请求的Header信息，比如用户IP地址和用户Agent（通常是浏览器的名称和版本号）。 注意，Header信息的完整列表取决于用户所发送的Header信息和服务器端设置的Header信息。 这个字典中几个常见的键值有：

  - `HTTP_REFERER`，进站前链接网页，如果有的话。（请注意，它是`REFERRER`的笔误。）

  - `HTTP_USER_AGENT`，用户浏览器的`user-agent`字符串，如果有的话。 例如：`"Mozilla/5.0 (X11; U; Linux i686; fr-FR; rv:1.8.1.17) Gecko/20080829 Firefox/2.0.0.17"`。

  - `REMOTE_ADDR` 客户端IP，如：`"12.345.67.89"` 。(如果申请是经过代理服务器的话，那么它可能是以逗号分割的多个IP地址，如：`"12.345.67.89,23.456.78.90"` 。)

  - 注意，因为 request.META 是一个普通的Python字典，因此当你试图访问一个不存在的键时，会触发一个KeyError异常

  - HTTP header信息是由用户的浏览器所提交的、不应该给予信任的“额外”数据，因此你总是应该好好设计你的应用以便当一个特定的Header数据不存在时，给出一个优雅的回应。）你应该用`try/except` 语句，或者用Python字典的`get()`方法来处理这些“可能不存在的键”

    ```python
    # BAD!
    def ua_display_bad(request):
        ua = request.META['HTTP_USER_AGENT']  # Might raise KeyError!
        return HttpResponse("Your browser is %s" % ua)
    
    # 不可信任的数据，因此需要做特殊的处理
    
    
    # GOOD (VERSION 1) -> 捕获异常
    def ua_display_good1(request):
        try:
            ua = request.META['HTTP_USER_AGENT']
        except KeyError:
            ua = 'unknown'
        return HttpResponse("Your browser is %s" % ua)
    
    # GOOD (VERSION 2)
    
    def ua_display_good2(request): # ->如果没有查询到 ua显示的就是 get后面的参数
        ua = request.META.get('HTTP_USER_AGENT', 'unknown')
        return HttpResponse("Your browser is %s" % ua)
    ```

  - request.META中的键名都是大写的，而request.headers中的键名是原样的。比如http传递的是traceId, 在META之中会变成`HTTP_TRACEID`

    ```
    因此，如果要获取HTTP请求头信息，可以使用以下两种方式：
    1. 使用request.META.get()方法，例如：auth_token = request.META.get(‘HTTP_AUTH_TOKEN’)
    2. 使用request.headers.get()方法，例如：timestamp = request.headers.get(‘timestamp’)
    ```

    

- 手动编写view查看字典之中有什么

  ```python
  # 查看字典之中有一些什么
  def show_dict(request):
      values = request.META.items()  # items返回所有的字典对
      html = []  # 列表
      for k, v in values:  # 循环往列表里面添加kv值
          html.append('<tr><td>%s</td><td>%s</td></tr>' % (k, v))
      
      return HttpResponse('<table>%s</table>' % '\n'.join(html))
  ```

- 提交的数据信息
  - 除了基本的元数据，HttpRequest对象还有两个属性包含了用户所提交的信息：`request.GET`和`request.POST`。二者都是类字典对象，你可以通过它们来访问GET和POST数据
    - POST数据是来自HTML中的`〈form〉`标签提交的，而GET数据可能来自`〈form〉`提交也可能是URL中的查询字符串(`the query string`)

# 二、一个简单的表单处理实示例

- 通常，表单开发分为两个部分： 前端HTML页面用户接口和后台`view`函数对所提交数据的处理过程。第一部分很简单；现在我们来建立个`view`来显示一个搜索表单：![image-20220312140412072](../../Library/Application Support/typora-user-images/image-20220312140412072.png)