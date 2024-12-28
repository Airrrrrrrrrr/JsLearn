# hook的使用

hook就是钩子函数，什么是钩子函数，就是拦截修改属性的值的函数。

影响调试体验的，就hook掉。

简单的hook，就是直接修改函数：

```
var aaa = function(){debugger;}
setTimeInterval(aaa,1000);
```

这里开启了一个定时器，执行无限debugger；

那么我们可以直接hook这个执行无限debugger的函数：

```
var aaa = function(){};
//直接置空，就不会执行debugger操作了。
```



高级一点的hook，一般是一个自执行函数，里面先把要hook的变量进行保存，避免后续无法正常使用，然后通过浏览器的API函数(Object.defineProperty)进行hook：

```
(function () {
    let cookieCache = "";
    Object.defineProperty(document, "cookie", { //document就是要hook的对象(obj)，cookie是hook的属性(proprety)
        set: function (val) {
            console.log("Hook set cookie => ", val);
            if (val.indexOf("spiderapi.cn") !== -1) { //这里偷懒用了虫术的模板
                debugger;
            }
            cookieCache = val;
            return val;
        },
        get: function () {
            return cookieCache;
        }
    });
})();
```

我们hook的时候会看见cookie值设置时的堆栈，从而可以找出加密的位置。



https://spiderapi.cn/pages/js-hook
