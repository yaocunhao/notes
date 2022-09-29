# 一、字体样式

- 字体大小：font-size

  - 游览器中默认字号为16

- 字体粗细：font-weight

  - 取值关键字
    - 正常：normal
    - 加粗：bold
  - 纯数字：100-900的整百数
    - 正常：400
    - 加载：700
  - 注意
    - 不是所有字体都提供了九种粗细，因此部分取值页面之中无变化
    - 实际开发中以：正常、加粗两种取值使用最多

- 字体样式：font-style(**控制是否倾斜**)

  - 正常：normal
  - 倾斜：italic

- 字体类型：font-family

  - 一般采用默认的即可
  - 如果设置了多个，电脑按顺序进行匹配，匹配上了就显示什么字体

- 字体类型：font属性连写

  - 所谓的连写就是font后面的单词省略

    ```css
     * {
          /* front: style weight size 字体*/
          font: italic 700 70px 宋体;
        }
    ```

    

![image-20220917225310159](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172253234.png)	

- 问题记录
  - Css 是层叠渲染，因此设置了相同的属性，会直接覆盖

![image-20220917225146310](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172251384.png)

# 二、文本样式

- 文本缩进：text-ident

  - 取值

    - 数字+px

    - 数字+em(推荐：1em=当前标签的forn-size 大小)

      ```css
       p {
            /*首行缩进两个字*/
            text-indent: 2em;
          }
      ```

      

- 文本水平对齐方式：text-align

  - 取值

    | 属性值 | 效果             |
    | ------ | ---------------- |
    | left   | 左对齐(默认效果) |
    | center | 居中对齐         |
    | right  | 右对齐           |

    ```css
        p {
          text-align: left; /*默认值*/
          text-align: right;
          text-align: center;
        }
    ```

    

  - 可以让那些元素居中对齐

    - 文本、span标签、a标签、input标签、img标签

  - 注意点

    - **如果需要让以上元素水平居中，text-align：center 需要给以上元素的父元素设置**

      ![image-20220917231206313](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172312373.png)

- 文本修饰：text-decoration

  - 取值

    | 属性值       | 效果     |
    | ------------ | -------- |
    | underline    | 下划线   |
    | line-through | 删除线   |
    | overline     | 上划线   |
    | none         | 无装饰线 |

  - 注意点

    - 开发中会使用text-decoration:none 清除a标签默认的下划线

# 三、line-height 行高

- 控制不同行字体之间的间隔高度
- 可以在大小后面`/`后面连接行高

![image-20220917233531024](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172335118.png)

```css
    * {
      /*像素写法*/
      line-height: 10px;
      /*倍数写法*/
      line-height: 1;
    }
```

# 四、颜色取值

![image-20220917234728688](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172347773.png)

# 五、标签水平居中

- 如何让div、p、h（大盒子）水平居中
  - 可以通过margin:0 auto 实现
- 注意
  - 如果需要让div、p、h(大盒子)水平居中，直接给当前元素本身设置即可
  - margin 一般针对固定宽度的盒子，如果大盒子没有设置宽度，此时会默认占满父元素的宽度

```css
  <style>
    p {
      width: 200px;
      height: 200px;
      background-color: pink;
      margin: 0 auto;
    }
  </style>
</head>

<body>

  <p></p>
```

![image-20220917235646950](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172356052.png)