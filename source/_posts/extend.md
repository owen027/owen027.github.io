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

##### **转义符 `\`**
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
------------

### Function Extend

#### **形参指定默认值**
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
function fn(){}
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
------

### Array Extend

#### **扩展运算 (...)**

>主要用于函数调用， 将一个数组，变为参数序列。
```javascript
function add(x, y) {
  return x + y;
}

add(... [1,2]) // 3
```
##### **clone数组**
 ```javascript 
 //es5 
let arr = [1,2];

let arr1 = arr.concat();

 //es6
let [...arr2] = arr;

let arr3 = [...arr];
```
##### **合并数组**
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

##### **配合解构赋值**
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
##### **将伪数组(内部实现了Iterator)转化伪数组**
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
#### **find and findIndex**
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
#### **fill**
> 填充数组,修改原数组
> 第一个参数为填充值，第二个参数为开始填充的位置，第三个参数为结束位置
```javascript
let arr =[1,2,3];
arr.fill('Owen');
//["Owen","Owen","Owen"]
arr.fill(1,0,2);
//[1,1,3]

```
#### **Interator**
> keys,values,entries 对应属性遍历
```javascript
// keys
let arr = ['o','w','e','n'];
for (let key of arr.keys()){
   console.log(key);
   //0 1 2 3
}

for (let item of arr.values()){
   console.log(item);
   //o w e n
}

for (let [i,item] of arr.entries()){
   console.log(i,item)
//1 "w"
//2 "e"
//3 "n"
}

```
#### **includes**
> 返回一个Boolean ,数组是否包含给定的值
```javascript
[1,2,3,4].includes(3)//true
//第二个参数为搜索的起始位置
[1,2,3,4].includes(3,4)//false

/* 是否支持 */
const contains = (() =>
  Array.prototype.includes
    ? (arr, value) => arr.includes(value)
    : (arr, value) => arr.some(el => el === value)
)();
contains(['foo', 'bar'], 'baz'); // => false
```

#### **flat ,flatMap**
> 将二维数组变成一位数组，并返回新数组
```javascript
[1,2,[3,4]].flat()
//[1,2,3,4]
//默认拉平1层
[1,2,[3,[4]]].flat(2)
//[1,2,3,4]
//将所有维度变成一维
array.flat(Infinty)
//会忽略空位
[1,,[3,4]].flat()
//[1,3,4]
```
---
#### ES5 methods
##### every
-  访问每一个成员,给定某个条件，如果<label>所有成员满足条件</label>就返回true
```javascript
var num  = [0,1,2,2,3,4,5,3,5];
 
var n = num.every(function(item,key,array){
  return ( item>2 )
})
 n //false

```
#### some
- 访问每一个成员,给定某个条件，<label>只要有一个成员满足条件就返回true</label>
- 有一个满足条件的成员之后的不再执行
```javascript
var num  = [0,1,2,2,3,4,5,3,5];
 
var n = num.some(function(item,key,array){
  return ( item > 2 )
})
 n //true
```
##### filter 
- 访问每一个成员,给定某个条件，<label>将满足条件的成员组成数组返回</label>， 否则返回 `[]`
- <label>只会在已经赋值的索引上被调用，对于那些已经被删除或者从未被赋值的索引不会被调用</label>

```javascript
var num  = [0,1,2,2,3,4,5,3,5];
 
var n = num.filter(function(item,key,array){
  return ( item > 2 )
})
 n //[3, 4, 5, 4, 3]

```
##### map 
- 访问每一个成员,将每次访问执行的结果组成数组返回，也就是说<label>每个成员都 必须要有return返回结果，否则都是`unndefined`</label>
- map不支持continue跳出循环

```javascript
var num  = [0,1,2,2,3,4,5,3,5];
 
var n = num.map(function(item,key,array){
  return item * 2
})
 n // [0, 2, 4, 4, 6, 8, 10, 6, 10]
```
##### forEach
- 访问每一个成员,没有返回值
- 中途不能用常规操作跳出循环
- 不支持链式操作
```javascript
var num  = [0,1,2,2,3,4,5,3,5];
 
var n = num.map(function(item,key,array){
  return item * 2
})
 n // undefined
```


##### reduce and reduceRight
- reduce 从数组的第一项开始，逐个遍历到最后。
- reduceRight  从数组的最后一项开始，向前遍历到第一项。
- 接收两个参数 回调函数和 回调函数第一个参数的值，默认数组第一个元素。
- 返回 累计处理的结果

