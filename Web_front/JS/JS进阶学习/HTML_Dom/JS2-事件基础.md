# 一、什么是事件基础

![image-20230103004522085](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030045168.png)

# 二、事件三要素

- 事件由三部分组成
  - 事件源、事件类型、事件处理程序![image-20230103004644576](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030046631.png)

- 事件执行步骤![image-20230103004707734](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030047812.png)

- 常见鼠标事件![image-20230103004757485](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202301030047570.png)

- [`addEventListener` 事件监听](https://blog.csdn.net/handsomeAndHero/article/details/125315491?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522170183062716800211595549%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=170183062716800211595549&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-125315491-null-null.142^v96^pc_search_result_base3&utm_term=addEventListener&spm=1018.2226.3001.4187)
  - [第三个参数的含义](https://blog.csdn.net/m0_47807991/article/details/124407389?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522170183150116800197057699%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=170183150116800197057699&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-124407389-null-null.142^v96^pc_search_result_base3&utm_term=addEventListener%20%E7%AC%AC%E4%B8%89%E4%B8%AA%E5%8F%82%E6%95%B0%E7%9A%84%E5%90%AB%E4%B9%89&spm=1018.2226.3001.4187)
    - true 事件处理函数是在事件的捕获阶段发生的
    - false 事件处理函数是在事件的冒泡阶段发生的（默认值，如果不写默认为false）


- [JavaScript的事件冒泡与事件捕获](https://blog.csdn.net/weixin_43715214/article/details/129415062?ops_request_misc=&request_id=&biz_id=102&utm_term=%E4%BA%8B%E4%BB%B6%E6%8D%95%E8%8E%B7%E6%98%AF%E4%BB%8E%E7%88%B6%E5%88%B0%E5%AD%90&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-129415062.142^v96^pc_search_result_base3&spm=1018.2226.3001.4187)

  ```html
  // 事件传递测试代码
  
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
  </head>
  
  <body>
  
    <div id="btn1">
      <button> btn1 </button>
      <div id="btn2">
        <button>btn2</button>
      </div>
  
  </body>
  <script>
    document.getElementById("btn1").addEventListener("click", () => { console.log("aa") }, false)
    document.getElementById("btn1").addEventListener("click", () => { console.log("bb") }, true)
  
    document.getElementById("btn2").addEventListener("click", () => { console.log("cc") }, true)
    document.getElementById("btn2").addEventListener("click", () => { console.log("dd") }, true)
  </script>
  
  </html>
  ```

  

- [三个阶段](https://blog.csdn.net/JQuery_QQ/article/details/51448842?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-51448842-blog-124407389.235%5Ev39%5Epc_relevant_3m_sort_dl_base1&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-51448842-blog-124407389.235%5Ev39%5Epc_relevant_3m_sort_dl_base1&utm_relevant_index=2)

  -    当鼠标点击所看到的的按钮时，其实发生了一系列的事件传递，可以想象一下，button实际上是被body“包裹”起来的，body是被html“包裹”起来的，html是被document“包裹”起来的，document是被window“包裹”起来的。所以，在你的鼠标点下去的时候，最先获得这个点击的是最外面的window，然后经过一系列传递才会传到最后的目标button，当传到button的时候，这个事件又会像水底的泡泡一样慢慢往外层穿出，直到window结束

  - 综上，一个事件的传递过程包含三个阶段，分别称为：捕获阶段，目标阶段，冒泡阶段

    ```js
      function onClickFn(event) {
        var tagName = event.currentTarget.tagName;
        var phase = event.eventPhase; （3：冒泡阶段，2：目标阶段，1：捕获阶段）
        console.log(tagName, phase);
      }
    ```

  - [link](https://blog.csdn.net/qq_42039970/article/details/88323274?ops_request_misc=&request_id=&biz_id=102&utm_term=js%20%E4%BA%8B%E4%BB%B6%E8%A2%AB%E6%8D%95%E8%8E%B7%E4%BA%86%E5%B0%B1%E4%B8%8D%E4%BC%9A%E5%BE%80%E4%B8%8B%E4%BC%A0%E9%80%92%E4%BA%86%E5%90%97&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-88323274.142^v96^pc_search_result_base3&spm=1018.2226.3001.4187)

    - 捕获阶段（capture phase）：在捕获阶段时从最外层的祖先元素（window对象），向目标元素进行事件的捕获，但是默认此时不会触发事件。事件捕获过程中，document对象首先接受click事件，然后事件沿DOM树依次向下，一直传播到事件的实际目标，即div元素。<font color=yellow>传递到事件的实际目标，因此不会捕获的方式一旦到达目标，则不会继续向下传递</font>
    - 目标阶段（target phase）：事件捕获到目标元素，捕获结束开始在目标元素上触发事
    - 冒泡阶段（bubble phase）：事件从目标元素向他的祖先元素传递，依次触发祖先元素上的事件；（通俗来讲就是元素A中有子元素B，B元素的事件触发了，那么A元素相同的事件也会触发）
