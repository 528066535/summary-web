
    上一章讲了怎么跨域，这章就讲让感受下，为啥别人不想让你随便跨域。

### 一. Cookie sessionStorage localStorage 对比

 #### Cookie

 网景公司当时一名员工Lou Montulli，在1994年将“cookies”的概念应用于网络通信，用来解决用户网上购物的购物车历史记录，这就是它的由来，
 目前所有浏览器都支持cookies。

 Cookie 以名/值对形式存储，username=John Doe。

 一个cookie如上4个重要的属性:

 * maxAge:cookie 被客户端保持的时间,单位为(秒),正数表示在指定的秒数后过期被客户端删除,0表示删除此cookie(置空),负数则表示此 cookie
 不会被客户端存储,将在浏览器关闭后清除.

 * domain:cookie可被有效操作的域,可以为ip/hostname等,不过需要声 明:*.abc.com,.abc.com,abc.com这三种方式会有区别,客户端会做简单的匹配.
 多数情况下直接使用abc.com可以接受多级子 域名.只有正确匹配domain的cookie才会被发送给server.

 * secure:是否只允许安全加密url访问,默认为false,如果为true,那么cookie只对https/SSL等加密连接才会发送给server.

 * path:cookie对domain何路径下访问有效,"/"表示domain下根目录中所有请求有效,"/open"表示只对domain /open目录下请求有效,
 如果此path忘记设置,你将遇到一个很尴尬的问题:明明在其他页面设置了cookie输出,但是换个页面却死活不行.

 注意：domain和path是决定可跨域的2个参数.对于domain,"abc.com"则可以在abc.com主域名之下的多级子域名有效,"
 .abc.com"只能在二级域名以及 www.abc.com 下有效,其实客户端只是做了简单的匹配.你可以在此基础上做更多的分级控制.path是个有参考意义的属性,
 对于部分路径下开放访问的系统有意义,比如:abc.com/open下的程序和登录是开放给特殊开发者接入的,这里的数据活着cookie需要做一些另类的处理..

 如果创建了一个cookie，并将他发送到浏览器，默认情况下它是一个会话级别的cookie（即存储在浏览器的内存中），用户退出浏览器之后即被删除。
 若希望浏览器将该cookie存储在磁盘上，则需要使用maxAge，并给出一个以秒为单位的时间。将最大时效设为0则是命令浏览器删除该cookie。

 注意：一个WEB站点可以给一个WEB浏览器发送多个Cookie，一个WEB浏览器也可以存储多个WEB站点提供的Cookie。浏览器一般只允许存放300个Cookie，
 每个站点最多存放20个Cookie，每个Cookie的大小限制为4KB。

 #### sessionStorage

 sessionStorage 用于临时保存同一窗口(或标签页)的数据，在关闭窗口或标签页之后将会删除这些数据。

 sessionStorage 的存储限制一般为5MB

 ```
 sessionStorage.setItem("key", "value"); //保存数据
 var lastname = sessionStorage.getItem("key"); //读取数据
 sessionStorage.removeItem("key"); //删除指定键
 sessionStorage.clear(); //删除所有数据
 ```
 注意：刷新界面数据还在。

 #### localStorage

 IE8以上的IE版本才支持localStorage，并且存储的值类型限定为string类型，而localStorage与sessionStorage的唯一一点区别就是 localStorage
 属于永久性存储

 注意：localStorage在浏览器的隐私模式下面是不可读取的，localStorage本质上是对字符串的读取，如果存储内容多的话会消耗内存空间，会导致页面变卡

 ```
 // 存储值
 localStorage.setItem("a",3);  //或者 localStorage.a = 3;
 // 读取值
 localStorage.getItem("a"); //或者 localStorage.a;
 // 删除指定键
 storage.removeItem("a");
 // 清空所有值
 storage.clear();
 ```

