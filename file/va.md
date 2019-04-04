
    我承认，之前我确实不太懂vertical-align属性

### 一. vertical-align 概念

 vertical-align用在哪些元素上面？

 该属性定义行内元素的基线相对于该元素所在行的基线的垂直对齐方式，允许指定负长度值和百分比值。这会使元素降低而不是升高。在表单元格中，
 这个属性会设置单元格框中的单元格内容的对齐方式。简单地说，就是 to inline-level and 'table-cell' elements 。

 内联盒子的组成：

 1. containing box。外层盒模型的大小。

 2. line boxes。一行算一个line boxes。

 3. inline boxes。简单的说就是一个元素标签，如，span。

 4. content area。围绕文字看不见的box，其大小与font-size有关，其高度可以认为鼠标选中文字时背景色的高度。

 ![内联盒子的组成](/img/va1.jpg)

 vertical-align的取值：

 1. baseline。默认的对齐方式，基线对齐，与父元素的基线对齐 ;

 2. top。与行中的最高元素的顶端对齐，一般是父级元素的最顶端对齐，会受到 line-height 影响 ;

 3. middle。 与父元素中线对齐(近似垂直居中);

 4. bottom。 与 top 相反，与父级元素的最低端对齐 ，会受到 line-height 影响 ;

 5. text-top。 与父级元素 content area 的顶端对齐，不受行高以及周边其他元素的影响 ;

 6. text-bottom。 与 text-top 相反，始终与父级元素 content area 的低端对齐。

### 二. 基线，baseline。

 什么是基线？简单的说，基线就是字母x底部所在的位置。在css中这叫baseline。

 1. inline-table元素的baseline是它的table第一行的baseline。

 2.