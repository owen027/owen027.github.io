---
title: performance
date: 2019-07-03 22:35:43
tags: 
- Iteration
---
# for,for...of, for...in, array iteration 运行速度
- 由于不同浏览器，不同版本性能不一，且控制台本质是是套用了一大堆[eval](https://www.zhihu.com/question/29352114/answer/44050599)，沙盒化程度高,所以需使用[node](https://nodejs.org/en/)环境测试来提高准确性
```javascript

// 准备待测数组
const NUM = 1e7;
let arr = new Array(NUM).fill(1);
// for 测试
let arr1 =[];
console.time('for');
for (let i = 0; i < arr.length; i++) {
  arr1.push(arr[i])
}

console.timeEnd('for');


// Chrome/75.0.3770.100 Safari/537.36 环境
//VM1324:10 for: 576.733154296875ms

// node v10.11.0 环境 
// for:  412.087ms
```

## for 几种写法

1. **常规写法**
```javascript
let arr1 = []
console.time('one')
for (let i = 0; i < arr.length; i++ ){
    arr1.push(arr[i])
}
console.timeEnd('one')

```
数组长度是会动态变化，每次循环会重新计算length长度，可能会出现死循环

2. **cache arr.length**
```javascript
for (let i = 0, len = arr.length; i < len; i++ ){
     arr1.push(arr[i])
}
```
缓存length 值，无需重新计算length

3. **倒序**
```javascript
for (let i = arr.length-1; i >= 0; i--){
     arr1.push(arr[i])
}
```
比第二种方法更简洁

 3.1  **倒叙简洁版**
```javascript
for (let i = arr.length-1; i--;){
     arr1.push(arr[i])
}
```
两个分号之间的表达式为 true 会一直执行直到 判断为 false (i = 0)

4. **正序简洁版**
```javascript
for (let i = 0, len;len = arr[i++]; ){
     arr1.push(arr[i])
}
```
当 i 大于等于数组长度或arr[i++]值为false时 将停指循环，同时由于arr.length动态变化时可能会造成死循环

## for...of
```javascript
for (let value of arr){
     arr1.push(value)
}
```
es6推出的迭代器，最简洁，可以是用 break，continue和return 终止循环

## for...in
```javascript
for (let key in arr){
     arr1.push(arr[key])
}
```
for...in 一般用于遍历对象，他会将本身属性和原型链上的属性（除系统内置属性）全部遍历出来即便是不可枚举属性（enumerable:false), 可以通过 `items.hasOwnProperty`来遍历本身属性,由于查询到自己的原型链上，所以性能方面比较差

## forEach
```javascript
arr.forEach(function(value){
    arr1.push(value)
})
     
```
数组的迭代方法，没有返回值

## map 
```javascript
arr.map(function(value){
   return  arr1.push(value)
})
     
```
浅拷贝原数组，并且返回一个新数组

## 性能测试



| 测试次数 | 常规for | cache for | 倒序 for | 倒叙简版 for | 正序简版 for | for..of | for..in | forEach | map |
| ------ | ------ | ------ | ------ | ------ | ------ | ------ |------ |------ |------ |------ |
| 1 | 542.121ms | 573.618ms | 764.181ms | 755.961ms | 571.464ms | 945.199ms | 4077.020ms | 625.859ms |3573.946ms |
| 2 | 430.008ms | 541.933ms | 524.474ms | 668.276ms | 553.475ms | 897.442ms | 4402.246ms | 605.271ms |2732.859ms |
| 3 | 409.531ms | 661.765ms | 534.167ms | 655.481ms | 600.939ms | 1141.093ms| 3806.704ms | 584.712ms |2779.192ms |
| 4 | 412.972ms | 643.868ms | 536.026ms | 674.081ms | 725.149ms | 930.655ms | 3201.387ms | 599.780ms |3152.499ms |
| 5 | 417.034ms | 624.323ms | 520.674ms | 799.568ms | 574.713ms | 943.449ms | 3261.512ms | 587.182ms |2954.195ms |
| 6 | 525.771ms | 955.737ms | 526.208ms | 771.443ms | 531.962ms | 954.199ms | 4351.009ms | 608.264ms |2888.752ms |
| 7 | 498.039ms | 602.703ms | 555.588ms | 531.464ms | 541.599ms | 916.678ms | 3264.334ms | 596.168ms |2834.663ms |
| 8 | 431.694ms | 523.381ms | 544.974ms | 527.472ms | 517.833ms | 1049.283ms| 3744.972ms | 600.286ms |3467.499ms |
| 9 | 417.521ms | 518.093ms | 547.404ms | 611.024ms | 594.503ms | 767.059ms | 4979.348ms | 601.420ms |3638.023ms |
|10 | 424.806ms | 557.961ms | 535.541ms |  837.561ms| 541.882ms | 772.686ms | 3284.424ms | 602.443ms |3599.642ms |
|11 | 409.402ms | 521.131ms | 534.265ms | 517.709ms | 551.397ms | 752.101ms | 3228.123ms | 629.625ms |3535.545ms |
|12 | 425.362ms | 532.882ms | 406.637ms | 522.287ms | 570.259ms | 914.135ms | 3449.256ms | 800.857ms |3429.123ms |
|平均值 | 439.2738 ms | 578.3565 ms |553.1468 ms |651.7057ms| 507.8718ms | 909.0785 ms | 3,686.9 6ms | 605.6298 ms | 3,221.5056 ms |
|堆值差 | 357245536 Byte | 357245808 Byte | 357245624 Byte | 357245872 Byte | 357246824 Byte | 199268080 Byte |757187208 Byte|357244456 Byte | 437247640 Byte | 