### 二. XSS攻击

 Cross-Site Scripting（跨站脚本攻击）简称 XSS，是一种代码注入攻击。攻击者通过在目标网站上注入恶意脚本，使之在用户的浏览器上运行。
 利用这些恶意脚本，攻击者可获取用户的敏感信息如 Cookie、SessionID 等，进而危害数据安全。

 ```
 <input type="text" value="<%= getParameter("keyword") %>">
 <button>搜索</button>
 <div>
   您搜索的关键词是：<%= getParameter("keyword") %>
 </div>

 //浏览器无法分辨出 <script>alert('XSS');</script> 是恶意代码，因而将其执行。这里不仅仅 div 的内容被注入了，而且 input 的 value 属性也被注入，alert 会弹出两次。
 <input type="text" value=""><script>alert('XSS');</script>">
 <button>搜索</button>
 <div>
   您搜索的关键词是："><script>alert('XSS');</script>
 </div>
 ```

 #### XSS 分类

 * 存储区：恶意代码存放的位置。
 * 插入点：由谁取得恶意代码，并插入到网页上。

 a. 存储型 XSS

 1) 攻击者将恶意代码提交到目标网站的数据库中。
 2) 用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。
 3) 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
 4) 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

 b. 反射型 XSS

 1) 攻击者构造出特殊的 URL，其中包含恶意代码。
 2) 用户打开带有恶意代码的 URL 时，网站服务端将恶意代码从 URL 中取出，拼接在 HTML 中返回给浏览器。
 3) 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
 4) 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

 c. DOM 型 XSS

 1) 攻击者构造出特殊的 URL，其中包含恶意代码。
 2) 用户打开带有恶意代码的 URL。
 3) 用户浏览器接收到响应后解析执行，前端 JavaScript 取出 URL 中的恶意代码并执行。
 4) 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

 #### XSS 攻击特点

 * 攻击者提交恶意代码。
 * 浏览器执行恶意代码。

 #### 预防存储型和反射型 XSS 攻击

 存储型和反射型 XSS 都是在服务端取出恶意代码后，插入到响应 HTML 里的，攻击者刻意编写的“数据”被内嵌到“代码”中，被浏览器所执行。

 预防这两种漏洞，有两种常见做法：

 * 改成纯前端渲染，把代码和数据分隔开。
 * 对 HTML 做充分转义。

 #### 预防 DOM 型 XSS 攻击

 DOM 中的内联事件监听器，如 location、onclick、onerror、onload、onmouseover 等，<a> 标签的 href 属性，JavaScript 的 eval()、setTimeout()、
 setInterval() 等，都能把字符串作为代码运行。如果不可信的数据拼接到字符串中传递给这些 API，很容易产生安全隐患，请务必避免。

