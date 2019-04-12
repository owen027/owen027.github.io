---
title: JSquestion
date: 2019-04-10 10:39:57
categroies: Question
tags: JSquestion
---

#### 赋值运算符多次出现，优先计算问题(结合性)
> 赋值表达式为右结合
> 先从左到右解析各个引用，然后计算最右侧的表达式的值，最后把值从右到左赋给各个引用。
```javascript
a = b = c = v

/*
先解析 左侧的操作数
1. refa
2. refb
3. refc
4. value v
*/
refa = (refb = (refc = value v))

/* 再计算右侧表达式的值 然后和从右到左赋值
1. value v 赋值 refc
2. value v 赋值 refb
3. calue v 赋值 refa
4. 返回 value v
*/


 //example
var foo = {n:1};
var bar = foo;

foo.x = foo = {n:2};


/* 
变量名 存储在栈中， 引用对象存储在堆中
根据规则 先解析 =号左侧的引用 
 refx = (refo = {n:2})
再赋值
refo = {n:2} //refo  开辟新指针 foo  指向 {n:2} 
refx =  {n:2} // 在原refo指针对象中开辟新指针 指向 {n:2}

结果  refo.x == undefined

*/
/* 
或者
 foo.x赋值前是{n:1,x:unfiend};
赋值后{n:1,x:{n:2}};
*/
foo.x //undefined
bar.x // {n: 1, x: {n:2}};
```

#### split

```javascript
'81 newO'.split('') //["8", "1", " ", "n", "e", "w", "O"] 

'81 newO'.split(' ')// ["81", "newO"]

'81 newO'.split('').reverse().join('');
//"Owen 18"

```