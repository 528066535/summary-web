
 插播一篇可以稍微提高性能的文章。

 ### 一. 页面的呈现

 在学习回流和重绘前，可以先了解下页面的渲染过程。盗用网上一种图片，来分析渲染过程

 ![浏览器渲染过程](/img/hc-1.png)

 1） DOM树

 通常，浏览器会先把获取到的HTML代码解析成DOM树，DOM树的根节点是 document 对象，DOM 树中包含了 display:none和js动态添加的元素。

 2） 样式结构体

 浏览器会把样式解析成样式对应的结构体，并且去掉一些不能识别的样式。

 3）Render树

 浏览器会把DOM Tree和样式结构体组合并且构建成 Render 树，Render 树类似于DOM树，但是会插入样式，并且去掉隐藏的节点（比如display：none，
 不包括visibility：hidden，也不包括script、meta、link等标签）。

 ![渲染树](/img/hc-2.png)

 注意：渲染树只包含可见的节点

 4）绘制

 ### 二. 回流与重绘

 #### 回流

 回流又叫重排，是 Render 树 需要重新计算的过程，并且需要计算Render 树在设备视口(viewport)内的确切位置和大小（如把百分比的宽度转化成像素）。

 回流一定会重绘，重绘不一定会回流。

 触发回流的操作：

 1. DOM元素的几何属性变化

 常见的几何属性有 width、height、padding、margin、left、top、border 等等。
 当DOM元素的几何属性变化时，渲染树中的相关节点就会失效，浏览器会根据DOM元素的变化重建构建渲染树中失效的节点。之后，会根据新的渲染树重新绘
 制这部分页面。而且，当前元素的重排也许会带来相关元素的重排。例如，容器节点的渲染树改变时，会触发子节点的重新计算，也会触发其后续兄弟节点的重排，
 祖先节点需要重新计算子节点的尺寸也会产生重排。最后，每个元素都将发生重绘。可见，重排一定会引起浏览器的重绘，一个元素的重排通常会带来一系列的反应，
 甚至触发整个文档的重排和重绘，性能代价是高昂的。

 2. DOM树的结构变化

 当DOM树的 结构变化时，例如节点的增减、移动等，也会触发重排。浏览器引擎布局的过程，类似于树的前序遍历，是一个从上到下从左到右的过程。
 通常在这个过程中，当前元素不会再影响其前面已经遍历过的元素。所以，如果在body最前面插入一个元素，会导致整个文档的重新渲染，而在其后插入一个元素，
 则不会影响到前面的元素。

 3. 获取某些属性

 由于每次重排都会造成额外的计算消耗，因此大多数浏览器都会通过队列化修改并批量执行来优化重排过程。浏览器会将修改操作放入到队列里，
 直到过了一段时间或者操作达到了一个阈值，才清空队列。但是！当你获取布局信息的操作的时候，会强制队列刷新。比如：offsetTop、offsetLeft、
 offsetWidth、offsetHeight、scrollTop、scrollLeft、scrollWidth、scrollHeight、 clientTop、clientLeft、clientWidth、clientHeight、
 getComputedStyle() (currentStyle in IE)。所以，在多次使用这些值时应进行缓存。

 4. 页面一开始渲染的时候

 此外，改变元素的一些样式，调整浏览器窗口大小等等也都将触发重排。

 #### 重绘

 重绘是一个元素外观的改变所触发的浏览器行为，例如改变vidibility、outline、背景色等属性。浏览器会根据元素的新属性重新绘制，使元素呈现新的外观。
 重绘不会带来重新布局，并不一定伴随重排。

 ### 三. 如何避免回流和重绘

 ```
 const container = document.getElementById('container')
 container.style.width = '100px'
 container.style.height = '200px'
 container.style.border = '10px solid red'
 container.style.color = 'red'
 ```

 我们来看一段代码，实际上，大部分浏览器出发了一次回流和一次重绘。

 浏览器缓存了一个 flush 队列，把我们触发的回流与重绘任务都塞进去，待到队列里的任务多起来、或者达到了一定的时间间隔，或者“不得已”的时候，
 再将这些任务一口气出队。因此我们看到，上面就算我们进行了 4 次 DOM 更改，也只触发了一次 重绘 和一次 回流。

 所以，有些时候浏览器也会帮助减少重绘回流次数，但是总不能依赖浏览器，无限制的玩，所以我们也需要知道如何减少重绘回流的办法。

 1) 避免逐条改变样式，可以统一用一个类名去合并样式。

 2) 将 DOM 脱离文档流，比如利用 display none 样式修改完后再设置回去。

 3) 尽量少用，或者避免重复使用能让浏览器强制刷新队列的属性，比如：

 ```
 const width = box.offsetWidth;
 function initP() {
     for (let i = 0; i < paragraphs.length; i++) {
         paragraphs[i].style.width = width + 'px'; //用 width 避免多次获取 offsetWidth 的值
     }
 }
 ```

 4) 对于复杂动画效果,使用绝对定位让其脱离文档流。

 5) 利用 GPU 加速优先使用渲染层合并属性，避免 layout，paint。

 ### 四. GPU

 GPU英文全称Graphic Processing Unit，中文翻译为“图形处理器”。GPU是专门为处理图形任务而产生的芯片。对于GPU来说，
 它的任务是在屏幕上合成显示数百万个像素的图像——也就是同时拥有几百万个任务需要并行处理，因此GPU被设计成可并行处理很多任务。

 #### GPU 加速能做什么？

 大多数设备的刷新频率是 60 次/秒，（1000/60 = 16.6ms）也就说是浏览器对每一帧画面的渲染工作要在 16ms 内完成，超出这个时间，页面的渲染就会出现卡顿现象，影响用户体验。
 而浏览器在一帧里面，会依次执行开始说的页面的呈现过程。GPU 加速可以减少或者避免 layout，paint 可以让页面不卡顿，动画效果更加流畅。

 #### GPU 加速是如何做到减少回流和重绘呢？

 通过改变元素的 transform 实现移动，伸缩变换而非改变物体的 left，top，width，height 避免 layout，paint。让动画效果更加流畅。

 ```
 @keyframes demo {
     0% {
        top: 10px;
     }
     100% {
        top: 30px;
     }
 }

 //优化
 @keyframes demo {
     0% {
        transform: translateY(10px);
     }
     100% {
        transform: translateY(30px);
     }
 }
 ```

 #### 浏览器如何把 Render Tree 渲染到页面上呢？

 * 获取 DOM 并将其分割为多个层(RenderLayer)
 * 将每个层栅格化，并独立的绘制进位图中
 * 将这些位图作为纹理上传至 GPU
 * 复合多个层来生成最终的屏幕图像(终极 layer )。

 Chrome 开启查看 renderlayer，可以自行百度，这里就不多做讲解了。

 而实际上页面在渲染只是渲染改变的那个图层，所以我们可以把经常改动的 dom 放在一个独立的图层，并且启动GPU加速，跳过或者减少重绘和回流，来让
 界面更流畅。

 #### 如何开启 GPU 加速

 1) 3D 或透视变换(perspective，transform) CSS 属性

 2) 使用加速视频解码的video元素

 3) 拥有 3D (WebGL) 上下文或加速的 2D 上下文的 canvas 元素

 4) 混合插件(如 Flash)

 5) 对自己的 opacity 做 CSS 动画或使用一个动画 webkit 变换的元素

 6) 拥有加速 CSS 过滤器的元素

 7) 元素A有一个 z-index 比自己小的元素B，且元素B是一个合成层（换句话说就是该元素在复合层上面渲染），则元素A会提升为合成层

 ```
 //启用GPU加速
 .useGPU{
   transform: translateZ(0);

   backface-visibility: hidden;

   perspective: 1000px;
 }
 ```

