### AST将混淆代码还原的过程

第一步：let ast = parser.parse(code);将混淆代码转换成AST；

第二步：用traverse函数将AST遍历，traverse(AST,{visitor})；	//visitor为包含操作的对象，里面进行`增删改查`

第三步：将遍历操作后的AST重新转换成代码。

```
const parser = require('@babel/parser');
const traverse = require('@babel/traverse').default;
const generator = require('@babel/generator').default;
const types = require('@babel/types');
const fs = require('fs');

// readFileSync 同步读取文件，不接收回调函数，直接返回函数结果
const code = fs.readFileSync("codes.js", "utf-8");

// parse 方法将文件内容转换为 AST
let ast = parser.parse(code);
traverse(ast,{这里传入的是一个对象，里面包含需要的操作，你可以自己编写对特定节点的操作})

// generate 将 ast 转换为 JavaScript 代码
const { code: output } = generator(ast);
console.log(output);
```



一、表达式的还原操作

表达式出现在赋值语句

```
const a = !![];									// 这句话其实就是const a = true；通过!和[]来做判断操作，类似JsFuck
const b = "abc" == "bcd";						// 判断两个字符串是否相等
const c = (1 << 3) || 2;						// `||`或操作
const d = parseInt("5" + "0");					// 字符串拼接后进行整数转换
const e = ''.concat("a", "b", "c") + 333;		// 
const f = a ? "a is ture" : "a is false";		// 三元运算
const g = '\x30\x78\x32\x64\x31';				// 代码混淆
```

所有这些操作都是为了使调试的过程变得难受，增加我们的调试时间；而且这个操作的量增大后，会使代码变得十分难以分析。

如果这些操作先被执行了，然后将执行前的值替换成执行后的值，那么表达式就清晰明了了。

```
//path 是 Babel 的遍历器（traverse）函数在访问 AST（抽象语法树）节点时传递给每个访问器函数的第一个参数。它代表了当前正在访问的节点以及该节点在树中的位置和上下文信息。

//如果多个类型的节点，处理的方式都一样，那么还可以使用 | 将所有节点连接成字符串，将同一个方法应用到所有节点：
"UnaryExpression|BinaryExpression|ConditionalExpression|CallExpression": (
        path
    ) => {
        // evaluate 执行 path 对象返回计算结果
        const { confident, value } = path.evaluate();
        
        // Infinity 正无穷
        if (value == Infinity || value == -Infinity) return;
        // 若 confident 为 true，则替换执行结果 value 的值
        confident && path.replaceWith(types.valueToNode(value));
    },
```

参考这篇大神的文章：[逆向进阶，利用 AST 技术还原 JavaScript 混淆代码 - 吾爱破解 - 52pojie.cn](https://www.52pojie.cn/thread-1744206-1-1.html)