
    我承认,之前我确实不太懂vertical-align属性

### 一. vertical-align 概念

 vertical-align用在哪些元素上面？

 该属性定义行内元素的基线相对于该元素所在行的基线的垂直对齐方式,允许指定负长度值和百分比值.这会使元素降低而不是升高.在表单元格中,
 这个属性会设置单元格框中的单元格内容的对齐方式.简单地说,就是 to inline-level and 'table-cell' elements .

 内联盒子的组成：

 1. containing box.外层盒模型的大小.

 2. line boxes.一行算一个line boxes.

 3. inline boxes.简单的说就是一个元素标签,如,span.

 4. content area.围绕文字看不见的box,其大小与font-size有关,其高度可以认为鼠标选中文字时背景色的高度.

 ![内联盒子的组成](/img/va-1.jpg)

 vertical-align的取值：

 1. baseline.默认的对齐方式,基线对齐,与父元素的基线对齐 ;

 2. top.与行中的最高元素的顶端对齐,一般是父级元素的最顶端对齐,显示效果还会受本身的line-height影响 ;

 3. middle. 与父元素中线对齐(近似垂直居中);

 4. bottom. 与 top 相反,与父级元素的最低端对齐,显示效果还会受本身的line-height影响 ;

 5. text-top. 与父级元素 [content area] 的顶端对齐 ;

 6. text-bottom. 与 text-top 相反,始终与父级元素 content area 的低端对齐.

 7. 数值与百分比.当数值为正值时,对齐方式将以基线为基准,往上偏移响应的大小,当为负值时,往下偏移.而百分比则是根据行高的大小,
 计算出响应的数值,再以数值表现的方式进行偏移.

 8. super与sub.这两个值均是找到合适的基线作为对齐的基线进行对齐,类似 super 标签和 sub 标签,但不缩放字体大小.

### 二. 基线,baseline.

 什么是基线？简单的说,基线就是字母x底部所在的位置.在css中这叫baseline.

 1. inline-table元素的baseline是它的table第一行的baseline.

 2. 父元素 [line box] 的baseline是最后一个inline box 的baseline.

 3. inline-block元素的baseline确定规则

  * 规则1：inline-block元素,如果内部有line box,则inline-block元素的baseline就是最后一个作为内容存在的元素[inline box]的baseline,
  而这个元素的baseline的确定就要根据它自身来定了.

  * 规则2：inline-block元素,如果其内部没有line box或它的overflow属性不是visible,那么baseline将是这个inline-block元素的底margin边界.

### 三. vertical-align的top属性和text-top属性

  这两个属性看起来有点像,但是实际上区别很大,top是针对当前行中最顶端高度的元素对齐,会受到line-height的影响,有一个line-height,看起来的高度会高有点了.
  text-top是当前行中文字最顶端的位置对齐,不会受到line-height的影响.平时用的话，一般都会用top.

### 四. 空隙

  ![下方的空隙](/img/va-2.png)

  代码：

  ```
    <style>
      .ctn-block{
        background-color: #bbb;
      }
      .ctn-block .child {
        display: inline-block;
        width: 100px;
        background-color: aliceblue;
      }
      .ctn-block .child-1 {
        height: 100px;
        /*    vertical-align: middle;*/
      }
      .ctn-block .child-2 {
        height: 200px;
      }
      .ctn-block .child-3 {
        height: 300px;
      }
    </style>

    <div class="ctn-block">
      <img style="height:100px;width:100px;">
      <div class="child child-1"></div>
      <div class="child child-2"></div>
      <div class="child child-3"></div>
      <span>123</span>
      <img style="height:100px;width:100px;">
    </div>
  ```

  1. 正常情况下,下方会有空隙,由于默认ctn-block,span,img的vertical-align取值是baseline,而baseline下方会给字母留一点空隙,
  导致了我们看到了下面会有一点点空隙.解决办法是加上vertical-align:middle.

  2. 水平也有空隙,这里的空隙是因为换行导致中间产生的空隙.解决办法是加注释<!-- 注释去空格加一个回车 -->

### 五. vertical-align:middle会下移动元素

  ![下移](/img/va-2.png)

  1. 问题描述
   设置了第二个白色方块vertical-align:middle;发现这个块并没有居中,反而下移了.

  2. 原因:、
   vertical-align middle的定位方式是:将子元素盒子的垂直中点与父盒子的baseline加上父盒子的x-height的一半位置对齐.x-height和字体大小,
   还有字体样式有关,所以,如果想用vertical-align middle来设置垂直居中,可能会有细微的误差.

  3. 就解决办法

  一种方式是将最高的元素设为vertical-align:middle,然后将想要居中的也设定为vertical-align:middle.最高的会把整个盒子撑开,baseline + x-height
  就约在整个盒子中间的位置了.

  4. 垂直居中一种方案

  为父元素设置:after伪元素,display:inline-block,vertical-align:middle,这样就能撑开父元素了.再设置所有子元素vertical-align:middle,
  达到居中的目的.注意:由于IE8不支持:after伪元素,可用span替代.