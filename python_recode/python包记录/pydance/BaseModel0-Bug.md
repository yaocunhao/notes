# 一、bug

- 来自ORM的列表和字典不再是内置的列表和字典，使用obj.dict() 会导致问题的发生
  - 当前遇到的问题就是从mongo之中取出对象，然后将这个对象初始化成BaseModel的结构，然后转换成dict，发生错误。 这是因为Mongo之中取出的对象的list不再是python的内置list
  - [解决方法](https://github.com/pydantic/pydantic/issues/2340)

- 小心和库产生重复字段
  - `pydantic.errors.ConfigError: duplicate validator function "xxx"; if this is intended, set `allow_reuse=True`
  - 这个错误就是某个类里面出现了重复字段





# 二、注意点

## 2.1 如何支持非内置类型的检验

- [link](https://www.nuomiphp.com/a/stackoverflow/zh/6238857095e6cb02de69b484.html)



# 三、v_1 和 v_2 的修改内容

- [link](https://docs.pydantic.dev/2.0/migration/#changes-to-validators)