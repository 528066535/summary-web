
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

### 二. 封装 XMLHttpRequest

 上一章讲了[XMLHttpRequest](/xml.md)，这里就直接引用上一章封装的 http 拿来再封装一层，实现 Promise。


