- [Fetch和Axios到底有什么区别](https://blog.csdn.net/weixin_48309048/article/details/126523603?ops_request_misc=&request_id=&biz_id=102&utm_term=fetch%E4%B8%8Eaxios%E7%9A%84%E5%8C%BA%E5%88%AB&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-126523603.142^v93^chatgptT3_1&spm=1018.2226.3001.4187)

  -  Axios是对XMLHttpRequest的封装，Fetch是一种新的获取资源的接口方式，并不是对XMLHttpRequest的封装

  - **最大的不同点在于Fetch是浏览器原生支持，而Axios需要引入Axios库**

  - 选择

    - 如果在项目中使用的话还是Axios使用起来方便
    -  如果是在浏览器控制台测试，或者想快速请求的话，可以使用Fetch，因为它不需要导入，是浏览器支持的

    ```js
    
    const options = {
      url: "http://example.com/",
      method: "POST",
      headers: {
        Accept: "application/json",
        "Content-Type": "application/json;charset=UTF-8",
      },
      data: {
        a: 10,
        b: 20,
      },
    };
     
    axios(options).then((response) => {
      console.log(response.status);
    ```

    