# 一、盒子模型的介绍

- 可以认为一盒子就是一个标签。**它的作用就是用来布局的**

![image-20230101193647033](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301011936101.png)

# 二、盒子模型的组成

## 2.1 内容区域的宽度和高度

![image-20230101194801618](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301011948667.png)

## 2.2 边框(border)

### 2.2.1 边框的属性

- 是一个复合属性：像素(px)、线种类(实线/虚线)、颜色

![image-20230101195504720](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301011955779.png)

### 2.2.2 边框单方向设置

![image-20230101200122052](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012001126.png)

### 2.2.3 边框的单个属性(了解)

- 一般不这么写，都是直接连写。并且可以通过方向来进行连写

![image-20230101200449695](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012004765.png)

### 2.2.4 代码展示

```css
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    div {
      width: 200px;
      height: 200px;
      background-color:peachpuff;
      /* boreder: 粗细 线条样式 颜色 -- 部分先后顺序*/
      /*dashed：虚线*/  /*solid：实线*/ /*点线*/
      /*border: 20px solid #000;*/

      /*设置方向*/
      border-left: 10px solid yellow;
      border-right: 10px solid yellow;
    }
  </style>
</head>
<body>
  
  <div>
    测试盒子属性
  </div>

</body>
</html>
```

![image-20230101202416096](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012024170.png)

## 2.3 内边距(padding)

- 设置边框与内容之间的距离

```html
  <style>
    div {
      width:200px;
      height:200px;
      background-color:peachpuff;
      /* boreder: 粗细 线条样式 颜色 -- 部分先后顺序*/
      /*dashed：虚线*/  /*solid：实线*/ /*点线*/
      /*border: 20px solid #000;*/

      /*设置方向*/
      border-left: 10px solid yellow;
      border-right: 10px solid yellow;
      
      /*设置内边距*/
      /*添加了4个方向的内边距*/
      /*padding: 20px;*/

      /*padding 属性可以当做符合属性使用，表示单独设置某个方向的内边距，也就是最多取4个值*/
      /*四值: 表示上右下左*/
     
      
     /*三值： 上 左右 下*/
     /*两值： 上下 左右*/
     /*多值写法，永远都是顺时针转一圈，如果数不够就看对面*/
    }
  </style>
```

## 2.4 CSS3盒模型(自动内减)

- 当给盒子设置border或者padding属性时，盒子会被撑大，需要人为的去做减法。可以通过给盒子设置属性，来自动内减![image-20230101203936129](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012039211.png)	

```css
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
<style>
  div {
    width: 100px;
    height: 100px;
    border: 1px solid;
    padding: 10px;
    background-color: blue;

    /*自动内减*/
    box-sizing: border-box;
    
  }
</style>

</head>
<body>
  <div>
    自动内减
  </div>
</body>
</html>
```

## 2.5 外边距(margin)

- 控制盒子与盒子之间的距离

### 2.5.1 设置方式

- 外边距的设置方式和内边距是一样的，只是位置不同

### 2.5.2  会出现的问题

- 合并现象

![image-20230101210510189](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012105270.png)

- 塌陷现象

  ![image-20230101210616178](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012106235.png)

```css
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .father {
      width: 100px;
      height: 200px;
      background-color: yellowgreen;
      /*如果设计稿没有border则不能使用这种方式*/
      /*border: 1px solid;*/

      /*overflow: hidden;*/
      
      /*转成行内块*/
      display:inline-block;
    }

    .son {
      height: 30px;
      width: 30px;
      background-color: red;
      margin-top: 80px;
    }
  </style>
</head>

<body>
  <div class="father">
    <div class="son">son</div>

  </div>

</body>

</html>
```



## 2.6 清除默认的内外边距

![image-20230101204839626](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012048720.png)

## 2.7 使用展示

![image-20230101194235014](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301011942055.png)

![image-20230101194439281](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301011944325.png)

## 2.8 行内元素的垂直内外边距

- margin和padding无法改变行内标签的垂直位置
- 要想改变行内标签的垂直位置使用行高：`line-height`![image-20230101221839094](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301012218181.png)