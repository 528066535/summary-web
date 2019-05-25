
    一. 一个面试题

    二. HTTP知识点

    三. HTTPS

### 一. 一个面试题

 这是一道经典的面试题，当你输入一个URL，请求到页面经历了哪些过程？

 1) [DNS解析](/file/dns.md)

 2) [TCP连接](/file/net-tcp.md)

 3) 发送 HTTP 请求

 4) 服务器处理请求并返回 HTTP 报文

 5) 浏览器解析 HTML 代码，并[渲染页面](/file/hc.md)

 而第三步就是我们关注的 HTTP 请求相关。

### 二. HTTP知识点

 #### HTTP 历史

 HTTP/0.9 是基于TCP/IP 协议的应用层协议，默认端口是80，它不涉及到数据包很简单，只有 GET 请求，并且只能返回HTML的字符串。

 HTTP/1.0 增加了 POST 和 HEAD 命令，每次请求都必须包括头部信息（HTTP header），新增了状态码、支持多种字符集（Content-Type）、多部分发送（multi-part type）、
 权限（authorization）、缓存（cache）、内容编码（content encoding）等。1.0中每个TCP连接只能发送一个 HTTP 请求，Connection: keep-alive 可以要求服务器不关闭 TCP。

 HTTP/1.1 最大的变化是引入持久连接，默认 Connection: keep-alive，如果要关闭 则改成 close。另外还增加了管道机制、支持 Content-Length、支持 分块传输编码，
 并增加其他请求命令。

 HTTP/2 加入了 二进制分帧、多工、数据流、头信息压缩、服务器推送。

 #### HTTP 请求是无状态的

 每次的请求都是独立的，它的执行情况和结果与前面的请求和之后的请求是无直接关系的，它不会受前面的请求应答情况直接影响，也不会直接影响后面的请求应答情况。

 #### Content-Type （1.0）

 * text/plain
 * text/html
 * text/css
 * image/jpeg
 * image/png
 * image/svg+xml
 * audio/mp4
 * video/mp4
 * application/javascript
 * application/pdf
 * application/zip
 * application/atom+xml

 这些数据类型总称为 MIME type，每个值都包括一级类型和二级类型，之间用斜杠分割。MIME type还可以在尾部使用分号，添加参数，如 text/html; charset=utf-8。

 除了这些类型，厂商也可以自定义类型。

 客户端请求的时候，可以使用Accept字段声明自己可以接受哪些数据格式。

 MIME type不仅用在HTTP协议，还可以用在其他地方，比如HTML网页。

 ```
 <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
 <!-- 等同于 -->
 <meta charset="utf-8" />
 ```

 常见请求类型：

 application/x-www-form-urlencoded：HTTP会将请求参数用key1=val1&key2=val2的方式进行组织，并放到请求实体里面，注意如果是中文或特殊字符如"/"、","、“:"
 等会自动进行URL转码。不支持文件，一般用于表单提交。

 multipart/form-data：与application/x-www-form-urlencoded不同，这是一个多部分多媒体类型。首先生成了一个 boundary 用于分割不同的字段，
 在请求实体里每个参数以------boundary开始，然后是附加信息和参数名，然后是空行，最后是参数内容。多个参数将会有多个boundary块。如果参数是文件会有特别的文件域。
 最后以------boundary–为结束标识。multipart/form-data支持文件上传的格式，一般需要上传文件的表单则用该类型。

 application/json：JSON 是一种轻量级的数据格式，以“键-值”对的方式组织的数据。这个使用这个类型，需要参数本身就是json格式的数据，参数会被直接放到请求实体里，
 不进行任何处理。服务端/客户端会按json格式解析数据（约定好的情况下）。

 #### Content-Encoding（1.0）

 由于发送的数据可以是任何格式，因此可以把数据压缩后再发送。Content-Encoding字段说明数据的压缩方法。

 客户端在请求时，用Accept-Encoding字段说明自己可以接受哪些压缩方法。

 ```
 Content-Encoding: gzip
 Content-Encoding: compress
 Content-Encoding: deflate

 Accept-Encoding: gzip, deflate
 ```

 #### 管道机制（1.1）

 在同一个TCP连接里面，客户端可以同时发送多个请求。这样就进一步改进了HTTP协议的效率。举例来说，客户端需要请求两个资源。以前的做法是，
 在同一个TCP连接里面，先发送A请求，然后等待服务器做出回应，收到后再发出B请求。管道机制则是允许浏览器同时发出A请求和B请求，
 但是服务器还是按照顺序，先回应A请求，完成后再回应B请求。

 #### Content-Length （1.1）

 一个TCP连接现在可以传送多个回应，势必就要有一种机制，区分数据包是属于哪一个回应的。这就是Content-length字段的作用，声明本次回应的数据长度。

 #### 分块传输编码

 使用Content-Length字段的前提条件是，服务器发送回应之前，必须知道回应的数据长度。对于一些很耗时的动态操作来说，这意味着，服务器要等到所有操作完成，
 才能发送数据，显然这样的效率不高。更好的处理方法是，产生一块数据，就发送一块，采用"流模式"（stream）取代"缓存模式"（buffer）。

 因此，1.1版规定可以不使用Content-Length字段，而使用"分块传输编码"（chunked transfer encoding）。只要请求或回应的头信息有Transfer-Encoding字段，
 就表明回应将由数量未定的数据块组成。

 ```
 Transfer-Encoding: chunked
 ```

 #### 性能优化 （1.1）

 由于一个 TCP 连接中服务器只能在一个请求返回后，再处理下一个请求，如果前面的请求慢了，后面会有很多排队等待了。这称为"队头堵塞"。

 解决办法一个是多开 TCP。另外一个是合并请求。合并一些公用的css，js，图片嵌入css代码中，避免404，使用CDN，图片合并加载等等。

 #### 二进制分帧 （2）

 HTTP/1.1 版的头信息肯定是文本（ASCII编码），数据体可以是文本，也可以是二进制。HTTP/2 则是一个彻底的二进制协议，头信息和数据体都是二进制，
 并且统称为"帧"（frame）：头信息帧和数据帧。

 那如何进行分帧？

 试想，如果我们的请求中包含的是一个1G的文件的 内容，HTTP 1.1 要求写完这整个 1G 的文件内容之前，我们都不能写入其他内容。而 HTTP 2 分帧可以
 把请求的数据分块n个块，比如每次写入1M，写完后发现有其他请求了，可以先处理其他请求。

 #### 数据流（stream）和 推送 （2）

 我们在分帧的时候，可以把一个大的请求数据分成n块，每次把其中一个包响应后，会多传一个id，最后我们在读取数据的时候把每个id相同的包合在一起，
 这样就能组成一个完成的响应了。这个过程就是 数据流（stream）。在这个过程中，服务器多次向客户端推送数据，服务器未经请求，主动向客户端发送资源，
 这叫做服务器推送（server push）。

 ![layout viewport](/img/http-1.png)

 上图中，每个块代表一帧，而相同颜色的块则代表是同一个流。

 #### 多工 （2）

 双向的、实时的通信，就叫做多工。

### 三. HTTPS

 #### 加密技术

 加密技术主要用到以DES为代表的对称加密算法和以RSA为代表的非对称加密算法。

 对称加密算法一般很难破解，但是不太好保管，安全性也不是很高，为啥呢？因为客户端和服务端拿到的密钥是一样的，不可能每次都把key给改了，而不改的话，一直用同一个key的话也会存在安全隐患。

 因此https的加密的方式采取的是混合方式。交换密钥的时候采取非对称的，建立通信交换报文的时候采取对称加密的方法。

 #### 身份验证技术

 所谓身份验证就是用公钥生成可信赖的证书。因为非对称加密存在一个问题就是没法验证拿到的公钥就是服务端公开的公钥。
