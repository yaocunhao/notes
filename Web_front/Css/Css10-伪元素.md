# 一、伪元素

![image-20230101223816800](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012238841.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .father {
      width: 300px;
      height: 200px;
      background-color: red;
    }
    .father::before{
      /*内容*/
      content: '老鼠';
      color: green;
      width: 100px;
      height: 100px;
      background-color: blue;
      /*默认行内元素宽高不生效*/
      display: block;
    }
    .father::after{
      content: '大米';
    }
  </style>
</head>
<body>
  <!-- 伪元素 通过css创建标签，装饰性的不重要的小图 -->
  <!--  找父级，在这个父级里面创建子级标签 -->
  <div class="father">
    爱
  </div>
</body>
</html>
```

