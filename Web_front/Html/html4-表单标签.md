# 一、表单标签的作用

- 比如在**登录、搜索、注册**的时候的框框就是表单标签



# 二、input 系列标签

## 2.1 基本介绍

![image-20220917200935019](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172009093.png)

```html
  文本框：<input type="text">
  <!--文本框，写什么显示什么-->
  <br>
  密码框： <input type="password">
  <!--输入内容不可键-->
  <br>
  单选框： <input type="radio">
  <br>
  多选框： <input type="checkbox">
  <br>
  文件选择上传：<input type="file">
  <br>
  提交按钮： <input type="submit">
  <br>
  重置按钮： <input type="reset">
  <br>
  普通按钮，默认无功能：<input type="button">
```

![image-20220917201919932](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172019015.png)

## 2.1 文本框默认提示

- type属性：text/password

- 常用属性placeholder：占位符，提示用户输入内容

  ```html
  文本框：<input type="text" placeholder="请输入账号">
    <br>
    密码框： <input type="password" placeholder="请输入密码">
  ```

  ![image-20220917202250021](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172022079.png)

## 2.2 单选框

- type属性：radio

- 常用属性

  | 属性名  | 说明                                                         |
  | ------- | ------------------------------------------------------------ |
  | name    | 分组。**有相同的name属性值的单选框为一组**，一组中同时只能有一个被选中(**单选功能**) |
  | checked | 默认选中                                                     |

  ```html
    性别单选框： <input type="radio" name="sex">男 <input type="radio" name="sex" checked> 女
  ```

  ![image-20220917202800773](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172028853.png)

## 2.3 多选框

```html
性别多选框： <input type="checkbox" checked>男 <input type="checkbox" >女 <input type="checkbox"> 未知
```

![image-20220917203114121](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172031188.png)

##  2.4 文件上传

- 文件多选 multiple 

  ```html
    文件选择上传：<input type="file" multiple>
  ```

## 2.5 按钮 

- 提交和重置需要写在一个表单域里面才能起效果，同时连接后台需要配合js

  ```python
    <form action=""> <!--表单域-->
      用户名： <input type="text" placeholder="输入用户名">
      密码： <input type="password" placeholder="输入密码">
      提交： <input type="submit" placeholder="提交">
      重置：<input type="reset">
    </form>
  ```

- 按钮提示文字

  - value = 'xxx'

    ```html
      <form action="">
        <!--表单域-->
        用户名： <input type="text" placeholder="输入用户名">
        密码： <input type="password" placeholder="输入密码">
    
        提交： <input type="submit" placeholder="提交" value="注册">
        重置：<input type="reset" value="重置">
      </form>
    
    ```

    ![image-20220917204455229](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172044305.png)

# 三、button 按钮标签

- button 可以单独作为一个标签出现

  | 标签名 | type属性值 | 说明                   |
  | ------ | ---------- | ---------------------- |
  | button | submit     | 将数据提交给后端服务器 |
  | button | reset      | 重置                   |
  | button | button     | 默认无功能             |

- 注意

  - 谷歌游览器button 默认是提交按钮

  - button 标签是双标签，更便于包裹其它内容：文字、图片等等

    ```python
      <form action="">
        <!--表单域-->
        用户名： <input type="text" placeholder="输入用户名">
        密码： <input type="password" placeholder="输入密码">
    
        <button type="submit">submit</button>
        <button type="reset">reset </button>
        <button type="button">没有任何功能 </button>
    
      </form>
    ```

    ![image-20220917205102499](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172051587.png)

# 四、select 下拉菜单标签

- 场景：在网页中提供多个选择项的下拉菜单表单控件
- 标签组成
  - select标签： 下拉菜单的整体
  - option标签：下拉菜单的每一项
- 常见属性：selected 下拉菜单的默认选中

```html

  <select>
    <option>北京1</option>
    <option>北京2</option>
    <option>北京3</option>
    <option>北京4</option>
    <option selected>北京5</option>
  </select>
```

![image-20220917205519759](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172055851.png)

# 五、textarea 文本域标签

- 场景：在网页中提供可输入多行文本的表单控件
- 标签名：textarea
- 常见属性(通过CSS 设定)
  - cols： 规定了文本域内可见宽度
  - rows：规定了文本域内可见行数
- 注意点：
  - 右下角可以进行拖拽改变大小
  - 实际开发时，针对样式效果推荐使用CSS设置

```html
<textarea cols="30" rows="10">
  </textarea>
```

![image-20220917210018970](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209172100071.png)

# 六、label 标签

- 场景：**常用于绑定内容与表单标签的关系(点击字也可以选中单选框)**
- 标签名：lable
- 使用方法一
  - 使用lable标签把内容(如：文本)包裹起来
  - 在表单标签上添加id属性
  - 在lable标签的for属性中设置对应的id属性值
- 使用方法二
  - 直接使用lable标签把内容(如：文本) 和表单标签一起包裹起来
  - 需要把lable标签的for属性删除即可

```html
<input type="radio" name="sex" id="nan"> <label for="nan"> 男 </label> <!--方法1-->
  <label><input type="radio" name="sex"> 女 </label> <!--方法2-->
```

