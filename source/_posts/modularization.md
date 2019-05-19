---
title: 模块化开发(二)
date: 2019-03-07 22:07:50
categories:
- JavaScript
tags:
- moduleDevelop
---

### ES6 详解
 > ES6 的模块自动采用严格模式，不管你有没有在模块头部加上"use strict"。
 顶层的this指向undefined，即不应该在顶层代码使用this。

严格模式主要有以下限制：

1. 变量必须声明后再使用
2. 函数的参数不能有同名属性，否则报错
3. 不能使用`with`语句
4. 不能对只读属性赋值，否则报错
5. 不能使用前缀 0 表示八进制数，否则报错
6. 不能删除不可删除的属性，否则报错
7. 不能删除变量`delete prop`，会报错，只能删除属性`delete global[prop]`
8. `eval`不会在它的外层作用域引入变量
9. ` eval`和`arguments`不能被重新赋值
10. arguments不会自动反映函数参数的变化
11. 不能使用`arguments.callee`
12. 不能使用`arguments.caller`
13. 禁止this指向全局对象
14. 不能使用`fn.caller`和`fn.arguments`获取函数调用的堆栈
15. 增加了保留字（比如`protected`、`static`和`interface`）
上面这些限制，模块都必须遵守。

 #### export命令
- `export`除了输出变量，还可以输出函数或类（class）,还可以通过 `as` 更改输出名。 
```javascript
let age = 18;
let obj ={name:  "Owen"};
let multiply = (x, y) => x * y;
export {
    age,
    obj as monicker, //改变量名
    multiply
}
```
- 导出的对象必须是对外的接口

```javascript 
let num =2;
export num ; //error 因为实际输出的是一个值，须放在对象中 {num}

exprot 2  //error  输出的是值 而不是一个对外接口 => 须改成 export let num =2；

```
- 不能放到局部作用域中导出， 因为导出的对象是动态绑定的
```javascript
 let foo = (r) => r++;
 let fn = () => export default foo; //error
```
### export default 
- 全局只能有一个 `export default`
- `export default`  后面不能有 变量声明的关键字
- 使用 `export default` ，import 就不需要使用 `{}`,只需要自定义一个变量即可

```javascript
export default var a=1; //error
export default obj ={name:'Owen'};
//or
let num = 1;
let obj1 = {name:"Owen"};
export default {num, obj1}

```



### `import`
- 配合 `from` 导入模块,  通过 `as` 修改导入接口, 接口不能重新赋值，但对象可以修改内部属性或方法。
```javascript
import {age, monicker as obj, multiply} from './preson.js';
  age = 0 ; //error
  obj.feature = "handsome Owen"; 
  console.log(age, obj。name, multiply(1,2))
  import * as preson from "./preson.js" //引入所有接口
```
- `import` 具有提升的效果，会提升到作用域顶部执行，同一个模块多次引入只执行一次，并且不能再局部作用域中引入。
```javascript

multiply(2,2); //4
import {age, monicker as obj, multiply} from './preson.js';

if (true){
import {age, monicker as obj, multiply} from './preson.js'; //error

}

```

### `import()`

- 因为require是运行时加载模块，import命令无法取代require的动态加载功能。因此，有一个提案，引入`import()`函数，完成动态加载。
- import()函数可以用在任何地方，不仅仅是模块，非模块的脚本也可以使用。
- 和 `require`类似，不同之处在于 `import()`是同步加载 `require()`是异步加载
```javascript
const path =import('path');
```
- 我们可以利用 `import()` 进行按需加载,动态加载，按条件加载:

        ```javascript
        import('./a.js')
            .then(r => {
                r.fn();
            })
            .catch(error => {
                /* Error handling */
            })
        if( x == 1){
            const path =import('path');
        }
        ```
- 如果想同时加载多个模块，可以采用下面的写法
```javascript
  Promise.all([
  import('./a.js'),
  import('./b.js'),
  import('./c.js'),
]) .then(([a,b,c]) => {
   // ...
 }).catch(error => {
                /* Error handling */
            })

```