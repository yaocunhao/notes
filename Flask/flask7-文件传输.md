[参考链接1](https://dormousehole.readthedocs.io/en/latest/patterns/fileuploads.html)

[参考链接2](https://www.w3cschool.cn/flask/flask_url_building.html)

# 一、文件的上传

# 一、Flask 是如何处理文件上传的

- 如果上传的文件很小，那么会把它们储存 在内存中。否则就会把它们保存到一个临时的位置（通过 [`tempfile.gettempdir()`](https://docs.python.org/3/library/tempfile.html#tempfile.gettempdir) 可以得到这个位置）

- 但是，如何限制上传文件 的尺寸呢？缺省情况下， Flask 是不限制上传文件的尺寸的。可以通过设置配置 的 `MAX_CONTENT_LENGTH` 来限制文件尺寸:

  ```python
  #  配置参数
  from flask import Flask, Request
  
  app = Flask(__name__)
  app.config['MAX_CONTENT_LENGTH'] = 16 * 1000 * 1000
  ```



# 二、代码演示

- 上传的简单逻辑流程就是
  - `f = request.files['file']` 获取文件对象
  - f.save('path') 进行文件的存储
  - 然后可以根据个人需求进行文件存储前的一些处理

![image-20220804120453111](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202208041204444.png)

```python
from flask import Flask, render_template, request, make_response, session, redirect, url_for, escape, request, flash
from werkzeug.exceptions import abort
from werkzeug.utils import secure_filename
from flask import send_from_directory
import os

app = Flask(__name__)
UPLOAD_FOLDER = '/tmp/release/'
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER


# 当访问这个页面的时候就返回一个表单
@app.route('/')
def index():
  return render_template('upload.html')


# 进行文件的上传
# 表单之中选择文件之后，就会转到这里
@app.route('/uploader', methods=['GET', 'POST'])
def upload_file():
  if request.method == 'POST':
    f = request.files['file']
    
    # 进行文件的保存
    f.save(os.path.join(app.config['UPLOAD_FOLDER'],secure_filename(f.filename)))  # secure_filename 获取文件名，此文件名可以自定义
    
    return 'file uploaded successfully'


  
# 进行文件的加
@app.route('/uploads/<name>')
def download_file(name):
  return send_from_directory(app.config["UPLOAD_FOLDER"], name)


if __name__ == '__main__':
  app.run(debug=True)

```



# 二、文件的返回

