# 一、是什么

- [参考链接](https://zhuanlan.zhihu.com/p/387855002)

```python
# Docker map backend service image
FROM python:3.6.4 # 初始化一个新的构建阶段，并设置基础映像：

WORKDIR /app/map_server # 工作目录，如果WORKDIR不存在，即使它没有在后续Dockerfile指令中使用，它也会被创建

COPY . . # COPY指令只能从执行docker build所在的主机上读取资源并复制到镜像中

RUN set -x \ # 将在当前镜像之上的新层中执行命令，在 docker build时运行
    && apt-get update  \
    && apt-get install -y vim \
    && python3 -m pip install pipenv \
    && pipenv install --system --deploy --ignore-pipfile

VOLUME /app/logs # 创建一个具有指定名称的挂载数据卷

EXPOSE 8000 # Docker 容器在运行时侦听指定的网络端口。可以指定端口是监听TCP还是UDP，如果不指定协议，默认为TCP

ENTRYPOINT ["uwsgi"] # 指定容器启动程序及参数
CMD ["-y", "deploy_conf/uwsgi.yaml"] # 运行程序，在docker run 时运行，但是和run命令不同，RUN 是在 docker build时运行
```

- RUN 在构建docker镜像的时候就会运行
- CMD 在docker 容器启动的时候才会运行



# 二、docker镜像打包

- 镜像构建：docker build -t `image_name` `file_name` 
- 镜像导出：docker save `image_name:version`| gzip > xxx.tgz
- 镜像导入：docker load -i ` xxx.tgz`

