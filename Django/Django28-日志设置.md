- [参考链接](https://docs.djangoproject.com/en/4.1/howto/logging/)
- python 可在配置文件之中设置`LOGGIN` 来配置全局的日志
  - 如果记录器的map映射名称设置为''，则所有的日志(包括内容日志)都会采用该记录器

```python
LOGGING = {
    # 1、Django自身配置
    'version': 1,  # 字典配置的版本
    'disable_existing_loggers': False,  # 是否关闭默认的日志配置器

    # 2、处理器
    # 定义一个名为file的日志处理器
    'handlers': {
        'file': {
            'class': 'logging.FileHandler',  # 处理器本身将日志输出的位置
            'filename': 'general.log',  # 输出文件的名称
            'level': 'DEBUG',  # 定义处理器处理的日志等级(一个日志记录器可以有多个处理器)
            'formatter': 'verbose',  #格式器： 设置日志输出的格式
        },
        'file2': {
            'class': 'logging.StreamHandler',  # 输出到终端处理器
            'level': 'DEBUG',
            'formatter': 'simple'
        },
    },

    # 3、日志记录器对象
    # 该对象将处理的等级为DEBUG， 使用的处理器为’file'处理器
    # 此配置 ( '')未命名。这意味着它将处理来自所有记录器的记录
    # 注意： 记录器和处理器是一对对的，即一个记录器可以映射至多个处理器
    'loggers': {
        'test_name1': {
            'level': 'WARNING',  # 设置日志记录器的等级
            'handlers': ['file'],  # 设置日志记录器使用的
        },
        'test_name': {
            'level': 'DEBUG',
            'handlers': ['file2']
        }
    },

    # 4、格式化对象
    'formatters': {
        'verbose': {
            'format':
                '{name} {levelname} {asctime} {module} {process:d} {thread:d} {message}',
            'style':
                '{',
        },
        'simple': {
            'format': '{levelname} {message}',
            'style': '{',
        },
    },
}
```

