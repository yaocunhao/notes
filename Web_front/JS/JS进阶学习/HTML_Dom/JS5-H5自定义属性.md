#  一、H5自定义属性

![image-20230107234921577](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301072349639.png)

#  二、获取H5自定义属性

![image-20230107235052207](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301072350256.png)

![image-20230107235103771](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301072351838.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>

  <div getTime="20" data-index="2" data-list-name="andy"> </div>
  <script>
    var div = document.querySelector('div')
    console.log(div.getAttribute('getTime'))
    console.log(div.getAttribute('data-index'))
    console.log(div.getAttribute('data-list-name'))

    // h5 新增，一个集合中放了所有以data开头的自定义属性

    console.log(div.dataset) // DOMStringMap {index: '2', listName: 'andy'}
    console.log(div.dataset.index) // 2
    console.log(div.dataset['index']) // 2

    console.log(div.dataset.listName) // andy
    console.log(div.dataset['listName']) // andy

  </script>

</body>

</html>
```

 