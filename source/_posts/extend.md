---
title: Extend
date: 2019-03-19 19:28:30
categories: 
- ES6
tags:
- Extend Methods
---

### String Extend

**被 `for ... of` 遍历**
```javascript

for (let val of 'abc'){
    console.log(val);
    //a
    //b
    //c
}
```
**查找字符**
```javascript
let str = 'Hello Brother!';

//includes 是否包含参数字符串
str.includes('Br'); //true

//startsWith  开头是否存在参数字符串
str.startsWith('Hello') //true

//endsWith 结尾是否存在参数字符串
str.endsWith('er!')//true

```
**重复字符**
```javascript
//repeat  重复 n 次，  n 为参数,返回新字符串
let re = 'Hello Brother!';

re.repeat(2); //"Hello Brother!Hello Brother!"

re.repeat(-2); // Invalid count value
re.repeat(Infinity); // Invalid count value
//小数会取整 
re.repeat(1.9) // "Hello Brother!"
re.repeat(-0.9) // ""

```
**补全字符**
```javascript
//补全开头 padStart 补齐尾部 padEnd
let pd = 'Brother!'; 
pd.padStart(14,'Hello '); //"Hello Brother!"
//第一个参数为补齐的最大长度（整串字符） 第二个参数为补齐缺省的字符串

//如果原始字符长度大于等于最大长度，返回原字符串
pd.padStart(7,'Hello ')// 'Brother!'
pd.padStart(8,'Hello ')// 'Brother!'

//如果最大长度小于补齐长度，则去除超出部分
pd.padStart(9,'Aay ')//'ABrother!'

//如果缺省第二个参数则 默认使用空格补齐
pd.padStart(9)//' Brother!'
```


