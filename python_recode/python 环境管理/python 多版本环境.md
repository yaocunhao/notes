# 一、多版本的安装

- 安装完之后， 需要在/usr/bin 修改对应的软链接，将可执行程序放在/usr/local/bin 之下



# 二、python多版本共存

- 下载pip3.9 `python39 -m pip install --upgrade pip --force-reinstall`
- 下载pipenv
  - pip3.6 install pipenv : 下载的是pipenv3.6 
  - pip3.9 install env: 下载pipenv3.9 

- pipenv 采用的是3.6， 3.9的pipenv在/usr/local/bin 之下
- pipenv 发生错误notype可能是[版本问题](https://github.com/pypa/pipenv/issues/4829)，请使用pipenv==`2021.5.29`