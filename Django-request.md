# 一、GET 方法

- request.GET 返回一个字典对象，,包括所有的 HTTP GET 参数



# 二、POST

## 2.1 获取方法

- **request.POST(只能获取表单数据) **
- **request.body(获取非表单数据) 是django原生的属性**
- **request.data(包含表单及非表单数据)则是DRF封装的属性**

## 2.2 获取场景

- 如果请求头中包含: Content-Type: application/x-www-form-urlencoded说名字是表单数据,request.POST中才会有值（才会去request.body中解析数据）
- 否则采用request.body.decode(encoding) 获取非表单数据