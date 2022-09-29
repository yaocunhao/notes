[文件加载](https://blog.csdn.net/s_daqing/article/details/107624455?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-107624455-blog-84803924.t0_edu_mix&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-107624455-blog-84803924.t0_edu_mix&utm_relevant_index=5)

# 一、文件上传

- 通过`request.FILES` 获取请求之中的文件内容

- 然后通过write进行文件的写入

  ```python
  def func(request: HttpRequest):
    print("run success!!!")
    # 读取文件的内容
    con = request.FILES.getlist('file')  # 获取文件列表
    file_name = con[0].name
    url = "/Users/didi/django_test/Demo/test_app/" + file_name
    with open(url, 'wb+') as f:
      # print(type(con[0].read())) 导致指针的位置移动了,con[0].seek(0) 进行归位
      f.write(con[0].read())
  ```

# 二、文件加载

- [文件加载](https://blog.csdn.net/s_daqing/article/details/107624455?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-107624455-blog-84803924.t0_edu_mix&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-107624455-blog-84803924.t0_edu_mix&utm_relevant_index=5)
- [协议头](https://crayon-shin-chan.blog.csdn.net/article/details/122903087?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7EPayColumn-1-122903087-blog-88120353.pc_relevant_multi_platform_whitelistv4&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7EPayColumn-1-122903087-blog-88120353.pc_relevant_multi_platform_whitelistv4&utm_relevant_index=1)



## 2.1 以流的形式返回文件

- 使用Djang的响应对象： StreamingHttpResponse

- 通过设置http协议的请求头的参数，来使得游览器知晓如何去解析返回的内容

```python
from django.http import StreamingHttpResponse

def read_file(file_name, chunk_size=512):
    with open(file_name, "rb") as f:
        while True:
            c = f.read(chunk_size)
            if c:
                yield c
            else:
                break
                
def func(request):
  print(request.path)
  file_path = "/Users/didi/django_test/Demo/test_app/test.txt"
  file_name = "test.txt"
  response = StreamingHttpResponse(read_file(file_path))
  response["Content-Type"] = "application/octet-stream"  # 返回内容的类型
  
  response["Content-Disposition"] = 'attachment; filename={0}'.format(file_name)  # 表示是以内联的形式还是附加的形式展示，filename 设置前端展示下载时候的默认名称
  # response["Content-Disposition"] = "inline"  # 以内联的形式
  
  response[
      "Access-Control-Expose-Headers"] = "Content-Disposition"  # 表示哪些内容可以暴露给外部 (为了使前端获取到Content-Disposition属性)

  return response

```



## 2.2  以文件对象的形式返回

- 使用Django响应对象：FileResponse，是StreamingHttpResponse的子类

  ```python
  from django.http import FileResponse
  
  def func(request):
    file_path = "/Users/didi/django_test/Demo/test_app/test.txt"
    response = FileResponse(open(file_path, 'rb'),as_attachment=True) # as_attachment 设置返回的形式(是否加载)
    return response
  ```

  
