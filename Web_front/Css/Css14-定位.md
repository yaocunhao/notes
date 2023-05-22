# 一、 定位的作用

- 可以让元素自由的摆放在网页的任意位置，**一般用于盒子之间的层叠情况**

![image-20230102164530829](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301021645897.png)

# 二、定位的常见应用场景

![image-20230102164728516](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301021647581.png)

# 三、定位的使用步骤

- 静态定位`static`就是没有改变( 相当于没有用)

![image-20230102164839588](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301021648665.png)

# 三、相对定位

- 相对于原来没有加标签的位置

![image-20230102164958303](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301021649383.png)

​	

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<style>
  /*css书写：1.定位 /浮动/ display; 2.盒子模型；3.文字属性*/
  .box {
    position: relative;
    left: 100px;
    top: 200px;
    /*
      相对定位特点：
      1、占有原来的位置
      2、仍然具体标签原有的显示模式特点
      3、改变位置参照自己原来的位置
    */
    width: 200px;
    height: 200px;
    background-color: pink;
  }
</style>
<body>
  <p>测试定位测试定位测试定位测试定位</p>
  <p>测试定位测试定位测试定位测试定位</p>
  <p>测试定位测试定位测试定位测试定位</p>
  <div class="box">box</div>
  <p>测试定位测试定位测试定位测试定位</p>
  <p>测试定位测试定位测试定位测试定位</p>
  <p>测试定位测试定位测试定位测试定位</p>
  <p>测试定位测试定位测试定位测试定位</p>
</body>
</html>
```

 

# 四、定位注意事项

- 如果只有`position`没有设置偏移值，是不会生效的
- 如果四个方向都设置有偏移值，以left和top为准

# 五、绝对定位

![image-20230102170230109](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301021702202.png)

## 5.1 以游览器为参考

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<style>
  /*css书写：1.定位 /浮动/ display; 2.盒子模型；3.文字属性*/
  .box {
    /* 绝对定位：
        先找已经定位的父级，如果有这样的父级，就以这个父级为参照物进行定位
        有父级，但是父级没有定位，以游览器窗口为参照物进行定位
     */
    position: absolute;
    /*
      特点：
      1、脱标：不占位置
      2、改变标签的显示模式特点：具备了行内块的特点(在一行共存，宽高生效) 
     */

    width: 200px;
    height: 200px;
    background-color: pink;
  }
</style>

<body>
  <p>测试定位测试定位测试定位测试定位</p>
  <p>测试定位测试定位测试定位测试定位</p>
  <p>测试定位测试定位测试定位测试定位</p>
  <div class="box">box</div>
  <p>测试定位测试定位测试定位测试定位</p>
  <p>测试定位测试定位测试定位测试定位</p>
  <p>测试定位测试定位测试定位测试定位</p>
  <p>测试定位测试定位测试定位测试定位</p>
</body>

</html>
```

## 5.2 以父级为标准

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
      width: 400px;
      height: 400px;
      background-color: pink;
    }
    .son {
      /* 静态定位：就是不定位 */
      /* 
      一般父级采用相对定位模式，子级采用绝对定位 --子绝父相
      因为父级相对定位也是按照子级的原来位置进行变化，这样在添加偏移值时就是根据自己原来的位置变化
       */
      position: relative; /*进行定位，sun就会根据这个父级改变定位 */
      right: 100px;
      width: 300px;
      height: 300px;
      background-color:skyblue;
    }
    .sun {
      position:absolute;
      /* left: 20px;
      top: 30px; */
      right: 20px;
      bottom: 50px;

      width: 200px;
      height: 200px;
      background-color: green;
    }
    /* 绝对定位查找父级的方式：就近找到定位的父级，如果逐层查找不到这样的父级，就以游览器窗口进行定位 */
  </style>
</head>
<body>
  <div class="father">
    <div class="son">
      <div class="sun">

      </div>
    </div>
  </div>
</body>
</html>
```

# 六、定位居中

- 如果有定位， 不能使用`margin o auto`居中

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
        width: 500px;
        height: 500px;
        background-color: blue;
      }
  
      .son {
        position: absolute;
        /* 绝对定位的盒子，不能使用左右margin auto居中 */
        /* margin: 0 auto; */
        
        /* 水平居中的方式 */
        /* left：50%,整个盒子移动到游览器中间偏右 */
        left: 50%;
        /* 把盒子往左挪动，盒子宽度的一半 */
        margin-left: -100px;
  
        /* 垂直居中 */
        top: 50%;
        margin-top: -100px;
  
        width: 200px;
        height: 200px;
        background-color: red;
      }
  
    </style>
  </head>
  <body>
    <div class="son"></div>  
  </body>
  </html>
  ```

# 六、定位-位移居中

- 自动联动宽高计算，不需要手动进行计算

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
      width: 500px;
      height: 500px;
      background-color: blue;
    }

    .son {
      position: absolute;
      /* 绝对定位的盒子，不能使用左右margin auto居中 */
      /* margin: 0 auto; */
      
      /* 水平居中的方式 */
      /* left：50%,整个盒子移动到游览器中间偏右 */
      left: 50%;
      /* 把盒子往左挪动，盒子宽度的一半 */
      /* margin-left: -100px; */

      /* 垂直居中 */
      top: 50%;
      /* margin-top: -100px; */

      /* 位移：自己宽度高度的一般，不需要自己手动计算 */
      transform: translate(-50%,-50%);


      width: 200px;
      height: 200px;
      background-color: red;
    }

  </style>
</head>
<body>
  <div class="son"></div>  
</body>
</html>
```

# 七、固定定位

- 网页随便动，但是某个区域是固定在网页之中某个位置的

![image-20230102173037148](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301021739526.png)

# 八、元素的层级关系

![image-20230102173324696](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301021739621.png)

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
      width: 400px;
      height: 400px;
    }
    .one {
      z-index: 10;
      position: absolute;
      left: 20px;
      top: 50px;

      background-color: pink;
    }
    .two {
      left: 50px;
      top: 100px;
      position: absolute;
      background-color: green;

    }
    /* 
    默认情况下，定位的盒子， 后来者居上 
    z-index：整数，取值越大，显示顺序越靠上,默认值时0,必须有定位属性才生效
    */
  </style>
</head>
<body>
  <div class="one">one</div>
  <div class="two">two</div>
</body>
</html>
```

