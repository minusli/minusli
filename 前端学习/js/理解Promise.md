# 理解 Promise
参考：[你真完全掌握promise了吗？](https://juejin.im/post/5af29a62f265da0b8f628973)
- 基本使用：
    ```javascript
    var promise = new Promise(function(resolve, reject) {
        if (true /* 异步操作成功 */){
            resolve(value);
        } else {
            reject(error);
        }
    });
    ```
- 三种状态：pending，fulfilled，rejected
    - pending
    - fulfilled
    - rejected
- PromiseStatus和PromiseValue一经改变，则不再变化。
- promise链中，前一个func的返回是后一个func的输入（如果前一个func的返回是promise，则后一个func的触发取决于前一个func返回的promise的status，同时后一个func的输入则是前一个func返回的promise的value）
    ```javascript
    new Promise(f).then(return Promise()).then(console.log());
    ```
- promise_instance.then(func)
    - 返回一个新的promise（状态会重新计算，根据当前func决定）
        ```javascript
        var p1 = new Project(function(resolve){resolve(1);}).then(console.log);
        // p1:fulfilled
        
        var p2 = new Project(function(resolve){resolve(1);}).then(function(msg){throw "123";});
        // p2: rejected
        ```
    - fulfilled触发func（主动调用resolve or 返回非promise or 返回一个fulfilled的promise）
- promise_instance.catch(func)
    - 返回一个新的promise（状态会重新计算，根据当前func决定）
        ```javascript
        var p1 = new Project(function(resolve, reject){reject(1);}).catch(console.log);
        // p1:fulfilled

        var p2 = new Project(function(resolve, reject){reject(1);}).then(function(msg){throw "123";})  
        // p2: rejected
        ```
    - rejected触发func（主动调用reject or 异常 or 返回一个rejected的promise）
- promise_instance.finally(func)
    - 返回一个新的promise（状态会重新计算，根据当前func决定）
        ```javascript
        var p1 = new Project(function(resolve){resolve(1);}).then(console.log);
        // p1:fulfilled
  
        var p2 = new Project(function(resolve, reject){reject(1);}).then(function(msg){throw "123";});
        // p2: rejected
        ```
    - 只要不是pending，都会触发func
- Promise.all([promise...])
    - 返回新的promise（全部fulfilled，新promise才是fulfilled，value是所有promise的返回列表。只要有一个reject，则返回的promise是reject，value是第一个reject的value）
    - 有一个reject或者全部fulfilled才触发状态变化
- Promise.race([promise...])
    - 返回新的promise（状态和最先变化的promise一直，value也和其一样）
    - 最先变化状态promise触发新promise状态变化
- Promise.resolve(value)
    - value 不是promise，创建一个promise，状态是fulfilled；
    - value 是promise，则返回这个promise
- Promise.reject(value)
    - 创建一个promise，状态是reject
- promise链中，如果有其中一个节点状态reject，则不会再调用后续的then，而是直接调用catch。
    ```javascript
    p1().then(p2).then(p3)
      .then(function(data) {
        console.log('data: ' + data);
      })
      .catch(function(error) {
        console.log('error: ' + error);
      });
     
    function p1() {
      return new Promise(function(resolve, reject) {
        console.log('p1 resolved');
        resolve(123);
      });
    }
     
    function p2() {
      return new Promise(function(resolve, reject) {
        console.log('p2 rejected');
        reject(456);
      });
    }
     
    function p3() {
      return new Promise(function(resolve, reject) {
        console.log('p3 resolved');
        resolve(789);
      });
    }
    
    // 输出
    // p1 resolved
    // p2 rejected
    // error: 456
    ```
- 理解promise（先前知识：宏任务，微任务，EventLoop）：
```
console.log(1);  // 第一个宏任务(script)开始

const f = function () {
    return new Promise((resolve) => {
        setTimeout(function () {console.log(2)});  // 添加一个宏任务(setTimeout)
        console.log(3);  // 同步
        resolve(4);  // 同步, 把4放到一个地方去，等待then的func来调用
        console.log(5)  // 同步
    });
};

var p = f();

var s = new Date();
while(new Date() - s < 5000){} // 阻塞5s

p.then(function (res) {console.log(res)});  // 添加微任务(promise.then)

console.log(6);  // 当前宏任务(script)结束

/* 输出 */
1
3
5
6
4
2
```

