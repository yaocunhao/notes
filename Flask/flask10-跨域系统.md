[解决跨域方法](https://blog.csdn.net/qq_42778001/article/details/101436742?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166142368116782395327171%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166142368116782395327171&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-101436742-null-null.142^v42^pc_rank_34,185^v2^control&utm_term=flask_cors%20import%20CORS&spm=1018.2226.3001.4187)

- [验证方法](https://blog.csdn.net/qq_37290531/article/details/115079764?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-115079764-blog-106764581.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-115079764-blog-106764581.pc_relevant_default&utm_relevant_index=2)

  ```python
  var xhr = new XMLHttpRequest();
  xhr.open("GET", "要测试的接口地址");
  xhr.send(null);
  xhr.onload = function(e) {
   var xhr = e.target;
   console.log(xhr.responseText);
  }
  
  var xhr = new XMLHttpRequest();
  xhr.open("GET", "http://test.com:6001/pikachu/api/constraint/get/single?id=7");
  xhr.send(null);
  xhr.onload = function(e) {
   var xhr = e.target;
   console.log(xhr.responseText);
  }
  
  # 如果请求失败，则线直接在游览器之中请求，再在当前页面下进行此操作
  ```
  

[link](https://blog.csdn.net/zlsdmx/article/details/103541432?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166530239816800186530250%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166530239816800186530250&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-103541432-null-null.142^v52^pc_rank_34_2,201^v3^control_1&utm_term=%E8%B7%A8%E5%9F%9F%E6%B5%8B%E8%AF%95&spm=1018.2226.3001.4187)