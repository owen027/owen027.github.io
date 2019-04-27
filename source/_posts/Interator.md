---
title: Interator
date: 2019-04-04 19:32:22
categories: 
- ES6
tags:
- Interator
---

## Interator
> "集合"数据的结构主要有 `Array` 、`Object`、 `Set`  and `Map` ，任何数据结构只要部署 Iterator 接口，就可完成遍历操作

**遍历过程：** 
- 创建指针，指向当前数据结构起始位。（遍历对象本质是一个指针对象）。
- 依次循环调用指针对象的 `next`方法，对应指向数据结构成员，直至结束。
`
每次调用 `next` 方法，返回对象当前成员的信息 `{value：text,done:true}`,其中`value` 表示成员值，`done` 表示遍历是否结束

```javascript
let arr = ['Owen','18'];

let makeInterator = array =>{
    let  index = 0;
    return {
       next (){
        return index < array.length ? 
        {value:array[index++],done:false} : 
        {value:undefined, done:true}
        }
    }
}

let  inter = makeInterator(arr);
inter.next()
it.next() // { value: "Owen", done: false }
it.next() // { value: 18, done: false }
it.next() // { value: undefined, done: true }

```
对与遍历器对象来说 `done: false` and `value:undefined` 可省略

原生具备 Iterator 的数据结构：`Array` 、 `String` 、 `Map`、 `Set` 、`arguments` and `NodeList`
> 都默认部署 `[Symbol.iterator]` 方法
```javascript
//
const obj = {
  [Symbol.iterator] : function () {
    return {
      next: function () {
        return {
          value: 1,
          done: true
        };
      }
    };
  }
};
obj[Symbol.iterator]().next() //{value: 1, done: true}

let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();

iter.next() // { value: 'a', done: false }
iter.next() // { value: 'b', done: false }
iter.next() // { value: 'c', done: false }
iter.next() // { value: undefined, done: true }
iter.next() // { value: undefined, done: true }
```

>对象（Object）之所以没有默认部署 Iterator 接口，是因为对象的哪个属性先遍历，哪个属性后遍历是不确定的，需要开发者手动指定。
```javascript

//原型上部署Interator
class RangeIterator {
  constructor(start, stop) {
    this.value = start;
    this.stop = stop;
  }

  [Symbol.iterator]() { return this; }

  next() {
    var value = this.value;
    if (value < this.stop) {
      this.value++;
      return {done: false, value: value};
    }
    return {done: true, value: undefined};
  }
}

function range(start, stop) {
  return new RangeIterator(start, stop);
}

for (var value of range(0, 3)) {
  console.log(value); // 0, 1, 2
}

//对象上部署Interator
function Obj(value) {
  this.value = value;
  this.next = null;
}

Obj.prototype[Symbol.iterator] = function() {
  var iterator = { next: next };

  var current = this;

  function next() {
    if (current) {
      var value = current.value;
      current = current.next;
      return { done: false, value: value };
    } else {
      return { done: true };
    }
  }
  return iterator;
}

var one = new Obj(1);
var two = new Obj(2);
var three = new Obj(3);

one.next = two;
two.next = three;

for (var i of one){
  console.log(i); // 1, 2, 3
}
```
#### 类似数组对象，可引用 `Array.prototype[Symbol.iterator]`

```javascript
//NodeList
NodeList.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator];
// 或者
NodeList.prototype[Symbol.iterator] = [][Symbol.iterator];

[...document.querySelectorAll('div')] 

//obj
let iterable = {
  0: 'a',
  1: 'b',
  2: 'c',
  length: 3,
  [Symbol.iterator]: Array.prototype[Symbol.iterator]
};
for (let item of iterable) {
  console.log(item); // 'a', 'b', 'c'
}

```

### 一些 默认调用 Iterator 的操作


```javascript

// 解构赋值
let set = new Set().add('a').add('b').add('c');

let [x,y] = set; // x='a'; y='b'

let [first, ...rest] = set; // first='a'; rest=['b','c'];

//扩展运算符

var str = 'hello';
[...str] //  ['h','e','l','l','o']

let arr = ['b', 'c'];
['a', ...arr, 'd']  // ['a', 'b', 'c', 'd']


//yield*后面跟的是一个可遍历的结构，它会调用该结构的遍历器接口。

let generator = function* () {
  yield 1;
  yield* [2,3,4];
  yield 5;
};

var iterator = generator();

iterator.next() // { value: 1, done: false }
iterator.next() // { value: 2, done: false }
iterator.next() // { value: 3, done: false }
iterator.next() // { value: 4, done: false }
iterator.next() // { value: 5, done: false }
iterator.next() // { value: undefined, done: true }

// for...of
//for...of循环可以使用的范围包括数组、Set 和 Map 结构、某些类似数组的对象（比如arguments对象、DOM NodeList 对象）、 Generator 对象，以及字符串。
let arr = document.querySelectorAll("p");

for (let item of arr ) {
    console.log(item)
}

/* 其他操作*/

Array.from()
Map(), Set(), WeakMap(), WeakSet() //生成对应数据结构
Promise.all()
Promise.race()

```
### 遍历器return方法和throw方法

#### return()
> return方法必须返回一个对象。
>如果一个对象在完成遍历前，需要清理或释放资源，就可以部署return方法。
>如果for...of循环提前退出（通常是因为出错，或者有break语句），就会调用return方法。

```javascript
function readFile(file) {
    return {
        [Symbol.iterator]() {
            return {
                next() {
                    return {done:false}
                },
                return(){
                    file.close()
                    return {done:true}
                }
            };
        }
    };
}

//触发 return()
for (let line of readFile(fileName)) {
  console.log(line);
  break;
}
//触发 return()
for (let line of readLinesSync(fileName)) {
  console.log(line);
  throw new Error();
}

```

### for...of 和其他遍历语法对比

```javascript
let arr = [1,2];
//普通 `for`循环
 
for (var index = 0; index <arr.length; index++) {
  console.log(arr[index]);
}

/**
 * 书写比较麻烦
*/

// Array.forEach 

arr.forEach(function(item){
    console.log(item)
})

/**
 * 无法跳出循环，无法使用 break or return 
 */

// for...in

for (let index in arr) {
  console.log(arr[index]);
}

/**
 * 1.数组键名为字符串形式的数字
 * 2.会遍历出原型链上的键，和手动添加的键
 * 3.有些情况是无顺序遍历
 */

for (let item of arr) {
  console.log(item);
}

/**
 * 1.语法简洁
 * 2.没有 for...in 的缺点
 * 3.可以使用 return、break and continue
 * 
 */
```