---
title: Extend
date: 2019-03-19 19:28:30
categories: 
- ES6
tags:
- Extend Methods
---

###  String Extend

#### **被 `for ... of` 遍历**
```javascript

for (let val of 'abc'){
    console.log(val);
    //a
    //b
    //c
}
```
#### **查找字符**
```javascript
let str = 'Hello Brother!';

//includes 是否包含参数字符串
str.includes('Br'); //true

//startsWith  开头是否存在参数字符串
str.startsWith('Hello') //true

//endsWith 结尾是否存在参数字符串
str.endsWith('er!')//true

```
#### **重复字符**
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
#### **补全字符**
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

#### **模板字符**
> 通过反引号 “ `` ” 当普通字符串使用
```javascript
//所有换行和空格会保留，嵌入变量，须将变量包含在 `${}` 中，并且可进行运算,函数调用，对象调用
let num =1,obj ={name:'Owen'}, fn=()=> 3;
const str =` 
${num * 3 + 1 + fn() + obj.name}`;

console.log(str)
/* 
" 
7Owen"
*/

//如果紧更在函数名后，函数将被调用（标签模板）
alert`123`  // 等同于
alert(123)

```
**转义符 `\`**
```javascript
//以 x 开头,会被当做 16 进制

 `\x23` // #

 //以 u 开头,会被当做 unicode  字符
 `\u004F` //"O"

//如果无法编译将会报错

 ```
ES2018 放松了对`标签模板`里面的字符串转义的限制，无法转义的返回`undefined`；

```javascript
console.log`\uw`;
//[undefined, raw: Array(1)]
//undefined

```

----

### Function Extend

####  **形参指定默认值**
> 形参 不能再次使用 let 和 const 声明
> 形参不能重名 
> 函数 length 不包含设置默认值和后面的形参个数
> 使用 `...arg` 中的参数 length 也不包含
```javascript

const fn = (x, y = 'Owen') =>( console.log(x,y));
fn(1) // 1 "Owen"

//默认参数 惰性求值
let x = 99;
function foo(y = x + 1) {
  console.log(y);
}
foo() // 100
x = 100;
foo() // 101
//调用一次计算一次

```

 > 事实上 每次调用函数，如果不传递参数， 形参默认传递 `undefined`
 ```javascript
 // 默认参数最好定义再尾部，因为使用形参默认参数，那么那个位置的形参必传

function f(x, y = 5, z， ...arg) {
  return [x, y, z];
}

f() // [undefined, 5, undefined]
f(1) // [1, 5, undefined]
f(1, ,2) // 报错
f(1, undefined, 2) // [1, 5, 2]

//length 不包含设置默认值 和后面的形参 的个数，
f().length // 1

```
#### **作用域**
> 函数中的<label>变量无法访问</label> 默认值
> 函数中的形参名不能和默认名一样
```javascript
//函数变量无法访问默认值
function f(y = x) {
  let x = 2;
  console.log(y);
}

f() // ReferenceError: x is not defined

//函数中的形参名不能和默认名一样
//参数x = x形成一个单独作用域。实际执行的是let x = x，由于暂时性死区的原因，这行代码会报错
function f(x = x) {
  console.log(x);
}
f()//  x is not defined

var x = 1;
function foo(x, y = function() { x = 2; }) {
  var x = 3;
  y();
  console.log(x);
}

foo() // 3
x // 1
```
由于  var 声明的 x 和函数形参 x 不再同一个作用域 ， 因此调用 y() x值不变；
如果 去掉 var , 那么 x 就指向 形参 x ,调用 y() x = 2。

####  **reset 参数 （...）**
> 使用形式 `...arg`  实数以数组的形势赋给变量
> reset 参数后不能再有形参，否则报错
```javascript
function fn (a,...arg){
    return arg;
}
fn(0,2,3,4,5)//[2,3,4,5]

function foo (a,...arg,b){
    return arg;
}
//ught SyntaxError: Rest parameter must be last formal parameter
```
<label>只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错。 </label>
```javascript
// 报错
function f(a, b = a) {
  'use strict';
  // code
}

// 报错
const foo = function ({a, b}) {
  'use strict';
  // code
};

// 报错
const fn = (...a) => {
  'use strict';
  // code
};

const obj = {
  // 报错
  fn({a, b}) {
    'use strict';
    // code
  }
};
```

####  **箭头函数**
>使用 ` () => `  定义函数
注意：
- this 指向函数定义时所绑定的普通函数，不会被(bind,call,apply)更改,也不会被调用时的上下文改变。
```javascript
let fn = () =>console.log(this);
let obj = {name:"Owen"};

fn.call(obj) //window

fn.bind(obj)
fn() //window

fn.apply(obj)  //window
 
 
 //可以通过改变宿主环境来改变 this 指向
 
 function foo (){
 
return () =>{ 
  console.log(this);

  return ()=> {console.log(this)};
  }
}
foo.call(obj)() //{name: "Owen"}
foo.call(obj)()() //{name: "Owen"} {name: "Owen"}

```

- 外层没有普通函数 ，严格模式和非严格模式下它的this都会指向window(全局对象)。

- 不可以当作构造函数，也就是说，不可以使用new命令，没有`prototype`属性，不支持`new.target`,否则会抛出一个错误。
- 参数和箭头之间不能换行
- 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

- 不可以使用yield命令，因此箭头函数不能用作 Generator 函数。

```javascript 

//定义简单函数
let fn = () => 'Owen';
fn()// 'Owen'

