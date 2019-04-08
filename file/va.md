
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

 ![内联盒子的组成](/img/va1.jpg)

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

  1. 正常情况下,下方会有空隙

  > <div class="ctn-block"> </br>
  >  <img style="height:100px;width:100px;"> </br>
  >  <div class="child child-1"></div> </br>
  >  <div class="child child-2"></div> </br>
  >  <div class="child child-3"></div> </br>
  >  <span>123</span> </br>
  >  <img style="height:100px;width:100px;"> </br>
  > </div> </br>

  ![下方的空隙](/img/va2.jpg)