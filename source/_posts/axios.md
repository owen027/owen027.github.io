---
title: Axios 
date: 2019-04-17 09:40:28
tags:
---
### 原生ajax

```javascript

function Ajax(obj) {
    this.url = obj.url ||'';
    this.type = obj.type || 'get';
    this.data = obj.data ||{};
    this.success = obj.success || null;
    this.error = obj.error || null;
}

Ajax.prototype.send = function(){
    var self = this;
    var  toStr = Object.prototype.toString; 
    if (self.data === null && typeof self.data !== 'object' && Array.isArray(obj)) return;
    return (function(){
         
            var xhr = new XMLHttpRequest();
            var data = '';

            for (var k in self.data){
                    data += k + '=' + self.data[k] + '&';
            }
                data = data.substr(0,data.length - 1);
                xhr.onreadystatechange = function(){
                    if (xhr.readyState === 4){
                        if(xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
                            isFunction(self.success)  &&  self.success(xhr.responseText)
                        }else{
                            isFunction(self.error)  && self.error(xhr)
                        }
                    }
                }
            if(self.type.toLocaleLowerCase() === 'post'){
                    xhr.open ('post',self.url,true)
                    xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded');
                    xhr.send(data)
                } else {
                    xhr.open('get', self.url + "?" + data,true)
                    xhr.send(null)
            }
    }());
};

function isFunction(obj){
    return toStr.call(obj) === "[object Function]"
}
var ajax = new Ajax({
     type:'post',
     url:"/login",
     data:{ 
         loginname:"admin",
         password:"admin" 
        },
      success:function(e){
            console.log(e)
            }, 
       error:function(err){
              console.log(err)
              },
        }).send();
```
