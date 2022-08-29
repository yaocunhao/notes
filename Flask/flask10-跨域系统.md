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
  ```

  