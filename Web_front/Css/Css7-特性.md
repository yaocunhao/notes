# 一、继承性介绍

- 需要注意的是，a和h标签本身是有属性的因此不会继承。**也就是说一个标签如果本身是有属性的，那么就不会继承父标签的属性**

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