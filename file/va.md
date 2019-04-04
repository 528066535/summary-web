
    我承认，之前我确实不太懂vertical-align属性

### 一. 概念

 vertical-align用在哪些元素上面？

 该属性定义行内元素的基线相对于该元素所在行的基线的垂直对齐方式，允许指定负长度值和百分比值。这会使元素降低而不是升高。在表单元格中，
 这个属性会设置单元格框中的单元格内容的对齐方式。简单地说，就是 to inline-level and 'table-cell' elements 。

 内联盒子的组成？

 ![内联盒子的组成](/img/va1.jpg)

 什么是基线？简单的说，基线就是字母x底部所在的位置。在css中这叫baseline。

 1. inline-table元素的baseline是它的table第一行的baseline。

 2.