let foo = r => r;
foo('Owen') // 'Owen'

let f = (num1,num2) => num1 + num2;
f(1,2)//3

//如果返回一个对象需要小括号包裹,f否则会报错
let f = (name,age) => ({name,age});
f('Owen',18)//{name: "Owen", age: 18}


//如果代码部分大于一条语句，那么需要 大括号包裹，使用return 返回值

let fn1 = r => {
    let a = 1;
    console.log(a);
    return r + a;

}

####  **不推荐使用场景**

- 在对象中使用 this.

```javascript
var obj = {
  gender:"man",
  getSex: () =>  {console.log(this.gender)}
}
obj.getSex() //undefined
//this -> global

```
- 动态使用 this. 

```javascript
var lis = document.querySelector('li');
lis.addEventListener('click',() => {
  console.log(this)
})
// this -> global
```
- 内部有大量的读写操作，不单纯是为了计算值，这时也不应该使用箭头函数


####  **双冒号运算符 `::`**

> 目前只是一个提案，用来绑定函数的 this 类似于 (bind,call,apply)
> 将做边的对象作为参数，绑定到右边函数上。

```javascript
bar:: fn
//等同于
fn.bind(bar);

bar::fn(...arguments);
//等同于
fn.apply(bar, arguments);

```
####  **函数调用**
```javascript
function f(){
 foo()
}
function foo(){
fn()
}
function fn(){
}
f()
```

> 函数调用会在内存中形成一个 调用记录（`调用帧`），保存着调用位置和内部变量等信息。
> 函数 `f`内部调用 `foo`函数，`f`调用帧的上方会形成 `foo`的调用帧， `foo`运行接受并且将结果返回给 `f`，`foo`的调用帧才会消失，同理，`foo`函数 内部调用`fn`函数，还会有 `fn` 的调用帧，以此类推，形成一个`调用栈`。

####  **尾调用**
> 指某个函数的最后一步是调用另一个函数。
> 不一定出现在函数尾部，只要是最后一步操作即可。
```javascript
//尾调用
function f(x){
  return fn(x);
}


//不属于尾调用
// 情况一
function f(x){
  let y = g(x);
  return y;
}

// 情况二
function f(x){
  return g(x) + 1;
}

// 情况三
function f(x){
  g(x);
}
  //等同于
function f(x){
  g(x);
  return undefined;
}
```
---

### Array Extend

#### **扩展运算 (...)**

>主要用于函数调用， 将一个数组，变为参数序列。
```javascript
function add(x, y) {
  return x + y;
}

add(... [1,2]) // 3
```
 **clone数组**
 ```javascript 
 //es5 
let arr = [1,2];

let arr1 = arr.concat();

 //es6
let [...arr2] = arr;

let arr3 = [...arr];
```
**合并数组**
```javascript
let arr = [1,2];
let arr1 = [3,4];
let arr2 = [5,6];
//es5
let arr3 = arr.concat(arr1,arr2);
//es6
let arr4 = [...arr,...arr1,...arr2];
```
合并和clone 都是浅拷贝；

**配合解构赋值**
```javascript

const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]

const [first, ...rest] = [];
first // undefined
rest  // []

const [first, ...rest] = ["foo"];
first  // "foo"
rest   // []

//扩展运算符，只能放在参数的最后一位，否则会报错。

const [first, ...middle, last] = [1, 2, 3, 4, 5];
//error
```
**将伪数组(内部实现了Iterator)转化伪数组**
```javascript

//内部实现Iterator
//字符串， Set, Map, Generator, NodeList, htmlcollection, classList ,argument内部都实现了迭代接口
let nodes = document.querySelectorAll('div');

let divs = [...nodes];

//内部未实现Iterator
let objs = {
  '0': 'a',
  '1': 'b',
  '2': 'c',
  length: 3
};
let arr =[...objs];//error
//可通过 Array.from方法转化
let arr1 = Array.from(objs)

```
#### Array.from
> 将伪数组转化为数组

```javascript
let arrLike = {
  '0': 'a',
  '1': 'b',
  '2': 'c',
  length: 3
};

let arr = Array.from(arrLike)

/*第二个参数
 遍历每个元素并返回运算结果
*/
Array.from(arrLike, x => x * x);
// 等同于
Array.from(arrLike).map(x => x * x);

Array.from([1, 2, 3], (x) => x * x)
// [1, 4, 9]

//将布尔值为false的成员转为0
Array.from([1, , 2, , 3], (n) => n || 0)
// [1, 0, 2, 0, 3]

//获取DOM 节点的文本内容。
Array.from
(document.querySelectorAll('span.name');, s => s.textContent)
//第三个参数 绑定第二个参数中回调函数的this 
```
**find and findIndex**
> 回调函数遍历所有成员，返回符合条件的值， 没有则返回 undefined
> find 返回值为，第一个符合条件的成员
>findIndex ，没有则返回 -1
> 回调函数接受三个参数（成员，位置，原数组）
> 第二个参数 绑定回调函数 this 
> 可以识别NaN
```javascript
[1, 4, -5, 10].find(function(v,i,arr){
  return v<0;
})
// -5

[1, 4, -5, 10].findIndex((n) => n < 0)
// 2
```
**fill**
> 填充数组,修改原数组
> 第一个参数为填充值，第二个参数为开始填充的位置，第三个参数为结束位置
```javascript
let arr =[1,2,3];
arr.fill('Owen');
//["Owen","Owen","Owen"]
arr.fill(1,0,2);
//[1,1,3]

```