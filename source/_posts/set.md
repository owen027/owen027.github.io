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

//也可以通过 add()方法添加 只有一个参数
const set2 =new Set();
set2.add(1) //Set(1) {1}

//不会发生类型转换 NaN 会 = NaN 
set1.add("1") //Set(2) {1,"1"},

//两个对象不相等
set1.add({})
set1.size //3
set1.add({})
set1.size //4
//数组去重
const arr1 = [... new Set([1,2,3,4,3,3,3,3])];
```