```javascript
 /**
  * @param
  *  prev 上次调用回调时的累积值
  *  cur  当前数组成员
  *  i  数组索引
  *  arrary 原数组
  * 
   */
var num  = [0,1,2,2,3,4,5,3,5];
var n = num.reduce(function(prev,cur,i,array){
  return prev*2 + cur
})
 n // 431

 var num  = [0,1,2,2,3,4,5,3,5];
var n = num.reduce(function(prev,cur,i,array) {
  return prev*2 + cur
})
 n // 2186
 ```
 以上都不会修改原数组，除非使用第三个参数做些操作，注意<label>数组成员是值类型，还是引用类型</label>

##### sort 
- 对数组排序 默认排序按字母升序（根据字符串Unicode ）
- 参数为回调函数`callback(a,b)`  
- 返回值为负数 那么 a 会被排列到 b 之前
- 返回值为 0 位置不变
- 返回值为正数 b 会被排列到 a 之前
```javascript
var num = [1,3,2,7,44,2,3,4,9];
num.sort(function(a,b){
    return a-b
})
num // [1, 2, 2, 3, 3, 4, 7, 9, 44]
```
#### reverse
- 翻转数组
```javascript
var num = [1,3,2,7,44,2,3,4,9];
num //[9, 4, 3, 2, 44, 7, 2, 3, 1]
num.reverse()
```
#### 数组去重 

 - has方法 会发生隐式转化 1 =='1' 
 ```javascript
 var arr = [2,3,4,2,3,5,6,4,3,2];
var unique = function(arr) {
      var obj = {};
      var res = [];     
      arr.forEach(function(item,i){
          if(!obj[item]){
              obj[item] = true;
              res.push(item) 
          }
       })
    return  res
}
unique(arr)
```
- filter 
```javascript
//会忽略 undefined
var arr = [null,null,null,undefined,undefined,'','',1,1,1];
var unique = function (arr) {
   return  arr.sort().filter(function(item,i,array) { 
     return item !== array[i+1]; 
     })
}
 unique(arr) //  ["", 1, null]


var unique = function(arr) {
  return  arr.filter( function(item, idx )  {
        return arr.indexOf(item) === idx;
  })
}

unique(arr) //[null, undefined, "", 1]

```
### Object extend

#### 对象中的简写

```javascript
//函数
const obj ={
  methode(){
    return 'Owen'
  },
  * m() {
    yield 'hello world';
  }
}
//等同于
const obj ={
  methode:function(){
    return 'Owen'
  }
  m:function* (){
    yield 'hello world';
  }
}

//使用变量定义对象
  //默认情况下会自动将对象转为字符串[object Object]，这一点要特别小心。
let lastWord = 'last word';

const obj = {
  'first word': 'hello',
  [lastWord]: 'world',
  ['h' + 'ello']() {
    return 'hi';
  }
};

obj['first word'] // "hello"
obj[lastWord] // "world"
obj['last word'] // "world"
obj.hello() // hi


//对象中 函数 name属性
//对象的方法是一个 Symbol 值，那么name属性返回的是这个 Symbol 值的描述。
const key1 = Symbol('description');
const key2 = Symbol();
let obj = {
  [key1]() {},
  [key2]() {},
  sayName() {
    console.log('Owen');
  },
  get foo() {},
  set foo(x) {}
};
obj[key1].name // "[description]"
obj[key2].name // ""
obj.sayName.name   // "sayName"
const descriptor = Object.getOwnPropertyDescriptor(obj, 'foo');

descriptor.get.name // "get foo"
descriptor.set.name // "set foo"

//通过构造函数创立的函数
(new Function()).name // "anonymous"

//通过 bind 绑定的函数
var doSomething = function() {
  // ...
};
doSomething.bind().name // "bound doSomething"

```

#### 枚举和遍历

> Object.getOwnPropertyDescriptor方法可以获取该属性的描述对象

