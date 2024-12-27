# AST（Abstract syntax tree）抽象语法树		



这是一个简单的js语句

```
const e = ''.concat("a", "b", "c") + 333;
```

![image-20241224180611355](C:\Users\20962\AppData\Roaming\Typora\typora-user-images\image-20241224180611355.png)

这里我用数字简单代替对应的长度，显而易见可以看见这个语句的length是  `41`  。

我们把这句语句放到网站[AST explorer](https://astexplorer.net/)中，会显示出一个结构，这个结构就是语句的抽象语法树。

<img src="C:\Users\20962\AppData\Roaming\Typora\typora-user-images\image-20241224181202381.png" alt="image-20241224181202381" style="zoom: 67%;" />

这里的end的值刚好与我们语句的length相等，那么这个end的值就是我们语句的长度。

现在我们来看`body`中的内容

```
type:"VariableDeclaration"
kidn:"const"
```

代表我们这句js语句是一个**变量声明**，并且是由const声明的。

接着看declarations里的内容

<img src="C:\Users\20962\AppData\Roaming\Typora\typora-user-images\image-20241224181857251.png" alt="image-20241224181857251" style="zoom:67%;" />

**Q:**`start` 和`end` 代表了声明的主体位置，`start`的值是怎么来的呢？

**A:**const有5个字符，所以`start`的值为`5+1=6`。

**Q:**`end`为什么是40，而不是41？

**A:**因为我们声明结束后，有个结束标识符`;`，在这里声明的主体不需要这个标识符参与，或者说不参与计算。



现在我们来看`id`中的内容

```
type:"Identifier"	==>这是与一个标识符
start:6				==>标识符的起始坐标
end:7				==>标识符的结束坐标
name:"e"			==>这个标识符的名字

这一部分全是与标识符相关的内容，所以id的内容代表标识符的信息
```

那么`init`中的内容自然是表达式的信息,BinaryExpression(二元表达式	==>	x + y = ?)

`left`的内容有点多，我们先看`right`的内容。

<img src="C:\Users\20962\AppData\Roaming\Typora\typora-user-images\image-20241224183818325.png" alt="image-20241224183818325" style="zoom:67%;" />

这里的operator代表操作的意思，"+"自然是加法。

```
Literal代表这是一个字面量
value
raw
```

现在来看`left`的部分

<img src="C:\Users\20962\AppData\Roaming\Typora\typora-user-images\image-20241224184344627.png" alt="image-20241224184344627" style="zoom: 50%;" />

```  
CallExpression    	调用表达式 	 				=========>	''.concat("a", "b", "c")
MemberExpression  	成员表达式       			=========>	''.concat 
object:Literal	  	这是一个对象也是一个字面量	   =========>  ''
property:Idenifier	属性:标识符					=========>  concat

arguments	参数，里面有三个字面量a、b、c
```

这样的形式可以代表这个js语句的信息。

那么我们就可以通过修改替换删除AST中的值，从而来改变js语句。

