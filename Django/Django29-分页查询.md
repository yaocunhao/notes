# 一、分页返回

- [参考链接](https://blog.csdn.net/shirukai/article/details/81086272?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166662912316782395335100%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166662912316782395335100&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-81086272-null-null.142^v59^pc_new_rank,201^v3^control_1&utm_term=Django%20%E5%88%86%E9%A1%B5%E6%9F%A5%E8%AF%A2&spm=1018.2226.3001.4187)

- `objects.all() 获取所有的对象`
- `paginator = Paginator(objs, pageSize)`获取分页器对象
  - 参数含义
    - objs：获取的总的对象
    - pageSize：每页返回的数量
  - API接口
    - `paginator.count`：总的页数
    -  paginator.page(page)：返回第page页的数据库对象

```python
from .models import Book
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger
import json
from django.core import serializers
from django.http import JsonResponse

def select_test(request, **kwargs):
  objs = Book.objects.all() # 获取所有的对象
  pageSize = 3  # 设置每页的数量
  paginator = Paginator(objs, pageSize)  # 获取分页器对象
  ret ={}
  ret['total'] = paginator.count
  try:
    page = '1'  # 这是第几页，一般由前端传入
    books = paginator.page(page) # 根据分页器获取数据
  except PageNotAnInteger:  # 页数不是整形出现异常则默认返回第一页
    books = paginator.page(1)
  except EmptyPage:
    books = paginator.page(paginator.num_pages)  # 返回总页数

  ret['list'] = json.loads(serializers.serialize("json", books))  # 序列化对象
  return JsonResponse(ret)  # 返回json响应
```

