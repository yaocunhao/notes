# 一、不用提前声明类型

![image-20230102220556945](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022205000.png)

# 二、数据的分类

- Js把数据类型分为两类：
  - 简单数据类型(Number, String, Boolean,Undefined,Null)
  - 复杂数据类型(object)
- typeof(var) 获取变量类型

## 2.1 简单数据类型

![image-20230102220737860](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022207939.png)

### 2.1.1 数字类型

- `isNan()`
  - 判断是否是数字，如果是数字返回false，如果不是返回true

![image-20230102220945967](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022209010.png)

![image-20230102220916360](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022209420.png)

### 2.1.2 字符串类型

- 获取字符串长度
  - length(str.length)
- 字符串拼接
  - 只要有字符串和其它类型拼接，最后得到的结果就是字符串类型
- 字符串拼接加强
  - 可以字符串+变量名的方式进行拼接

![image-20230102221120916](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022211001.png)

### 2.1.3 布尔型

![image-20230102221826151](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022218230.png)

### 2.1.4 Undefined 和 Null

- 一个声明后没有被赋值的变量会有一个默认值undefined(如果进行相连或者相加时，注意结果)![image-20230102222022340](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022220420.png)



## 2.2 复杂数据类型



# 三、数据类型转换

## 3.1 转换为字符串

![image-20230102222501714](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022225779.png)

## 3.2 转换为数字型

![image-20230102222600797](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022226873.png)

## 3.3 转换为布尔型

![image-20230102222820468](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022228549.png)

# 四、数组

## 4.1 数组的创建

![image-20230102225100624](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022251701.png)

![image-20230102225130103](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022251177.png)

# 五、对象

## 5.1 利用字面量创建对象

- 就是花括号{}里面包含了表达这个具体事物(对象)的属性和方法![image-20230102231504868](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022315950.png)

- 调用对象的属性：对象名.属性名

## 5.2 利用new Object 创建对象

![image-20230102231701143](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022317207.png)

## 5.3 利用构造函数来创建对象

![image-20230102231752734](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022317808.png)

![image-20230102231923422](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022319501.png)

## 5.4 New关键字执行过程

![image-20230102232039122](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022320203.png)

## 5.5 遍历对象属性值

![image-20230102232131579](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301022321649.png)