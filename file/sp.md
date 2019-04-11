
    每个人,都有自己的最佳适配方案

### 一. 基础概念

 非css的概念:

 #### 1. 物理像素

 手机屏幕上显示的最小单元，该最小单元具有颜色及亮度的属性可供设置，iphone6、7、8 为：750 * 1334，iphone6+、7+、8+ 为 1242 * 2208。

 #### 2. 设备独立像素

 此为逻辑像素，计算机设备中的一个点，css 中设置的像素指的就是该像素。老早在没有 retina 屏之前，设备独立像素与物理像素是相等的。

 #### 3. 设备像素比(devicePixelRatio)

 设备像素比(dpr) = 物理像素/设备独立像素。如 iphone 6、7、8 的 dpr 为 2，那么一个设备独立像素便为 4 个物理像素，因此在 css 上设置的
 1px 在其屏幕上占据的是 2个物理像素，0.5px 对应的才是其所能展示的最小单位。这就是 1px 在 retina 屏上变粗的原因，目前有很多办法来解决这一问题。

 css中的概念:

 #### 1. pc端的viewport

  在pc端上viewport通常代表的是可视区域,改变浏览器大小的时候viewport也随之改变.

 #### 2. 移动端的viewport会比较复杂

  a. layout viewport: 在移动端,可视区域会很小,viewport如果为可视区域,那一个页面的宽度会很小很小,排版都会错乱,所以在移动端会默认设置一个
  layout viewport,可以通过 document.documentElement.clientWidth 来获取,如下图所示:

  ![layout viewport](/img/sp-2.png)

  b. 那么在移动端,就用visual viewport来表示可视区域了,可以用 window.innerWidth 来获取,如下图所示

  ![visual viewport](/img/sp-3.png)

  c. 除了以上两个viewport,浏览器规定了另外一个更适合移动端的 ideal viewport ,简单地说 ideal viewport 等同于设备的宽度,
  ideal viewport并没有固定的尺寸,每款手机都可能不同,苹果手机6一下都是320,[具体查询链接](http://viewportsizes.com/?filter=IOS)

 #### 3. meta

  在移动端中,默认的 viewport 是 layout viewport, 但是实际的开发过程, 我们需要改成 ideal viewport, 这时候就需要用到 meta.

  `<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">`

  content 属性对应的值如下:

    width: 设置layout viewport  的宽度，为一个正整数，或字符串"width-device"
    initial-scale: 设置页面的初始缩放值，为一个数字，可以带小数
    minimum-scale: 允许用户的最小缩放值，为一个数字，可以带小数
    maximum-scale: 允许用户的最大缩放值，为一个数字，可以带小数
    user-scalable: 是否允许用户进行缩放，值为"no"或"yes", no 代表不允许，yes代表允许
    height: 设置layout viewport  的高度，这个属性对我们并不重要，很少使用

  #### 4. 关于缩放以及initial-scale的默认值

   由于px和实际的像素有区别,一个屏幕的initial-scale=2的时候,一个px代表2个实际的像素,那么如果屏幕的ideal viewport的宽度是640px,那么,
   css中px设置成320,则可以占满屏幕.那么我们可以知道:

    visual viewport宽度 = ideal viewport宽度  / 当前缩放值
    当前缩放值 = ideal viewport宽度  / visual viewport宽度

   但是安卓上的原生浏览器以及IE有些问题.安卓自带的webkit浏览器只有在 initial-scale=1 以及没有设置width属性时才是表现正常的,
   也就相当于这理论在它身上基本没用;而IE则根本不甩initial-scale这个属性,无论你给他设置什么,initial-scale表现出来的效果永远是1.

   根据测试发现, layout viewport 不管是多少, 在没有指定初始缩放值的时候, iphone和ipad会自动计算这个缩放值, 以保证当前layout viewport
   的宽度在缩放后就是浏览器可视区域的宽度. 计算方法如下:

    前缩放值 = ideal viewport宽度  / visual viewport宽度

  #### 5. 获取屏幕宽度

  ![屏幕的宽高](/img/sp-4.png)

  屏幕分辨率：screen.width+"/"+screen.height

  屏幕可见宽度包括滚动条：window.innerWidth

  网页可见区域：document.body.clientWidth +"/"+ document.body.clientHeight

  网页可见区域(包括边线的宽)：document.body.offsetWidth +"/"+ document.body.offsetHeight

  网页正文全文宽： document.body.scrollWidth

  网页正文全文高： document.body.scrollHeight

### 二. 早期方案（Media Queries）

 早期部分项目在做同一套前端代码同时适配pc和手机端采用的方案,后来由于各种原因,最明显的两个坏处就是难维护,增加了冗余的代码导致这种方案慢慢的被遗弃.

 #### 1. 使用方法

  ```
  @media screen and (min-width: 300px)  and (max-width: 900px){
      body {
          background-color: white;
      }
  }
  ```
 #### 2. 在智能设备上，例如iPhone、iPad等，还可以根据屏幕设备的尺寸来设置相应的样式（或者调用相应的样式文件）。
 同样的，对于屏幕设备同样可以使用“min/max”对应参数，如“min-device-width”或者“max-device-width”。

  ```
  <link rel="stylesheet" media="screen and (max-device-width:480px)" href="iphone.css" />
  ```

### 三. rem方案

 #### 1. em作为font-size的单位时,其代表父元素的字体大小,他是一个相对的大小,可精确到小数点后三位,计算方法是(需要转换的像素值/父元素的font-size),



### 四. flex方案

 #### 1. flex基本知识

  ![flex基本知识](/img/sp-1.jpg)

 #### 2. 淘宝手机H5 Flexible 适配方案

### 五. 最新方案
