---
title: Vue组件开发常见问题及技巧
tag:
- Vue
categories:
- Vue
---

Vue组件库开发，记录过程中的问题和经验。 手机端Vue组件，戳>  [Swan UI](https://github.com/future-team/swan-ui)

<!--more-->

## 常见问题
### 一、@click.native 组件原生事件触发两次
调成手机模式，原因还没弄清楚...囧

### 二、props检测设定任意类型

设置为null

```
props: {
	title: null
}
```

### 三、组件外部传入class无法覆盖组件内部动态class

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

### 四、根节点需唯一
<p style="color:red;">
Component template should contain exactly one root element. If you are using v-if on multiple elements, use v-else-if to chain them instead.
</p>

外包一个div

### 五、找不到vue-template-compiler模块
<p style="color:red;">
ERROR in ./src/app.vue
Module build failed: Error: Cannot find module 'vue-template-compiler'
</p>
			
```npm install vue-template-compiler --save``` 不行

加上```sudo npm install vue-template-compiler```

### 六：组件未注册
<p style="color:red;">

[Vue warn]: Unknown custom element: <sw-button> - did you register the component correctly? For recursive components, make sure to provide the "name" option.

</p>

添加：

- Vue.use(SwanUI)
- Vue.component(Button.name, Button)


### 七： webpack2中的后缀-loader不能省略
<p style="color:red;">
Module build failed: Error: The node API for `babel` has been moved to `babel-core`.</p>

webpack中的loader：  babel 改成 babel-loader

### 八、样式文件中引入npm安装包报错

<p style="color:red;">
Module not found: Error: Can't resolve './phoenix-styles/less/public.less' in '/Users/chang/future-team/swan-ui/src/modules'
 @ ./~/css-loader!./~/vue-loader/lib/style-compiler?{"vue":true,"id":"data-v-e577e6cc","scoped":false,"hasInlineConfig":false}!./~/vue-loader/lib/selector.js?type=styles&index=0!./src/modules/Input.vue 3:10-95
</p>
 
 *.vue文件中引入npm安装包:
 
 ```css
 <style lang="less" src="phoenix-styles/less/public.less"></style>
 ```
 
### 九、class属性不能作为组件的prop
<p style="color:red;">
 "class" is a reserved attribute and cannot be used as component prop.
</p>

我想拿到传给组件的class，不想让他直接添加到组件根元素上肿么破？

>Vue handles passing the class attribute through automatically - I think you can just delete the entire object and prop declaration and it will continue working fine.


## 开发技巧

### 一、将属性合并赋值

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

-
### 二、 在vue的render方法中使用JSX

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

### 三、自定义 v-model + mixins
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

### 四、声明组件props时，同时声明组件内部使用的其他组件的props

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

### 五、双向watch或双向computed解决使用v-model的问题

## 我希望vue可以改进的地方

- props校验希望能自定义错误提示信息
- props属性必须声明才有用，这对类似Input之类的组件扩展性不好，需要把input属性都声明出来，有点啰嗦。






