# 一、高效率http请求

- [gd](https://requests.readthedocs.io/en/latest/)

## 2.1 Session object

- 缓存会自动保留下来，传递到下一个请求之中

  ```python
  s = requests.Session()
  
  # cookies 被保存到 s对象之中
  s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
  # cookies 被带上
  r = s.get('https://httpbin.org/cookies') 
  
  print(r.text)
  # '{"cookies": {"sessioncookie": "123456789"}}'
  ```

- 需要注意的是，会话对象将持久化url请求自己设置的任何cookie，但如果您提供cookie作为参数，它将不会在下一次请求中持久化

  ```python
  s = requests.Session()
  
  # 自己提供的cookies 参数，将不会在下一次的请求之中
  r = s.get('https://httpbin.org/cookies', cookies={'from-my': 'browser'})
  print(r.text)
  # '{"cookies": {"from-my": "browser"}}'
  
  r = s.get('https://httpbin.org/cookies')
  print(r.text)
  # '{"cookies": {}}'
  ```

- 增强request请求

  - 增强请求效果一般需要进行如下几步

    - 复用tcp链接，减少握手消耗

    - 设置http超时、重试次数

      ```python
      # 1、定义session对象
      session = requests.Session()
      # 2、定义重试数据
          retry = Retry(
              total=retries,
              read=retries,
              connect=retries,
              backoff_factor=backoff_factor,
              status_forcelist=status_forcelist,
          )
        # 3、定义http适配器
          adapter = HTTPAdapter(max_retries=retry)
        # 4、前缀挂载
          session.mount('http://', adapter)
          session.mount('https://', adapter)
      ```

- [cookies 复用](https://blog.csdn.net/qq_25986923/article/details/105332640)

  ```python
  # 以下代码纯为了举例，没有效果的伪代码
  import requests
  # 登陆接口
  response1 = requests.get(url_login,params,headers)
  # 获取cookies信息
  cookies = response.cookies
  # 得到的cookies 是一个字典类型
  cookie = cookies.get("cookies的key")
  # 请求 查询接口
  response2 = requests.get(search_url,params,headers,cookies=cookie)
  # 查看查询响应的结果
  response2.json()
  
  ----------------------------------------------------------------
  
  # 使用session 代码
  import requests
  # 获取 session对象
  session = requests.session()
  # 登陆接口
  response1 = session.get(url_login,params,headers)
  # 请求 查询接口
  response2 = session.get(search_url,params,headers)
  # 查看查询响应的结果
  response2.json()
  
  ```

  

- [tcp 复用验证 ](https://stackoverflow.com/questions/24873927/python-requests-module-and-connection-reuse)
