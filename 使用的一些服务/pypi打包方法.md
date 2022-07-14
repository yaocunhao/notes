[参考链接](https://blog.csdn.net/chenfeidi1/article/details/80873979?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165658121416782390565904%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165658121416782390565904&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-80873979-null-null.142^v26^pc_rank_34,157^v15^new_3&utm_term=setuptools&spm=1018.2226.3001.4187)

[官方打包指南](https://packaging.python.org/en/latest/tutorials/)



# 一、需安装工具

-  pip install setuptools
-  pip install wheel



# 二、上传流程

## 2.1 本地打包使用

- 包文件,也就是日后到使用的包名称 `from myapp .....`

  ```python
  myapp/
  ├── greet.py
  ├── __init__.py
  └── setup.py
  
  # greey.py
  def hello():
      print('Hello, welcome to setuptools!')
  ```

- setup.py 打包文件

  ```python
  from setuptools import setup
  
  setup(
      name='firstApp', # 应用名
      version='0.0.1', # 版本号
      packages=['myapp'], # 包括在安装包内的 Python 包
  )
  ```

- 打包 `python setup.py bdist_wheel`

- **从本地安装包**(打包后的dist文件之中就是当前的包)  `pip install dist/fisrtApp-0.0.1-py3-none-any.whl`

- 使用

  ```python
  from myapp.greet import hello # 导入包
  
  hello()
  ```

## 2.2 上传到pypi

- 账号注册 [pypi](https://pypi.python.org/pypi)

- 安装 twine `pip install twine`

- 家目录下添加免密配置文件

  ```python
  [distutils]
  index-servers =
      pypi
      pypitest
  
  [pypi]
  username: 
  password: 
  
  [pypitest]
  repository: https://test.pypi.org/legacy/
  username: 
  password: 
  
  ```

- 上传 `twine upload dist/*`

  - **注意自己的打包出来的文件名，必须是唯一的，否则会上传失败**
  - 登录pypi可以查看到自己的包![image-20220630192922513](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206301929213.png)

- 上传完后，这个包就是共享的了

  ```shell
  # 使用pip install 进行包的加载
  pip install ych_firstApp
  
  Collecting ych_firstApp
    Downloading ych_firstApp-0.0.1-py3-none-any.whl (1.6 kB)
  Installing collected packages: ych-firstApp
  Successfully installed ych-firstApp-0.0.1
  ```



#  三、进一步了解打包 

## 3.1 setup 参数了解

- 复杂的打包，比如模块过滤，非 py 文件打包，作者信息等常见的需求，都被实现在 `setup()` 内。所以接下来详细介绍 setup()

- setup 参数含义

  - name 项目名，也是最终在 PyPI 上搜索的名称

  - version 项目版本号，一般由三部分组成：MAJOR, MINOR, MAINTENANCE

    ```python
    version='0.0.1'
    ```

  - packages 列出项目内需要被打包的所有 package。一般使用 setuptools.find_packages() 自动发现

    ```
    packages=find_packages(exclude=['contrib', 'docs', 'tests*'])
    exclude 用于排除不打包的 package
    ```

  - description 

    - 项目的简短描述，一般一句话就好，会显示在 PyPI 上名字下端

    - 对项目的完整描述，使用 `long_description`如果此字符串是 rst 格式的，PyPI 会自动渲染成 HTML 显示。也可指定使用 markdown。

      ```
      # 方法一
      long_description=long_description,
      long_description_content_type='text/x-rst'
      
      
      # 方法二
      long_description = file: README.md
      long_description_content_type = text/markdown
      ```

  - url 通常为 GitHub上 的链接或者 readthedocs 的链接

  - author 作者信息

  - license 项目许可证 `关于各种许可证的介绍和选择，参考：https://choosealicense.com/`

  - classifiers 项目分类

  - keywords 项目关键词列表

  - project_urls 项目相关额外链接，如代码仓库，文档地址等

  - install_requires 项目依赖的Python库，使用pip安装本项目时会自动检查和安装依赖

    ```
    install_requires=['pyyaml']
    ```

  - python_requires 指定项目依赖的python版本

    ```
    python_requires='>=3'
    ```

  - package_data 项目依赖数据文件，数据文件必须放在项目目录内且使用相对路径

    ```
    package_data={
        'myapp': ['data/*.yml'],
    }
    ```

    

  - data_files 如果数据文件存在于项目外，则可以使用 data_files 参数或者 MANIFEST.in 文件进行管理

    - 如果用于源码包，则使用 MANIFEST.in；

    - 如果用于 wheel，则使用 data_files

      ```
      data_files=[(‘mydata’, [‘data/conf.yml’])]
      上述设置将在打包 wheel 时，将 data/conf.yml 文件添加至 mydata 目录
      data_files 不能使用路径通配符
      ```

  ## 3.2 setup.cfg

  