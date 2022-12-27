# 一、简介

- logging模块是Python内置的标准模块(**是一个模块，而不是某一个类，这个模块之中包含了其它的组件**)，主要用于输出运行日志，可以设置输出日志的等级、日志保存路径、日志文件回滚等；相比print，具备如下优点：
  - 可以通过**设置不同的日志等级**，在release版本中只输出重要信息，而不必显示大量的调试信息，**低于设置等级的日志不会输出**
    - DEBUG 细节信息，调试使用
    - INFO 正常信息，确认程序正常运行
    - WARNING 程序已经或者将要发生问题(小问题)
    - ERROR 程序出现错误，某些功能已经不能正常运行
    - CRITICAL 严重错误，程序已经不能运行
  - logging则可以由开发者决定**将信息输出到什么地方，以及怎么输出**

# 二、基本使用方法

## 2.1 记录日志到文件

- filemode 设置文件的输入模式，默认为 a

```python
import logging
# 记录日志到文件
# logging.basicConfig(filename='example.log', level=logging.DEBUG) # 记录的文件名称，时间级别

# logging.debug('This message should go to the log file')
# logging.info('So should this')
# logging.warning('And this, too')
# logging.error('And non-ASCII stuff, too, like Øresund and Malmö')

# #  从多个模块记录日志
# import logging
# import log

# def main():
#     logging.basicConfig(filename='myapp.log', level=logging.INFO) # 打开日志文件
#     logging.info('Started')
#     log.do_something()
#     logging.info('Finished')

# if __name__ == '__main__':
#     main()
```

## 2.2 从多个模块记录日志

- 在当前文件下包含其它模块，然后在其它模块之中直接进行日志输出即可输出到一个文件中，不需要再进行创建

  ```python
  # mylib.py
  import logging
  
  def do_something():
      logging.info('Doing something')
      
  # myapp.py
  import logging
  import mylib
  
  def main():
      logging.basicConfig(filename='myapp.log', level=logging.INFO)
      logging.info('Started')
      mylib.do_something()
      logging.info('Finished')
  
  if __name__ == '__main__':
      main()
  
  # 文件结果内容，可以看到都输出到了一个文件之中
  INFO:root:Started
  INFO:root:Doing something
  INFO:root:Finished
  ```

## 2.3 数据格式化

- 记录变量数据

  ```python
  import logging
  logging.warning('%s before you %s', 'Look', 'leap!')
  ```

- 更改显示消息格式

  ```python
  import logging
  logging.basicConfig(format='%(levelname)s:%(message)s', level=logging.DEBUG)
  logging.debug('This message should appear on the console')
  logging.info('So should this')
  logging.warning('And this, too')
  
  # DEBUG:This message should appear on the console
  # INFO:So should this
  # WARNING:And this, too
  ```

- 时间显示

  - 要显示事件的日期和时间，你可以在格式字符串中放置 '%(asctime)s'

  ```python
  logging.basicConfig(format='%(asctime)s %(message)s') # 2022-06-14 14:31:30,517 is when this event was logged.
  logging.warning('is when this event was logged.')
  ```

  -  进一步修改日志时间显示格式

    ```python
    logging.basicConfig(format='%(asctime)s %(message)s', datefmt='%m/%d/%Y %I:%M:%S %p') # %m:十进制表示月，%d表示日，%Y表示年， 十进制的(时分秒)，AM/PM
    
    logging.warning('is when this event was logged.') # 06/14/2022 02:33:36 PM is when this event was logged.
    ```

    

