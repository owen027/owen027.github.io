---
title: array method
date: 2019-05-20 18:25:19
tags: es5
---
js 内置 7种类型

>基本类型: string number boolean null undefined symbol 

> 引用类型: object


---

### Array
> es3/es5:
```javascript
//改变原数组
//push :向数组尾部添加数据 可添加多位
//自定义push
Array.prototype.push =function(){ }

//pop :剪切尾部一项数据
//unshift : 向数组前方添加数据
//revesre : 翻转数组

//splice : 第一个参数从第几位开始，第二个参数，截取几位，第三个参数覆盖截取的数据，返回截取的数据
/* 


sort :排序
一个函数为参数 ，同时 函数必须有两参数(a,b)
1. 函数返回值为正时 b在a前面
2. 为负数时 a在b前面
3. 0 时 不动
原理冒泡排序 */
 var obj = [{age:18,name:'1'},{age:38,name:'1'},{age:14,name:'1'},{age:28,name:'1'}], 
arr.sort(function(a,b){
    return a.age - b.age;//升序
})
//打乱有序的数组

arr.sort(function(a,b){
    return Math.random() - 0.6;
})



//不改变原数组需要变量接收
concat ： 拼接数组并返回 

join : 将数组每一位以传进的参数链接

slice : 第一个参数 开始截取为，第二个参数 截取到的位置

//将类数组转化为数组 
var arr=[].slice.call(arguments)



```

---

#### string methods

```javascript
var str ='123'
str.split('');

//camelize: 连字符转驼峰
const camelizeRE = /-(\w)/g
export const camelize = cached((str: string): string => {
  return str.replace(camelizeRE, ( _, c) => c ? c.toUpperCase() : '')
})

//变量的值转换为 string 类型并返回
export function toString (val: any): string {
  return val == null
    ? ''
    : typeof val === 'object'
      ? JSON.stringify(val, null, 2)
      : String(val)
}



```
---
###  Label 
```javascript
// 举一个比较典型的例子，看完后即明白 Label 的应用：（未添加 Label）
        var num = 0;
        for (var i = 0 ; i < 10 ; i++){
             for (var j = 0 ; j < 10 ; j++){
                  if( i == 5 && j == 5 ){
                        break;
                  }
             num++;
             }
        }
        alert(num); // 循环在 i 为5，j 为5的时候跳出 j循环，但会继续执行 i 循环，输出 95

// 对比使用了 Label 之后的程序：（添加 Label 后）
    var num = 0;
    outPoint:
    for (var i = 0 ; i < 10 ; i++){
         for (var j = 0 ; j < 10 ; j++){
              if( i == 5 && j == 5 ){
                    break outPoint;
              }
         num++;
         }
    }
    alert(num); // 循环在 i 为5，j 为5的时候跳出双循环，返回到outPoint层继续执行，输出 55
```


