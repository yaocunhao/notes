# 一、什么是变量

- 变量就是一个盒子，用于存放数据的容器

  ```html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script>
      // 声明一个age变量
      var age;
      // 赋值
      age = 19;
      // 输出
      console.log(age)
  
      // 变量的初始化
      var myname='lihua'
      console.log(myname)
  
      </script>
  </head>
  
  <body>
  </body>
  
  </html>
  ```

  

# 二、测试用例

## 2.1 输入内容然后弹出

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <script>
    // 声明一个age变量
    var age;
    // 赋值
    age = 19;
    // 输出
    console.log(age)

    // 变量的初始化
    var myname='lihua'
    console.log(myname)

    </script>
</head>

<body>
</body>

</html>
```

## 2.2 同时声明多个变量

![image-20230102220021773](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022200861.png)

## 2.3 声明变量的特殊情况

- 不提倡不声明直接使用

![image-20230102220107664](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022201738.png)

# 三、变量命名规范

![image-20230102220222778](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022202862.png)

# 四、杂记

- let

  ```js
  在JavaScript中，let是一种用于声明变量的关键字。与var相比，let具有块级作用域，意味着在声明它的块（通常是花括号内的代码块）中有效。这意味着let声明的变量只在其所在的代码块中可见，并且不会被提升到块的顶部。另外，使用let声明的变量可以被重新赋值。
  
  例如，以下代码示例演示了let的使用：
  
   
  function example() {
    let x = 10;
    if (true) {
      let x = 20; // 不同的块级作用域
      console.log(x); // 输出20
    }
    console.log(x); // 输出10
  }
  
  example();
  在上述示例中，函数example()中使用了两个不同的x变量。在if语句块中的let x = 20声明只在该块内部起作用，不影响外部作用域中的x变量。因此，第一个console.log(x)会输出20，而第二个会输出10。
  ```

  