---
title: Immutable.js 使用mergeWith解决Map和List相互嵌套时的合并问题
categories:
- 工作
---

这里有两组不可变数据，Map里面有List，List 又包含Map。

```javascript
const defaultData = Immutable.fromJS({
	  condition:{
	    mainCategory: 23,
	    category: 2185
	  },
	  categoryOptions: [
	        { id:51, name:"KTV" },
	        { id:2184, name:"录音棚" },
	        { id:2185, name:"其他KTV" }
	  ],
	  regionOptions: [
	      { id: 1924, name: "古城/八角" }
	  ]
})

const data = Immutable.fromJS({
	  condition:{
	    category: 275
	  },
	  categoryOptions: [
	      { id:275, name: "洗浴足疗养生" },
	      { id:2139, name: "棋牌" }
	  ]
})
```
要将data与defaultData合并，Immutable.js合并有哪些方法？ 

- merge 
- mergeDeep
- mergeWith

<!-- more -->
可以使用merge或者mergeDeep方法合并，都只对相同的key合并，不同的key保留，但merge是浅合并，mergeDeep是深合并。

```javascript
const resultMerge = defaultData.merge(data)
console.dir(resultMerge.toJS())

const resultMergeDeep = defaultData.mergeDeep(data)
console.dir(resultMergeDeep.toJS())
```

上面代码执行后，merge的结果是：

```json
{
    "condition":{
        "category":275
    },
    "categoryOptions":[
        { "id":275, "name":"洗浴足疗养生" },
        { "id":2139, "name":"棋牌" }
    ],
    "regionOptions":[
        { "id":1924, "name":"古城/八角" }
    ]
}
```
可以看到，具有相同key的 condition和categoryOptions完全被替代了，regionOptions因为没有相同key比较因而保留。

mergeDeep的执行结果是：

```json
{
    "condition":{
        "mainCategory":23,
        "category":275
    },
    "categoryOptions":[
        { "id":275, "name":"洗浴足疗养生" },
        { "id":2139, "name":"棋牌" },
        { "id":2185, "name":"其他KTV" }
    ],
    "regionOptions":[
        { "id":1924, "name":"古城/八角" }
    ]
}
```
Map的深度合并会根据key合并Map的内容，数组深度合并也一样，会按照数组索引合并，所以就有上面的结果，前者的categoryOptions第一项和第二项被后者第一项和第二项替换了，但第三项仍然保留。

再举个例子

```javascript
const array = Immutable.fromJS(['a','b','c'])
const array1 = Immutable.fromJS(['a','c'])

const result = array.mergeDeep(array1)
//output:  ['a','c','c']
```

数组的这种深合并符合深度合并的概念，但是一般不符合实际应用需求。

比如上面defaultData中的categoryOptions属于‘K歌’这个分类，data中的categoryOptions属于‘休闲娱乐这个分类’,我不希望合并之后两个分类混杂在一起，而是希望完全被替代

## Map深合并，List浅合并
幸好，Immutable.js提供了mergeWith方法，我可以自定义合并的逻辑。

mergeWith的用法：

```javascript
const resultMergeWith = defaultData.mergeWith(merger,data)
```
这样我只要实现merger方法：

```javascript
function merger(prev,next){
	// console.log(prev,next);
	if(Immutable.List.isList(prev) && Immutable.List.isList(next)){
		return next
	}
	if(prev && prev.mergeWith){
		return prev.mergeWith(merger,next)
	}
	return next
}
```

就可以得到resultMergeWith的结果：

```json
{
    "condition":{
        "mainCategory":23,
        "category":275
    },
    "categoryOptions":[
        { "id":275, "name":"洗浴足疗养生" },
        { "id":2139, "name":"棋牌" }
    ],
    "regionOptions":[
        { "id":1924, "name":"古城/八角" }
    ]
}
```
完美~~~

## Map深合并，List Concat
我在项目中遇到的场景，是需要像上面那样将数组全部替换的，应该也有需要把数组concat的场景吧，也是可以自己实现一个merger方法：

```javascript
function merger(prev,next){
  // console.log(prev,next);
  if(Immutable.List.isList(prev) && Immutable.List.isList(next)){
    return prev.concat(next)
  }
  if(prev && prev.mergeWith){
    return prev.mergeWith(merger,next)
  }
  return next
}
```

那结果就是下面这样的：

```json
{
    "condition":{
        "mainCategory":23,
        "category":275 
    },
    "categoryOptions":[
        { "id":51, "name":"KTV" },
        { "id":2184, "name":"录音棚" },
        { "id":2185, "name":"其他KTV" },
        { "id":275, "name":"洗浴足疗养生" },
        { "id":2139, "name":"棋牌" }
    ],
    "regionOptions":[
        { "id":1924, "name":"古城/八角" }
    ] 
}
```

完毕~~

## 参考
- [Immutable.js](https://facebook.github.io/immutable-js/)
- [mergeDeep clobbers List items; concatDeep isn't implemented](https://github.com/facebook/immutable-js/issues/406)


    
