# JavaScript中的混淆

time：2024.12.24

### 一、JJ混淆

特征：代码中有特别多的`$`和`\`。

目标网址：https://spa10.scrape.center/

解决方法：

　　1：去掉代码最后一个符号 ('_') 后，放到浏览器里面去直接执行就可以看到源码，为了方便查看还可以加上.toString()。

---

### 二、AA混淆

特征：代码像颜文字，有很多半角符号。

解决方法：

　　1：去掉代码最后一个符号 ('_') 后，放到浏览器里面去直接执行就可以看到源码。

　　2：在线调试，在 AAEncode 代码第一行下断点，然后一步一步执行，最终也会在虚拟机（VM）里看到源码。

---

### 三、jsFuck混淆

特征：代码有很多`！`和`[]`。

```
false       =>  ![]
true        =>  !![]
undefined   =>  [][[]]
NaN         =>  +[![]]
0           =>  +[]
1           =>  +!+[]
2           =>  !+[]+!+[]
10          =>  [+!+[]]+[+[]]
Array       =>  []
Number      =>  +[]
String      =>  []+[]
Boolean     =>  ![]
Function    =>  []["filter"]
eval        =>  []["filter"]["constructor"]( CODE )()
window      =>  []["filter"]["constructor"]("return this")()
```

解决方法：

JSFUCK解密工具：[https://lelinhtinh.github.io/de4js/ ](https://cloud.tencent.com/developer/tools/blog-entry?target=https%3A%2F%2Flelinhtinh.github.io%2Fde4js%2F&objectId=2229985&objectType=1&isNewArticle=undefined) 

JSFUCK解密工具：[http://codertab.com/JsUnFuck](https://cloud.tencent.com/developer/tools/blog-entry?target=http%3A%2F%2Fcodertab.com%2FJsUnFuck&objectId=2229985&objectType=1&isNewArticle=undefined)

断点调试。

---

### 四、OB混淆

特征：代码中含有特别多的`_0x123456`和`\x12`。

 `\x12`是一种16进制编码形式的数据，可以转换成二进制后对应ASCII编码的文本；例如`\x31`就代表数字1。

```
List = "0x30"		16进制的表示方式
List - 0 = 48
这是一种利用Js的特性的操作；在Js中用于将变量转换为数字。
如果 List 是一个可以被解析为数字的字符串（例如 "0x30"），这个操作会将它转换为数字 48。
```



OB 混淆（Opaque Predicate Obfuscation）是一种复杂的混淆技术，通过插入不透明谓词来掩盖代码的真实逻辑，使逆向工程师难以理解代码的实际功能。

---

### 五、针对混淆的方法

**混淆提示**

- 1、点号(.)是无法混淆的；
- 2、冒号(:)是无法混淆的；

### 简单hook

在控制台编写JS运行： eval = function(){debugger;} Function = function(){debugger;} 指定方法名进行hook：sojson.$ = function(){debugger;} **当function被调用时则会断住**

- 1、通过控制台打印arguments参数进行分析；
- 2、通过二次断点进入function跟值进行分析；

在JJ混淆的案例基础上，进行hook扩展；