# 一、安装

- `pip install uwsgi`

# 二、服务器启动

## 2.1 启动服务

- uWSGI本身就是一个用C语言构建的http服务器，所以只需要将其本身启动起来就可以进行通信了

- 在python之中，uWSGI将会自动搜索` application(env, start_response)`函数接口进行调用(**也就是web服务器将请求通过该接口传递给web应用**),可以通过两个参数来配置对应的服务

  ```python
  def application(env, start_response):
      start_response('200 OK', [('Content-Type','text/html')]) # 构建http 响应
      return [b"Hello World"] 
      
      
  # 运行命令
   uwsgi --http :9090 --wsgi-file ./test.py 
  # 游览器输入ip：port = 结果
  Hello World
  ```

## 2.2 三种启动方式

### 2.2.1 --http

- 在指定地址上添加一个HTTP路由器/服务器

- 如果我们想直接将[uwsgi](https://so.csdn.net/so/search?q=uwsgi&spm=1001.2101.3001.7020)用作服务器（例如Apache和nginx那样）直接暴露在公网那么就使用http

- http 自己会产生一个http进程(可以认为与[nginx](https://so.csdn.net/so/search?q=nginx&spm=1001.2101.3001.7020)同一层)负责路由http请求给worker, http进程和worker之间使用的是uwsgi协议

- **这种方式可以直接在外部进行访问**

### 2.2.2 --http-socket

- 使用HTTP协议绑定到指定的UNIX/TCP套接字
- 如果有单独的服务器（例如Apache或者nginx,**也就是说在uWSGI前面还有一层Nginx**），由服务器将请求转发给uwsgi处理，并且使用http协议，那么此时使用http-socket
- http-socke 不会产生http进程, 一般用于在前端webserver不支持uwsgi而仅支持http时使用, 他产生的worker使用的是http协议
  因此, http 一般是作为独立部署的选项; **http-socket 在前端webserver不支持uwsgi时使用**

- http和http-socket对应的nginx配置

  ```nginx
  # http和http-socket对应的nginx配置：侦听当前的80端口，然后将访问/ 重定向到5000接口
  
  server{
  listen 80;
  location / {
  proxy_pass http://127.0.0.1:5000/ // 必须加http://
  }
  }
  ```

### 2.2.3 --socket

- 使用默认协议绑定到指定的UNIX/TCP套接字

- 如果前端webserver支持uwsgi, 则直接使用socket即可(tcp or unix)

- 按照uwsgi文档给出的解释是：bind to the specified UNIX/TCP socket using default protocol.也就是说指定UNIX/TCP socket作为默认的协议（引）。UNIX/TCP socket其实是两类socket。**UNIX socket是进程间的通信**（Inter Process Communication），但只在同一台机器上；**TCP/IP sockets允许进程通过网络通信**

- 在uwsgi中如果配置如下则是使用**UNIX socket**

  ```nginx
  # uwsgi 配置
  [uwsgi]
  socket = /tmp/uwsgi.sock
  
  # nginx配置
  
  uwsgi_pass unix:///tmp/uwsgi.sock;
  include uwsgi_params;
  ```

- 如果配置如下则是使用**TCP/IP socket**

  ```python
  # uwsgi 配置
  [uwsgi]
  socket = 127.0.0.1:8000
  
  # nginx 配置
  
  uwsgi_pass 127.0.0.1:8000;
  include uwsgi_params;
  ```

- **这种方式必须再在外面套一层服务器(比如nginx)，否则是无法访问服务的**

## 2.2 性能提升

- uWSGI默认是单进程的，可以通过`--processes`和` --threads`进行性能设置
- `uwsgi --http :9090 --wsgi-file foobar.py --master --processes 4 --threads 2`
  - 一个`master`守护进程
  - 4个进程，每个进程之中两个线程

## 2.3 监控（日志）

- stats子系统允许，将uWSGI的内部统计数据导出为JSON格式

  ```python
  # 如下所示， --start ip:port 将日志写入到该服务器的端口上
  uwsgi --http :9090 --wsgi-file foobar.py --master --processes 4 --threads 2 --stats 127.0.0.1:9191
  ```

- `telenet 127.0.0.1 9191` 查看日志数据

  ```python
  telnet 127.0.0.1 9191
  Trying 127.0.0.1...
  Connected to 127.0.0.1.
  Escape character is '^]'.
  {
  	"version":"2.0.20",
  	"listen_queue":0,
  	"listen_queue_errors":0,
  	"signal_queue":0,
  	"load":0,
  	"pid":11710,
  	"uid":1000,
  	"gid":1000,
  	"cwd":"/home/didi/uwsgi",
  	"locks":[
  		{
  			"user 0":0
  		},
  		{
  			"signal":0
  		},
  #	.......
  	],
  	"sockets":[
  		{
  			"name":"127.0.0.1:45153",
  			"proto":"uwsgi",
  			"queue":0,
  			"max_queue":100,
  			"shared":0,
  			"can_offload":0
  		}
  	],
  	"workers":[
  		{
   #	.......     
  ```

  

# 三、uWSGI和其它服务通信

## 3.1 介绍

- uWSGI 包含了多种协议，比如：HTTP, FastCGI, SCGI 以及自带的uwsgi 协议
- **性能最好的协议显然是uwsgi，它已经被nginx和Cherokee支**持(同时各种Apache模块也可用)
- 服务之间想要通信就需要遵守协议规则，在上述多种协议中`uwsgi`性能最好 ，因此下面将围绕此介绍服务间的通信

## 3.2 Nginx 配置

- 示例如下

  ```python
  location / {
      include uwsgi_params; # 将所有的参数转到uwsgi下
      uwsgi_pass 127.0.0.1:3031; # Nginx将请求转发重定向到127.0.0.1的3031端口
  }
  ```

  -  服务都是访问的nginx的端口，nginx再将请求分发出去
  - 当访问的url为`/`时，Nginx将其传递给uWSGI

- 通过uWSGI原生启动方法

  ```python
  uwsgi --socket 127.0.0.1:3031 --wsgi-file foobar.py --master --processes 4 --threads 2 --stats 127.0.0.1:9191
  ```

  

## 3.3 Django 部署

- 示例

  ```python
  # 显然命令行之中使用功能这么长的命令是很不合理的
  uwsgi --socket 127.0.0.1:3031 --chdir /home/foobar/myproject/ --wsgi-file myproject/wsgi.py --master --processes 4 --threads 2 --stats 127.0.0.1:9191
  
  
  # 配置文件启动： xxx.ini
  [uwsgi]
  socket = 127.0.0.1:3031 # 绑定端口
  chdir = /home/foobar/myproject/ # 工作目录
  wsgi-file = myproject/wsgi.py # wsgi.py文件
  processes = 4 # 进程数量
  threads = 2 # 每个进程中几个线程
  stats = 127.0.0.1:9191
  
  ```

  - 配置 `.ini`文件
  - `uwsgi xx.int` 启动, **注意配置文件之中不要有注释， 可能会导致启动失败**

## 3.4 Flask 部署

- 相对与Django 仅仅增加了` --callable app 选项`

  ```python
  # Flask 项目启动文件
  
  from flask import Flask
  
  app = Flask(__name__)
  
  @app.route('/')
  def index():
      return "<span style='color:red'>I am app 1</span>"
  
  # 启动命令
  uwsgi --socket 127.0.0.1:3031 --wsgi-file myflaskapp.py --callable app --processes 4 --threads 2 --stats 127.0.0.1:9191
  ```

  

# 四、注意事项

- 当uWSGI是单线程启动时，Python的GIL锁将会失效，因此在python代码之中启动使用多线程时，将不会生效
- 设置解决办法
  - 命令行启动加上选项： `--enable-threads`
  - `xx.ini`文件启动：`enable-threads = true`