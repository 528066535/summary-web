
    Promise 是最早接触的一个语法，确实也给项目带来了不少方便的地方

### 一. 介绍

 #### Promise 特点

 1) Promise 有三种状态:pending（进行中）、fulfilled（已成功）和rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，
 任何其他操作都无法改变这个状态。

 2) 一旦状态改变，就不会再变，任何时候都可以得到这个结果。

 #### 注意：Promise 新建后就会立即执行。 哪怕只是声明，也立即执行。

 #### Promise 例子

 ```
 let promise1 = new Promise(function(resolve, reject) {
   console.log('Promise');
   resolve();
   console.log('123')
 });

 promise.then(function() {
   console.log('resolved.');
 });

 console.log('Hi!');

 //Promise
 //123
 //Hi!
 //resolved.
 ```

 #### catch

 catch 用于指定发生错误时的回调函数。

 ```
 // bad
 promise
   .then(function(data) {
     // success
   }, function(err) {
     // error
   });

 // good
 promise
   .then(function(data) { //cb
     // success
   })
   .catch(function(err) {
     // error
   });
 ```

 #### finally

 finally方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法是 ES2018 引入标准的。

 #### Promise.all()

 （1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。

 （2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

 ```
 const p = Promise.all([p1, p2, p3]);

 p.then(function (posts) {
   // ...
 }).catch(function(reason){
   // ...
 });
 ```

 #### Promise.race()

 Promise.race方法的参数与Promise.all方法一样，只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。

### 二. Event Loop

 由于 JS 是单线程，同一时间只能处理一件事情，不能既处理DOM，又执行其他js。所以所有的同步任务都会放在一个队列里面执行。但是有些比如网络请求，必须等待请求结果才能执行，
 所以又有了异步任务。所以除了主线程，还有异步任务的"任务队列"。

 #### 任务执行过程

 1）所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。

 2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。

 3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，结束等待状态，进入执行栈，开始执行。

 4）主线程不断重复上面的第三步。这个过程也叫做 Event Loop（事件循环）。

 #### 微任务（micro task）和宏任务（macro task）

 不同的异步任务被分为两类：微任务（micro task）和宏任务（macro task）。

 以下事件属于宏任务：

 * setInterval()
 * setTimeout()

 以下事件属于微任务

 * new Promise()
 * new MutaionObserver()

 我们只需记住当当前执行栈执行完毕时会立刻先处理所有微任务队列中的事件，然后再去宏任务队列中取出一个事件。同一次事件循环中，微任务永远在宏任务之前执行。

 ```
 setTimeout(function () {
     console.log(1);
 });

 new Promise(function(resolve,reject){
     console.log(2)
     resolve(3)
 }).then(function(val){
     console.log(val);
 })

 //结果为： 2 3 1
 ```

### 三. Promise 封装 请求

 上一章讲了[XMLHttpRequest](/xml.md)，这里就直接引用上一章封装的 http 和 doUpload 拿来再封装一层，自己实现一套网络请求。

 ```
 import http from 'xxx'
 import doUpload from 'xxx'

 let post = (url, data) => {
    return new Promise((resolve, reject)=>{
        http({url, data, (res)=>{
            resolve(res.data);
        }, (res)=>{
            reject(res.data);
        }, type: 'POST'})
    })
 }

 let get = (url, data) => {
     return new Promise((resolve, reject)=>{
         http({url, data, (res)=>{
             resolve(res.data);
         }, (res)=>{
             reject(res.data);
         }, type: 'GET'})
     })
 }

 let httpWithMethod = (url, data, type) => {
     return new Promise((resolve, reject)=>{
         http({url, data, (res)=>{
             resolve(res.data);
         }, (res)=>{
             reject(res.data);
         }, type: type})
     })
  }

 let upload = (url, file, progress=()=>{}) => {
    return new Promise((resolve, reject) => {
        let multiple = Object.prototype.toString.call(files) == '[object Array]' &&
        doUpload(url, file, progress,(res) => {
            resolve(res);
        },(error) => {
            reject(error);
        }, multiple)
    })
 }

 let apiUrlList = {
    normal: {
        userInfo: ['/index/info', 'get']
    },
    login: {
        login: ['/login', 'post']
    }
 }

 function link(url) {
     let realUrl = url;
     window.open(realUrl, 'download');
 }

 let apiActionList = {};

 Object.keys(apiUrlList).forEach(apiModel=>{
    apiActionList[apiModel] = {};

    Object.keys(apiUrlList[apiModel]).forEach((apiItem) => {
        let url = apiUrlList[apiModel][apiItem][0];
        let type = apiUrlList[apiModel][apiItem][1]?apiUrlList[apiModel][apiItem][1]:'';
        apiActionList[apiModel][apiItem] = ((url, type)=>{
            if(type == 'get') {
                return get(url, arguments[0]);
            }
            else{
                return post(url, arguments[0]);
            }
        })(url, type);
    })
 })

 export default {
     action: apiActionList,
     link: link,
     upload: upload,
     apiUrlList:apiUrlList,
     httpWithMethod: httpWithMethod
 }
 ```


