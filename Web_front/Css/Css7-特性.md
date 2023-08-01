# 一、继承性介绍

- 常见可继承属性
  - 字体属性：font、font-family、font-size、font-weight、font-style等
  - 文本属性：color、text-align、line-height、white-space等。

- 需要注意的是，a和h标签本身是有属性的因此不会继承。**也就是说一个标签如果本身是有属性的，那么就不会继承父标签的属性**
- **如果父元素没有设置font-size属性，那么h标签会使用浏览器的默认字体大小。所以，如果你想改变h标签的字体大小，可以通过设置父元素的font-size属性来实现**
- [link](https://blog.csdn.net/Huyingxue107/article/details/126068943?ops_request_misc=&request_id=&biz_id=102&utm_term=h%20%E6%A0%87%E7%AD%BE%E7%BB%A7%E6%89%BF%E4%BA%86%E7%88%B6%E6%A0%87%E7%AD%BE%E7%9A%84font-size&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-126068943.142^v91^insertT0,239^v3^insert_chatgpt&spm=1018.2226.3001.4187)

![image-20221231230805947](../../../../Library/Application Support/typora-user-images/image-20221231230805947.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    div{
      color: green;
      font-size:33px;
    }
  </style>
</head>
<body>
  <div>
    div标签之中的内容
    <!-- spand会继承div的属性 -->
    <span>span 之中的内容</span> 

    <!-- 超链接本身就是蓝色的因此不会继承 -->
    <a href="xx">超链接</a>
    <!-- h标签本身大小也有规定，因此也不会继承 -->
    <h1>h1标签</h1>
  </div>

</body>
</html>
```

- 不可继承的点
  - CSS中的宽度和高度默认是不可以继承的

# 二、层叠性

![image-20230101152614689](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301011526786.png)

# 三、优先级

## 3.1 基本介绍

![image-20230101154604258](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301011546335.png)

- !import 可以将优先级提高到最高

  ![image-20230101155301564](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301011553648.png)

- 不要给继承添加`!import`,自己本身有样式无法继承父级样式

## 3.2 权重叠加计算

- [计算练习](https://www.bilibili.com/video/BV1Kg411T7t9?p=89&spm_id_from=pageDriver&vd_source=d0e4be6fd02fea51faad92fec15cda81)

![image-20230101155506233](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301011555292.png)

![image-20230101160042044](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301011600121.png)