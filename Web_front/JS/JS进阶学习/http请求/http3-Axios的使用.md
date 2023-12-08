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

  