- [link](https://blog.csdn.net/ASHIYI66/article/details/127956515?ops_request_misc=&request_id=&biz_id=102&utm_term=Axios&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-127956515.142^v96^pc_search_result_base3&spm=1018.2226.3001.4187)

  ```js
  
  import axios from "axios";
  
  
  
  function axios_send() {
    axios({
      method:'GET',
      url:'http://www.baidu.com'
    }).then(response => console.log(response), err => console.log("请求失败"+err))
  }
  axios_send()
  
  ```

  - axios 是node模块的包，在游览器中想要被识别可以通过下列方式
  
    ```
    在JavaScript中使用axios进行网络请求是一种常见的做法。axios是一个基于Promise的HTTP客户端，适用于node.js和浏览器。要在浏览器中使用axios，你可以通过以下几种方法之一来引入和使用它：
    
    1. 通过CDN引入
    最简单的方式是通过CDN（内容分发网络）引入axios。这允许你在不需要下载和安装axios包的情况下使用它。你只需在HTML文件的<head>部分或在需要使用axios之前的位置，添加一个<script>标签来引入axios的CDN链接。例如：
    
    html
    <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
    这行代码会从jsDelivr CDN加载压缩过的axios库。加载后，axios库会在全局范围内可用，这意味着你可以在任何脚本中使用axios进行HTTP请求。
    
    2. 使用模块导入
    如果你的项目配置允许使用ES模块导入（通常在使用现代JavaScript框架或构建工具如Webpack、Parcel等情况下），则可以直接在JavaScript文件中导入axios。首先，你需要通过npm或yarn将axios安装到你的项目中：
    
    bash
    npm install axios
    # 或者
    yarn add axios
    然后，在JavaScript文件中通过import语句导入axios：
    
    javascript
    import axios from 'axios';
    
    // 使用axios
    axios.get('https://api.example.com/data')
      .then(response => console.log(response.data))
      .catch(error => console.error(error));
    3. 使用构建工具
    如果你的项目中使用了构建工具（如Webpack、Rollup或Parcel），你可以将axios作为项目依赖安装，并在项目中正常导入使用。构建工具会负责处理axios的打包和在浏览器中的使用。
    
    确保你已经通过npm或yarn将axios添加到了你的项目依赖中，然后在项目的JavaScript文件里按需导入。
    
    注意事项
    当通过CDN引入axios时，axios会自动成为全局变量，可以直接在任何地方使用。
    使用ES模块或构建工具时，需要确保你的项目配置支持ES模块导入。
    如果你在使用TypeScript，可能需要安装axios的类型定义文件（@types/axios），以便获得类型提示和代码补全功能。
    选择哪种方式取决于你的项目需求和配置。对于简单的项目或原型开发，通过CDN引入可能是最快捷方便的方法。对于更复杂的应用程序，使用模块导入和构建工具会更加灵活和强大。
    ```
  
    