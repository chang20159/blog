---
title: 理解BFC
tag:
- CSS
categories:
- CSS
---

网上有太多关于BFC的文章，看了很多，还是不能理解。想想还是看全英文的官方文档，关于BFC的规则，在w3c文档中的[第9章](https://www.w3.org/TR/CSS21/visuren.html)和[第10章](https://www.w3.org/TR/CSS21/visudet.html)全局搜一下Block formatting context，就能找到。
<!-- more -->
## 什么是BFC
CSS中每个元素都会根据盒模型生成0个或多个Box，Box是布局的对象和基本单位，Visual formatting model描述了一套规则，决定元素如何对其内容进行布局，以及与其他元素的关系和作用。盒布局由以下几个方面组成：

- 盒的尺寸和类型（明确指定、受限或没有指定   行内级盒、块级盒）
- 盒布局方案（常规流、浮动、绝对定位）
- 文档树中元素之间的关系
- 其他 如视口、图片大小等

对于浮动布局，由float属性决定
对于绝对定位布局，由position属性决定
而对于常规流的布局，则与display属性有很大关系

根据display属性的计算值，可以将box分为块级盒和行内级盒
- 块级盒    
    display的计算值 为 block, list-item 或 table 
- 行内级盒
    display的计算值为 inline, inline-block 或 inline-table 

在常规流中，不同的Box会参与不同formatting context（在这个格式化上下文中有一套渲染规则），block-level box（块级盒）参与block formatting context（BFC）中的规则，inline-level box（行内级盒）参与 inline formatting context(IFC)中的规则。

我们常常会让一个元素生成block formatting context（BFC），在这个block formatting context中的块级盒都遵循一套布局规则，下面会讲怎样让一个元素生成BFC以及块级盒在BFC中会遵循哪些规则


## 哪些元素会触发BFC
官方文档是这样说的
>Floats, absolutely positioned elements, block containers (such as inline-blocks, table-cells, and table-captions) that are not block boxes, and block boxes with 'overflow' other than 'visible' (except when that value has been propagated to the viewport) establish new block formatting contexts for their contents.

解释一下就是，以下这些元素可以生成BFC,其内部块级盒按照BFC的规则布局

- 浮动元素 float非none
- 绝对定位元素 position为absolute和fixed
- diplay属性为：inline-block | table-cell | table-caption等非块盒
- overflow属性是非visible值

一般情况下，都是使用overflow:hidden来触发BFC，对元素内部布局影响比较小，也不会影响其他元素

## BFC规则
官方文档是这样说的：
>In a block formatting context, boxes are laid out one after the other, vertically, beginning at the top of a containing block. 

---

>The vertical distance between two sibling boxes is determined by the 'margin' properties. Vertical margins between adjacent block-level boxes in a block formatting context collapse.

---

>In a block formatting context, each box's left outer edge touches the left edge of the containing block (for right-to-left formatting, right edges touch). This is true even in the presence of floats (although a box's line boxes may shrink due to the floats), unless the box establishes a new block formatting context (in which case the box itself may become narrower due to the floats).

---

>The border box of a table, a block-level replaced element, or an element in the normal flow that establishes a new block formatting context (such as an element with 'overflow' other than 'visible') must not overlap the margin box of any floats in the same block formatting context as the element itself.

---

>But in CSS 2.1, if, within the block formatting context, there is an in-flow negative vertical margin such that the float's position is above the position it would be at were all such negative margins set to zero, the position of the float is undefined.

---

>10.6.7 'Auto' heights for block formatting context roots
In certain cases (see, e.g., sections 10.6.4 and 10.6.6 above), the height of an element that establishes a block formatting context is computed as follows:

>If it only has inline-level children, the height is the distance between the top of the topmost line box and the bottom of the bottommost line box.

>If it has block-level children, the height is the distance between the top margin-edge of the topmost block-level child box and the bottom margin-edge of the bottommost block-level child box.

>Absolutely positioned children are ignored, and relatively positioned boxes are considered without their offset. Note that the child box may be an anonymous block box.

>In addition, if the element has any floating descendants whose bottom margin edge is below the element's bottom content edge, then the height is increased to include those edges. Only floats that participate in this block formatting context are taken into account, e.g., floats inside absolutely positioned descendants or other floats are not.

按照顺序来解释一下BFC规则就是这些
1、从包含块顶部开始，内部的box一个接一个垂直排列
2、两相邻box在垂直方向的距离由margin决定，**属于同一个BFC的相邻box的margin会发生重叠collapse**
3、在同一个BFC中，每个box的左外边缘（margin边界）与包含块的左边缘(border内边界)接触（对于从右至左排列的布局则相反）;这对于浮动元素也一样
4、**常规流中一个建立块格式化上下文的元素（触发BFC的元素）不会和任何浮动元素的外边界框重叠**
5、没明白啥意思 囧
6、关于BFC中元素高度的计算
 在某些情况下，建立块格式化上下文的元素的高度是这样计算的：
 - 如果它只有行内级child box，它的高度就是最上面的线框的顶部和最下面的线框的底部之间的距离；
 - 如果它有块级child box，它的高度就是最上面块child box的上外边界（margin-top）与最下面块child box的下外边界（margin-bottom）之间的距离；
 - 绝对定位的child box会被忽略，相对定位的child box不将偏移计算在内。注意child box可能是匿名块盒；
 - 另外，**如果这个元素有任何浮动后代的底部外边界超出了在这个元素底部内容边界，这些超出的边界也计算在元素的高度之内**
 注意：只有参与这个元素产生的BFC的浮动元素才会参与此元素的高度计算，在绝对定位的child box中的浮动元素不参与计算

