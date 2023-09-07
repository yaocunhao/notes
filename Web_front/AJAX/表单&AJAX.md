# 一、是什么

- HTML 表单用于收集用户的输入信息
- HTML 表单表示文档中的一个区域，此区域包含交互控件，将用户收集到的信息发送到 Web 服务器

# 二、属性测试

- [表单属性参考链接](https://blog.csdn.net/weixin_64122448/article/details/124068002?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169276894416800182174873%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169276894416800182174873&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-124068002-null-null.142^v93^chatsearchT3_2&utm_term=%E8%A1%A8%E5%8D%95&spm=1018.2226.3001.4187)

  - action 提交地址
  - method 提交方法
  - target 在哪里打开(没有测试出来效果)
  - anctype 提交的数据格式

- 填充好表单后，点击提交按钮，数据就会被提交给服务器后端。 然后服务器后端去解析http协议即可

  ```html
  //  前端代码
  <body>
    <div>
      <form action="http://xxxx:6001/test35" ,method="GET" target="_self" enctype="application/json"> <!--表单域-->
        region:<input type="text" id="region_name" name="region" placeholder="shanghai_anting">
        <br>
        conver_type: <input type="radio" id="conver_type" name="flag">gcj02->o_utm <input type="radio"
          name="flag">o_utm->gcj02
        <br>
        <button>转换</button>
        <br>
        <input type="reset">
      </form>
    </div>
    
    
  //—————————————————————— 后端代码——————————————————————————
  def test35(request):
    """测试表单数据"""
    # 打印数据： <QueryDict: {'region': ['beijing_yizhuang'], 'flag': ['on']}>
    print(request.GET)
    return HttpResponse('Success!!')
  ```

# 三、什么时候用表单、和AJAX 的区别

- [link](https://blog.csdn.net/baidu_28149499/article/details/89418115?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169278153416800215029033%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169278153416800215029033&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-89418115-null-null.142^v93^chatsearchT3_2&utm_term=%E8%A1%A8%E5%8D%95%E5%92%8Cajax%E7%9A%84%E5%8C%BA%E5%88%AB&spm=1018.2226.3001.4187)

- 前后端交互有两种方式
  - 表单提交， 但是会刷新整个页面

  - Ajax 提交，可以进行动态刷新

- 表单提交获取的数据会刷新当前页面或者打开一个新的页面
- from和ajax最大的区别就是：form需要刷新页面，但ajxa在不刷新页面的情况下可以执行提交数据请求，如果需要在ajax提交数据成功后刷新页面，可以调用window对象的Location属性的load()方法重新加载当前文档

# 四、AJAX的一些使用方法

- [link](https://blog.csdn.net/weixin_43585850/article/details/120234402?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169278482316800180647987%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169278482316800180647987&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-6-120234402-null-null.142^v93^chatsearchT3_2&utm_term=AJAX&spm=1018.2226.3001.4187)
- responseText：作为响应体返回的文本。
- responseXML：如果响应的内容类型是"text/xml"或"application/xml"，那就是包含响应数据的 XML DOM 文档

# 五、AJAX  无法接收参数

- 由于跨域了。 如果想要获取到数据，在django之中需要设置STATICFILES_DIRS

