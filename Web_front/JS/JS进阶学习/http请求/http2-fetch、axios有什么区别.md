# 一、Fetch 和 Axios 的区别

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


# 二、Fetch 的使用方法

- [link](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)

- `fetch` 接口在很多情况下返回的都是`Promise` 对象, 并且只可以读取一次

  ```js
  var url = "http://127.0.0.1:6001/test42"
  fetch(url,{"method":"GET"}).then(
    resp => {
      console.log("Sucess!!")
      console.log(resp.status) // 状态码
      console.log(resp.statusText) // 状态码解释
      // 注意返回的是Promise 对象，并且只可以读取一次
      // resp.text().then(data=>console.log(data))
      // resp.json().then(data=>console.log(data))
  
  
      // 请求头接口返回的是迭代器
      var hs = resp.headers.entries()
      for(var h of hs) {
        console.log(h)
      }
    }
  ).catch(
    err=>{
      console.log("请求失败!!")
      console.log(err)
    }
  )
  ```

  