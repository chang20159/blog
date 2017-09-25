---
title: 理解Flexbox弹性盒布局
categories:
- CSS
declare: true
---

<img src="http://saki007ster.github.io/public/images/flex-container.png"/>
<!--more-->
## 基本概念
- 主轴： main axis
	- 主轴起点： main start
	- 主轴终点： main end
	
- 交叉轴（侧轴）： cross axis
	- 交叉轴起点： cross start
	- 交叉轴终点： cross end

- container和item，这两个不翻译最直白，叫容器和项目有点别扭

### 生成弹性盒

- display: flex  生成块盒
- inline-flex  生成行内盒


### container属性

- flex-direction

	- 定义container主轴方向
	- 可选值： row | row-reverse | column | column-reverse
	- 默认值： row
	
- flex-wrap
	
	- 控制container为单行还是多行，定义了侧轴的方向，控制多行时向哪个方向堆砌
	- 可选值：nowrap | wrap | wrap-reverse
	- 默认值：nowrap
	
- flex-flow

	- flex-direction 和 flex-wrap的简写，这两个属性加起来就可以控制弹性盒内item的布局流向了
	- 默认值： row nowrap

- justify-content

	- 设置container内所有item沿主轴上的对齐方式，并分配剩余可用空间。
	- 如果主轴横向，侧轴竖向， justify-content就是控制所有item在横向的位置布局。
	- 可选值：flex-start | flex-end | center | space-around | space-between
	- 默认值： flex-start

- align-items
	
	- 设置container内所有item在侧轴上的对齐方式
	- 可选值： flex-start | flex-end | center | baseline | stretch
	- 默认值： stretch
	- item可以通过设置align-self覆盖container的align-items属性

- align-content

	- 定义多根轴线之间的对齐方式，并分配剩余可用空间。若没有多根轴线，则不起作用
	- 可选值： flex-start | flex-end | center | space-around | space-between | stretch
	- 默认值： stretch

### item属性

- align-self
 
 - 设置item在侧轴方向的对齐方式，功能与align-items相同。可覆盖align-items属性。
 - 可选值： auto | flex-start | flex-end | center | baseline | stretch
 - 默认值：auto

- order

	- 设置item的序数顺序，值越小，越靠前
	- 默认值： 0

- flex-grow

 - 设置item的增长因数，不接受负值。
 - 按比例分配剩余空间，没有剩余空间时flex-grow无效
 - 默认值： 0

- flex-shrink

	- 设置item的收缩因素，不接受负值
	- 每个item按比例缩小，使总缩小空间为不足的空间，空间足够时flex-shrink无效
	- 默认值：1

- flex-basis

	- 设置item伸缩的基准值，不接受负值
	- 默认值： auto  类似于width

- flex

 - 设置item的尺寸，包括flex-grow、flex-shrink、flex-basis
 - 默认值： 0,1,auto


## 示例

[这里看更宽敞>>](http://chang20159.github.io/css-example/flex/index.html)
<iframe style="width:100%;height:1000px;border:none" 
src="http://chang20159.github.io/css-example/flex/index.html"></iframe>

## 应用
[这里看更宽敞>>](http://chang20159.github.io/css-example/flex/flex.html)
<iframe style="width:100%;height:800px;border:none" 
src="http://chang20159.github.io/css-example/flex/flex.html"></iframe>


## 参考

- [CSS 弹性盒子布局](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout)
- [使用 CSS 弹性盒子](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes)
- [使用弹性盒子进行高级布局](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/%E5%BC%B9%E6%80%A7%E6%A1%86%E7%9A%84%E9%AB%98%E7%BA%A7%E5%B8%83%E5%B1%80)
- [使用flexbox来布局web应用](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_flexbox_to_lay_out_web_applications)
- [box-sizing](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-sizing)
