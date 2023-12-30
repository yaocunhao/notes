# 一、清除浮动介绍
- 如果子元素浮动了，此时子元素不能撑开块级父元素
- 原因是子元素浮动后脱标，不占位置

![image-20230101235936291](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012359341.png)

# 二、清除浮动的方法

- 直接设置父元素的高度。比如新闻内容(今天内容10条，明天15条，变动比较大，因此不能加高度)![image-20230102000501576](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301020005634.png)

- 额外标签法 

  ![image-20230102000731117](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301020007195.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .top{
      width: 1000px;
      /*height: 100px;*/
      background-color: green;
    }
    .left {
      float: left;
      width: 200px;
      height: 200px;
      background-color: black;
    }
    .right {
      float: right;
      width: 200px;
      height: 200px;
      background-color: blue;
    }
    .bottom {
      width: 1000px;
      height: 100px;
      background-color: yellow;
    }
    .clearfix {
      /*清除左右两侧浮动的影响*/
      clear:both;
    }
  </style>
</head>
<body>
  <!-- 父子级标签，子级浮动，父级没有高度，后面的标准流盒子会受影响 -->
  <div class="top">
    <div class="left"></div>
    <div class="right"></div>
    
    <!-- 清除浮动 -->
    <div class="clearfix"></div>
  </div>
  <div class="bottom"></div>
</body>
</html>
```

- 单伪元素清除法

![image-20230102002011243](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301020020340.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .top{
      width: 1000px;
      /*height: 100px;*/
      background-color: green;
    }
    .left {
      float: left;
      width: 200px;
      height: 200px;
      background-color: black;
    }
    .right {
      float: right;
      width: 200px;
      height: 200px;
      background-color: blue;
    }
    .bottom {
      width: 1000px;
      height: 100px;
      background-color: yellow;
    }
    .clearfix::after {
      content: '';
      display: block;
      clear: both;

      /*为了兼容性*/
      height: 0;
      visibility: hidden;
    }

  </style>
</head>
<body>
  <!-- 父子级标签，子级浮动，父级没有高度，后面的标准流盒子会受影响 -->
  <div class="top clearfix">
    <div class="left"></div>
    <div class="right"></div>
  </div>
  <div class="bottom"></div>
</body>
</html>
```

- 双伪元素清除法

  ![image-20230102002509316](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301020025403.png)

![image-20230102002808750](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301020028819.png)

- 给父元素设置overflow:hidden

  ![image-20230102002834236](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301020028310.png)