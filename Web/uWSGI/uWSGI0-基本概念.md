# 零、参考链接

[参考链接](https://blog.csdn.net/weixin_37780776/article/details/106041458?ops_request_misc=&request_id=&biz_id=102&utm_term=web%20server%20%E5%92%8C%20web%20%E6%A1%86%E6%9E%B6%E7%9A%84%E5%85%B3%E7%B3%BB&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-106041458.142^v39^pc_rank_34_2&spm=1018.2226.3001.4187)

[c2](https://blog.csdn.net/weixin_42134789/article/details/115713572?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166080141816781685356674%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166080141816781685356674&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-115713572-null-null.142^v41^pc_rank_34_1,185^v2^control&utm_term=uwsgi&spm=1018.2226.3001.4187)

[WSGI/uwsgi/uWSGI 之间的区别和联系](https://blog.csdn.net/weixin_45455015/article/details/100113330?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166176422116782414965200%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166176422116782414965200&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-100113330-null-null.142^v42^pc_rank_34,185^v2^control&utm_term=uwsgi%20%E5%92%8C%20wsgi%E7%9A%84%E5%8C%BA%E5%88%AB&spm=1018.2226.3001.4187)

# 一、web 服务器和web框架

## 1.1 是什么

- 假设，我们通过底层的套接字编写了一个http服务器，这个服务器就具有了基本的通信功能，这就是web开发的过程
- 实际上，在真实的web开发中，为了**提高开发效率、复用已有成果、降低系统耦合度**，实际运行在服务器上的程序可以大致**解耦**为**web服务器、web框架**
- **常用的web服务器**有Apache、Nginx等，**常用的Python后端web框架**有Django、Flask、Tornado等
- 在Python的web开发中，一般**web服务器和web框架之间为了实现互通，二者还会遵循web服务器网关接口**

## 1.2 各自功能

- web服务器的功能一般为

  - 用于**以并发方式处理**来自多客户端基于HTTP协议的**连接请求**
  - 当客户端请求静态资源时，web服务器直接将部署在其上的静态资源在响应中返回客户端。**也就是不需要与web框架进行交互，从而提高效率**
  - 当客户端请求动态资源，以及当后端希望实现复杂的业务逻辑（如：用户注册、登录、验证等）时，web服务器负责先将收到的响应请求发送web框架，而后web服务器负责将web框架处理的结果以HTTP协议应答方式返回

- web框架的功能一般为

  - 处理web服务器转发过来的动态资源请求

  - 处理复杂的业务逻辑，如：用户注册、登录、验证、订单管理等

- 综上所述

  - web服务器就是处理链接请求，来实现服务器的负载均衡和高并发的处理
  - web框架则是进行一些内部逻辑处理，最终的处理结果还是需要通过服务器发送回给客户端

# 二、网络服务网关接口(WSGI)

- [board](https://boardmix.cn/app/editor/6GzdgQW3KurrIEiwwKob2A)

- 在进行web后端开发的时候，可以根据需要搭配使用web服务器和web框架，以使得二者的组合可以最大限度满足自己的需求
- 因为web服务器和web框架的开发者并不是同一批人，二者内部的功能封装并不遵循相同的接口，故早先时候，开发者对web服务器的选择会影响其对可用web框架的选择，反之亦然
- 鉴于上述原因，在PEP（Python Enhancement Proposal） 333中，Python社区提出了WSGI，**这是一套简单、通用的接口，用于web服务器和web框架之间的协议**，使得web服务器和web框架的开发者专注于各自的领域，也能降低web后端的开发者进行web服务器和web框架之间适配的工作![image-20220803165737935](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202208031657034.png)

# 三、uWSGI

## 3.1 是什么

- Django 自带一个内置的web服务器，但是由于 Django 并不专注于实现服务器，所以内置的 WSGI 服务器无法满足生产环境的需求，所以我们在这里要介绍另外一种 WSGI **服务器**，即 uWSG
- **uWSGI 是当下最流行的一种 WSGI 服务器，同样遵守 WSGI 协议**
- 它可以与各种 Python Web 框架实现兼容，而且配置过程与使用方式都非常简单
- 我们之前使用 **runserver 命令启动项目，通常只是在开发和测试环境中使用**
- 当开发结束后，**完善的项目代码需要在一个高效稳定的环境中运行，这时候可以使用 uWSGI**，它是 WSGI 服务器的一种，它可以让 Django、Flask 等开发的 Web 应用站点运行其中

## 3.2 作用

- Django 是一个 Web 框架，框架的作用在于处理 request 和 reponse，其他的不是框架所关心的内容。所以怎么部署 Django 不是 Django 所需要关心的
- Django 所提供的是一个开发服务器，这个开发服务器，没有经过安全测试，而且使用的是 Python 自带的 simple HTTPServer 创建的，在安全性和效率上都是不行的
- 而uWSGI 是一个全功能的 HTTP 服务器，他要做的就是把 HTTP 协议转化成语言支持的网络协议。比如把 HTTP 协议转化成 WSGI 协议，让 Python 可以直接使用
- uwsgi 是一种 uWSGI 的内部协议，使用二进制方式和其他应用程序进行通信

## 3.3 uwsgi

- uWSGI为web服务器，是web server 和 web application 沟通的桥梁
- 通信则需要标准，这个标准就是协议，uWSGI  服务器有很多种协议，而常用的协议就是uwsgi

## 3.4 Nginx就是web服务器，为什么还需要uWSGI

- **因为nginx具备优秀的静态内容处理能力，然后将动态内容转发给uWSGI服务器，这样可以达到很好的客户端响应。将静态的映射到服务器上**