# 一、项目管理

- npm 是什么
  - npm 是 Node Package Manager 的缩写，是一个用于管理和共享 JavaScript 代码的包管理工具。<font color=yellow>它是 Node.js 平台的默认包管理器</font>，也是世界上最大的开源软件注册表之一。通过 npm，开发者可以安装、发布和共享代码包，方便地管理项目的依赖关系和版本控制
- [package.json 文件作用](https://blog.csdn.net/song_6666/article/details/123739622?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169436154216800182751940%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169436154216800182751940&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-123739622-null-null.142^v93^chatgptT3_1&utm_term=package.json%20%E6%98%AF%E4%BB%80%E4%B9%88&spm=1018.2226.3001.4187)
  - package.json 文件其实就是对项目或者模块包的描述，里面包含许多元信息。比如项目名称，项目版本，项目执行入口文件，项目贡献者等等。npm install 命令会根据这个文件下载所有依赖模块
  - package.json 文件创建有两种方式，手动创建或者自动创建
    - 手动创建： 直接在项目根目录新建一个 package.json 文件，然后输入相关的内容
    - 自动创建： 也是在项目根目录下执行 npm init，然后根据提示一步步输入相应的内容完成后即可自动创建

- [link](https://cloud.tencent.com/developer/article/2218912)
- [库的导入方式](https://m.php.cn/faq/523134.html)
  - 源码引用，script/import 标签导入
  - npm 安装， import导入