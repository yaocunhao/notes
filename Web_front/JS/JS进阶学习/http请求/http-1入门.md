# 一、Promise 对象

- [link-廖雪峰](https://www.liaoxuefeng.com/wiki/1022910821149312/1023024413276544)
- [link2-原理模拟实现](https://blog.csdn.net/wz__QWERTY/article/details/123975234?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-1-123975234-blog-127197204.235^v38^pc_relevant_default_base3&spm=1001.2101.3001.4242.2&utm_relevant_index=4)
- [link3-结构清晰](https://zhuanlan.zhihu.com/p/26523836)

## 1.1 基础概念

- Promise的两个特点
  - Promise对象的状态不受外界影响：<font color=yellow>pending 初始状态、fulfilled 成功状态、rejected 失败状态</font>>。Promise 有以上三种状态，只有异步操作的结果可以决定当前是哪一种状态，其他任何操作都无法改变这个状态
  - Promise的状态一旦改变，就不会再变，任何时候都可以得到这个结果，状态不可以逆，只能由 pending变成fulfilled或者由pending变成rejected
- Promise 的三个缺点
  - 无法取消Promise,一旦新建它就会立即执行，无法中途取消
  - 如果不设置回调函数，Promise内部抛出的错误，不会反映到外部
  - 当处于pending状态时，无法得知目前进展到哪一个阶段，是刚刚开始还是即将完成

## 1.2 基本用法

- 构造实例
  - 创建(new)Promise 对象，该对象接收一个函数为参数
  - 构造函数接受一个函数作为参数
  - 调用构造函数得到实例的同时，作为参数的函数会立即执行
  - <font color=yellow>参数函数接受两个回调函数参数resolve和reject</font>
  - 在参数函数被执行的过程中，如果在其内部调用resolve，会将实例的状态变成fulfilled，或者调用reject，会将实例的状态变成rejected
  - <font color=yellow>resolve 或者 reject 中填充的值就是返回值</font>
- 调用then
  - 调用.then可以为实例p注册两种状态回调函数
  - 当实例p的状态为fulfilled，会触发第一个函数执行
  - 当实例p的状态为rejected，则触发第二个函数执行

​	



## 1.3 Promise Api

- promise的内容分为构造函数、实例方法和静态方法
  - 1个构造函数： new Promise
    - new Promise能将一个异步过程转化成promise对象。先有了promise对象，然后才有promise编程方式
  - 2个实例方法：.then 和 .catch
    - .then用于为promise对象的状态注册回调函数。它会返回一个promise对象，所以可以进行链式调用，也就是.then后面可以继续.then。在注册的状态回调函数中，可以通过return语句改变.then返回的promise对象的状态，以及向后面.then注册的状态回调传递数据；也可以不使用return语句，那样默认就是将返回的promise对象resolve
    
      ```js
      // 注意： 
      // 1. then的真实参数是 then(resolve, rejection)
      // 2.因此当没有写catch 时，可以在then中写失败的方法，具体如下所示
      
      function test(resolve, reject) {
        var timeOut = Math.random() * 2
        setTimeout( function() {
          if (timeOut < 1) {
            console.log("Success!!")
            resolve("success!!")
          }
          else {
            console.log("Faild")
            reject("faild!!")
          }
        }
        )
      }
      new Promise(test).then(result=> console.log("promise success!!" + result),err => console.log("promise faild"+err))
      
      ```
    
      
    - .catch用于注册rejected状态的回调函数，同时该回调也是程序出错的回调，即如果前面的程序运行过程中出错，也会进入执行该回调函数。同.then一样，也会返回新的promise对象
    
      - Promise.prototype.catch方法是.then(null, rejection)的别名，用于指定发生错误时的回调函数
  - 4个静态方法：Promise.all、Promise.race、Promise.resolve和Promise.reject
    
    - 调用Promise.resolve会返回一个状态为fulfilled状态的promise对象，参数会作为数据传递给后面的状态回调函数
    - Promise.reject与Promise.resolve同理，区别在于返回的promise对象状态为rejected
- 

## 1.4 实践

```js
// 输出log到页面:
function log(s) {
  console.log("这里是log信息",s)
}

function test(resolve, reject) {
  var timeOut = Math.random() * 2;
  log('set timeout to: ' + timeOut + ' seconds.');
  setTimeout(function () {
      if (timeOut < 1) {
          log('call resolve()...');
        // 执行成功，填充返回值
          resolve('200 OK');
      }
      else {
          log('call reject()...');
        // 执行失败，填充返回值
          reject('timeout in ' + timeOut + ' seconds.');
      }
  }, timeOut * 1000);
}


// 创建一个承诺对象.变量p1是一个Promise对象，它负责执行test函数
var p1 = new Promise(test);

// 由于test函数在内部是异步执行的，当test函数执行成功时，我们告诉Promise对象：
// 匿名函数，接收参数result:返回值
// 如果成功执行这个函数
var p2 = p1.then(function (result) {
    console.log('成功：' + result);
});

// 当test函数执行失败时，我们告诉Promise对象：
// 匿名函数，接收参数reason，失败原因
var p3 = p2.catch(function (reason) {
    console.log('失败：' + reason);
});


// Promise对象可以串联起来，所以上述代码可以简化为：
// result、reason 就是返回值， 这里是可以随意命名的
new Promise(test).then(function (result) {
    console.log('成功：' + result);
}).catch(function (reason) {
    console.log('失败：' + reason);
});

```

- 批量执行

  - [link](https://blog.csdn.net/Ge_Daye/article/details/131935682?ops_request_misc=&request_id=&biz_id=102&utm_term=js%20%E6%89%B9%E9%87%8F%E8%B0%83%E7%94%A8%E5%87%BD%E6%95%B0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-131935682.142^v93^chatgptT3_1&spm=1018.2226.3001.4187)

  - Promise.all 方法接收一个包含各个 Promise 对象的数组作为参数，返回一个新的 Promise 对象。当数组中所有的 Promise 对象都成功（即状态为 resolved）时，返回的 Promise 对象状态为 resolved，并将所有 Promise 对象的结果组成的数组作为参数传递给回调函数；如果其中任意一个 Promise 对象失败（即状态为 rejected），**返回的 Promise 对象状态为 rejected，并将第一个失败的 Promise 对象的错误信息作为参数传递给回调函数**

    ```js
    // 1s 后返回结构
    const func1 = () => {
      return new Promise(resolve => {
        setTimeout(() => {
          resolve('result1');
        }, 1000);
      });
    };
    
    const func2 = () => {
      return new Promise(resolve => {
        setTimeout(() => {
          resolve('result2');
        }, 500);
      });
    };
    
    const func3 = () => {
      return new Promise(resolve => {
        setTimeout(() => {
          resolve('result3');
        }, 1500);
      });
    };
    
    // romise.all 方法接收一个包含各个 Promise 对象的数组作为参数，返回一个新的 Promise 对象
    Promise.all([func1(), func2(), func3()])
      .then(results => {
        console.log(results); // ['result1', 'result2', 'result3']
      })
      .catch(error => {
        console.error(error);
      });
    
    ```

    

# 二、async 对象

- [link1](https://blog.csdn.net/weixin_43523043/article/details/126279284?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169410392516800188592661%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169410392516800188592661&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-126279284-null-null.142^v93^chatgptT3_1&utm_term=js%20async%E5%87%BD%E6%95%B0&spm=1018.2226.3001.4187)

## 2.1 基本介绍

- 是什么
  - async函数是异步的一种方案，可以让异步的操作同步执行
- 返回值
  - async 函数返回一个 Promise 对象，可以使用 then 方法添加回调函数

## 2.2 测试

- 使用`async function`可以定义一个异步函数，异步函数和Promise可以看作是等价的，在async function内部，用`await`调用另一个异步函数，写起来和同步代码没啥区别，但执行起来是异步的

  ```js
  let resp = await fetch(url); // 自动实现了异步调用，它和下面的Promise代码等价：
  
  let promise = fetch(url);
  promise.then((resp) => {
      // 拿到resp
  })
  
  // 如果我们要实现catch()怎么办？用Promise的写法如下：
  let promise = fetch(url);
  promise.then((resp) => {
      // 拿到resp
  }).catch(e => {
      // 出错了
  });
  
  //用await调用时，直接用传统的try{ ... } catch：
  async function get(url) {
      try {
          let resp = await fetch(url);
          return resp.json();
      } catch (e) {
          // 出错了
      }
  }
  ```

  

- 普通函数里面如何调用async

  - 普通函数里面不能使用await 会报错，可以获得一个Promise 对象，然后 使用then语法获取结果。<font color=yellow>这里获取的是一个异步promise对象，因此先输出“run here，再输出函数结果”</font>

    ```js
    async function get(url) {
      return url
    }
    
     
    function helloAsync() {
      let promise = get("hello world"); // 得到Promise 对象
      promise.then(resolve=>{
        console.log(resolve)
      })
      console.log("run here!!")
    }
    
    helloAsync ();
    
    ```

- 异步函数里面await，<font color=yellow>会等待执行完成</font>

  ```js
  
  async function sleep(m) {
    return new Promise(resolve=>setTimeout(resolve,m))
  }
  
  async function get(url) {
    setTimeout(()=>{
      console.log("1s后执行")
    },1000)
    return url
  }
  
   
  async function helloAsync() {
    var x = await sleep(1000) // 会在此等待
    console.log(x)
    console.log("run here!!")
  }
  
  helloAsync();
  ```

  