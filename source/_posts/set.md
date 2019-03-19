---
title: Set
date: 2019-03-15 21:20:20
categories: 
- ES6
tags:
- Set 
---

## Set
 > ES6 提供的数据结构类似于数组，值是唯一的，通过 `Set` 构造函数来生成

 ```javascript
const arr = [1,2,2,3,4,5,3,2,3,4,5];
//参数可以是一个可以迭代的数据结构
const set = new Set(arr); //Set(5){1,2,3,4,5}
[...set] //[1,2,3,4,5]

const set1 = new Set('12343333433225');  //Set(5) {"1", "2", "3", "4", "5"}
set1.size //5

//数组去重
const arr1 = [... new Set([1,2,3,4,3,3,3,3])];
const arr2 = Array.from(new Set([1,2,3,4,3,3,3,3]));
```
### 属性和方法

```javascript
//属性
const s = new Set()
  s.constructor //指向 Set 函数 
  s.size //成员总数


/**
 * 操作方法 
 */

//也可以通过 add()方法添加 只有一个参数
const set2 =new Set();
set2.add(1) //Set(1) {1}

//不会发生类型转换  NaN 会 = NaN 
set2.add("1") //Set(2) {1,"1"}

set2.add(NaN).add(NaN)// Set(3) {1,"1",NaN}  链式调用

//两个对象不相等
set2.add({})
set2.size //4
set2.add({})
set2.size //5


//通过 delete() 删除某个值 返回 Boolean 值 
set2.delete(1) //true
set2.delete({}) //false 


//has()  是否存在某个值 
set2.has(NaN) //true
set2.has({}) // false


//clear() 清除所有成员
set2.clear() //Set(0) {}


/**
 * 遍历方法 
 */

/**
 *   keys 返回键名的迭代器， values 返回 键值的迭代器， 由于 Set 键名和键值 相同 所以
 * keys 和 values 方法返回值相同
 */
 const sK = new Set([1,2,3,4,5]).keys()   //SetIterator {1, 2, 3, 4, 5}
 const sV= new Set([1,2,3,4,5]).values()   //SetIterator {1, 2, 3, 4, 5}


 //enters 返回键值对
 for (let item of new Set([1,2,3,4,5]).entries()) {
  console.log(item);
}
// [1, 1]
// [2, 2]
// [3, 3]
// [4, 4]
// [5, 5]

//推荐使用for of 
const sI = new Set([1,2,3,4,5]);
for (let key of sI) {
 console.log(key); //1 2 3 4 5
}
// Set的遍历顺序就是插入顺序。使用 Set 保存一个回调函数列表，调用时能保证按照添加顺序调用。

//forEach
sI.forEach((value, key) => console.log(key + ' : ' + value))
// 1 : 1
// 2 : 2
// 3 : 3
// 4 : 4
// 5 : 5
```
### weakSet 
> 和Set 类似 但是只能存储对象，不可遍历
> 如果其他对象未应用该对象，垃圾回收机制会自动收回所占用的内存
> 适合存储临时对象和绑定对应对象的信息

```javascript
const ws = new WeakSet([[1, 2], [3, 4]]); //WeakSet {[1, 2], [3, 4]}
//a 的成员自动成为 weakSet 的成员

//add 方法
let arr =[[1]];
ws.add(arr);
//delete 删除对应成员
ws.delete(arr); //true
// has 是否包含
ws.has(arr) //false
```
由于 WeakSet 的成员都是弱引用随时可能被回收，因此可以存储DOM节点，不用担心节点移除时，发生内存泄漏。

```javascript
const foo = new WeakSet()
 class Foo {
   constructor(){
     foo.add(this)
   }
   method (){
     if(!foo.has (this)){
       throw new TypeError("method 只能在Foo 实例上使用");
     }
   }
 }
```