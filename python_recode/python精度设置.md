- [参考链接1](https://blog.csdn.net/qdPython/article/details/125187735?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-125187735-blog-122458512.pc_relevant_3mothn_strategy_recovery&spm=1001.2101.3001.4242.1&utm_relevant_index=3)
- [参考2](https://hllyzms.blog.csdn.net/article/details/79473706?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-79473706-blog-83477607.pc_relevant_3mothn_strategy_and_data_recovery&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-79473706-blog-83477607.pc_relevant_3mothn_strategy_and_data_recovery&utm_relevant_index=5)
- [参考 3](https://blog.csdn.net/craftsman2020/article/details/111145393?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166736749216782395360200%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166736749216782395360200&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-111145393-null-null.142^v62^pc_rank_34_queryrelevant25,201^v3^control_1,213^v1^control&utm_term=python%20decimal%E4%BB%8B%E7%BB%8D&spm=1018.2226.3001.4187)

# 注意事项

- 可以给Decimal 传入整形或者字符串参数，**不能直接传递浮点数据**，因为浮点数本身就不正确



# 测试代码记录

```python
"""测试精度类型decimal"""

from decimal import *

def test3():
  """decimal  和 float 进行计算"""
  """
    结论：
    decimal 不支持和float进行计算
  """
  num = 0.2
  v1 = Decimal(0.1) + 0.2
  v2 = Decimal(0.1) + num
  print(v1)
  print(v2)


def until_float_to_dcl(num):
  """将浮点数，字符串转换成decimal"""
  return Decimal(num).quantize(Decimal('0.0'))


def test4():
  """float 转换成 decimal"""
  v = 1111.1231
  a = until_float_to_dcl(v)
  b = until_float_to_dcl(0.1)
  print(a + b)


def test5():
  """小数字符串转换成int类型"""
  """
  结论： 无法转化，出现异常
  """
  str = "0.1"
  print(int(str))


def test6():
  """判断0是不是为false"""
  """
  结论： 0 是false
  """

  v = Decimal(0)
  print(type(v))
  if v:
    print('True')
  else:
    print('False')


print(until_float_to_dcl(9.9) + until_float_to_dcl(0.1))
```

