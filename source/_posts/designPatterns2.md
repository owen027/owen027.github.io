---
title: 设计模式基础（二）
date: 2019-07-08 13:13:34
categories:
- design patterns
tags:
- Pattern intro
---

# 原型模式
在以类为中心的面向对象编程语言中，类和对象的关系就像铸模和铸件的关系，对象总是从类中创建。而原型编程中，类不是必须的，对象未必从类中创建而来，可以拷贝另一个对象而变成新对象

> 从设计模式角度讲，原型模式是用于创建对象的一种模式，找到一个对象，不需要关心对象的具体类型，通过克隆来创建一个一模一样的对象。 原型不单是一种设计模式，也被称为编程泛型

原型模式实现的关键是语言本身是否提供了 clone 方法， ECMAScript 5 提供了 `Object.create` 方法
```javascript
function Plane (propertys){ //工厂方法
    this.blood = propertys.blood;
    this.attackLevel = propertys.attackLevel;
    this.defenseLevel = propertys.defenseLevel;
}
var plane = new Plane({
    blood :100,
    attackLevel :10,
    defenseLevel :10
});
var clonePlane = Object.create(plane);
console.log(clonePlane.__proto__) // Plane {blood: 100, attackLevel: 10, defenseLevel: 10}

```
通过克隆 `plane 对象`得到 `clonePlane 对象`,那么 plane 就被称为 clonePlane 的原型；而 plane 中所有的属性和方法都将挂载到`__proto__` 原型属性上

**由于 JavaScript 作为一门基于原型的语言，没有类的概念，每一个对象都是基于另一个对象的克隆，因此语言最初只有一个根对象 `Object.prototype`,如果A对象是从B对象克隆而来，那么B就是A的原型，如果C又是B的原型。它们之间就形成了一条原型链**
 
<label> **原型编程中有一个重要特性：访问对象中某个属性或方法，而对象本身没有对应属性和方法，那么它会从自己的原型链上查找，一直查到根对象为止**
</label> 
```javascript
console.log(clonePlane) // Plane {}

console.log(clonePlane.blood) // 100
console.log(clonePlane.color) // undefined

console.log(clonePlane.__proto__) //原型 Plane {blood: 100, attackLevel: 10, defenseLevel: 10}
```
上述代码中的`clonePlane 对象` 本身是没有 blood 和 color 属性，却能访问到blood的值而访问不到color的值，是因为 `clonePlane 对象` 原型链中有 blood 属性而没有color属性。

**原型编程基本规则：**
1. 所有数据都是对象
2. 对象会记住它的原型（类似于继承）
3. 要得到一个对象，需要将另一个对象作为原型并克隆它
4. 访问不到对象的某个属性或方法，那么它会从自己的原型链上查找，一直查到根对象为止

## 原型继承

在JavaScript中我们遇到的每一个对象都是从 `Object.prototype`克隆而来，通过`Object.getPrototypeOf`来判断对象的原型从属关系。
```javascript
let obj = new Object();
let obj1 = {};

console.log(Object.getPrototypeOf(obj) === Object.prototype) // true
console.log(Object.getPrototypeOf(obj1) === Object.prototype) // true

```
由于引擎内部会自动实现对 `Object.prototype` 的克隆，所有我们执行显示的使用`let obj = new Object(); let obj1 = {};`

下面我们来看`new` 运算符

 <label> **JavaScript中没有类的概念，所以 `Object` 是一个构造函数，JavaScript 中的函数即可作为普通函数调用，又可作为构造器调用,当使用 new 运算调用函数时，函数就是构造器。**</label>

 ### `new`命令的原理