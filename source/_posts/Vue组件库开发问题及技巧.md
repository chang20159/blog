---
title: Vue组件开发常见问题及技巧
tag:
- Vue
categories:
- Vue
---

Vue组件库开发，记录过程中的问题和经验。 手机端Vue组件库，戳>  [Swan UI](https://future-team.github.io/swan-ui/examples/index.html#/)

<!--more-->


### 一、props检测设定任意类型

设置为null

```
props: {
	title: null
}
```

### 二、组件外部传入class无法覆盖组件内部动态class

当给自定义组件添加class时，组件根元素上class的顺序是：

- inner-class（内部class）
- outer-class(外部传入class)
- inner-dynamic-class(内部动态计算class)

使用组件：

```xml
<sw-button class="outer-class">按钮</sw-button>
```

组件内部结构：

```xml
<button class="inner-class" :class="{'inner-dynamic-class':true}">
	<slot></slot>
</button>

```

最终结构：

```xml
<button class="inner-class outer-class inner-dynamic-class" >
	按钮 
</button>
```

对于需要修改组件样式的使用方，外部传入组件的class属性 outer-class 始终在内部动态属性之前，无法覆盖内部属性


**解决办法是：组件外部也是用动态class**

```xml
<sw-button :class="{'outer-class':true}">按钮</sw-button>
```


### 三、样式文件中引入npm安装包报错

<p style="color:red;">
Module not found: Error: Can't resolve './phoenix-styles/less/public.less' in '/Users/chang/future-team/swan-ui/src/modules'
 @ ./~/css-loader!./~/vue-loader/lib/style-compiler?{"vue":true,"id":"data-v-e577e6cc","scoped":false,"hasInlineConfig":false}!./~/vue-loader/lib/selector.js?type=styles&index=0!./src/modules/Input.vue 3:10-95
</p>
 
 *.vue文件中引入npm安装包:
 
 ```css
 <style lang="less" src="phoenix-styles/less/public.less"></style>
 ```
 
### 四、class属性不能作为组件的prop
<p style="color:red;">
 "class" is a reserved attribute and cannot be used as component prop.
</p>

我想拿到传给组件的class，不想让他直接添加到组件根元素上肿么破？

>Vue handles passing the class attribute through automatically - I think you can just delete the entire object and prop declaration and it will continue working fine.


### 五、将属性合并赋值

利用v-bind可以直接赋值对象

```xml
<template>
    <label class="ph-multi-group">
        <div :class="`ph-${type}`">
            <input v-bind="nativeProps" @change="handleChange"/>
            <i></i>
        </div>
        <span>{{label}}</span>
    </label>
</template>
```


### 六、 在vue的render方法中使用JSX

babel配置文件.babelrc添加插件 transform-vue-jsx

```json
{
  "presets": [["es2015", { "loose": false }]],
  "plugins": ["transform-vue-jsx"]
}

```

安装插件

```
npm install --save-dev babel-plugin-transform-vue-jsx
```

### 七、自定义 v-model + mixins
隐藏/显示类组件采用双向绑定，避免书写关闭打开等样板代码。

```javascript
/**
 * 定制隐藏/显示切换类组件的 v-model
 */
export default {
    model: {
        prop: 'visible',
        event: 'toggle'
    },
    methods: {
        handleToggle(val){
            this.$emit('toggle',val)
        }
    },
    props: {
        /**
         * 是否可见标识
         * @property visible
         * @type Boolean
         * */
        visible: {
            type: Boolean,
            default: false
        }
    }
}
```

### 八、声明组件props时，同时声明组件内部使用的其他组件的props

```xml
<template>
    <sw-checkbox v-bind="$props" @change="handleChange" />
</template>
```

```javascript
<script>
    import SwCheckbox from '../checkbox/Checkbox.vue'
    export default {
        name: 'SwRadio',
        components: {SwCheckbox},
        methods: {
            handleChange(value,evt){
                this.$emit('change',value,evt)
            }
        },
        props: Object.assign({},SwCheckbox.props,{
            type: {
                type: String,
                default: 'radio'
            }
        })
    }
</script>
```

**注意： 2.4.0版本新增了 inheritAttrs选项和实例属性$attrs，可以这样写了：**

```xml
<template>
    <sw-checkbox  :type="type" 
    				 v-bind="$attrs" 
    				 @change="handleChange" />
</template>
```

```javascript
<script>
    import SwCheckbox from '../checkbox/Checkbox.vue'
    export default {
        name: 'SwRadio',
        inheritAttrs: false,
        components: {SwCheckbox},
        methods: {
            handleChange(value,evt){
                this.$emit('change',value,evt)
            }
        },
        props: {
            type: {
                type: String,
                default: 'radio'
            }
        }
    }
</script>
```

### 九、双向watch或双向computed解决使用v-model的问题

例如使用dialog组件：

```xml
<sw-dialog title="提示" v-model="visible">
    您确定以及肯定要提交吗？
</sw-dialog>
```
当另一个组件Alert使用dialog组件时：

```xml
<template>
	<sw-dialog title="提示" v-model="visible">
	    {{content}}
	</sw-dialog>
</template>
```

那如果我们要使用Alert组件:

```xml
<sw-alert title="提示" v-model="visible" content="您确定以及肯定要提交吗？" />
```

由于内部dialog组件使用双向绑定，弹框关闭时，visible属性会被直接更改，不能通知sw-alert。这种情况在使用数据管理框架例如vuex时也可能出现。

那我们可以添加一个中间属性，使用双向watch或双向computed来解决。

在alert组件组件中双向watch：

```xml
<sw-alert title="提示"  v-model="currentVisible" content="您确定以及肯定要提交吗？" />
```

```javascript
model: {
    prop: 'visible',
    event: 'toggle'
}
watch: {
    visible(val){
        this.currentVisible = val
    },
    currentVisible(val){
        this.$emit('toggle',val) 
    }
}
```

或者双向computed(这个没有具体实施，应该是可以的):

```javascript
model: {
    prop: 'visible',
    event: 'toggle'
}
computed: {
    visible(){
    	 this.$emit('toggle',this.currentVisible) 
    	 return this.currentVisible
    },
    currentVisible(){
        return  this.visible
    }
}
```

### 十、props属性不需要声明也可以使用

  2.4.0版本之前，父作用域传递的属性，在props中声明才可以使用，这对类似Input之类的组件扩展性不好，需要把input属性都声明出来，有点啰嗦。
  
  vue 2.4.0新增的inheritAttrs选项和实例属性$attrs， 可以解决这个问题啦～
  
  **inheritAttrs选项:**
  
  >默认情况下父作用域的不被认作 props 的特性绑定 (attribute bindings) 将会“回退”且作为普通的 HTML 特性应用在子组件的根元素上。
  
  >当撰写包裹一个目标元素或另一个组件的组件时，这可能不会总是符合预期行为。通过设置 inheritAttrs 到 false，这些默认行为将会被去掉。而通过 (同样是 2.4 新增的) 实例属性 $attrs 可以让这些特性生效，且可以通过 v-bind 显性的绑定到非根元素上。
  
 >**注意：这个选项不影响 class 和 style 绑定。**

**实例属性$attrs:**

>包含了父作用域中不被认为 (且不预期为) props 的特性绑定 **(class 和 style 除外)**。当一个组件没有声明任何 props 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件——在创建更高层次的组件时非常有用。
  
之前对于Input组件，我需要声明尽可能多的常用的input的原生属性，并手动分离出来添加到input标签上：

```xml
<input v-bind="nativeProps"/>
```

```javascript
computed: {
	nativeProps(){
        let props = ['name','maxlength','minlength','disabled','autocomplete','autofocus','min','max','readonly']
	    let nativeProps = {}
	    props.forEach((key)=>{
	        if(this.$props[key]){
	            nativeProps[key] = this.$props[key]
	        }
	    })
	    return nativeProps
	}
},
props:{
    visible: Boolean,
    clear: Boolean,
    //以下是input原生属性
	placeholder: String,
    disabled: Boolean,
    maxlength: Number,
    minlength: Number,
    autocomplete: String,
    autofocus: Boolean,
    readonly:Boolean,
    value: null,
    name: null,
    min:null,
    max:null
}
```

现在可以这样写了：

```xml
<input v-bind="$attrs"/>
```

```javascript
inheritAttrs: false,
props: {
	visible: Boolean,
	clear: Boolean
}
```





