## 一. 什么是BFC，如何定义BFC？

    BFC全称是Block Formatting Context，即块格式化上下文。

    在MDN中是这么定义的。
    >A block formatting context contains everything inside of the element creating it that is not also inside a descendant element that creates a new block formatting context.
    *一个BFC包含创建该上下文元素的所有子元素，但不包括创建了新BFC的子元素的内部元素。*

    BFC是可翻译为块级格式化上下文。它是一个独立的渲染区域，规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干。

    与Block Formatting Context对应的是Inline formatting context，规定了内部的inline-level的Box如何布局。

## 二. BFC布局规则

    -1. BFC内部的盒子会从顶部开始在垂直方向上一个一个排列。

    -2. Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠

    -3. 每个元素的margin box的左边，与包含块border box的左边相接触（对于从左到右的格式化，否则相反）。即使存在浮动也是如此。

    提到包含块，那么什么是包含块？
        * 根元素所在的包含块叫初始包含块 initial containing block。
        * 对于其他元素，如果元素的position属性是relative或static，他的包含块是由最近的祖先块容器盒（block container ancestor box）的内容区域（content edge：width属性和height属性确定的区域）创建的。
        * 如果一个元素的position属性为fixed，他的包含块由视口创建或者由页面区域创建。
        * 如果元素的position为absolute，他的包含块由最近的position不为static的祖先元素创建，具体创建方式如下：
           A.如果创建包含块的祖先元素是行内元素（inline element），包含块的范围是这个祖先元素中的第一个和最后一个行内盒的padding box围起来的区域。
           B.如果这个祖先元素不是行内元素，包含块的范围是这个祖先元素的内边距+width区域（padding edge）。

## 三. 如何创建BFC

    * 1. 根元素或者其他包含他的元素。

    * 2. float属性不为none

    * 3. display为inline-block, table-cell, table-caption, flex, inline-flex

    * 4. position 属性值为：absolute | fixed

    * 5. overflow不为visible

