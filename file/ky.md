
    上章是不是有点水，写了不是很知识点的东西，就当是给这章做铺垫吧

### 一. 跨域问题

 #### 浏览器同源策略

 如果两个页面的协议，端口（如果有指定）和主机都相同，则两个页面具有相同的源。

 * 协议:https和http

 * 端口:http:// 80是默认的

 * 主机:域名不同

 #### 不涉及跨域的情况

 凡是拥有scr这个属性的标签都可以跨域例如 <script> <img> <iframe>，form表达也可以跨域请求。

### 二. 跨域的解决方案之 iframe

 #### 同主域下面，不同子域之间的跨域

 同主域，不同子域跨域，设置相同的document.domian就可以解决;

 父页访问子页，可以document.getElementById("myframe").contentWindow.document来访问iframe页面的内容；如果支持 contentDocument
 也可以直接document.getElementById("myframe").contentDocument访问子页面内容；

 步骤：

 1) 创建iframe - 在a.html文件中，动态创建iframe元素/标签

 2) 视觉控制 - 为了让用户无法看到这个iframe元素/标签，需要使用CSS将其移出可视区

 3) 设置domain - 为了保证a.html与b.html的访问能够顺畅进行，需要为两个文件均定义domain，即将document.domain设置为“主域名”

 4) 数据操作与传递 - 在a.html文件中编写好AJAX申请，而这个AJAX的内容就是b.html要负责执行的内容；除了编写好AJAX申请之外，
 还需要在a.html文件“命令”b.html去执行

 ```
 //a.html
 <script>
    var iframeJquery = null;    // 用于存储iframe中的b.html的jQuery对象
    function addIframe(cb) {
         document.domain = 'h5course.com';
         exec_obj = document.createElement('iframe');
         exec_obj.src = 'http://B.h5course.com';
         exec_obj.id = 'newIframe';
         exec_obj.style.display = 'none';
         document.body.appendChild(exec_obj);//动态创建一个iframe
         $('#newIframe').on('load', function(){
             iframeJquery = $('#newframe')[0].contentWindow.$;
             cb();
         })
    };

    addIframe(function() {
            iframeJquery.ajax({
                url: 'http://B.h5course.com/data.php',
                type: 'GET',
                success: function(data) {
                    $('.wrap').html(data);
                }
            });
        });
 </script>

 //b.html
 <script src="js/jquery.js"></script>
 <script>
     document.domain = 'h5course.com';
 </script>
 ```

 #### 不同主域跨域

 步骤：

 1) www.a.com 下的 a.html 页面设置 iframe 调用 www.b.com 的 b.html。
 2) b.html 页面设置 iframe 调用 www.a.com 下的 c.html，b.html是不无法直接访问a.html的对象，因为涉及到跨域，b 在访问 c 的时候，并没有
 调用 c 的 js ，只是传递了参数。
 3) c.html 和 a.html同域，是可以访问 a 下的对象的，这里的 c 相当于是一个代理。b 可以通过 c 访问 a ，并且 c 中url的内容也是 b 传过来的。

 ```
    //a.html
    <body>
    <iframe src="http://www.b.com/b.html" ></iframe>
    <ul id="getText"></ul>
    <script>
        function dosome(text){
            document.getElementById("getText").innerHTML= decodeURI(text);
        }
    </script>
    </body>

    //b.html
    <body>
    <iframe id="myfarme" src="###"></iframe>
    <ul id="ct">
        <li>这里是内容1</li>
        <li>这里是内容2</li>
        <li>这里是内容3</li>
        <li>这里是内容4</li>
        <li>这里是内容5</li>
        <li>这里是内容6</li>
    </ul>
    <script>
        window.onload = function(){
            var text = document.getElementById('ct').innerHTML;
            document.getElementById('myfarme').src="http://www.a.com/c.html?content="+encodeURI(text);
        }
    </script>
    </body>

    //c.html
    <script>
        window.onload = function(){
            var text = window.location.href.split('=')[1]
            console.log(parent.parent)
            parent.parent.dosome(text);
        }
    </script>
    </head>
    <body>
    ddddddddddd
    </body>
 ```

