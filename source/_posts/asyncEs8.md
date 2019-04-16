---
title: Async/await
date: 2019-03-14 19:27:09
categories:
- ES6
tags: 
- async
---

### ES2017引入async 函数
- Generator 的语法糖

```javascript

//generator
const fs = require('fs');

const readFile = function (path) {
    return new Promise(function (resolve,reject) {
        fs.readFile(path, function(err,data) {
            if (err) return reject(err);
            resolve(data);
        });
    });
};

const g = function* () {
    const f1 = yield readFile(url1);
    const f2 = yield readFile(url2);
}

// async
const asyncFile = async function () {
    const f1 = await readFile(url1);
    const f2 = await readFile(url2);

}

/**
 * 将 g 函数的 * 变成 axync
 * 将 yield  变成 await 
 */

```

#### async  对  generator 进行以下优化

- 不再需要调用 `next` 方法就能执行
- 更加语义化
- 实用性更强 `await` 后面可以是 Promise , 原始类型值（会自动转成立即 resolved 的 Promise 对象）
- 返回值为Promise 对象更加方便，<label>当函数执行遇到 `await` 时暂停执行，等到异步操作完成，再执行后面的代码</label>

```javascript
function timeout(ms) {
    return new Promise(resolve => setTimeout(resolve,ms))
}
async function asyncPrint(val, ms ) {
     await timeout(ms);
    console.log(val)
}

asyncPrint('Owen',1000) //1s 后 "Owen"

//因为 asnyc 返回 Promise 所以可以改用 async

async function timout(ms) {
    await new Promise(resolve => setTimeout(resolve,ms));
}

async function asyncPrint(val, ms ) {
     await timeout(ms);
    console.log(val)
}

asyncPrint('Owen',1000) //1s 后 "Owen"