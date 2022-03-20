## pipenv管理你的虚拟环境

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

# 安装lock文件中packages中的所有包，加上dev-packages中的包
pipenv sync --dev

# 安装pipfile中的所有包（如果已存在pipfile文件）
pipenv install

# 安装pipfile中的所有包，加上dev-packagas中的包
pipenv install --dev

# 安装pipfile中的所有包，略过lock步骤
pipenv install --skip-lock
```