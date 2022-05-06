[参考链接](https://blog.csdn.net/zxbylx1120471286/article/details/107187517?ops_request_misc=&request_id=&biz_id=102&utm_term=pipevn%20%20%E5%92%8C%20conda%E7%9A%84%E5%8D%B4%E5%88%AB&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-107187517.142^v9^control,157^v4^control&spm=1018.2226.3001.4187)

# 一、目的

- pyhon是不兼容的，不同的版本使用的库，包括python本身是不一样的，因此需要使用虚拟环境进行项目的隔离
- 为了保证项目在不同机器上也可以正常运行，因此出现了一些环境管理工具



# 二、Virtualenv 

- 新建虚拟环境(创建虚拟环境目录)
  - python3 -m venv `name` （`python -m venv . `虚拟环境配置就会创建在当当前文件夹下，而不是在name文件夹下）
  - name文件夹里面就是解释器和需要的库文件夹

- 激活虚拟环境

  - source venv/bin/activate

- 退出虚拟环境

  - deactivate

- 复制或者导出虚拟环境

  - 在虚拟环境内输入 pip freeze > requirements.txt 

  - 注意，这个在哪个目下输入，requirements.txt文件就在当前目录生成，包含库和对应的版本号，**每运行一次，会覆盖上一次生成的文件，所以这个文件不是自动更新的**

  - 新建项目，**创建并且激活虚拟环境**，拷贝requirements.txt 文件到bin目录下，然后执行对应的命令，就将对应的环境导入到当前项目了

    ```python
    pip install -r requirements.txt
    ```

# 三、Pipenv

- Pipfile 文件相当于 requirements文件，但是这个文件是自动更新的

# 四、Conda

[官网参考链接](https://blog.csdn.net/weixin_34297704/article/details/86467702?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165113717716781483781284%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165113717716781483781284&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-86467702.142^v9^control,157^v4^control&utm_term=conda%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B&spm=1018.2226.3001.4187)

[参考链接](https://blog.csdn.net/weixin_43216928/article/details/117079124?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165110436216781432973397%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165110436216781432973397&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-5-117079124.142^v9^control,157^v4^control&utm_term=Anaconda&spm=1018.2226.3001.4187)