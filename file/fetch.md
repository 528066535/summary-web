
### 一. 介绍

 fetch 返回的是 Promise，看起来也会清爽，清晰很多。

 #### 兼容性

 ![浏览器兼容性](/img/fetch-1.png)

 #### 使用

 ```
 let formData = new FormData();
 formData.append('name' , '认识c语言');
 formData.append('cp_weight' , '0');
 formData.append('avatar', fileField.files[0]); //可上传文件

 fetch(url, {
    　　method: 'POST',
    　　credentials: 'include',
    　　headers: { //设置头信息
       　　'Content-Type': '' //(指定数据类型和编码),
           credentials: 'include' // 强制带Cookie
       },
       mode: 'cors', // no-cors, cors, *same-origin
       body: formData, //注意可以传json数据，JSON.stringify(jsonData)
    }).then(function(response) {
        return res.json()   //注意 如果返回的是html，则  return res.text()
    }).then(res=>{
        console.log(res);
    }).catch(error=>{
        console.log(error);
    })
 ```

### 二. 小小的封装

 ```
 let fetchRes = function(url,data,method){
    let option = {
         method: method
    }
    let query = [];
    if(method == 'GET') {
        Object.keys(data).forEach((k) => {if(data[k]){query.push(`${k}=${filter(data[k])}`)}});
        url = url + '?' + query.join('&');
    }
    else if(method == 'POST') {
        option.body = JSON.stringify(data);
    }

    fetch(url,option)
     //第一个then并不是真实的返回结果，真实的结果需要到第二个then里面查看
     .then(response => {
         let contentType = response.headers.get('content-type');
         if (response.ok && contentType.includes('application/json')) {
             return response.json();
         } else {
             return Promise.reject({
                 status: response.status,
                 statusText: response.statusText
             });
         }
     })
 }

 export default {
    get(url,data){
        return fetchRes(url,data,'GET')
    },

    post(url,data){
            return fetchRes(url,data,'POST')
        },
 }
 ```

 #### fetch 和 async/await
 
 async/await 会在 [promise](/promise.md) 章节讲到。

 ```
 async function showAvatar() {
     // read our JSON
     let response = await fetch('/article/promise-chaining/user.json')
     let user = await response.json()

     // read github user
     let githubResponse = await fetch(`https://api.github.com/users/${user.name}`)
     let githubUser = await githubResponse.json()

     // 展示头像
     let img = document.createElement('img')
     img.src = githubUser.avatar_url
     img.className = 'promise-avatar-example'
     documenmt.body.append(img)

     // 等待3s
     await new Promise((resolve, reject) => {
         setTimeout(resolve, 3000)
     })

     img.remove()

     return githubUser
 }
 showAvatar()
 ```

### 三. 浅谈 fetch

 #### fetch不支持JSONP

 #### fetch不支持progress事件

 #### fetch跨域问题

 XHR2是支持跨域请求的，只不过要满足浏览器端支持CORS，服务器通过Access-Control-Allow-Origin来允许指定的源进行跨域，仅此一种方式。

 与XHR2一样，fetch也是支持跨域请求的，只不过其跨域请求做法与XHR2一样，需要客户端与服务端支持；另外，fetch还支持一种跨域，不需要服务器支持的形式，
 具体可以通过其mode的配置项来说明。但是跨域不能访问响应返回的内容。

 * same-origin：该模式是不允许跨域的，它需要遵守同源策略，否则浏览器会返回一个error告知不能跨域；其对应的response type为basic。

 * cors: 该模式支持跨域请求，顾名思义它是以CORS的形式跨域；当然该模式也可以同域请求不需要后端额外的CORS支持；其对应的response type为cors。

 * no-cors: 该模式用于跨域请求但是服务器不带CORS响应头，也就是服务端不支持CORS；这也是fetch的特殊跨域请求方式；其对应的response type为opaque。

