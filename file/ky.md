
    上章是不是有点水，写了不是很知识点的东西，就当是给这章做铺垫吧

### 一. 跨域问题

 #### (1) 浏览器同源策略

 如果两个页面的协议，端口（如果有指定）和主机都相同，则两个页面具有相同的源。

 * 协议:https和http

 * 端口:http:// 80是默认的

 * 主机:域名不同

 #### (2) 不涉及跨域的情况

 凡是拥有scr这个属性的标签都可以跨域例如<script> <img> <iframe>

### 二. Cookie Session localStorage 对比

 #### (1) Cookie

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
 .abc.com"只能在二级域名以及"www.abc.com"下有效,其实客户端只是做了简单的匹配..你可以在此基础上做更多的分级控制.
 path是个有参考意义的属性,对于部分路径下开放访问的系统有意义,比如:abc.com/open下的程序和登录是开放给特殊开发者接入的,
 这里的数据活着cookie需要做一些另类的处理..

 如果创建了一个cookie，并将他发送到浏览器，默认情况下它是一个会话级别的cookie（即存储在浏览器的内存中），用户退出浏览器之后即被删除。
 若希望浏览器将该cookie存储在磁盘上，则需要使用maxAge，并给出一个以秒为单位的时间。将最大时效设为0则是命令浏览器删除该cookie。

 注意：一个WEB站点可以给一个WEB浏览器发送多个Cookie，一个WEB浏览器也可以存储多个WEB站点提供的Cookie。浏览器一般只允许存放300个Cookie，
 每个站点最多存放20个Cookie，每个Cookie的大小限制为4KB。

 #### (2) Session



### 三. 安全问题

 当服务器把用户的登录信息存储在 Cookie 中，攻击者就可以获取到 Cookie 中的信息，再去请求对应服务器，而服务器不能识别是用户还是攻击者，
 这样则会引发安全问题了。

 #### (1) 漏洞

 * CSRF 漏洞



 ### (2) 如何增加安全性

 * 禁止javascript操作cookie（为避免跨域脚本(xss)攻击，通过javascript的document.cookie无法访问带有HttpOnly标记的cookie。）



### 四. 跨域的解决方案

 #### (1) jsonp

 注意：jsonp 的方式只能是 get 请求

 #### (2) iframe

 #### (3) CORS

 #### (4) 代理

### 五. 同源策略限制下Dom查询的正确打开方式

 #### (1) postMessage