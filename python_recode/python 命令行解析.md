- [参考链接](https://blog.csdn.net/yy_diego/article/details/82851661)

- [参考链接](https://blog.csdn.net/qq_41731861/article/details/120827681)

- [官方文档](http://blog.xiayf.cn/2013/03/30/argparse/)

- [大概游览概括](https://blog.csdn.net/qq_34243930/article/details/106517985?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165223501316781818750618%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165223501316781818750618&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-106517985-null-null.142^v9^control,157^v4^control&utm_term=add_argument&spm=1018.2226.3001.4187)

- argparse 是python自带的[命令行](https://so.csdn.net/so/search?q=命令行&spm=1001.2101.3001.7020)参数解析包，可以用来方便地读取命令行参数

  ```python
  import argparse
  
  
  def main():
      # 根据argparse包中的ArgumenParser 类生成一个对象，description 是描述这个参数解析器的作用
      # python main.py --help 将会显示这个信息
      parser = argparse.ArgumentParser(description="Demo of argparse")
  
      # 增加参数
      # -n 和 --name 是同一个参数
      parser.add_argument('-n', '--name', default=' Li ')
      parser.add_argument('-y', '--year', default='20')
  
      # 解析参数
      args = parser.parse_args()
      print(args) # Namespace(name=' Li ', year='20') # -、--同时出现，默认采用后者作为参数名
  
      name = args.name
      year = args.year
  
      print('Hello {}  {}'.format(name, year)) # Hello  Li   20
  
  ```

  - **当`'-'`和`'--'`同时出现的时候，系统默认后者为参数名，前者不是，但是在命令行输入的时候没有这个区分**

    ```bash
    # 在前面
    (venv) didi@DIDI-FVFDHCREP3XY pythonProject % python main.py -n 'tom' --name 'jim' -y 
    Namespace(name='jim', year='1000')
    Hello jim  1000
    
    # -- 在前面
    (venv) didi@DIDI-FVFDHCREP3XY pythonProject % python main.py  --name 'jim' -n 'tom'  -y '1000'
    Namespace(name='tom', year='1000')
    Hello tom  1000
    
    # 可以看到在命令行中 - 和 -- 同时存在的时候，谁后输入采用的就是谁
    ```

- default 

  - 没有设置值的时候使用默认参数

- required

  - 表示这个参数是一定要设置

- type

  - 参数类型

- choices

  - 参数只能从几个选项里面选

- help

  - 指定参数的说明信息

- dest

  - 设置参数的变量名，然后在代码中yong args.xxx 来进行读取

- nargs

  - 设置参数在使用时可以提供的个数




## 子命令

[参考链接](https://blog.csdn.net/qq_41629756/article/details/105689494?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164974732916782246485787%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164974732916782246485787&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-105689494.142^v7^control,157^v4^control&utm_term=add_subparsers&spm=1018.2226.3001.4187)

## 参数解析

[参考链接](https://blog.csdn.net/weixin_44626952/article/details/102913431)

- **关键字action**控制匹配到命令行选项后的行为








# 一、argparse 模块使用介绍

- argparse 使用流程

  ```python
  导入argparse包；
  
  创建ArgumentParser()参数对象；# 可以填入帮助信息 运行时 -h显示帮助信息
  
  调用add_argument()方法往参数对象中添加参数；
  
  使用parse_args()解析添加参数的参数对象，获得解析对象；程序的其他部分需要使用命令行参数时，用解析对象.参数获取。
  ```

- 示例一

  ```python
  import argparse
  
  
  def main():
      # 根据argparse包中的ArgumenParser 类生成一个对象，description 是描述这个参数解析器的作用
      # python main.py --help 将会显示这个信息
      parser = argparse.ArgumentParser(description="Demo of argparse")
  
      # 增加参数
      # -n 和 --name 是同一个参数 && 为可选参数(如果直接 n 就是必须 参数)
      parser.add_argument('-n', '--name', default=' Li ') # 默认参数为 Li
      parser.add_argument('-y', '--year', default='20')
  
      # 解析参数
      args = parser.parse_args()
      print(args) # Namespace(name=' Li ', year='20') # -、--同时出现，默认采用后者作为参数名
  
      name = args.name
      year = args.year
  
      print('Hello {}  {}'.format(name, year)) # Hello  Li   20
  
  main()
  
  
  # 一、显示帮助信息，-h / -help 如果这样调用则不能传入其它参数
  
  #  python main.py -h
  #  usage: main.py [-h] [-n NAME] [-y YEAR]
  
  #  Demo of argparse
  
  #  optional arguments:
      -h, --help            show this help message and exit
      -n NAME, --name NAME
      -y YEAR, --year YEAR
  
  # 二、有默认参数
  
  # python main.py
  # Namespace(name=' Li ', year='20')
  # Hello  Li   20
  
  # 三、从命令行输入参数
  # python main.py -n 'jim' -y 111
  # Namespace(name='jim', year='111')
  # Hello jim  111
  ```

- 示例二

  - 验证必选参数

  ```python
  # 修改代码
  parser.add_argument('name', default=' Li ')
  
  # 不传参时报错
  # python main.py                
  # usage: main.py [-h] [-y YEAR] name
  # main.py: error: the following arguments are required: name
  
  # 默认读取参数，不需要带-
  # python main.py jim
  # Namespace(name='jim', year='20')
  # Hello jim  20
  
  ```

# 二、参数介绍

## 2.1 可选参数

- 通过在参数名前加`--`，设置为可选参数。如果未输入，则使用`default`默认值（若未设置`default`，则会默认赋值`None`）
- 可选参数还可以使用 `-`进行引用名的缩短

# 三、子命令

- 
