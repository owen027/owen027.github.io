---
title: 笔记
date: 2019-06-13 18:17:27
categories:
- notes
tags:
-  question
---

##  vue 框架中遇到的问题

### props
如果 data中的属性取不到 props 中的数据，可以通过 computed 或者 watch 属性监听变化

### 如果想要组件或者原生HTML节点重新渲染可以使用 v-if

### 使用 prototype 挂载自定义方法或插件
```javascript
 Vue.prototype.$bus = Bus;
 // or
 Vue.use(bus);
```

### template 中的 全局变量白名单
```js
Infinity,
undefined,
NaN,
isNaN,
isFinite,
parseFloat,
parseInt,
decodeURI,
decodeURIComponent,
encodeURI,
encodeURIComponent,
Math,
Number,
Date,
Array,
Object,
Boolean,
String,
RegExp,
Map,
Set,
JSON,
Intl,
require
```

### $slots

只读属性，可访问插槽分发的内容，每个具名插槽都挂载到 `$slots` 中,默认插槽则为 `default`

使用渲染函数JSX时非常有用
```js
Vue.component('anchored-heading', {
  render: function (createElement) {
    return createElement(
      'h' + this.level,   // 标签名称
      this.$slots.default // 子节点数组
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

---


## axios 问题

### 接收二进制流文件乱码问题。
**1. 须将axios 配置中的`responseType`设置为'arraybuffer'，这样就不会让表格出现乱码现象；**
**2. 如果要动态设置文件名则需要让后台将名字设置到响应头中，否则将是一个乱码的文件名；**
**3. 然后通过`<a></a>` 标签的特性来，自动点击下载文件；**
**4. 如果要兼容IE则需要利用`navigator.msSaveOrOpenBlob`方法；**
**5. 兼容Firefox 须将`<a></a>` 标签添加到`body`中，最后再移除`<a></a>` 标签**

```javascript
// axios config
 config = {
     responseType: 'arraybuffer'
    }

// 返回数据处理
getUserInfoExport(data).then(({data,headers}) => {
        let blob = new Blob([data], { type: 'application/vnd.ms-excel' }) // 将服务端返回的文件流（二进制）excel文件转化为blob
        let fileName = headers.filename

        if (window.navigator && window.navigator.msSaveOrOpenBlob) { // IE10+
          window.navigator.msSaveOrOpenBlob(blob, fileName)
        } else {
          let objectUrl = (window.URL || window.webkitURL).createObjectURL(blob)
          let downFile = document.createElement('a')
          downFile.style.display = 'none'
          downFile.href = objectUrl
          downFile.download = fileName // 下载后文件名
          document.body.appendChild(downFile)
          downFile.click()
          document.body.removeChild(downFile) // 下载完成移除元素
          // window.location.href = objectUrl
          window.URL.revokeObjectURL(objectUrl) // 只要映射存在，Blob就不能进行垃圾回收，因此一旦不再需要引用，就必须小心撤销URL，释放掉blob对象。
        }
      })
```
[参考连接](https://www.w3.org/TR/FileAPI/#url)

## DOM 树
HTML 文档的骨干是标签。
根据文档对象模型(DOM)，每个HTML标签都是一个对象，同样标签内的文本也是一个对象。因此这些对象都可通过 JavaScript 操作
如果文档中有空格（就像任何字符一样），那么它们将成为 DOM 中的文本节点，如果我们删除它们，则不会有任何内容。
`<head>` 之前的空格和换行符被忽略
`</body>` 之后放置了一些东西，那么它会自动移动到 body 内部，因为 HTML 规范要求所有内容必须位于 <body> 内。所以 </body> 后面可能没有空格。

通常再浏览器中的文本不会显示开头/结尾的空文本节点，标签之间也不会显示空文本节点。

如果浏览器遇到格式不正确的HTML，在形成DOM是会自动修复它
如：
`<html>` 即使不在文档中，浏览器也会自动创建它

按DOM规范，table 必须具有 `<tbody>`,因此table中未使用`<tbody>` 形成DOM时会自动添加。

**其它 节点：**
注释不会以任何方式影响视觉表示，但是必须遵循一条规则 —— 如果HTML中有东西，那么它必须在DOM树中。
HTML中所有内容都是DOM的一部分，
注释是一个节点甚至`<!DOCTYPE...>`也是一个节
DOM总有[12种节点](https://dom.spec.whatwg.org/#node)

### 遍历DOM节点
所有对DOM的操作都是从`document`对象开始，将这个对象赋予一个变量，对其进行修改操作

#### 最顶端的节点

DOM节点树可以通过 `document`属性使用
顶端的节点对应`<html>` 并且 `<html> = document.documentElement`
而`<body> = document.body`,`<head> = document.head`
`docment.body`可能为null，如果将`script`脚本放入 `<head>`标签种，那么此脚本无法访问到`document.body`，即为null

#### childNodes
`childNodes` 集合提供对所有子节点包括文本节点的访问，它看起来是一个数字，实际上只是一个可迭代的类数组对象，因此没有数组的方法
**所有的Dom 集合节点都是只读的无法通过赋值来替换对应的节点**
**除小部分节点，几乎所有的DOM集合都是实时的，它们反应的是DOM的实时状态**
**不要是有 `for...in`来遍历DOM集合，此方法会列出其所有的属性。**
**注意此属性只能访问到当前`script`脚本之前对应的节点**
可以通过`elem.hasChildNodes()`来检测是否含有子节点

#### parentNOde / siblingNode
通过`elem.parentNode`可访问当前节点的父节点
通过`elem.previousSibling/elem.nextSibling`可访问对应节点的上/下兄弟节点

#### 只访问元素节点
- children： 只获取类型为元素节点的子节点
- firstElementChild,lastElementChild：只获取第一或最后一个子元素
- previousElementSibling, nextElementSibling：兄弟元素
- parentElement：父元素

**parentElement 可能为null,因为其方法返回的是父元素节点，而parentNode返回的是任何类型的父节点，因此,`document.documentElement.parentElement === null`**

#### HTMLCollection （动态）
通过元素查找子元素如果子元素是一个集合将返回 `HTMLCollection` 类数组
```js
let tb = documet.querySelector('table')
let tbs = tb.tBodies  // HTMLCollection [tbody]
let trs =tbs.rows // HTMLCollection [tr,tr,tr,...]
let tr1 = trs[0]
    tr1.sectionRowIndex //0 当前 tr 在集合中的位置
    tr1.rowIndex // 1 当前 tr 在整张表中的 位置
