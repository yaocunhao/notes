# 一、浮动的作用

![image-20230101225419442](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012254496.png)

![image-20230101225255326](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012252373.png)

- 通过浮动标签来消除空格间隙

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
      div {
        /*游览器如果解析行内块或者行标签的时候，如果标签换行会产生一个空格的间隙*/
        display: inline-block;
        width: 100px;
        height: 100px;
      }
      .one {
        background-color: pink;
        float: left; /*消除空格带来的间隙*/
      }
      .two {
        background-color: red;
        float: left;
      }
    </style>
  </head>
  <body>
    <div class="one">one</div>
    <div class="two">two</div>
  </body>
  </html>
  ```

# 二、浮动的特点

- 浮动元素会脱离标准流，在标准流中不占位置
- 浮点元素比标准流高半个级别，可以覆盖标准流中的元素
- 浮动找浮动，下一个元素的浮动会在下一个浮动元素后面排列
- 浮动标签是顶对齐的，加margion-top往下挪
- 浮动元素有特殊的显示效果
  - 一行可以显示多个
  - 可以设置宽高
- 浮点的元素不能通过text-align:center 或者margin:0 auto 居中 ![image-20230101230513450](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012305511.png)

# 三、Css书写顺序

- 这个顺序是给人看的，并且游览器的执行效率能够更高
- 浮点/display
- 盒子模型：margin border padding 宽度高度背景色
- 文字样式