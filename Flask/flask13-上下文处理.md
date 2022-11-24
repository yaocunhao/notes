- [参考链接](https://www.cnblogs.com/lili37/p/16044842.html)
- [gd](https://flask.palletsprojects.com/en/2.2.x/appcontext/#purpose-of-the-context)

# 一、上下文目的

- flask 应用程序对象(`app`)是有一些自身属性的，比如`config`配置。 然后app对象只有一个，如果需要在多处`import`很可能发生重复引用的问题。 因此就退出了上下文概念
- 在上下文之中，不是直接引用`app`对象而是使用指向当前程序的代理`current_app`

```python
def context():
  """验证上下文"""
  
  # app 和 current_app 含义是相同的
  print(app) # <Flask 'req_test'>
  print(current_app) # <Flask 'req_test'>
  # 创建上下文对象
  with flask.current_app.app_context() as cp:
    print(cp)
```

