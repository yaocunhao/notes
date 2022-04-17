## pipenv管理你的虚拟环境

[pipenv run](https://www.jianshu.com/p/d06684101a3d)

[virtual、venv、pipenv之间的关系](https://blog.csdn.net/weixin_40922744/article/details/103721870?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164869285716782246412791%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=164869285716782246412791&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-103721870.142^v5^pc_search_result_control_group,143^v6^control&utm_term=pipenv+%E5%92%8C+virtualenv+%E4%B9%8B%E9%97%B4%E7%9A%84%E5%85%B3%E7%B3%BB&spm=1018.2226.3001.4187)

### 1.下载安装

```bash
pip install pipenv
```

### 2.查看版本

```bash
pipenv --version
```

### 3.创建虚拟环境

```bash
# 创建与本机相同python版本的虚拟环境
pipenv install

# 指定版本创建并初始化虚拟环境（如果你本机装的是3.7版本python，那么你无法创建3.6的虚拟环境，巨坑）
pipenv install --python 3.6
```

### 4.激活虚拟环境

```bash
pipenv shell
```

### 5.退出虚拟环境

```bash
exit
```

### 6.移除虚拟环境

```bash
# 删除当前虚拟环境
pipenv --rm
```

### 7.查看当前虚拟环境目录

```bash
# 查看当前项目的虚拟环境目录
pipenv --venv

# 查看当前虚拟环境python解释器路径
pipenv --py
```

### 8.查看安装的所有包及依赖包

```bash
pipenv graph
```

### 9.安装第三方包

```bash
# 安装并加入到Pipfile文件packages自动lock
pipenv install requests

#  安装并加到Pipfile中dev-packages自动lock
pipenv install --dev requests

# 冻结软件包名称及其版本以及其自己的依赖关系的列表
pipenv lock
```

### 10.卸载全部包并从`Pipfile`文件中移除

```bash
pipenv uninstall --all
```

### 11.使用创建好的`Pipfile`文件安装虚拟环境

```bash
# 安装lock文件中packages中的所有包
pipenv sync

# 安装lock文件中packages(依赖项)中的所有包，加上dev-packages(开发环境)中的包
pipenv sync --dev

# 安装pipfile中的所有包（如果已存在pipfile文件）
pipenv install

# 安装pipfile中的所有包，加上dev-packagas中的包
pipenv install --dev

# 安装pipfile中的所有包，略过lock步骤
pipenv install --skip-lock
```



## 12.在真实环境中使用虚拟环境中包并运行

[参考链接](https://blog.csdn.net/wzp7081/article/details/111503711?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164869350016782089399042%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=164869350016782089399042&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-111503711.142^v5^pc_search_result_control_group,143^v6^control&utm_term=pipenv+run+xxx+package+all+&spm=1018.2226.3001.4187)

```python
场景如下：
假设正式环境中为一个干净的仓库,有且仅有初始的包
虚拟环境中有所需要的第三方包。
如何实现在真实环境中使用虚拟环境中的第三方包并运行

pipenv run python xxx.py
```

# Pipfile

## 2.1 内容解析

- source用来设置仓库地址，即从哪下载虚拟环境所需要的包
- packages用来指定项目依赖的包，即你安装了的包
- dev-packages用来指定开发环境需要的包，这类包只用于开发过程，不用与生产环境，比如单元测试相关的包，只在开发阶段有用，这样分开便于管理。
- requires 中指定目标Python版本

## 2.2  不要手动修改Pipfile.lock

- 用来规定的基础上，目前在包装Pipfile，应该使用那些特定版本，避免依赖于对方破坏你的项目的依赖关系树自动升级包的风险。该文件用来保证包的完整性。记住，任何情况下不要手动修改该文件		