### 三. CSRF 漏洞

 CSRF（Cross-site request forgery）跨站请求伪造：攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。
 利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

 #### 过程

 * 受害者登录a.com，并保留了登录凭证（Cookie）。
 * 攻击者引诱受害者访问了b.com。
 * b.com 向 a.com 发送了一个请求：a.com/act=xx。浏览器会默认携带a.com的Cookie。
 * a.com接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求。
 * a.com以受害者的名义执行了act=xx。
 * 攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让a.com执行了自己定义的操作。

 #### 几种常见的攻击类型

 * GET类型的CSRF

 ```
 <img src="http://bank.example/withdraw?amount=10000&for=hacker">
 ```

 * POST类型的CSRF

 ```
 <form action="http://bank.example/withdraw" method=POST>
     <input type="hidden" name="account" value="xiaoming" />
     <input type="hidden" name="amount" value="10000" />
     <input type="hidden" name="for" value="hacker" />
 </form>
 <script> document.forms[0].submit(); </script>
 ```

 * 链接类型

 超链接<a>标签带有target=“_blank”属性的，容易被利用进行诸如钓鱼等攻击。当使用 target='_blank' 打开一个新的标签页时，新页面的 window
 对象上有一个属性 opener，它指向的是前一个页面的 window 对象，因此，后一个页面就获得了前一个页面的控制权。

 解决办法：给a标签设置属性，rel = noopener 。

 #### 特点

 * CSRF（通常）发生在第三方域名。
 * CSRF攻击者不能获取到Cookie等信息，只是使用。

 #### 防止 CSRF 漏洞的方法

 阻止不明外域的访问

 1) 同源检测

 * Origin Header
 * Referer Header

 这两个Header在浏览器发起请求时，大多数情况会自动带上，并且不能由前端自定义内容。 服务器可以通过解析这两个Header中的域名，确定请求的来源域。

 根据HTTP协议，在HTTP头中有一个字段叫Referer，记录了该HTTP请求的来源地址。 对于Ajax请求，图片和script等资源请求，Referer为发起请求的页面地址。
 对于页面跳转，Referer为打开页面历史记录的前一个页面地址。因此我们使用Referer中链接的Origin部分可以得知请求的来源域名。

 通过Header的验证，我们可以知道发起请求的来源域名，这些来源域名可能是网站本域，或者子域名，或者有授权的第三方域名，又或者来自不可信的未知域名。

 CSRF大多数情况下来自第三方域名，但并不能排除本域发起。如果攻击者有权限在本域发布评论（含链接、图片等，统称UGC），那么它可以直接在本域发起攻击，
 这种情况下同源策略无法达到防护的作用。

 这种方法并非万无一失，Referer的值是由浏览器提供的，虽然HTTP协议上有明确的要求，但是每个浏览器对于Referer的具体实现可能有差别，并不能保证浏览器自身没有安全漏洞。
 使用验证 Referer 值的方法，就是把安全性都依赖于第三方（即浏览器）来保障，从理论上来讲，这样并不是很安全。在部分情况下，攻击者可以隐藏，甚至修改自己请求的Referer。

 2) CSRF Token

  * token 验证

 前面讲到CSRF的另一个特征是，攻击者无法直接窃取到用户的信息（Cookie，Header，网站内容等），仅仅是冒用Cookie中的信息。

 而CSRF攻击之所以能够成功，是因为服务器误把攻击者发送的请求当成了用户自己的请求。那么我们可以要求所有的用户请求都携带一个CSRF攻击者无法获取到的Token。
 服务器通过校验请求是否携带正确的Token，来把正常的请求和攻击的请求区分开，也可以防范CSRF的攻击。

 用户登录时 Token 并不是在 Cookie 里面的，而是在请求的参数里面传给服务器。Token 中可以增加时间戳，表示登录时效。

 * 双重Cookie验证

 这个的原理是利用了攻击者只能借用Cookie的信息，而并不能获取到Cookie的值，当前端请求接口的时候，获取到Cookie里的token值，然后再传给后台，后台判断 Cookie 的 token
 值和url中的token值是否相同，相同则通过，

 #### 总结

 攻击可以来自很多方面

 * 攻击者自己的网站。
 * 有文件上传漏洞的网站。
 * 第三方论坛等用户内容。
 * 被攻击网站自己的评论功能等。

 对于来自黑客自己的网站，我们无法防护。但对其他情况，那么如何防止自己的网站被利用成为攻击的源头呢？

 * 严格管理所有的上传接口，防止任何预期之外的上传内容（例如HTML）。
 * 添加Header X-Content-Type-Options: nosniff 防止黑客上传HTML内容的资源（例如图片）被解析为网页。
 * 对于用户上传的图片，进行转存或者校验。不要直接使用用户填写的图片链接。
 * 当前用户打开其他用户填写的链接时，需告知风险（这也是很多论坛不允许直接在内容中发布外域链接的原因之一，不仅仅是为了用户留存，也有安全考虑）。

 我们能做的

 * CSRF自动防御策略：同源检测（Origin 和 Referer 验证）。
 * CSRF主动防御措施：Token验证 或者 双重Cookie验证。
 * 后端接口不要在GET页面中做用户操作。
