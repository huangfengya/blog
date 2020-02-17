## 什么是BFC

BFC 是一个独立的布局环境，其中的元素布局是不受外界影响的，并且所有的元素都会垂直的沿着父元素的左边排列。


## BFC 布局规则

1. 内部的Box会在垂直的方向一个接一个放置
2. Box 垂直的方向距离由 margin 决定，属于同一个 BFC 的margin 会发生重叠
3. BFC的区域不会与float box重叠。
4. 每个元素的左外边距与包含块的左边界相接触（从左向右），即使浮动元素也是如此
5. BFC 就是页面上的一个隔离的容器，容器里面的子元素不会影响外面，反之亦然
6. 计算BFC高度时，浮动元素也参与计算

## 怎么产生 BFC

1. \<html\>
2. float 属性不为 none
3. position 为 absolute 或 fixed
4. display 为 inline-block, table-cell, flex, inline-flex
5. overflow 不为 visible

## 应用

1. 清除浮动，因为计算 BFC 高度时，浮动元素也参与计算
2. 根据BFC的区域不会与float box重叠，实现自适应两栏布局。