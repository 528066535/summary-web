
    跨域的时候没提到 XMLHttpRequest 如何跨域，这里会讲到。

### 一. XMLHttpRequest 1

 XMLHttpRequest 是一个浏览器接口，使得Javascript可以进行HTTP(S)通信。

 ```
 //新建实例
 var xhr = new XMLHttpRequest();

 xhr.open('GET', 'example.php');

 //发出一个HTTP请求
 xhr.send();

 //监控XMLHttpRequest对象的状态变化
 xhr.onreadystatechange = function(){
  　　　　if ( xhr.readyState == 4 && xhr.status == 200 ) {
  　　　　　　alert( xhr.responseText );
  　　　　} else {
  　　　　　　alert( xhr.statusText );
  　　　　}
  　　};
 ```

 #### 缺点

 * 只支持文本数据的传送，无法用来读取和上传二进制文件。

 * 传送和接收数据时，没有进度信息，只能提示有没有完成。

 * 受到"同域限制"（Same Origin Policy），只能向同一域名的服务器请求数据。

### XMLHttpRequest 2

 新版本针对以上缺点进行了优化

 #### * 可以设置HTTP请求的时限。

 ```
 xhr.timeout = 3000;
 xhr.ontimeout = function(event){
 　　alert('请求超时！');
 }
 ```

 #### * 可以使用FormData对象管理表单数据。

 ```
 var formData = new FormData();
 formData.append('username', '张三');
 formData.append('id', 123456);

 xhr.send(formData);

 //获取表单的值

 var form = document.getElementById('myform');
 var formData = new FormData(form);
 formData.append('secret', '123456'); // 添加一个表单项
 xhr.open('POST', form.action);

 xhr.send(formData);
 ```

 #### * 可以上传文件。

 ```
 <input type="hidden" value="" id="images"/>

 var currentFile = document.getElementById('input_upload_image').files;
 var files = currentFile;
 var formData = new FormData();

 for (var i = 0; i < files.length;i++) {
 　　formData.append('files[]', files[i]);
 }

 var xhr = new XMLHttpRequest();

 xhr.open('post', url);

 xhr.send(formData);
 ```

 #### * 可以请求不同域名下的数据（跨域请求）。

 ```
 var xhr = new XMLHttpRequest();
 xhr.open('get','http://wthrcdn.etouch.cn/weather_mini?city=杭州');

 xhr.onload = function(){
     if ( xhr.readyState == 4 && xhr.status == 200 ) {
         alert( xhr.responseText );
     } else {
         alert( xhr.statusText );
     }
 };

 xhr.send();
 ```

 #### * 可以获取服务器端的二进制数据。

 1) 改写MIMEType

 ```
 xhr.overrideMimeType("text/plain; charset=x-user-defined");

 var binStr = xhr.responseText;
 for (var i = 0, len = binStr.length; i < len; ++i) {
 　　var c = binStr.charCodeAt(i);
 　  var byte = c & 0xff;
 }
 ```

 2) responseType属性

 ```
 //blob
 var xhr = new XMLHttpRequest();
 xhr.open('GET', '/path/to/image.png');
 xhr.responseType = 'blob';

 var blob = new Blob([xhr.response], {type: 'image/png'});

 //arraybuffer
 var xhr = new XMLHttpRequest();
 xhr.open('GET', '/path/to/image.png');
 xhr.responseType = "arraybuffer";

 var arrayBuffer = xhr.response;
 if (arrayBuffer) {
 　　var byteArray = new Uint8Array(arrayBuffer);
 　　for (var i = 0; i < byteArray.byteLength; i++) {
 　　　　// do something
 　　}
 }
 ```

 #### * 可以获得数据传输的进度信息。

 ```
 xhr.onprogress = updateProgress;
 xhr.upload.onprogress = updateProgress;

 function updateProgress(event) {
     if (event.lengthComputable) {
     　　var percentComplete = event.loaded / event.total;
     }
 }
 ```

