# 一、后代选择器

## 1.1 后代选择器

- 只要是后代都会被选中

![image-20220924155722012](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241557133.png)

```html
  <style>
    div p {
      color: red;
    }
  </style>
</head>

<body>
  <p>标签1</p>
  <div>
    <p> 标签2</p>
  </div>

```

## 1.2 子代选择器

- 只会选中 子代

![image-20220924160529998](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241605123.png)

# 二、并集选择器

- 同时设置多个选择器的样式

![image-20220924160737268](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241607389.png)

# 三、交集选择器

![image-20220924161312351](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241613469.png)

```html
  <style>
    p {
      color: red;
    }

    .box {
      color: yellow;
    }

    交集选择器
    如果某个p标签又设置了类属性，那么就会显示蓝色
    p.box {
      color: blue;
    }

  </style>
</head>

<body>

  <p class="box">标签1</p> 

  <p>标签2</p>
```



# 四、hover 伪类选择器

- 任何标签都可以由该选择器

![image-20220924161757184](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241617264.png)

```html
  <style>
    /*鼠标悬停就显示蓝色，背景被黄色*/
    a:hover {
      color: blue;
      background-color: yellow;
    }
  </style>
</head>

<body>
  <a href="#">这是超链接</a>
```



# 五、emmet语法

![image-20220924162351377](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209241623513.png)

- CSS 提示，写单词的首字母即可