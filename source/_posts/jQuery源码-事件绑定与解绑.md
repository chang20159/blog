---
title: jQuery源码 - 事件绑定与解绑
tag:
- jQuery3.1.1源码
categories:
- JQuery
---
jQuery事件是DOM事件的封装，提供了一些事件绑定和触发的方法,并且支持自定义事件。
- 绑定事件 
  - [bind](http://jquery.cuishifeng.cn/bind.html)
  - [delegate](http://jquery.cuishifeng.cn/delegate.html)  
  - [on](http://jquery.cuishifeng.cn/on.html)
  - [one](http://jquery.cuishifeng.cn/one.html)
  - blur focus focusin focusout resize scroll ...等事件名方法
- 解绑事件 
  - [unbind](http://jquery.cuishifeng.cn/unbind.html)
  - [undelegate](http://jquery.cuishifeng.cn/undelegate.html)
  - [off](http://jquery.cuishifeng.cn/off.html)
- 触发事件 
  - [trigger](http://jquery.cuishifeng.cn/trigger.html)
<!-- more -->

### 对外提供的方法
从源码看
1、绑定事件的bind、delegate最终都是调用jQuery.prototype.on方法，jQuery.prototype.on调用的是一个全局的on方法 on( elem, types, selector, data, fn, one ) ，与on方法的区别：
- bind方法的selector是null,不支持传入selector
- delegate方法只是参数的传入顺序与on方法不同,jQuery 3.0中已弃用此方法，用 on()代替。

2、 移除事件的unbind、undelegate最终都是调用jQuery.prototype.off方法function( types, selector, fn ) ,与off方法的区别：
- unbind方法的selector是null,不支持传入selector
- undelegate方法只是参数的传入顺序与off方法不同

3、 one方法绑定的事件处理器只执行一次，它与on方法相同，调用的全局的on方法
```javascript
jQuery.fn.extend( {
    bind: function( types, data, fn ) {
        return this.on( types, null, data, fn );
    },
    unbind: function( types, fn ) {
        return this.off( types, null, fn );
    },
    delegate: function( selector, types, data, fn ) {
        return this.on( types, selector, data, fn );
    },
    undelegate: function( selector, types, fn ) {
        // ( namespace ) or ( selector, types [, fn] )
        return arguments.length === 1 ?
            this.off( selector, "**" ) :
            this.off( types, selector || "**", fn );
    }
} );
jQuery.fn.extend( {
    on: function( types, selector, data, fn ) {
        return on( this, types, selector, data, fn );
    },
    one: function( types, selector, data, fn ) {
        return on( this, types, selector, data, fn, 1 );
    },
    off: function( types, selector, fn ) {
        ....
    }
} );
```
jQuery还提供了一种绑定事件的方法,将事件类型名作为jQuery.prototype的方法属性名，所以我们还可以这样绑定事件：$('#id').click(eventHandler)。
```javascript
jQuery.each( ( "blur focus focusin focusout resize scroll click dblclick " +
    "mousedown mouseup mousemove mouseover mouseout mouseenter mouseleave " +
    "change select submit keydown keypress keyup contextmenu" ).split( " " ),
    function( i, name ) {
    // Handle event binding
    jQuery.fn[ name ] = function( data, fn ) {
        return arguments.length > 0 ?
            this.on( name, null, data, fn ) :
            this.trigger( name );
    };
} );
jQuery.fn.extend( {
    hover: function( fnOver, fnOut ) {
        return this.mouseenter( fnOver ).mouseleave( fnOut || fnOver );
    }
} );
```
### on函数
jQuery.prototype上绑定事件的方法 bind、delegate、on、one最终都是调用全局的on( elem, types, selector, data, fn, one )方法，有这些参数：
- elem 数组类型 要绑定事件的dom元素 
- types 事件对象(a map of types/handlers) 或者事件类型(click、change) 
- selector 选择器
- data 附加参数，在使用trigger方法触发事件时传入，用户操作触发时，data是undefined
- fn  事件处理函数，事件触发后要做的事情
- one 传入参数为1，事件只执行一次，然后移除事件
on方法的绝大部分代码都是在处理参数不同（数量和类型）的情况下，统一调用jQuery.event.add( this, types, fn, data, selector )方法
```javascript
function on( elem, types, selector, data, fn, one ) {
    var origFn, type;
    // Types can be a map of types/handlers
    // 例如$('#id').on({click:clickHandler,change:changeHandler},'#id0',{name:'on'})
    if ( typeof types === "object" ) { 
        // 【1】( types-Object, selector, data ) types是object时只有三个参数的情况
        // 当types是一个对象时，需要对每一个type依次绑定对应的事件处理器
        // 并在一次调用on方法前做个参数处理
        if ( typeof selector !== "string" ) {
            //【2】( types-Object, data )
            // 如果第三个参数不是string类型，那参数中就没有selector
            // 例如$('#id').on({click:clickHandler,change:changeHandler},{name:'on'})
            data = data || selector; // data = null || {name:'on'}
            selector = undefined;
        }
        for ( type in types ) {
            on( elem, type, selector, data, types[ type ], one );
        }
        return elem;
    }
    //如果第4位参数data 和第5位参数fn没有，即$('#id').on(param1,param2)这样只有两位参数
    //elem之后的参数依次是事件类型和事件处理函数
    if ( data == null && fn == null ) {
        // 【3】( types, fn ) 只有两个参数，事件类型和事件处理器
        fn = selector;
        data = selector = undefined;
    } else if ( fn == null ) {
        if ( typeof selector === "string" ) {
            // 【4】( types, selector, fn ) 三个参数，事件类型，过滤elem后代元素的选择器，事件处理器
            fn = data;
            data = undefined;
        } else {
            //【5】( types, data, fn )
            fn = data;
            data = selector;
            selector = undefined;
        }
    }
    //【6】fn也可以是一个false值，在on方法中fn为false时，转为一个直接返回false的函数，作为后续调用的jQuery.event.add方法的参数
    // false值就是做一个直接返回false的函数的简写
    if ( fn === false ) {
        fn = return false;
    } else if ( !fn ) { //如果没有事件处理器就啥也不干，返回这个dom元素
        return elem;
    }
    //【7】事件只会触发一次就移除时，对事件处理函数进行封装
    //第一次触发执行时，先移除事件处理器，再执行事件
    if ( one === 1 ) {
        origFn = fn;
        fn = function( event ) {
            // Can use an empty set, since event contains the info
            jQuery().off( event );
            return origFn.apply( this, arguments );
        };
        // Use same guid so caller can remove using origFn
        fn.guid = origFn.guid || ( origFn.guid = jQuery.guid++ );
    }
    return elem.each( function() {
        jQuery.event.add( this, types, fn, data, selector );
    } );
}
```
#### jQuery.event.add方法
jQuery.event.add将每个事件处理器封装成事件处理对象，包括唯一标识、事件处理函数、选择器等属性。
```javascript
add: function( elem, types, handler, data, selector ) {
        var handleObjIn, eventHandle, tmp,
            events, t, handleObj,
            special, handlers, type, namespaces, origType,
            elemData = dataPriv.get( elem );
        // 对elem第一次绑定事件前，elemData为空对象{}
        if ( !elemData ) {
            return;
        }
        // 传入的形式是{handler：handler，selector：selector}
        if ( handler.handler ) {
            handleObjIn = handler;
            handler = handleObjIn.handler;
            selector = handleObjIn.selector;
        }
        // 判断选择器是否存在
        if ( selector ) {
            jQuery.find.matchesSelector( documentElement, selector );
        }
        // 给事件处理器添加唯一标识
        if ( !handler.guid ) {
            handler.guid = jQuery.guid++;
        }

        // 第一次给该元素绑定时初始化elemData的events属性和handle属性
        if ( !( events = elemData.events ) ) {
            events = elemData.events = {};
        }
        if ( !( eventHandle = elemData.handle ) ) {
            eventHandle = elemData.handle = function( e ) {
                // Discard the second event of a jQuery.event.trigger() and
                // when an event is called after a page has unloaded
                return typeof jQuery !== "undefined" && jQuery.event.triggered !== e.type ?
                    jQuery.event.dispatch.apply( elem, arguments ) : undefined;
            };
        }
        types = ( types || "" ).match( rnothtmlwhite ) || [ "" ];
        t = types.length;
        while ( t-- ) {
            tmp = rtypenamespace.exec( types[ t ] ) || [];
            type = origType = tmp[ 1 ];
            namespaces = ( tmp[ 2 ] || "" ).split( "." ).sort();
            if ( !type ) { continue; }
            // If event changes its type, use the special event handlers for the changed type
            special = jQuery.event.special[ type ] || {};
            // If selector defined, determine special event api type, otherwise given type
            type = ( selector ? special.delegateType : special.bindType ) || type;
            // Update special based on newly reset type
            special = jQuery.event.special[ type ] || {};
            // handleObj is passed to all event handlers
            handleObj = jQuery.extend( {
                type: type,
                origType: origType,
                data: data,
                handler: handler,
                guid: handler.guid,
                selector: selector,
                needsContext: selector && jQuery.expr.match.needsContext.test( selector ),
                namespace: namespaces.join( "." )
            }, handleObjIn );

            // 在events对象中，存储该元素绑定事件对应的事件处理器数组
            // 对该元素的某一类型事件第一次绑定事件处理器时，对handlers初始化为空数组[]
            if ( !( handlers = events[ type ] ) ) {
                handlers = events[ type ] = [];
                handlers.delegateCount = 0;
                // Only use addEventListener if the special events handler returns false
                // 只有在此种条件下才会直接用addEventListener绑定事件
                if ( !special.setup ||
                    special.setup.call( elem, data, namespaces, eventHandle ) === false ) {
                    if ( elem.addEventListener ) {
                        elem.addEventListener( type, eventHandle );
                    }
                }
            }
            if ( special.add ) {
                special.add.call( elem, handleObj );
                if ( !handleObj.handler.guid ) {
                    handleObj.handler.guid = handler.guid;
                }
            }
            // 将处理器对象存放在dataPriv.get( elem )[type].handlers中
            // 如果是事件代理，处理器对象放在列表前面，用splice实现，将handleObj插入最后一个事件代理处理器之后 
            if ( selector ) {
                handlers.splice( handlers.delegateCount++, 0, handleObj );
            } else { //如果不是事件代理，直接放在列表最后，用push即可
                handlers.push( handleObj );
            }
            jQuery.event.global[ type ] = true;
        }
    }
```
当事件绑定顺序如下：
```javascript
$('#J_content').on('click',clickHandler);
$('#J_content').on('click',clickHandler);
$('#J_content').on('click','#J_jump',clickHandler);
$('#J_content').on('click','#J_jump1',clickHandler1);
$('#J_content').on('mousedown','#J_jump1',clickHandler1);
```
events的内容为：
![](/images/jqueryEventAdd/QQ20170208-213500@2x.png)
- 其中guid为事件处理器的唯一标识，事件处理器clickHandler的guid为1，clickHandler1的guid为2。
- events中存放每个事件对应的多个事件处理器对象
- 当类型相同、处理器相同是，也会存入一个事件处理器对象
- 对于同一类型的事件处理器，事件代理处理器总会放在前面
#### 事件绑定用法
```javascript
function clickHandler(){
    console.log("事件处理");
}
$('#J_confirm').click(clickHandler);
$('#J_confirm').on('click',clickHandler);
$('#J_confirm').on({
    click:  clickHandler,
    mousedown: clickHandler
});
$('#J_confirm').bind('click',clickHandler)
$('#J_content').on('click','#J_jump',clickHandler);
$('#J_content').one('click','#J_jump',clickHandler);
$('#J_confirm').one('click',clickHandler);
```
用trigger触发事件：
```
function clickHandler(e,name){
    console.log(name + " 事件处理");
}
$("#J_confirm").click(clickHandler);
$("#J_confirm").trigger("click", "use trigger");
// 可简写如下
$("#J_confirm").click(clickHandler).trigger("click", "use trigger");
```
### off方法
```javascript
off: function( types, selector, fn ) {
        var handleObj, type;
        if ( types && types.preventDefault && types.handleObj ) {
            // ( event )  dispatched jQuery.Event
            handleObj = types.handleObj;
            jQuery( types.delegateTarget ).off(
                handleObj.namespace ?
                    handleObj.origType + "." + handleObj.namespace :
                    handleObj.origType,
                handleObj.selector,
                handleObj.handler
            );
            return this;
        }
        if ( typeof types === "object" ) {
            // ( types-object [, selector] )
            for ( type in types ) {
                this.off( type, selector, types[ type ] );
            }
            return this;
        }
        if ( selector === false || typeof selector === "function" ) {
            // ( types [, fn] )
            fn = selector;
            selector = undefined;
        }
        if ( fn === false ) {
            fn = returnFalse;
        }
        return this.each( function() {
            jQuery.event.remove( this, types, fn, selector );
        } );
    }
```
#### jQuery.event.remove方法
remove方法最终调用javascript提供的removeEventListener
```javascript
if ( elem.removeEventListener ) {
        elem.removeEventListener( type, handle );
    }
```
#### 事件解绑用法
### 参考
- [jQuery官网](https://jquery.com/)
- [jQuery GitHub](https://github.com/jquery/jquery)
- [jQuery API 3.1.0 速查表  --作者：Shifone](http://jquery.cuishifeng.cn/index.html)
- [慕课网 jQuery源码解析（DOM与核心模块）](http://www.imooc.com/learn/222)