### 三. XMLHttpRequest 相关

 #### 事件

 * load事件：传输成功完成。

 * abort事件：传输被用户取消。

 * error事件：传输中出现错误。

 * loadstart事件：传输开始。

 * loadEnd事件：传输结束，但是不知道成功还是失败。


 #### xhr.readyState：XMLHttpRequest对象的状态。

     0 	  初始化状态。XMLHttpRequest 对象已创建或已被 abort() 方法重置。
     1    open() 方法已调用，但是 send() 方法未调用。请求还没有被发送。
     2    Send() 方法已调用，HTTP 请求已发送到 Web 服务器。未接收到响应。
     3    所有响应头部都已经接收到。响应体开始接收但未完成。
     4    HTTP 响应已经完全接收。

 #### xhr.status：服务器返回的状态码，等于200表示一切正常。

      1xx: 信息
      2xx: 成功
      3xx: 重定向
      4xx: 客户端错误
      5xx: 服务器错误

 200 OK 表示请求成功。

 400 Bad Request 表示请求报文中存在语法错误，导致服务器无法识别。

 500 Internal Server Error 可能是服务器在执行这个请求时，发生错误，也有可能是客户端传的参数有问题，导致的。

 503 Service Unavailable 服务器暂时停机维护。

 #### xhr.responseText：服务器返回的文本数据

 #### xhr.responseXML：服务器返回的XML格式的数据

 #### xhr.statusText：服务器返回的状态文本。

### 四. 封装 http 请求

 #### 常用 get post ，请求拦截器封装

 ```
 /**
  * 异步中间件方法调用
  */
 const nextMethod = (list = [], param = {}, complete = () => { }) => {
     var _list = list.map((active, index) => {
         return () => {
             const next = _list[index + 1] //下一个方法
             if (next) {
                 active(param, next) //执行当前方法
             } else {
                 active(param, () => { //完成执行
                     complete(param)
                 })
             }
         }
     });
     setTimeout(_list[0](), 0) //异步执行
 };

 const filter = (str) => { //特殊字符转义
     str += ''; //隐式转换
     str = str.replace(/%/g, '%25')
     str = str.replace(/\+/g, '%2B')
     str = str.replace(/ /g, '%20')
     str = str.replace(/\//g, '%2F')
     str = str.replace(/\?/g, '%3F')
     str = str.replace(/&/g, '%26')
     str = str.replace(/\=/g, '%3D')
     str = str.replace(/#/g, '%23')
     return str
 };

 var _startIntercept = [] //开始的拦截器
 var _endIntercept = [] //结束的拦截器

 var http = (option) => { //http请求方法
     if(!option.url) return;
     var setting = {
         url: '',
         type: 'GET',
         data: {},
         dataType: 'json',
         success() {

         },
         error() {

         }
     };
     //开始拦截器
     Object.assign(setting, option); //覆盖默认配置
     nextMethod(_startIntercept, setting, () => {
         var query = [];
         Object.keys(setting.data).forEach((k) => {if(setting.data[k]){query.push(`${k}=${filter(setting.data[k])}`)}})
         var xhr = new XMLHttpRequest();

         if (setting.type.toUpperCase() == 'GET') { // get请求方法
             xhr.open('GET', setting.url + '?' + query.join('&') + '&' + new Date().getTime(), true)
             xhr.send()
         } else { // post请求
             xhr.open(setting.type, setting.url, true);
             xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded')
             xhr.send(query.join('&'))
         }
         xhr.onload = () => { //请求结束执行方法
             let response = JSON.parse(xhr.responseText);

             if ( xhr.readyState == 4 && xhr.status == 200 ) {
                 nextMethod(_endIntercept, response.data, setting.success)
             } else {
                 nextMethod(_endIntercept, response.data, setting.error);
             }
         }
     })
 };

 /**
  * 开始请求的拦截器
  */
 http.beforeEach = (callback) => {
     _startIntercept.push(callback)
 };

 /**
  * 请求结束的拦截器
  */
 http.afterEach = (callback) => {
     _endIntercept.push(callback)
 };

 export default http
 ```

 #### 上传文件，带进度条封装

 ```
 var doUpload = function doUpload(url, files, progress, success, error, param, multiple=false) {
     if (!url) return;
     if (files) {
         let xhr = new XMLHttpRequest();
         xhr.upload.addEventListener("progress", function (e) {
             progress && progress(e.loaded / e.total);
         }, false);

         xhr.addEventListener("load", function (e) {
             if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
                 try {
                     let ret = $.parseJSON(xhr.responseText);
                     if (ret.status === undefined && ret.result !== undefined) {
                         ret.status = ret.result;
                     }
                     success(ret, files);
                 } catch (e) {
                     error && error(e);
                 }
             }
             if (xhr.status === 500) {
                 error && error(e);
             }
         }, false);

         xhr.addEventListener("error", function (e) {
             error && error(e);
         }, false);

         let formData = new FormData();
         if(multiple && files.length>0){
            for (let i = 0; i < files.length;i++) {
             　　formData.append('files[]', files[i]);
             }
         }
         else if(!multiple){
            formData.append("file", files);
         }
         else {
            return
         }

         if (param) {
             for (let key in param) {
                 formData.append(key, param[key]);
             }
         }

         xhr.open("post", url, true);
         xhr.send(formData);
     }
 }
 export default doUpload
 ```
