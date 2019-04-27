---
title: Map
date: 2019-03-19 18:48:52
categories: 
- ES6
tags:
- Map 
---

### Map
> 键值对的集合 键名的范围不限制于字符串

```javascript
const m = new Map();
const obj = {name: 'Owen'};
const fn = function(){};
//设值
m.set(obj,"boy"); //将 obj 作为键名， 给它赋值

//可以是对象，数组，甚至是函数，任何数据类型都可以当作属性名（键名）
m.set(fn,1) 
//对同一个键名赋值，后面的将覆盖前面的


//获值
m.get(obj) // 'boy'
//查询
m.has(obj) // true
//删除
m.delete(obj)//true

```
### Map 参数
> 任何 `Iterator` 接口，并且每个成员都是双元素数组的数据结构 都可以作为参数
```javascript
let arr = [[1,2],[2,3]];
const ma = new Map(arr); //Map(2) {1 => 2, 2 => 3}
ma.get(1) //2
ma.get(2) //3
//如果几组双元素成员相同那么只会生成一个值
arr = [[1,2],[1,2]];
mss = new Map(arr); //Map(1) {1 => 2}
ma.size//2
mss.size//1

// keys 键名
for (let key of ma.keys()){
    console.log(key)
    //1
    //2
}

//values 值
for (let val of ma.values()){
    console.log(val)
    //2
    //3
}

//entries  键值 等同于 遍历 ma 默认遍历的是entries 

for (let [key,val] of ma.entries()){
    console.log(key,val)
    //1,2
    //2,3
}

//转化为数组

[... ma.keys() ] //[1,2]

[... ma.values() ] //[2,3]

[... ma] //[[1,2],[2,3]]

```