```javascript 
let obj = {
  name:'Owen'
}
Objct.getOwnPropertyDescriptor(obj,'name');
//  {
//    value: Owen,
//    writable: true,
//    enumerable: true, //可枚举
//    configurable: true
//  }
```
##### **如果 enumerable 为 false**
有些操作会忽略，当前属性
- for...in循环：只遍历对象自身的和继承的可枚举的属性。
- Object.keys()：返回对象自身的所有可枚举的属性的键名。
- JSON.stringify()：只串行化对象自身的可枚举的属性。
- (ES6) Object.assign()： 忽略enumerable为false的属性，只拷贝对象自身的可枚举的属性。


> 共有 5 种方法可以遍历对象的属性。
- for...in

for...in循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。

- Object.keys(obj)

Object.keys返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。

- Object.getOwnPropertyNames(obj)

Object.getOwnPropertyNames返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。

- Object.getOwnPropertySymbols(obj)

Object.getOwnPropertySymbols返回一个数组，包含对象自身的所有 Symbol 属性的键名。

- Reflect.ownKeys(obj)
Reflect.ownKeys返回一个数组，包含对象自身的所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。

##### **都遵守同样的属性遍历的次序规则。**

- 首先遍历所有数值键，按照数值升序排列。
- 其次遍历所有字符串键，按照加入时间升序排列。
- 最后遍历所有 Symbol 键，按照加入时间升序排列。
```javascript
Reflect.ownKeys({ [Symbol()]:0, b:0, 10:0, 2:0, a:0 })
// ['2', '10', 'b', 'a', Symbol()]
```
上面代码中，Reflect.ownKeys方法返回一个数组，包含了参数对象的所有属性。这个数组的属性次序是这样的，首先是数值属性2和10，其次是字符串属性b和a，最后是 Symbol 属性。

**super**
> `this` 总是指向函数所在的当前对象
> `super` 指向当前对象的原型对象。
> super关键字表示原型对象时，`只能用在对象的方法之中`，用在其他地方都会报错。
目前，只有对象<label>方法的简写法可以让 JavaScript 引擎确认，定义的是对象的方法。</label>
```javascript
const proto = {
  foo: 'hello'
};

const obj = {
  foo: 'world',
  find() {
    return super.foo;
  }
};

Object.setPrototypeOf(obj, proto);
obj.find() // "hello"
```
#### 对象扩展运算符
> ES2018 将这个运算符引入了对象。
> 解构赋值的拷贝是浅拷贝
> 不能复制继承自原型对象的属性。
```javascript

// 解构

//必须保证右方为对象，否则报错
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
x // 1
y // 2
z // { a: 3, b: 4 }

//与函数参数扩展运算类似，解构赋值须最后一个参数
let { ...x, y, z } = someObject; // 句法错误
let { x, ...y, ...z } = someObject; // 句法错误

//无法继承原型
let o1 = { a: 1 };
let o2 = { b: 2 };
o2.__proto__ = o1;
let { ...o3 } = o2;
o3 // { b: 2 }
o3.a // undefined


//扩展运算

//数组是特殊的对象，所以对象的扩展运算符也可以用于数组
let foo = { ...['a', 'b', 'c'] };
foo
// {0: "a", 1: "b", 2: "c"}

//扩展运算符后面是一个空对象，无效果
{...{}, a: 1}
// { a: 1 }

//扩展运算符后面不是对象，则会自动将其转为对象 
{...1} // {}

//扩展运算符后面是字符串，它会自动转成一个类似数组的对象
{...'Owen'}
//{0: "O", 1: "w", 2: "e", 3: "n"}

let ab = { ...a, ...b };
// 等同于
let ab = Object.assign({}, a, b);

//完整克隆一个对象，还拷贝对象原型的属性，可以采用下面的写法。

// 写法一 非浏览器环境不一定部署 __proto__
const clone1 = {
  __proto__: Object.getPrototypeOf(obj),
  ...obj
};

// 写法二
const clone2 = Object.assign(
  Object.create(Object.getPrototypeOf(obj)),
  obj
);

// 写法三
const clone3 = Object.create(
  Object.getPrototypeOf(obj),
  Object.getOwnPropertyDescriptors(obj)
)

//对象的扩展运算符后面可以跟表达式
const obj = {
  ...(x > 1 ? {a: 1} : {}),
  b: 2,
};

//扩展运算符的参数对象之中，如果有取值函数get，这个函数是会执行let aWithXGetter = {
  ...a,
  get x() {
    throw new Error('not throw yet');
  }
};

// 会抛出错误，因为 x 属性被执行了
let runtimeError = {
  ...a,
  ...{
    get x() {
      throw new Error('throw now');
    }
  }
};

```
---
###  New method for objects

