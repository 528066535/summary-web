
    上章是不是有点水，写了不是很知识点的东西，就当是给这章做铺垫吧

### 一. 跨域问题

 #### 浏览器同源策略

 如果两个页面的协议，端口（如果有指定）和主机都相同，则两个页面具有相同的源。

 * 协议:https和http

 * 端口:http:// 80是默认的

 * 主机:域名不同

 #### 不涉及跨域的情况

 凡是拥有scr这个属性的标签都可以跨域例如<script> <img> <iframe>

### 二. Cookie sessionStorage localStorage 对比

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

### 三. 安全问题

 #### CSRF 漏洞

 CSRF（Cross-site request forgery）跨站请求伪造：攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。
 利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

 (1) 过程

 * 受害者登录a.com，并保留了登录凭证（Cookie）。

 * 攻击者引诱受害者访问了b.com。

 * b.com 向 a.com 发送了一个请求：a.com/act=xx。浏览器会默认携带a.com的Cookie。

 * a.com接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求。

 * a.com以受害者的名义执行了act=xx。
 * 攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让a.com执行了自己定义的操作。

 (2) 几种常见的攻击类型


### 四. 跨域的解决方案

 #### jsonp

 注意：jsonp 的方式只能是 get 请求

 #### iframe

 #### CORS

 #### 代理

### 五. 同源策略限制下Dom查询的正确打开方式

 #### postMessage