## BFC的应用
这是一个测试的文档结构和样式
```html
<div class="container">
    <div class="div1"></div>
    <div class="div2"></div>
</div> 
<div class="other">
</div>
```

```css
.container{
    border:5px solid #000;
}
.div1{
    width: 200px;
    height: 100px;
    background-color: #43235e;
    margin: 10px 0;
}
.div2{
    width: 300px;
    height: 100px;
    background-color: #ee5511;
    margin: 10px 0;
}
.other{
    background-color: #666;
    height: 100px;
}
```

### 解决垂直margin重叠问题

div1和div2的上下外边距重叠,间距两者最大值：30px
<div style="border:5px solid #000;color:#fff;"><div style="width: 200px;
            height: 100px;
            background-color: #43235e;
            margin-bottom: 20px;">div1  margin-bottom: 10px</div><div style="width: 300px;
            height: 100px;
            background-color: #ee5511;
            margin-top: 30px;">div2   margin-top: 20px</div></div> <div style="background-color: #666;
            height: 100px;"></div>



第二条说：属于同一个BFC的相邻box的margin会发生重叠，那只要相邻的box不属于同一个BFC，垂直margin就不会重叠啦
<div style="border:5px solid #000;color:#fff;"><div style="overflow:hidden"><div style="width: 200px;
            height: 100px;
            background-color: #43235e;
            margin-bottom: 20px;">div1  margin-bottom: 10px</div></div><div style="width: 300px;
            height: 100px;
            background-color: #ee5511;
            margin-top: 30px;">div2   margin-top: 20px</div>
    </div> <div style="background-color: #666;
            height: 100px;"></div>

### 自适应多栏布局
下面div1左浮动，脱离了常规流，margin外边界与包含块左侧边框内边界接触，div2向上移动，与div1重叠。
<div style="border:5px solid #000;color:#fff;"><div style="width: 200px;
            height: 50px;
            background-color: #43235e;
            margin-bottom: 20px;float:left">div1 左浮动 </div><div style="width: 600px;
            height: 100px;
            background-color: #ee5511;">div2  </div>
    </div> <div style="background-color: #666;
            height: 100px;"></div>

根据第四条：**常规流中一个建立块格式化上下文的元素（触发BFC的元素）不会和任何浮动元素的外边界框重叠**
所以只要让div2形成BFC，就不会重叠了,这里还是用overflow的非visible属性生成BFC
  <div style="border:5px solid #000;color:#fff;"><div style="width: 200px;
            height: 50px;
            background-color: #43235e;
            margin-bottom: 20px;float:left">div1 左浮动 </div><div style="width: 600px;
            height: 100px;
            background-color: #ee5511;
            overflow:hidden;">div2触发BFC  </div>
    </div> <div style="background-color: #666;
            height: 100px;"></div>          
### 解决浮动元素父元素高度塌陷问题
这里只有div1是浮动的，因为浮动元素脱离文档流，不参与父元素高度的计算
<div style="border:5px solid #000;color:#fff;"><div style="width: 200px;
            height: 100px;
            background-color: #43235e;
            margin-bottom: 20px;float:left">div1 左浮动</div><div style="width: 500px;
            height: 50px;
            background-color: #ee5511;
            margin-top: 30px;
        ">div2   margin-top: 20px</div></div> <div style="background-color: #666;
            height: 100px;"></div>


根据第六条，
如果一个生成BFC的元素有任何浮动后代的底部外边界超出了在这个元素底部内容边界，这些超出的边界也计算在元素的高度之内
所以，如果让container触发BFC，浮动元素div1超出container的那部分也会计入container的高度,**包括浮动元素div1的外边距**
<div style="border:5px solid #000;overflow:hidden;color:#fff;"><div style="width: 200px;
            height: 100px;
            background-color: #43235e;
            margin-bottom: 20px;float:left">div1左浮动</div><div style="width: 500px;
            height: 50px;
            background-color: #ee5511;
            margin-top: 30px;
        ">div2   margin-top: 20px</div></div> <div style="background-color: #666;
            height: 100px;"></div>
## BFC兼容性
在IE6和IE7中不是所有元素都拥有布局，元素是否拥有布局由hasLayout属性决定，hasLayout与BFC相似，有触发hasLayoutyongyong（hasLayout == true）的条件，也有拥有布局后的一套规则。
触发hasLayout的条件有：
- float: 非none
- display: inline-block
- position: absolute
- width: 非auto
- height: 非auto
- zoom: 非normal值
- writing-mode: tb-rl

hasLayout == true时的规则：
- 相对定位的元素没有布局
- 拥有布局的元素外边距不叠加
- 等等。。

在IE6和IE7中可以通过为元素添加zoom：1属性触发haslayout，修复因浮动引起的高度塌陷问题


## 参考
- [Box model](https://www.w3.org/TR/CSS21/box.html#box-dimensions)
- [Visual formatting model](Visual formatting model)
- [Visual formatting model details](https://www.w3.org/TR/CSS21/visudet.html)
- [display](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display)
