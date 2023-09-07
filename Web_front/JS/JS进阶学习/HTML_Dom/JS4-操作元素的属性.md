# 一、获取元素的属性

<img src="https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301072339162.png"/>

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="demo" index="1"></div>

  <script>
    var div = document.querySelector('div');
    console.log(div.id)
    // element.getAttribute('属性')
    // index: 自定义属性
    console.log(div.getAttribute('id'));
    console.log(div.getAttribute('index'));

  </script>
</body>
</html>
```



# 二、设置属性值

![image-20230107234143653](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301072341711.png)

![image-20230107234131126](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301072341182.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .navs {
      height: 120px;
      width: 120px;
      background-color: green;
    }

    .footer {
      position: absolute;
      left: 50%;
      top: 50%;
      /* height: 120px;
      width: 120px; */
      background-color: yellow;
    }
  </style>
</head>

<body>
  <div id="demo" index="1"></div>

  <script>
    var div = document.querySelector('div');
    // 1、获取属性
    console.log(div.id)
    // element.getAttribute('属性')
    // index: 自定义属性
    console.log(div.getAttribute('id'));
    console.log(div.getAttribute('index'));

    // 2、设置属性
    div.id = 'test'
    // 设置类选择器
    div.className = 'navs'
    // 设置自定义属性
    div.setAttribute('index', 111)
    // 设置类
    div.setAttribute('class', 'navs footer')

    // 3、移除属性
    div.removeAttribute('class')

    // 类名属性列表
    console.log(div.classList[0])
    // 标签名
    console.log(div.tagName)


  </script>
</body>

</html>
```



# 三、移除属性值

![image-20230107234211003](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301072342080.png)