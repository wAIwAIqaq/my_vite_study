# 、

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

```

