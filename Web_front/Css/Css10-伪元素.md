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

- 受flex 影响

  ```css
  
  flex 对伪元素的影响是有限的。伪元素是通过 CSS 伪类（如 ::before 和 ::after）创建的虚拟元素，它们可以用于在元素的内容之前或之后插入额外的内容。
  
  当使用 flex 布局时，伪元素也会参与到布局中。默认情况下，伪元素会被视为普通的行内元素，并且不会受到 flex 布局的影响。这意味着伪元素不会受到 flex 容器的主轴或交叉轴的对齐方式和排列顺序的影响。
  
  然而，可以通过一些技巧来影响伪元素的布局。例如，可以将伪元素设置为具有 flex 属性，使其成为 flex 项。这样，伪元素就可以参与到 flex 容器的布局中，并且受到主轴和交叉轴的对齐方式和排列顺序的影响。
  
  需要注意的是，不同浏览器对于伪元素是否支持 flex 属性可能会有差异。因此，在使用 flex 布局时，应该测试不同浏览器中伪元素的表现，并根据需要进行兼容性处理。
  ```

  