let tds = tr1.cells // HTMLCollection [td,td,td,...]
    td[0].cellIndex //0 当前 td 在父元素 tr节点 中的位置
```
#### NodeList （静态）
通过 `document` 中的方法 `document.querySelectorAll` 或`elem.querySelectorAll`获取的元素集合将返回`NodeList`类数组
`getElement*` 方法只能通过 `document`对象调用
```js
let divs = document.querySelectorAll('div') // NodeList(4) [div.Owen, div#modal, div.main, div]
 document.getElementsByTagName('div')//HTMLCollection [div.Owen]
```

#### matches
`elem.matches(css)`会检测 `elem`是否匹配到给定的css选择器，返回 true 或 false

#### closest
`elem.closest(css)`此方法会查找css选择器匹配到的祖先HTML，包括自身，并返回最先找到的元素

#### contains
`elem.catains(dom)` 判断 dom 是否为 elem 的后代，或等于elem，返回true 或false

#### 节点属性
所有的节点都继承自根节点 `EventTarget`
- EventTarget:作为基础，让所有DOM 节点都支持事件
- Node：作为DOM 节点的基础，提供DOM树的核心功能：`parentNode`、`nextSibling`、`previousSibling`、`ChildNodes`等（只能读取 getter）;文本节点 `Text`,元素节点`Element`,注释节点`Comment`都继承自Node
- Element：做为DOM 元素的基类。提供元素级导航： `nextElementSibling`、`children`、`getElement*`、`querySelector`等等，浏览器不仅支持HTML，还支持 XML、SVG等，分别对应的基类 `HTMLElement`、`XMLElement`、`SVGElement`
- HTMLElement:作为所有元素的基类，被各种元素继承

##### innerHTML 和 outerHTML

- innerHTML: 获取或替换当前节点的所有子节点（不包含当前节点）
- outerHTML: 替换当前节点

#### 文档片段 DocumentFragment
用于存储节点的包装器，不会再浏览器中展示，需要通过插值方法才能展示包装器里面的内容
```js
function creatEl(){
let frag = new DocumentFragment();
 for (let i=1;i<4;i++) {
let li = document.createElement('li')
  li.append(i)
  frag.append(li)
}

return frag
}
ul.append(creatEl())
```
#### 类样式的修改等操作
`elem.className` 对应元素的类名，多个类目以空格分隔
```js
ul.className // "class1 class2 ..."
```
同时还要一个 `elem.classList` 对象可访问类名，它以类数组的方式存在，同时具有 `add/remove/toggle/contains` 等方法
```js
ul.classList //  DOMTokenList(2) ["333", "444", value: "333 444"]
ul.classList.add('class1')
ul.classList.remove('class1')
ul.classList.toggle('class1') // true 新增
ul.classList.toggle('class1') // false 去除
ul.classList.contains('class1') //false 是否包含

```
通常我们使用 `style.*`单独对样式属性进行修改，如果想要对多种样式进行调整可使用 `cssText`，**此方法会直接替换之前的样式**
```js
ul.style.cssText = `
    color: red ;
    background-color: skyblue;
    width: 20px;
    text-align: center;`
