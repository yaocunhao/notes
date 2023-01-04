# 一、使用场景

![image-20220917172824482](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209171728246.png)

# 二、无序列表

- ul: 表示整个无需列表整体，用于包裹li标签(**且只允许包含li标签**)

- li:表示无序列表的每一项，用于包含每一行的内容(**允许包含任何内容**)

  ```html
    <ul>
      <li>苹果</li>
      <li>香蕉</li>
      <li><a href="www.baidu.com"> 跳到百度</a></li>
    </ul>
  ```

  ![image-20220917173435968](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209171734031.png)

- css 之中去掉前面的点

  ```css
  ul {
  list-style:none;
  }
  ```

  

# 三、有序列表

- 有序标签，ul替换成ol即可

  ```html
    <ol>
      <li>苹果</li>
      <li>香蕉</li>
      <li><a href="www.baidu.com"> 跳到百度</a></li>
    </ol>
  
  ```

# 四、自定义列表

- 在网页的底部导航之中通常你会使用自定义列表实现

- 标签的组成

  | 标签名 | 说明                                                 |
  | ------ | ---------------------------------------------------- |
  | dl     | 表示自定义列表的整体，用于包裹dt/dd 标签(**只允许**) |
  | dt     | 表示自定义列表的主题                                 |
  | dd     | 表示自定义列表的针对主题的每一项内容                 |

- 显示特点

  - dd 前默认会显示缩进效果

```html
	<dl>
    <dt> 主题一</dt>
    <dd>1.1</dd>
    <dd>1.2</dd>

    <dt>主题二</dt>
    <dd>2.1</dd>
    <dd>2.2</dd>
  </dl>
```

![image-20220917174202987](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209171742060.png)