# 一、结构伪类选择器

![image-20230101223654896](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012236948.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    /*选中第一个*/
    li:first-child {
      background-color: red;
    }
    /*选中第3个*/
    li:nth-child(3) {
      background-color: blue;
    }
  </style>
</head>
<body>
  <ul>
    <li>这是第1个标签</li>
    <li>这是第2个标签</li>
    <li>这是第3个标签</li>
    <li>这是第4个标签</li>
    <li>这是第5个标签</li>
    <li>这是第6个标签</li>
  </ul>
</body>
</html>
```

