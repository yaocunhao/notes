# 一、Promise 对象

- [link](https://www.liaoxuefeng.com/wiki/1022910821149312/1023024413276544)

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
          resolve('200 OK');
      }
      else {
          log('call reject()...');
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

new Promise(test).then(function (result) {
    console.log('成功：' + result);
}).catch(function (reason) {
    console.log('失败：' + reason);
});

```

