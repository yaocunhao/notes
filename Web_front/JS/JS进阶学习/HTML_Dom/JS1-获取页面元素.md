# 一、根据ID获取元素

![image-20230103002137867](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030021925.png)

![image-20230103002335258](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030023302.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="time">2019-9-9</div>
  <script>
    var timer = document.getElementById("time")
    console.log(timer)
    console.log(typeof timer)
    console.dir(timer)
  </script>
  
</body>
</html>
```



# 二、根据标签名获取

## 2.1 根据标签名获取

![image-20230103003800285](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030038338.png)

![image-20230103003911978](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030039045.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <ul>
    <li>123,213</li>
    <li>123,213</li>
    <li>123,213</li>
    <li>123,213</li>
    <li>123,213</li>
    <li>123,213</li>
  </ul>
  <script>
    var lis = document.getElementsByTagName('li')
    console.log(typeof lis)
    console.log(lis)
    console.log(lis[0])
    for (var i = 0; i < lis.length; i++) {
      console.log(lis[i])
    }

  </script>

</body>

</html>
```



## 2.2 获取某个父元素内部的标签

![image-20230103003956249](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030039326.png)

![image-20230103004031717](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030040807.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <ol>
    <li>123,213</li>
    <li>123,213</li>
    <li>123,213</li>
    <li>123,213</li>
    <li>123,213</li>
    <li>123,213</li>
  </ol>
  <script>
    var lis = document.getElementsByTagName('ol')
    console.log(lis[0].getElementsByTagName('li'))

  </script>

</body>

</html>
```



## 2.3 通过HTML5新增方法获取

![image-20230103004235741](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030042403.png)

![image-20230103004326533](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030043589.png)

- [querySelectorAll 和 getElementsByClassName的区别](https://blog.csdn.net/qq_44885374/article/details/91807435?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-91807435-blog-91808440.235%5Ev38%5Epc_relevant_default_base3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-91807435-blog-91808440.235%5Ev38%5Epc_relevant_default_base3&utm_relevant_index=2)

  ```html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TEST Document</title>
  
    <style>
      .box {
        background-color: yellow;
        height: 100px;
        width: 100px;
      }
    </style>
  
  </head>
  
  <body>
    <div class="box"></div>
    <ol>
      <li>123,213</li>
      <li>123,213</li>
      <li>123,213</li>
      <li>123,213</li>
      <li>123,213</li>
      <li>123,213</li>
    </ol>
    <script>
      // 获取元素的集合
      var boxs = document.getElementsByClassName('box')
      console.log(boxs)
      // 获取第一个元素
      var first_box = document.querySelector('.box')
      console.log(first_box)
      // 获取指定选择的所有元素对象的集合， 这里可以使用foreach
      var all_box = document.querySelectorAll('.box')
      console.log(all_box)
      all_box.forEach(element => {
        console.log(element)
      });
      
      // 返回body元素对象
      console.log(document.body)
      // 返回html 元素对象
      console.log(document.documentElement)
  
    </script>
  
  </body>
  
  </html>
  ```

  

## 2.4 获取页面中特殊元素

![image-20230103004424262](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030044338.png)