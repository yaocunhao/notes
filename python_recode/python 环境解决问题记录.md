# 一、pipenv 加载包的时候容易出现一些问题

- 当出现某一些包出现无法安装的时候，找到对应的报错，如果还是无法解决需要考虑是否是python版本的问题，因为一些库的出现，在版本上可能会不兼容
- pipfile 中下载不了的文件，就pipenv install 单独下载，可能是网络的原因



# 二、如何在ubuntu下安装python3.6.4

[参考链接](http://t.zoukankan.com/aidenzdly-p-11351316.html)





# 三、问题记录

- apt-get 出现问题
  - 更换源即可
- dockerfile出现问题，直接加载即可
- python 出现问题，修改 /usr/local/bin 和 /usr/bin
- pipenv 出现问题 多个地方存在，删除即可