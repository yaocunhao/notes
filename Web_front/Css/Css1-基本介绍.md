# 一、Css 基本介绍

- Css：层叠样式表
- Css作用
  - HTML 决定了身体结构
  - Css 决定了样式美观![image-20220917215839203](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172158260.png)

# 二、Css 语法规则

- 写在哪里

  - 一般写在style 里面，title 标签下面

  - 怎么写

    - 选择器：查找对应的标签，然后进行样式代码设定

    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
    
      <style>
        /* 这里是Css 代码 */
        /*选择器 {css 属性}*/
        /*选择器： 查找标签 */
    
        p {
          /*文字颜色 */
          color: red;
          /*字体变大 px：像素 */
          font-size: 40px;
          /*背景颜色*/
          background-color: yellow;
          /* 宽度、长度 */
          width: 200px;
          height: 100px;
        }
      </style>
    </head>
    
    <body>
      <p>这是一个标签</p>
    </body>
    
    </html>
    ```

    ![image-20220917220747289](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172207358.png)

# 三、Css 引入方式

![image-20220917220941043](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172209110.png)

- 内敛式

  ```html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <!-- 关系：样式表 -->
    <link rel="stylesheet" href="./test.css">
  </head>
  
  <body>
    <p>这是p标签</p>
  </body>
  
  </html>
  
  
  
  --------新的css文件--------
  p {
    color:yellowgreen;
  }
  ```

- 行内式

  ```html
  <body>
    <p>这是p标签</p>
    <div style="color:blue">这是div属性</div>
  </body>
  ```

  