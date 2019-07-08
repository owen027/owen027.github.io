---
title: designPatterns
date: 2019-07-08 14:13:34
categorise:
- design patterns
tags:
- Pattern intro
---

# 什么是设计模式？

> `模式` 起源于建筑学。20世纪70年代，哈佛大学建筑学博士`Christopher Alexander`和他的团队花大约20年，来研究为解决同一个问题而设计出的不同建筑结构，从中发现那些高质量设计中的相似性，并且用`模式`来指代这种相似性；

受`Christopher Alexander`观点的启发，`Erich Gamma, Richard Helm, Ralph Johnson和 John Vlissides `(人称Gang Of Four, GoF),将这种 `模式` 观点应用于面向对象的软件设计中，并总结了23种常见的软件开发设计模式， 于 1995 年发布一本名叫《Design Patterns: Elements Of Reusable Object-Oriented Software》的书

> 设计模式的定义：在面向对象软件设计过程种针对特定的问题的简洁而优雅的解决方案。就是给面向对象软件开发中一些 好的设计取个名。

## 为什么要给好设计取名？

在开发中，一些稍有经验的程序员也许在不知不觉中使用过设计模式，于是他向别人描述它时会遇到困难，沟通成本高、效率低。而 GoF 将这些好的设计从面向对象中挑选出来，这些都是久经考验的反应了开发者的经验和见解的使用模式来定义的技术，给它们一个好听又好记的名字，这样就方便我们更好的传播和学习，当遇到一个问题时，知道这是哪种设计模式，就能很快想出几种解决方案，提高工作效率。

**好处：**
1. 提供固定的解决方法来解决在软件开发中出现的问题。
2. 很容易地重用，防止在应用程序开发过程中出现的一些可能导致重大问题的小问题，提高工作效率。
3. 避免重复来减小我们代码的文件大小。
3. 模式善于表达，交流更快速，降低沟通成本。


 **所有设计模式的实现都遵循一条原则：找出程序中变化的地方，将变化封装起来**。一个程序的设计总是可以分为 可变部分和不变的部分；找出可变部分，将其封装，剩下的不变和稳定部分就非常容易复用。
**作用：** 让人写出可复用和可维护的程序。
JavaScript是一门`面向对象语言`[[1]](https://www.ibm.com/developerworks/cn/web/1304_zengyz_jsoo/)[[2]](https://www.infoq.cn/article/3*8POPcRSClQh1Cp9Sqg)[[3]](https://zhuanlan.zhihu.com/p/33658346)，设计模式通过对**封装、继承、多态、组合**等技术的反复使用，提炼出可复用的面向对象设计技巧。

---

# basic
> JavaScript 在语言层面没有抽象类和接口的支持，没有类式继承，而是通过原型委托的方式来实现对象与对象之间的继承。

## 编程语言类型

**编程语言按数据类型大体可分两大类：静态类型语言和动态类型语言** 。静态类型语言在编译时就已经确定变量的类型，动态类型语言只有在程序运行的时候才能确定变量的类型。而JavaScript就是动态类型语言。

**静态语言优点：**
1. 编译时就能发现类型不匹配的错误，可以避免程序运行时有可能发生的错误。
2. 编译器可针对对应变量的类型进行优化，提高程序运行速度。

**静态语言缺点：** 
1. 迫使程序员按照对应的规则来写程序，为每个变量规定数据类型分散程序员注意力，增加代码量。

**动态语言优点：** 
1. 代码简洁，程序员可以把更多精力放在业务逻辑处理上，更加专注
2. 无需类型检测，灵活性高，只关注须对象的行为，无需关注对象本身
3. 不必借助超类型来实现"面向接口编程"
**动态语言缺点：** 
1. 无法保证变量的类型，程序可能发生意想不到的bug.


## 多态

>定义：同一操作作用于不同对象，产生不同的解释和不同的执行结果

给不同对象发送同一个消息时，这些对象会根据这个消息分别给出不同的回应。

```javascript
var sendInfo = target => {
    if ( target instanceof ObjOne) {
        console.log(new ObjOne)
    }else if (target instanceof ObjTwo) {
        console.log(new ObjTwo)
    }
} 
 function ObjOne () {}
 function ObjTwo () {}

 sendInfo(new ObjOne())
 sendInfo(new ObjTwo())

```
上述代码段就体现了`多态性`,当发送 `sendInfo` 消息时每个对象做出不同的回应，但是这样的`多态性`很糟糕，如果再这个家一个对象 ObjThere 就得改动代码，对象越来越多时，**修改的代码越多，出错的可能性越大。**

多态背后的思想是将 `做什么` 和 `谁去做以及怎样做`分开，也就是将 `不变的事物和可能变化的事` 分开。上述代码段中，每个对象都会打印日志,这是不变的，而各个对象输出哪些信息是变化的，将不变的隔离出来，把变化的封装起来，这样程序就又了扩展能力，是可生长的，这样就符合 `开发-封闭` 原则。
**下面是修改后的代码：**

```javascript
let sendInfo = target => {
  target.log()
} 
 function ObjOne () {}
ObjOne.prototype.log = function(){console.log(this)};

function ObjTwo () {}
ObjTwo.prototype.log = function(){console.log(this)};

 sendInfo(new ObjOne())
 sendInfo(new ObjTwo())

```
现在我们增加一个对象就不需要修改 `sendInfo`里的代码了

```javascript
function ObjThere () {}
ObjThere.prototype.log = function(){console.log(this)};
sendInfo(new ObjThere())
```

### 类型检查和多态

类型检查是再表现出对象多态性之前一个绕不开的话题，`JavaScript` 是一门不必进行类型检查的动态类型语言，为了真正了解多态的目的，须从静态语言说起。

以JAVA为例，由于在编译时会进行类型检查，所有不能给变量赋予不同类型的值。
```Java
String str;

str = 'ab'
str =2 // error
```
将之前的例子换成 Java

```Java
public class ObjOne {
    public void log () {
        System.out.println('a')
    }
}
public class ObjTwo {
    public void log () {
        System.out.println('b')
    }
}
public class SendInfo {
    public void log (ObjOne objOne) {
      objOne.log()
    }
}
public class Test {
    public static void main (String args[] ) {
        SendInfo sendInfo = new SnedInfo();
        ObjOne objOne = new ObjOne()
        sendInfo(objOne) // 'a'
        ObjTwo objTwo = new objTwo()
        sendInfo(objTwo) // error
    }
}
```

上述代码段中 `a` 可以顺利输出，而`b`