#### Object.is() 比较两个值是否严格相等
>和 `===` 的区别
```javascript
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
es5 实现
Object.defineProperty(Object,'is',{
  value: function(x, y) {
    if (x === y) {
      // 针对+0 不等于 -0的情况
      return x !== 0 || 1 / x === 1 / y;
    }
    // 针对NaN的情况
    return x !== x && y !== y;
  },
  configurable: true,
  enumerable: false,
  writable: true
})
```
#### Object.assign()
> 浅拷贝对象，无法拷贝原型,也不拷贝不可枚举的属性。
> 总是拷贝一个属性的值，而不会拷贝它背后的赋值方法或取值方法。
> 同属性后面的覆盖前面的值

```javascript
Object.assign({b: 'c'},
  Object.defineProperty({}, 'invisible', {
    enumerable: false,
    value: 'hello'
  }) )
// { b: 'c' }
Object.assign([1, 2, 3], [4, 5])
//[4,5,3]
```

#### Object.getOwnPropertyDescriptors()
> 返回目标对象所有自身属性（非继承） 的描述对象
```javascript
const obj = {
  foo: 123,
  get bar() { return 'Owen' }
};

Object.getOwnPropertyDescriptors(obj)
// { foo:
//    { value: 123,
//      writable: true,
//      enumerable: true,
//      configurable: true },
//   bar:
//    { get: [Function: get bar],
//      set: undefined,
//      enumerable: true,
//      configurable: true } }
```
> 主要是为了解决Object.assign()无法正确拷贝get属性和set属性的问题。
```javascript
const source = {
  set foo(value) {
    console.log(value);
  }
};

const target = {};
const shallowMerge = (target, source) => Object.defineProperties(
  target,
  Object.getOwnPropertyDescriptors(source)
);
shallowMerge(target,source)
Object.getOwnPropertyDescriptor(target, 'foo')
// { get: undefined,
//   set: [Function: set foo],
//   enumerable: true,
//   configurable: true }
```
> 配合Object.create()方法，将对象属性克隆到一个新对象
```javascript
const shallowClone = (obj) => Object.create(
  Object.getPrototypeOf(obj),
  Object.getOwnPropertyDescriptors(obj)
);

```
> 实现一个对象继承另一个对象。
```javascript
//一
const obj1 = Object.create(prot);
obj.foo = 123;

//二
const obj2 = Object.assign(
  Object.create(prot),
  {
    foo: 123,
  }
//三
const  obj3 = Object.create(prot,Object.getOwnPropertyDescriptors({
  name:'Owen'
}))
);
```
> 实现`Mixin` 
```javascript
let mix = (obj) => (
  {
    with:(...mixins) => mixins.reduce(
      (c,mixin) => Object.create(  c, Object.getOwePropertyDescriptors( minxin )), obj
    )
  })
let a = {a: 'a'};
let b = {b: 'b'};
let c = {c: 'c'};
let d = mix(c).with(a, b);

d.c // "c"
d.b // "b"
d.a // "a"
```
#### __proto__属性，Object.setPrototypeOf()，Object.getPrototypeOf()
 > 设置，和 获取原型
```javascript
//set
let proto = {};
let obj = { x: 10 };
Object.setPrototypeOf(obj, proto);

proto.y = 20;
proto.z = 40;

obj.x // 10
obj.y // 20
obj.z // 40

//get
function Rectangle() {
  // ...
}

const rec = new Rectangle();

Object.getPrototypeOf(rec) === Rectangle.prototype
// true

Object.setPrototypeOf(rec, Object.prototype);
Object.getPrototypeOf(rec) === Rectangle.prototype
// false
```
#### Object.keys()，Object.values()，Object.entries()
```javascript
//keys
let obj = { foo: 'bar', baz: 42 }; //es5
Object.keys(obj)
// ["foo", "baz"]

//values
Object.values(obj)
//['bar,42]

Object.entries(obj)
// [['foo', 'bar'],  ['baz', 42]]
```
#### Object.fromEntries()
>  Object.entries 方法的逆操作
```javascript
Object.fromEntries([
  ['foo', 'bar'],
  ['baz', 42]
])
// { foo: "bar", baz: 42 }
```
目前谷歌版本 Chrome/72.0.3626.121 Safari/537.36  及以下不支持