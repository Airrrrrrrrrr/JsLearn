### 无限debugger01

一般无限debugger存在于setTimeoiut、setTimeInterval、Function的constructor里和eval中。

```
"aHR0cHM6Ly93d3cuYXFpc3R1ZHkuY24v"
```



![image-20241228151708993](C:\Users\20962\AppData\Roaming\Typora\typora-user-images\image-20241228151708993.png)

VM里的无限debugger

在右边的Call stack里查看上一步做了什么？

```
function txsdefwsw() {
    var r = "V"
      , n = "5"
      , e = "8";
    function o(r) {
        if (!r)
            return "";
        for (var t = "", n = 44106, e = 0; e < r.length; e++) {
            var o = r.charCodeAt(e) ^ n;
            n = n * e % 256 + 2333,
            t += String.fromCharCode(o)
        }
        return t
    }
    try {
        var a = ["r", o("갯"), "g", o("갭"), function(t) {
            if (!t)
                return "";
            for (var o = "", a = r + n + e + "7", c = 45860, f = 0; f < t.length; f++) {
                var i = t.charCodeAt(f);
                c = (c + 1) % a.length,
                i ^= a.charCodeAt(c),
                o += String.fromCharCode(i)
            }
            return o
        }("@"), "b", "e", "d"].reverse().join("");
        !function c(r) {
            (1 !== ("" + r / r).length || 0 === r) && function() {}
            .constructor(a)(),
            c(++r)
        }(0)
    } catch (a) {
        setTimeout(txsdefwsw, 100)
    }
}

```

这里做了混淆操作，实际上就是拼接debugger；

我们注意到catch里有一个setTimeoiut函数，定时器嘛，hook掉

```
var setTimeout_ = setTimeout;
setTimeout = function(){};
```

![image-20241228152413764](C:\Users\20962\AppData\Roaming\Typora\typora-user-images\image-20241228152413764.png)

hook操作后发现控制台一直在弹信息，这里应该是log和clear的操作。

跟进去

![image-20241228152544033](C:\Users\20962\AppData\Roaming\Typora\typora-user-images\image-20241228152544033.png)

hook掉

```
var log_ = console.log;
var clear_ = console.clear
console.log = function(){};
console.clear = function(){};
```

发现控制台不输出信息了，接着调试又出现一个debugger；

```
  var debugflag = false;
  document.onkeydown = function() {
    if ((e.ctrlKey) && (e.keyCode == 83)) {
      alert("检测到非法调试，CTRL + S被管理员禁用");
      return false;
    }
  }
  document.onkeydown = function() {
    var e = window.event || arguments[0];
    if (e.keyCode == 123) {
      alert("检测到非法调试，F12被管理员禁用");
      return false;
    }
  }
  document.oncontextmenu = function() {
    alert('检测到非法调试，右键被管理员禁用');
    return false;
  }
  !function () {
    const handler = setInterval(() => {
      if (window.outerWidth - window.innerWidth > 300 ||
       window.outerHeight - window.innerHeight > 300) {
        // document.write((window.outerWidth - window.innerWidth) + ',' + (window.outerHeight - window.innerHeight));
        document.write('检测到非法调试, 请关闭调试终端后刷新本页面重试!<br/>');
        document.write("Welcome for People, Not Welcome for Machine!<br/>");
        debugflag = true;
      }
      const before = new Date();
      (function() {}
        ["constructor"]("debugger")())
      const after = new Date();
      const cost = after.getTime() - before.getTime();
      if (cost > 50) {
        debugflag = true;
        document.write('检测到非法调试, 请关闭调试终端后刷新本页面重试!<br/>');
        document.write("Welcome for People, Not Welcome for Machine!<br/>");
      }

    }, 2000)
  }();
```

这里可以看到对右键、F12和保存本地操作进行了禁止；

后面是一个自执行：

检测了window的宽和高；

` (function() {}["constructor"]("debugger")())`  这里是在Function的constructor里做了debugger操作；

还有一步是对这个debugger的时间进行了检测，如果debugger的时间过长，则判断正在进行调试操作。

我们可以hook掉这里的构造函数；

```
(function () {
	Function.prototype.constructor_bc=Function.prototype.constructor;
	Function.prototype.constructor=function(){
		if(arguments[0]==="debugger"){}  //利用arguments关键字的属性获取当前方法里面的参数
		else{
			debugger;
			return Function.prototype.constructor_bc.apply(this,arguments)  //跟上面处理debugger和定时器一起用的处理方法一样
		}
	};
    //hook cost;
})();   
```

hook完毕后就过掉这个网站的无限debugger了。