### 一. 什么是BFC，如何定义BFC？

 BFC全称是Block Formatting Context，即块格式化上下文。

 在MDN中是这么定义的。
 > A block formatting context contains everything inside of the element creating it that is not also inside a descendant element that creates a new block formatting context.
 > 一个BFC包含创建该上下文元素的所有子元素，但不包括创建了新BFC的子元素的内部元素。

 BFC是可翻译为块级格式化上下文。它是一个独立的渲染区域，规定了内部的Block-level（display 属性为 block, list-item, table 的元素）的Box如何布局，并且与这个区域外部毫不相干。

### 二. BFC布局规则

 1. BFC内部的盒子会从顶部开始在垂直方向上一个一个排列。

 2. Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠

 3. 每个元素的margin box的左边，与包含块border box的左边相接触（对于从左到右的格式化，否则相反）。即使存在浮动也是如此。
 提到包含块，那么什么是包含块？

    * 根元素所在的包含块叫初始包含块 initial containing block。
    * 对于其他元素，如果元素的position属性是relative或static，他的包含块是由最近的祖先块容器盒（block container ancestor box）的内容区域（content edge：width属性和height属性确定的区域）创建的。
    * 如果一个元素的position属性为fixed，他的包含块由视口创建或者由页面区域创建。
    * 如果元素的position为absolute，他的包含块由最近的position不为static的祖先元素创建，具体创建方式如下：
        A.如果创建包含块的祖先元素是行内元素（inline element），包含块的范围是这个祖先元素中的第一个和最后一个行内盒的padding box围起来的区域。
        B.如果这个祖先元素不是行内元素，包含块的范围是这个祖先元素的内边距+width区域（padding edge）。

 4. BFC的区域不会与float box叠加

    正常情况下float元素会浮动在某个元素上方，当这个元素设置BFC后，则这个元素与浮动区域分开。

 5. BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。

    和第二条对应，可以去掉相邻Box的margin重叠现象。

 6. 计算BFC高度时，浮动元素也参与计算。

    清浮动的原理。

### 三. 如何创建BFC

 1. 根元素或者其他包含他的元素。

 2. float属性不为none

 3. display为inline-block, table-cell, table-caption, flex, inline-flex

 4. position 属性值为：absolute | fixed

 5. overflow不为visible

### 四. IFC

 与Block Formatting Context对应的是Inline formatting context，规定了内部的inline-level（display 属性为 inline, inline-block, inline-table）
 的Box如何布局，块级元素中仅包含内联级别元素,需要注意的是当IFC中有块级元素插入时，会产生两个匿名块将父元素分割开来，产生两个IFC。

 1. 子元素水平方向横向排列（float元素会优先排列），并且垂直方向起点为元素顶部。

 2. IFC的line box（线框）高度由其包含行内元素中最高的实际高度计算而来（不受到竖直方向的padding/margin影响)，同个IFC下的多个line box高度可能会不同。

 3. 在垂直方向上，子元素会以不同形式来对齐（vertical-align）。

 4. 能把在一行上的框都完全包含进去的一个矩形区域，被称为该行的行框（line box）。行框的宽度是由包含块（containing box）和与其中的浮动来决定。

 5. 当 inline-level boxes的总宽度少于包含它们的line box时，其水平渲染规则由 text-align 属性值来决定。

 6. 当一个 inline box 超过父元素的宽度时，它会被分割成多个boxes，这些 boxes 分布在多个 line box 中。如果子元素未设置强制换行的情况下，inline box
 将不可被分割，将会溢出父元素。

