# 一、背景颜色

![image-20220924163128549](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241631660.png)

# 二、背景图片

![image-20220924163501091](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241635211.png)

```html
 <style>
    div {
      background-image: url('test.png');
      background-color: blue;
      width: 200px;
      height: 300px;
    }
  </style>
</head>

<body>
  <div>
  </div>

</body>

</html>
```

- background-size:	

  - background-size 是一个 CSS 属性，用于设置背景图片的尺寸大小
  - background-size 的取值可以是具体的像素值、百分比值，也可以是以下几个关键字之一：

  - auto: 保持原始图片的尺寸比例，自动适应容器大小。
  - cover: 等比缩放图片，使其完全覆盖背景区域，可能会裁剪部分图片。
  - contain: 等比缩放图片，使其完全适应背景区域，可能会在背景区域内留白。
  - initial: 使用默认值。
  - inherit: 继承父元素的属性值

# 三、背景图平铺

- 可能图片小于盒子的大小，导致在盒子之中出现多份图片。因此设置是否平铺和平铺的方式即可(**平铺：填满的方式**)![image-20220924164026348](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241640419.png)

# 四、背景图的位置

- 默认图片显示在盒子的左上角，可以通过`bgp`设置图片的位置

![image-20220924164323268](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241643375.png)

```html
  <style>
    div {
      background-image: url('test.png');
      width: 200px;
      height: 300px;
      background-repeat: no-repeat;
      background-position: 500 500;
    }
  </style>
```



# 五、背景相关属性连写

- 连没有顺序要求。英文单词没有顺序要求，但是数字需要按顺序来写![image-20220924164636207](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241646281.png)

# 六、扩展

- 背景图依赖于标签盒子展示，因此需要设置一些属性
- img插入图片，用来实现比较重要的图片。不重要(装饰性的图片)的就用背景图

![image-20220924164937834](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241649957.png)