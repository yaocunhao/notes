-  this 指向的是事件函数的调用者btn

# 一、改变元素内容

![image-20230103004941398](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030049447.png)

- 区别演示

![image-20230103005358026](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030053077.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <p>12312341</p>
  <div>
    dasdasdsada
  </div>
  <script>
    var div = document.querySelector('div')
    console.log(div)
    // 只返回内容
    console.log(div.innerText)
    // 还返回html 标签
    console.log(div.innerHTML)
    
    div.innerText = '<strong> today id </strong> 2023';
    div.innerHTML = '<strong> today id </strong> 2023';

    // 这两个属性是可读的，可以获取元素之中的内容
    var p = document.querySelector('p');
    console.log(p.innerText);
    console.log(p.innerHTML);


  </script>
</body>

</html>
```



# 二、修改属性

![image-20230103005548330](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030055413.png)

![image-20230103005625108](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030056183.png)

# 三、表单元素的属性操作

![image-20230103005727859](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030057937.png)

![image-20230103005907154](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030059212.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <button>按钮</button>
  <input type="text" value="enter content">

  <script>
    var btn = document.querySelector('button')
    var input = document.querySelector('input')

    btn.onclick = function () {
      input.value = '被点击了'
      // this 指向的是事件函数的调用者btn
      this.disabled = true
    }
  </script>
</body>

</html>
```



# 四、样式属性修改

- 我们可以通过JS修改元素的大小、颜色、位置等样式![image-20230103011237108](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030112175.png)		![image-20230103010741618](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030107699.png)

![image-20230103011324385](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030113442.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>

  <style>
    .first {
      background-color: yellow;
    }

    .change {
      height: 300px;
    }
  </style>
</head>

<body>
  <div>
    123
  </div>
  <div class="first">
    文本
  </div>

  <script>
    var div = document.querySelector('div');
    div.onclick = function () {
      this.style.backgroundColor = "green";
      this.style.width = "200px";
    }

    // 注意getElementsByClassName 返回的是一个htmlCollection 集合
    var div2 = document.getElementsByClassName('first')[0];
    div2.onclick = function () {
      this.className = 'first change'
    }
  </script>
</body>

</html>
```



# 五、操作元素小结

![image-20230103011419434](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030114499.png)



