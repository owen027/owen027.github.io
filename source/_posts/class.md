---
title: class
date: 2019-04-17 18:32:50
tags:
---
## class
- 生成类的实例对象的写法，与 ES5 完全一样，也是使用new命令

```javascript
class Point {
  // ...
}

// 报错
var point = Point(2, 3);

// 正确
var point = new Point(2, 3);
```
- 实例的属性除非显式定义在其本身（即定义在this对象上）， 否则都是定义在原型上（即定义在class上）。 

```javascript
//定义类
class Point {

  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }

}

var point = new Point(2, 3);

point.toString() // (2, 3)

point.hasOwnProperty('x') // true
point.hasOwnProperty('y') // true
point.hasOwnProperty('toString') // false
point.__proto__.hasOwnProperty('toString') // true
```
- <label>与 ES5 一样，类的所有实例共享一个原型对象。</label>

- 避免对环境产生依赖，生产环境中，我们可以使用 `Object.getPrototypeOf`
方法来获取实例对象的原型，然后再来为原型添加方法/属性。


```javascript
var p1 = new Point(2,3);
var p2 = new Point(3,2);

p1.__proto__.printName = function () { return 'Oops' };

p1.printName() // "Oops"
p2.printName() // "Oops"

var p3 = new Point(4,2);
p3.printName() // "Oops"
```
由于p1的原型就是p2的原型，因此p2也可以调用这个方法。 而且，此后新建的实例p3也可以调用这个方法。这意味着，使用实例的` __proto__ `属性改写原型，必须相当谨慎，不推荐使用，因为这会改变“类”的原始定义，影响到所有实例。

###  constructor

- constructor方法是类的默认方法, <label>通过new命令生成对象实例时</label>，自动调用该方法。

一个类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。
相当于es5 new Vue 

```javascript
class Point {
}

// 等同于 
class Point {
  constructor() {} //JavaScript 引擎会自动为它添加一个空的constructor方法。
}
// 等同于
new Point (){
    
}
```
-  constructor方法默认返回实例对象（即this），完全可以指定返回另外一个对象。

```javascript
class Foo {
  constructor() {
    return Object.create(null);
  }
}

new Foo() instanceof Foo
// false
```
<label>类必须使用new调用</label>，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用new也可以执行。


### 表达式定义 

```javascript
const MyClass = class Me {
  getClassName() {
    return Me.name;
    //这个类的名字是MyClass而不是Me，Me只在 Class 的内部代码可用，指代当前类。
  }
};
//内部没用到Me的话可以省略Me
const YouClass = class {
//...
};
```
- 采用 Class 表达式，可以写出立即执行的 Class。

```javascript
let person = new class {
  constructor(name) {
    this.name = name;
  }

  sayName() {
    console.log(this.name);
  }
}('张三');

person.sayName(); // "张三"
```
- 不存在变量提升 （hoist），必须保证子类在父类之后定义 ，这一点与 ES5 完全不同。
```javascript
new Foo(); // ReferenceError
class Foo {}
```
继承类 
```
{
  let Foo = class {};
  class Bar extends Foo {
  //Bar继承Foo
  }
}
```
### 私有方法

私有方法是常见需求，但 ES6 不提供，只能通过变通方法模拟实现。
有三种方法可模拟

```javascript
//第一种
class Widget {

  // 公有方法
  foo (baz) {
    this._bar(baz);
  }

  // 私有方法
  _bar(baz) {
    return this.snaf = baz;
  }

  // _bar方法前面的下划线，表示这是一个只限于内部使用的私有方法 但是，在类的外部，还是可以调用到这个方法。
}



//第二种
class Widget {
  foo (baz) {
    bar.call(this, baz);
  }

  // 内部调用了bar.call(this, baz)，成为了当前模块的私有方法
}
//私有方法移出模块
function bar(baz) {
  return this.snaf = baz;
}


//第三种
const bar = Symbol('bar');
const snaf = Symbol('snaf');

export default class myClass{

  // 公有方法
  foo(baz) {
    this[bar](baz);
  }

  // 私有方法 
  [bar](baz) {
    return this[snaf] = baz;
  }

  // 利用Symbol值的唯一性，将私有方法的名字命名为一个Symbol值。
};

```
### **私有属性的提案**

- 方法是在属性名之前，使用#表示。
```javascript
class Point {
  #x=0;// 私有属性可以指定初始值，在构造函数执行时进行初始化。

  constructor(x = 0) {
    #x = +x; // 写成 this.#x 亦可
  }

  get #x() { return #x }
  set #x(value) { #x = +value }
  #sum() { return #a + #b; }  //私有方法
  
  //  #x是一个私有属性，它的读写都通过get #x()和set #x()来完成。 #x和x是两个不同的属性
}
//JavaScript 是一门动态语言，没有类型声明，使用独立的符号似乎是唯一的比较方便可靠的方法，能够准确地区分一种属性是否为私有属性。@已经留给了 Decorator。
```

- 私有属性不限于从this引用，类的实例也可以引用私有属性  
```javascript
class Foo {
  #privateValue = 42;
  static getPrivateValue(foo) {
    return foo.#privateValue;
  }
}

Foo.getPrivateValue(new Foo()); // 42
console.log(Foo.#privateValue) // 报错
```
#### class 的取值函数（getter）和存值函数（setter）

```javascript
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}

let inst = new MyClass();

inst.prop = 123;
// setter: 123

inst.prop
// 'getter'


```
