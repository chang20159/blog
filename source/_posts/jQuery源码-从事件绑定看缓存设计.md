---
title: 【jQuery源码】从事件绑定看缓存设计
tag:
- jQuery3.1.1源码
categories:
- JQuery
---
当事件绑定顺序如下：
```html
<div id="J_content">
    <button id="J_jump">跳转</button>
    <button id="J_jump1">跳转1</button>
</div>
<script src="./jquery-3.1.1.js"></script>
<script type="text/javascript">
    $('#J_content').on('click',clickHandler);
    $('#J_content').on('click',clickHandler);
    $('#J_content').on('click','#J_jump',clickHandler);
    $('#J_content').on('click','#J_jump1',clickHandler1);
    $('#J_content').on('mousedown','#J_jump1',clickHandler1);
</script>
```
<!-- more -->
dataPriv.get(elem)得到的结果如下，其中elem是id为J_content的DOM节点,dataPriv.get(elem)中存放的是elem元素的相关数据（这里是绑定的事件信息)。

而dataPriv由 var dataPriv = new Data();而来，jQuery设计了缓存系统，在给元素绑定事件时，事件信息不是直接依附在dom元素上，而是将事件信息放在缓存中，与dom元素关联。这样可以有效的避免内存泄漏。
![](/images/jqueryData/QQ20170208-223553@2x.png)

### jQuery缓存设计原理
var dataPriv = new Data();
elemData = dataPriv.get( elem );使用get方法去cache中取值。

jQuery缓存的设计思路是这样的：
- 对于DOM元素，给其扩展一个属性，属性名是jQuery.expando + Data.uid++的值
- 将数据存放在该属性中，也就是该属性的属性值就是要存储的数据
- 这样在读取、设置、和移除都是操作这个属性

直观一点就是下面这样，其实就是原本要直接附加到DOM元素上的对象，附加到DOM元素一个新的属性上。
![](/images/jqueryData/QQ20170209-210553@2x.png)
给DOM节点elem绑定事件，事件对象都存放在elem的jQuery311093659233154491051属性中
### Data类
这个缓存机制的实现由Data类完成,Data类的提供的实例方法都是对扩展属性(jQuery311093659233154491051)及其对象属性的操作。
![](/images/jqueryData/QQ20170208-223615@2x.png)
```javascript
function Data() {
    //每个页面都会生成一个jQuery的唯一标识
    // jQuery.expando: "jQuery" + ( version + Math.random() ).replace( /\D/g, "" ),如jQuery31109365923315449105
    this.expando = jQuery.expando + Data.uid++;
    //结果是： jQuery311093659233154491051
}
Data.uid = 1;
Data.prototype = {
    cache: function( owner ) { //当owner是id为J_content的DOM节点
        var value = owner[ this.expando ];
        if ( !value ) {
            value = {}; // cache中如果没有，就创建{},在内存中开辟区域存储
            if ( acceptData( owner ) ) {
                if ( owner.nodeType ) {
                    //并让DOM节点的jQuery311093659233154491051属性指向这个内存区域
                    owner[ this.expando ] = value;
                } else {
                    Object.defineProperty( owner, this.expando, {
                        value: value,
                        configurable: true
                    } );
                }
            }
        }
        return value;
    },
    get: function( owner, key ) {
        return key === undefined ? this.cache( owner ) :
            // Always use camelCase key (gh-2257)
            owner[ this.expando ] && owner[ this.expando ][ jQuery.camelCase( key ) ];
    },
    set: function( owner, data, value ) {... },
    access: function( owner, key, value ) {...},
    remove: function( owner, key ) {...},
    hasData: function( owner ) {...}
};
var dataPriv = new Data(); //jQuery内部使用,如事件
var dataUser = new Data(); //给开发者使用，如$.attr(),$.date()
```
### 参考
- [jQuery官网](https://jquery.com/)
- [jQuery GitHub](https://github.com/jquery/jquery)
- [慕课网 jQuery源码解析（架构与依赖模块）](http://www.imooc.com/learn/172)

