- [参考链接](https://blog.csdn.net/qq_41809113/article/details/121869338?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168559785816782427418711%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=168559785816782427418711&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-121869338-null-null.142^v88^control_2,239^v2^insert_chatgpt&utm_term=flex%E3%80%81&spm=1018.2226.3001.4187)
- [为什么使用flex](https://juejin.cn/post/7063823914136256543)
  - 如果采用传统的定位方式，很多地方使用不是很方便，比如进行居中对齐时就很不方便，因此w3c推出了flex布局。当采用flex布局时，浮动相关会失效
  - flex-direction 属性决定主轴的方向（也就是排列方向）。有4个属性值可以设置。
    - row（默认值）：主轴为水平方向，起点在左端。
    - row-reverse：主轴为水平方向，起点在右端。
    - column：主轴为垂直方向，起点在上沿。
    - column-reverse：主轴为垂直方向，起点在下沿。
  - 

- [flex 常见使用方法](https://juejin.cn/post/7010701578638196750)
  - justify-content属性定义了项目在主轴上的对齐方式,
  - align-items属性定义项目在交叉轴上如何对齐
  - 这里要注意的就是`假设flex-direction改变了主轴的方向,justify-content操作的是主轴方向上的项目而不一定是水平！！！`

- [菜鸟教程](https://www.runoob.com/w3cnote/flex-grammar.html)



# 二、杂记

- flex-grow 达到一定程度后失效

  - 当设置了`flex-grow`的值后，元素会根据可用空间进行伸展。**但是要注意，如果设置的`flex-grow`的总和超过了父容器的可用空间，那么这些设置可能会变得无效。**

    举个例子，如果父容器的宽度是500px，其中有3个子元素，每个子元素的`flex-grow`都设置为1。那么每个子元素将会平均占据父容器的宽度，即每个子元素占据500/3≈166.67px。但是如果你将其中一个子元素的`flex-grow`设置为2，那么它将会占据剩余空间的两倍，即占据约333.33px。

    然而，如果你将所有子元素的`flex-grow`设置为1，而父容器的宽度仍然是500px，那么每个子元素将会平均占据父容器的宽度，即每个子元素占据500/3≈166.67px。

- flext 是可以不设宽高的，因为它表示的就是动态布局，占父元素的比例

  ```css
  // 这里表示的是： 父元素高度占满屏幕、宽度占满屏幕的90%， 子元素占父元素的一半
  
    <style>
      /* 类选择器 */
      .container {
        display: flex;
        height: 100vh;
        width: 90vw;
        background: green;
      }
  
      /* 类选择器 */
      .panel {
        background-color: red;
        flex: 0.5;
      }
    </style>
  
  
  
  // 这里表示的是：父元素高度占满屏幕、宽度占满屏幕的90%
  // 子元素高度占20， 宽度为父元素一半
  
    <style>
      /* 类选择器 */
      .container {
        display: flex;
        height: 100vh;
        width: 90vw;
        background: green;
      }
  
      /* 类选择器 */
      .panel {
        /* bg 设置背景相关 */
        background-color: red;
        height:20vh;
        flex: 0.5;
      }
    </style>
  ```

  

# 练习

```html
<div class="father">
  <div class="son1">你好</div>
</div>
<style>
  /* justify-content定义了主轴方向的对其方式,在没有定义flex-direction的时候,
  默认的主轴就是水平,起点就是最左边 */
  .father {
    width: 200px;
    height: 200px;
    display: flex;
    background-color: burlywood;
    /* 定义属性在主轴上是如何定义的 */
    justify-content: center;
    /* 属性在交叉轴上是如何定义的 */
    align-items: center;
  }
</style>
```

```html
<div class="father">
  <div class="son1">你好</div>
  <div class="son2">哈哈哈哈哈哈</div>
  <div class="son3">可爱的很</div>
</div>
<style>
  .father {
    display: flex;
    /* 改变主轴方向为竖向 */
    flex-direction: column; 
    /* 在交叉轴上居中对齐 */
    align-items: center;
  }
```

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .fa {
      display: flex;
      background-color: green;
      width: 200px;
      height: 200px;
    }

    .ch1 {
      display: flex;
      background-color: black;
      /* flex项目的放大比例，默认为0即不放大 */
      flex-grow: 0;
      flex-basis: 100px;
    }


    .ch2 {
      display: flex;
      background-color: blue;
      /* flex 的孩子项目排列顺序， 默认为0
      越小越靠前
       */
      order: -2;
      flex-shrink: 1;
    }
  </style>
</head>

<body>

  <div class="fa">
    <div class="ch1">
      123
    </div>
    <div class="ch2">
      456
    </div>

  </div>
</body>

</html>
```