### 三. 跨域的解决方案之 jsonp

 我们知道浏览器有同源策略，不能访问其他非同源的数据文件，但是有时候我们又必须请求其他服务器的数据，这时候就需要跨域请求来解决我们遇到的问题了。

 #### jsonp

 自己用 nodejs 和 jquery 实现的 jsonp 获取数据，来增加写代码的乐趣。

 jsonp 的原理是利用了 script 等标签可以在非同源的情况下，获取到数据来实现的，但是 jsonp 又必须是 get 请求，又需要后台设置 callback 回调
 函数返回 json 数据。

 '''
 //nodejs 服务器代码
 module.exports=function(req,res){
     let data = JSON.stringify({code:200,data:"this is a get request"});
     //假设我们定义的回调函数名为test
     var callback = 'test'+'('+data+');';
     res.end(callback);
 }

 //前端利用 jquery
 jquery.ajax({
     url:"http://localhost:3000/api/get.json",
     type: 'get',
     dataType:"jsonp",   //必须指定
     jsonpCallback: "test",  //指定回调函数名称

     success:function(data){
         var result = JSON.stringify(data); //json对象转成字符串
         //业务逻辑执行代码
         console.log(data);
     }
 });

 //前端用 script 标签实现 要先声明回调函数，不然会报未声明的错
 <script type="text/javascript" type="text/javascript">
     var test = function test(data){
         console.log('script:');
         console.log(data);
     }
 </script>
 <script src='http://localhost:3000/api/get.json'></script>
 '''

### 四. 跨域的解决方案之 CORS

 跨域资源共享(CORS) 是一种机制，它使用额外的 HTTP 头来告诉浏览器  让运行在一个 origin (domain) 上的Web应用被准许访问来自不同源服务器上的指定的资源。
 当一个资源从与该资源本身所在的服务器不同的域、协议或端口请求一个资源时，资源会发起一个跨域 HTTP 请求。

 出于安全原因，浏览器限制从脚本内发起的跨源HTTP请求。 例如，XMLHttpRequest和Fetch API遵循同源策略。
 这意味着使用这些API的Web应用程序只能从加载应用程序的同一个域请求HTTP资源，除非响应报文包含了正确CORS响应头。

 #### CORS 如何设置跨域

 Access-Control-Allow-Origin: 跨域服务器允许的来源地址（跟请求的Origin进行匹配），可以是*或者某个确切的地址，不允许多个地址

 Access-Control-Allow-Methods: 授权请求的方法（GET, POST, PUT, DELETE，OPTIONS等)

 Access-Control-Allow-Headers: HTTP头(Accept,Accept-Language,Content-Language,Last-Event-ID,Content-Type)

 Access-Control-Max-Age: 预请求的返回结果(Access-Control-Allow-Methods和Access-Control-Allow-Headers)可以被缓存的时间，单位秒

 #### nodejs 实现 CORS 跨域

 自己写了一个 demo 不需要前端配合即可实现跨域请求

 ```
 function corsTest(req,res){
     res.header("Access-Control-Allow-Origin", "*");
     res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
     res.header("Access-Control-Allow-Headers", "Authorization,Origin, X-Requested-With, Content-Type, Accept");

     if (req.method == 'OPTIONS') {
         res.send(200);   //在正常的请求之前，会发送一个验证，是否可以请求。
     }
     else {
         res.json({code:200,data:"this is a post request"});
     }
 };

 router.post('/post.json', corsTest);
 ```

### 五. Node 代理实现跨域

 上面两种方式都必须服务器配合，才能实现跨域，如果想自己动手，可以选择这种方案。

 node 作为代理实现跨域的本质是，浏览器发送请求给自己的服务端（node），自己的服务端再转发到需要跨域的服务端，而服务端与服务端之间没有跨域
 的问题，达到跨域的目的。

 #### webpack 插件实现跨域

 webpack-dev-server配置代理非常的方便，只需要条件一个proxy属性，然后配置相关的参数就可以了：

 ```
 devServer: {
         contentBase: './dist',
         disableHostCheck: true,
         host: 'sale',
         port: 3004,
         proxy: {
             "/local-node": {
                 target: "http://localhost:3000",
                 pathRewrite: {"^/local-node" : ""}
             }
         }
     },
 ```

 ### 六. 其他

 #### postMessage

 postMessage()方法允许来自不同源的脚本采用异步方式进行有限的通信，可以实现跨文本档、多窗口、跨域消息传递。

 postMessage(data,origin)方法接受两个参数

 1.data:要传递的数据，html5规范中提到该参数可以是JavaScript的任意基本类型或可复制的对象，然而并不是所有浏览器都做到了这点儿，部分浏览器只能处理字符串参数，
 所以我们在传递参数的时候需要使用JSON.stringify()方法对对象参数序列化。

 2.origin：字符串参数，指明目标窗口的源，协议+主机+端口号[+URL]，URL会被忽略，所以可以不写，这个参数是为了安全考虑，postMessage()
 方法只会将message传递给指定窗口，当然如果愿意也可以建参数设置为"*"，这样可以传递给任意窗口，如果要指定和当前窗口同源的话设置为"/"。

 #### websocket

 这些办法往往不是为了跨域而用到的，而是业务需要，这里就不展开讲了。
