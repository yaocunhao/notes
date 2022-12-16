# 一、启动方式

- 启动方式一

  - 建立一个mytest.py 文件，**注册flask App对象，app.run()**

  ```python
  from flask import Flask
  app=Flask(__name__) # 注册app对象，实际上就是启动了一个服务器(也可以说启动了一个进程)
   
  @app.route('/')
  def index():
      return '<h1>Hello World</h1>'
   
  
  
  # 调试程序 app.run(debug = True) 修改完代码后自动启动
  # app.run() 启动项目，其实就是隐藏的创建一个服务器
  app.run() 
  
  python mytest.py # 运行项目
  ```

- 启动方式二
  - 在1.0版本之后，Flask 调整了开发服务器的启动方式，由代码编写app.run()语句调整为命令flask run启动,**因此程序之中不用再写app.run()**
  - 启动命令
    - flask run,比如 flask run -h 0.0.0.0 -p 5005 （ python -m flask run --no-debugger -h 0.0.0.0 -p 8000）
    - 可以通过flask run --help 来查看帮助信息
  - 在终端中启动需要设置的环境变量和其含义
    - FLASK_ENV
      -  表示当前的生产环境比如development
      - 默认情况下，FLASK_ENV的值为production，在开发时我们可以将其设为development来开启调试模式
    - FLASK_APP
      -  表示当前的启动项目
- 如何避免重复写环境变量
  - $ pip install python-dotenv
  - 当python-dotenv安装后，执行flask run命令会首先将项目根目录下的.env和.flaskenv文件中的环境变量写入
    - 所以，你可以将FLASK_APP写在这两个文件中
    - 按照约定
      - .env存储包含敏感数据的环境变量，这个文件需要加入到.gitignore中以避免提交到Git仓库中；
      - .flaskenv时Flask特别支持的文件，这个文件则用来存储和Flask相关的环境变量，比如FLASK_ENV、FLASK_DEBUG等，所以我们可以把FLASK_APP写到这个文件中
        

# 二、需要用到的一些扩展功能

- **Flask本身相当于一个内核**，其他几乎所有的功能都要用到扩展，都需要第三方的扩展来实现
- 常有的扩展包有这些：
  - Flask-SQLalchemy：操作数据库
  - Flask-script：插入脚本
  - [参考链接](https://blog.csdn.net/weixin_44038097/article/details/124587863?ops_request_misc=%25257B%252522request%25255Fid%252522%25253A%252522165450452916782184620789%252522%25252C%252522scm%252522%25253A%25252220140713.130102334.pc%25255Fall.%252522%25257D&request_id=165450452916782184620789&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-124587863-null-null.142%5Ev11%5Epc_search_result_control_group,157%5Ev13%5Econtrol&utm_term=+%25E6%2580%258E%25E4%25B9%2588%25E5%2588%25A4%25E6%2596%25AD%25E5%25BD%2593%25E5%2589%258D%25E9%25A1%25B9%25E7%259B%25AE%25E7%259A%2584%25E6%25A1%2586%25E6%259E%25B6%25E6%2598%25AF%25E4%25B8%258D%25E6%2598%25AFflask&spm=1018.2226.3001.4187)

