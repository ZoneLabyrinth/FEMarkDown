# BFC

**块级格式化上下文**，是一个独立的渲染区域，让处于BFC内部的元素与外部的元素相互隔离，使内外元素的定位不会相互影响。

> IE下为 Layout，可通过 zoom:1 触发

- 触发条件
  - 根元素
  - *position:absolute/fixed*
  - *display：inline-block/table*
  - *float*元素
  - *overflow!==visible2*
- 规则：
  - 属于同一个BFC的两个相邻box垂直排列
  - 属于同一个BFC的两个相邻box的margin会发生重叠
  - BFC中子元素的margin box左边，与包含块(BFC)border box的左边相接触(子元素absolute除外)
  - BFC的区域不会与float的元素区域重叠
  - 计算BFC的高度时，浮动子元素也参与计算
  - 文字层不会被浮动层覆盖，环绕于周围
- 应用
  - 阻止margin重叠
  - 可以包含浮动元素-清除内部浮动(清除浮动的原理是两个div都位于同一个BFC区域之中)
  - 自适应两栏布局
  - 可以阻止元素被浮动元素覆盖







## BEM

即`block`，`__element`，`—modifier`命令css类名

**block** ，一个快就是一个组件

**Modifier**  修饰符



```
[class*='button']:not([class*='button__']) { 
	padding: 0.5em 0.75em; 
}


```









