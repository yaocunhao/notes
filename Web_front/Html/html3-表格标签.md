# 一、表格的基本标签

| 标签名 | 说明                       |
| ------ | -------------------------- |
| table  | 表格整体，可以包裹多个tr   |
| tr     | 表格的**每行**，用于包裹td |
| td     | 表格的单元格，用于包裹内容 |

- 包含关系： table > tr > td

# 二、表格的相关属性

- 设置表格的基本展示效果(横纵线、格子大小) 、 一般推荐使用CSS 设置

  | 属性名 | 属性值 | 效果     |
  | ------ | ------ | -------- |
  | border | 数字   | 边框宽度 |
  | width  | 数字   | 表格宽度 |
  | height | 数字   | 表格高度 |

  ```html
    <table border="1" , width="100" , height="20">
      <tr>
        <!-- <tr>  表示每一行 -->
        <td>姓名</td>
        <td>成绩</td>
      </tr>
      <tr>
        <td>李云龙</td>
        <td>100</td>
      </tr>
    </table>
  ```

  ![image-20220917175707718](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209171757779.png)

# 三、表格标题和表头单元格标签

- 在表格中表示整体大标题和一系列小标题

| 标签名  | 名称       | 说明                                                         |
| ------- | ---------- | ------------------------------------------------------------ |
| caption | 表格大标题 | 表示表格整体大标题，默认在表格居中位置显示                   |
| th      | 表格单元格 | 表示一列小标题，通常用于表格第一行，默认内部文字**加粗并居中显示** |

- 注意
  - caption 标签卸载 table 标签内部
  - th标签书写在tr标签内部(用于替换td标签) 

```html
  <table border="1" , width="100" , height="20">
    <caption> 表格名称</caption>
    <tr>
      <!-- <tr>  表示每一行 -->
      <th>姓名</th>
      <th>成绩</th>
    </tr>
    <tr>
      <td>李云龙</td>
      <td>100</td>
    </tr>
  </table>
```

![image-20220917181916898](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209171819960.png)

# 四、表格的结构标签 (游览器有默认设置)

- 主要是给游览器识别，使得效率更高，加不加对人的视觉来说是没有区别的

| 标签名 | 名称                   |
| ------ | ---------------------- |
| thead  | 表格头部：也就是第一行 |
| tbody  | 表格主题：表格的内容   |
| tfoot  | 表格底部： 最后一行    |

```html
<table border="1" , width="100" , height="20">
    <caption> 表格名称</caption>
    <thead>
      <tr>
        <!-- <tr>  表示每一行 -->
        <th>姓名</th>
        <th>成绩</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>李云龙</td>
        <td>100</td>
      </tr>
    </tbody>
    <tfoot>
      <tr>
        <td>总结</td>
      </tr>
    </tfoot>
  </table>
```

![image-20220917182702972](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209171827050.png)

# 五、合并单元格

- 将水平或垂直多个但严格合并成一个单元格

  - 跨行合并
  - 跨列合并

- 合并单元格步骤

  - 明确合并几个单元格
  - 通过左上原则，确定保留谁删除谁
    - 上下合并：只保留最上的，删除其它
    - 左右合并：只保留最左的，删除其它
    - **在保留的地方添加标签属性、删除的地方删除掉内容**

- 给保留的单元格设置：跨行合并或者跨列合并

  | 属性名  | 属性值         | 说明     |
  | ------- | -------------- | -------- |
  | rowspan | 合并单元格个数 | 跨行合并 |
  | colspan | 合并单元格个数 | 跨列合并 |

- 注意

  - 只有同一个结构标签中的单元格才能合并，不能跨结构标签合并(不能跨thread、tbody、tfoot)

```html
 <table border="1" , width="100" , height="20">
    <caption> 表格名称</caption>
    <thead>
      <tr>
        <!-- <tr>  表示每一行 -->

        <th colspan="3">姓名</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>李云龙</td>
        <td rowspan="2">100</td> <!--保留的添加标签，删除的删除内容-->
        <td>100</td>
      </tr>
      <tr>
        <td>和善</td>
        <td>22</td>
      </tr>
    </tbody>
    <tfoot>
      <tr>
        <td>总结</td>
        <td>好极了</td>
        <td>好极了</td>
      </tr>
    </tfoot>
  </table>
```

![image-20220917183857394](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209171838494.png)