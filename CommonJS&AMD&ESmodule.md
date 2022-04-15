

# CommonJS规范

CommonJs是业界提出的最早的规范，常使用于服务端。

**统一的模块化代码规范；**

**实现自动加载模块的加载器（loader）；**

```js
// module-a.js
const data = 'hello world';
function getData(){
   return data;
}
module.exports = {
    getData
}
// index.js
const { getData } = require('./module-a.js');
console.log(getData());
```

经过node.js内部的loader转译后：

```js
(function (exports, require, module, _filename, _dirname) {
   // 执行模块代码
   // 返回exports 对象
})
```

模块加载由node.js提供，一方面它定义了一套完整的模块转化代码规范，另一方面node.js为之提供了自动加载模块的loader，但随之二代也带来了一些问题:

1.模块加载器由node.js提供，以来了node.js本身的功能实现，比如文件系统，直接放到浏览器是无法执行的。需要通过browserify这种打包工具来支持打包CommonJS模块，从而支持再浏览器中运行。

2.CommonJS本身约定以同步的方式进行模块的加载，一来所有模块都在本地，不需要进行网络I/O，二来只有服务器启动才会加载所有模块，而服务器通常启动后会一直运行，所以对服务器性能并没有太大影响。但是如果在浏览器内部进行模块加载，则会衍生大量同步加载请求，浏览器要等到响应后才能继续解析模块，**会导致浏览器JS解析过程中的阻塞**。

# AMD规范

AMD 全称Asynchronous Module Definition，异步模块定义规范。在浏览器中会异步进行加载，不会产生JS解析过程阻塞。

```js
// main.js
define(["./print.js"],function (printModule){
    printModule.print('main');
});

// print.js
define(function(){
   return {
       print:function (msg) {
           console.log("print" + msg);
       }
   }
})
```

在AMD规范中，我们可以通过define去定义或加载一个模块，比如上面的main.js模块或者print.js模块,如果模块需要导出一些成员需要通过在定义模块的函数中return出去(example：print.js)，如果当前模块一来了一些其他的模块则可以通过define的第一个参数来声明依赖（example：main.js），这样模块的代码执行之前浏览器会**先加载依赖模块**。

当然,你也可以使用require关键字来加载一个模块,example:

```js
// module-a.js
require(["./print.js"],function (printModule){
    printModule.print("module-a")
})
```

使用require只能加载模块，而不能定义模块。

由于没有得到浏览器的原生支持，AMD规范需要由第三方的loader来实现,比如requireJS。

# ES6 Module

ES  Module 也被称作 ES Module（ESM），是由 ECMAScript 官网提出的模块化规范，作为一个官方提的规范，ES Module已经得到现代浏览器的支持。在现代浏览器中如果在HTML中加入含有`type=“module”`属性的script标签,那么浏览器会按照ES Module规范来进行依赖加载和模块解析，这也是vite在开发阶段实现no-bundle的原因，由于模块加载的任务交给了浏览器，即使不打包也可以顺利运行代码。

```html
// main.js
import { methodA } from './module-a.js'

// module-a.js
const methodA = () => {
    console.log("a");
}

export { methodA };
<!DOCTYPE html>
<html lang="en">
    <head>
        <link rel="icon" type="imamg/svg+xml" href="/src/favicon.svg"/>
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Vite App</title>
    </head>
    <body>
        <div id="root"> </div>
        <script type="module" src="main.js"></script>
    </body>
</html>
```

如果在node.js环境中，你可以在package.json中声明type:"module"属性

```json
// packge.json
{
    type:"module"
}
```

然后node.js便会默认以ES Module规范去解析模块

```
node main.js
```

在node.js环境中，即使实在CommodJS模块里面，也可以通过import方法顺利加载ES模块，如下所示：

```js
async function func(){
  // 加载一个ES 模块
  // 文件名后缀需要时 mjs
    const { a } = await import("./module-a.msj");
    console.log(a)
}

func();

module.exports = {
    func,
};
```