```
style 属性仅针对 style 属性值进行操作，无法读取css类中的属性值
```html
<style>
body {margin:20 auto;}
</style>
<script>
document.body.style.margin // ""
</script>
```

这时我们需要使用 `getComputedStyle(el,[,pseudo])`方法来获取对应的值
如果不传参或值无意义，将返回元素所有样式,其属性值都为解析值，如 `font-size:1em` 最后获取的可能为解析后的值"16px"

```js
let res = getComputedStyle(document.body)
res.marginTop // "20px"
res.margin // 谷歌 "20px 0px" 在火狐中为 ""  因此访问确切属性值须使用完整属性名


```
#### 获取元素的尺寸和滚动距离

- offsetTop/Left: 获取相对于设置有position属性为 absolute relative、fixed 的值或td、th、table、body的元素的距离
- offsetWidth/Height：获取外部宽度/高度，包含border,padding,scrollbar (display:none 或自身不在文档中，其值为0或null，由此可判断当前元素是否被隐藏)
- clientTop/Left：获取内侧与外侧的距离（滚动条在左边时，包含滚动条的宽度）
- clientWidth/Height：获取可视区内容的宽高，即不包含滚动条和border
- scrollWidth/Height：获取全部内容（包含隐藏部分）的宽高
- scrollTop/Left: 获取 元素隐藏部分的上/左距离，包含border,这两个属性可修改，其它属性能只读取
**HTML 文件里如果没有 `<!DOCTYPE HTML>` 上述的属性可能会有所不同，这不是一个 JavaScript 的问题，但会影响到 JavaScript。**

#### 滚动浏览器窗口
pageXOffset/pageYOffset: 获取可视窗口移动的距离 无法设值

可通过 window.scrollBy, window.scrollTo, elem.scrollIntoView来滚动窗口
- `scrollBy(x,y)`：滚动页面至相对于现在位置的(x,y)位置
- `scrollTo(x,y)`：滚动到页面相对于文档左上方的（x,y）,位置，类似于 `scrollTop/scrollLeft`
- `elem.scrollIntoView(truly)`：如果 truly 为真则使 当前元素 滚动至窗口顶部，元素顶部与窗口顶部对齐，如果truly 为false,则当前元素底部与窗口底部对齐。

如果禁止窗口滚动可使 样式属性 `overflow` 值为 `hidden`

#### 坐标
##### getBoundingClientRect()`方法获取相对于可视窗口的坐标对象
其所有属性都是以可视窗口左端（X）和顶部（Y）为起点
```js
ul.getBoundingClientRect()
/*

DOMRect {
    bottom: 829.59375  // 元素底部的Y坐标
    height: 210  // 元素真实高度
    left: 0 // 元素左边 X 坐标
    right: 1903 // 元素右边 X 坐标
    top: 619.59375 // 元素顶部 Y 坐标
    width: 1903 // 元素自身真实宽度即不包含滚动条
    x: 0
    y: 619.59375
}

*/
```
###### document.elementFromPoint
`document.elementFromPoint(x,y)`返回可视窗口坐标（x,y），最顶层的元素
```js
let elem = document.elementFromPoint(0,0) // <p>556666</p>
```
如果`x,y`不在正常范围内将返回 null,

##### 相对于文档坐标，JS并未提供原生标准方法，可自己写一个出来：
```
function getDomCoords(el){
    let {top,left} = el.getBoundingClientRect()
  return {
    top:top+ window.pageYOffset,
    left:left+window.pageXOffest
  }
}
```


## Node 中的问题

### 获取本机 IP 地址
```javascript
const os = require('os');
const ip = showObj(os.networkInterfaces());

function showObj(obj){
/*     for (let devName in obj){
        let iface = obj[devName];
        for (let i = 0;i < iface.length;i++){
            let alias = iface[i];
            if (alias.family === 'IPv4'
                && alias.address !== '127.0.0.1'
                && !alias.internal){
                return alias.address;
            }
        }
    } */
     for (let devName in obj){
             let iface = obj[devName];
             for (let alias of iface ){
                if ( alias.family === 'IPv4'  && alias.address !== '127.0.0.1'  && !alias.internal) return alias.address;
             }
     }
}
```

## 开发方式
**当我们确定组件层级时，最容易的方式就是先用已有的数据模型渲染一个不包含交互功能的UI。最好将渲染UI 和添加交互两个过程分开**

因为，编写一个应用的静态版本时，往往需要编写大量代码，不需要考虑太多交互细节；
添加交互细节功能时则需考虑大量细节，而不需要编写太多代码。
所以将两个过程分开进行更为合适。
### 自上而下
首先编写较高的组件，对于简单应用使用这种方式更加方便

### 自下而上
对于复杂应用先编写较为底层的组件，同时为底层组件编写测试的方式更加简单
