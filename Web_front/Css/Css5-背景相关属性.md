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