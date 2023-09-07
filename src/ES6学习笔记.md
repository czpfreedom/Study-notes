# ES6学习笔记

## 准备工作

### 简介

ES6， 全称 ECMAScript 6.0 ，是 JavaScript 的下一个版本标准

### 环境搭建

#### Node.js

## Node.js文件中的五个实参

当node执行模块中的代码时，会在代码的最顶部，添加如下代码

```javascript
function(exports, require, module, __filename, __dirname) {}
```

因此看起来，Node.js文件总是带有这五个全局变量。

### module

为了让Node.js的文件可以相互调用，Node.js提供了一个简单的模块系统。

一个 Node.js 文件就是一个模块，这个文件可能是JavaScript 代码、JSON 或者编译过的C/C++ 扩展。

Node.js 提供了 exports 和 require 两个对象，其中 exports 是模块公开的接口，require 用于从外部获取一个模块的接口，即所获取模块的 exports 对象。

### exports

### require

```javascript
var hello = require('./hello');
hello.world();
```

以上实例中，代码 require('./hello') 引入了当前目录下的 hello.js 文件。

####  require 的查找策略

由于 Node.js 中存在 4 类模块（原生模块和3种文件模块），尽管 require 方法极其简单，但是内部的加载却是十分复杂的，其加载优先级也各自不同。

https://www.runoob.com/nodejs/nodejs-module-system.html

## Node.js中的重要组件

### Event

events 模块只提供了一个对象： events.EventEmitter。

EventEmitter 的属性如下：

| 方法                                              | 描述                                                         | 例子                                                         |
| ------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| addListener(event, listener)/ on(event, listener) | 为指定事件注册一个监听器，接受一个字符串 event 和一个回调函数。值得注意的是，on就是addListener的一个别称，二者是完全相同的。 | server.addListener('connection', function (stream) {   console.log('someone connected!'); }); |
| once(event, listener)                             | 为指定事件注册一个单次监听器，即 监听器最多只会触发一次，触发后立刻解除该监听器。 | server.once('connection', function (stream) {   console.log('Ah, we have our first user!'); }); |
| removeListener(event, listener)                   | 移除指定事件的某个监听器，监听器必须是该事件已经注册过的监听器。 | var callback = function(stream) {   console.log('someone connected!'); }; server.on('connection', callback); // ... server.removeListener('connection', callback); |
| setMaxListeners(n)                                | 默认情况下， EventEmitters 如果你添加的监听器超过 10 个就会输出警告信息。 setMaxListeners 函数用于改变监听器的默认限制的数量。 |                                                              |
| emit(event, [arg1], [arg2], [...])                | 按监听器的顺序执行执行每个监听器，如果事件有注册监听返回 true，否则返回 false。 | emitter.emit('someEvent', 'arg1 参数', 'arg2 参数');         |

### Buffer

JavaScript 语言自身只有字符串数据类型，没有二进制数据类型。

但在处理像TCP流或文件流时，必须使用到二进制数据。因此在 Node.js中，定义了一个 Buffer 类，该类用来创建一个专门存放二进制数据的缓存区。

#### 创建缓冲区

在v6.0以后，官方文档里面建议使用Buffer.from()接口去创建Buffer对象。

``` javascript
const buf = Buffer.from('runoob', 'ascii');

// 输出 72756e6f6f62
console.log(buf.toString('hex'));

// 输出 cnVub29i
console.log(buf.toString('base64'));
```

#### 写入缓冲区

### Stream

所有的 Stream 对象都是 EventEmitter 的实例。常用的事件有：

1. **data** - 当有数据可读时触发。
2. **end** - 没有更多的数据可读时触发。
3. **error** - 在接收和写入过程中发生错误时触发。
4. **finish** - 所有数据已被写入到底层系统时触发。

#### 从流中读取数据

```javascript
var fs = require("fs");
var data = '';

// 创建可读流
var readerStream = fs.createReadStream('input.txt');

// 设置编码为 utf8。
readerStream.setEncoding('UTF8');

// 处理流事件 --> data, end, and error
readerStream.on('data', function(chunk) {
   data += chunk;
});

readerStream.on('end',function(){
   console.log(data);
});

readerStream.on('error', function(err){
   console.log(err.stack);
});

console.log("程序执行完毕");
```

#### 写入流

```javascript
var fs = require("fs");
var data = '菜鸟教程官网地址：www.runoob.com';

// 创建一个可以写入的流，写入到文件 output.txt 中
var writerStream = fs.createWriteStream('output.txt');

// 使用 utf8 编码写入数据
writerStream.write(data,'UTF8');

// 标记文件末尾
writerStream.end();

// 处理流事件 --> finish、error
writerStream.on('finish', function() {
    console.log("写入完成。");
});

writerStream.on('error', function(err){
   console.log(err.stack);
});

console.log("程序执行完毕");

```

### Node.js 中的函数

#### 作为参数的函数

```javascript
function say(word) {
  console.log(word);
}

function execute(someFunction, value) {
  someFunction(value);
}

execute(say, "Hello");

```
#### 匿名函数

```javascript
function execute(someFunction, value) {
  someFunction(value);
}

execute(function(word){ console.log(word) }, "Hello");

```

## 常用模块

### express模块

express 是一个流行的基于 Node.js 的 Web 应用框架。

#### 定义路由

```javascript
app.get('/', (req, res) => {
  res.send('Hello, World!');
});

app.post('/users', (req, res) => {
  // 处理创建用户的逻辑
});
```

除此以外，Express 还支持其他 HTTP 方法的路由处理，例如 app.put 用于处理 PUT 请求。app.delete 用于处理 DELETE 请求，以及 app.all 用于处理所有方法的请求。

#### 中间件

中间件的结构如下：

```javascript
app.use([path],function)
```

其中，[path]是路由的url。

function是中间件函数。

##### 中间件函数

```javascript
function(request,response,next)
```

这三个参数中的第一个和第二个参数，与get/post请求是一样的，next参数则代表中间件的流转。

如果当前中间件没有终结请求--响应循环，则必须调用next()方法将控制权交给下一个中间件，否则请求就会挂起。

#### 静态文件服务

#### 请求响应

Express响应中有四中常用的API：

##### res.write

res.write() 发送字符串对象或buffer对象的响应。

1. res.write()响应的数据“所见即所得”

res.write()的返回数据是没有经过处理的，原封不动的返回原数据，所见即所得

2. res.write()与res.end()总是且必须成对出现

如果要使用res.write()最后必须要有res.end，两者是成对出现的，缺一不可，也就是说使用res.write方法向前端返回数据，必须调用res.end方法结束请求。否则浏览器会一直处于处于请求状态

3. res.write()方法在结束浏览器响应请求之前，允许多次调用

如果想要输出多条语句，使用的是res.write()，也就是说在res.end() 之前，res.write() 可以被执行多次），且返回的数据会被拼接到一起。

4. res.write()是可以结合HTML标签显示的

res.write()输出内容可以结合HTML标签进行使用。

5. res.write()只支持输出字符串类型或是Buffer对象两种内容类型的数据。

##### res.end

res.end()函数用于结束响应过程。该方法用于快速结束响应，而无需任何数据。也就是说用于在没有任何数据的情况下快速结束响应。如果有响应数据，就不能用 res.end。

##### res.send

res.send() 发送各种类型的响应。

res.json()只能被调用一次，因为它等同于res.json()+res.end()。

##### res.json

res.json() 发送各种类型的响应。

res.json()只能被调用一次，因为它等同于res.json()+res.end()。

### http

http 模块是 Node.js 官方提供的、用来创建 web 服务器的模块。通过 http 模块提供的 http.createServer() 方法，就能方便的把一台普通的电脑，变成一台 Web 服务器，从而对外提供 Web 资源服务。

#### 创建 web 服务器的基本步骤

```javascript
// 1.导入 http 模块。
const http = require('http');

// 2.创建 web 服务器实例。
const server = http.createServer();

// 3.为服务器实例绑定 request 事件，监听客户端请求。
server.on('request', function(req, res) {
    console.log('someone visit our web server');
});

// 4.启动服务器
server.listen(8080, function() {
    console.log('server runing at http://127.0.0.1:8080');
});

```

### url模块

url模块是Node.js标准库中的一个模块，主要用于处理和解析URL地址。

#### url模块的导入

```javascript
const url = require('url');
```

#### url.parse方法

url.parse()方法用于将一个URL地址解析成一个对象。

以下是一个示例代码：

```javascript
const url = require('url');

const urlString = 'http://www.example.com/hello?id=123&name=haha';

const urlObj = url.parse(urlString, true);

console.log(urlObj);
```

输出结果为：

```javascript
{
  protocol: 'http:',
  slashes: true,
  auth: null,
  host: 'www.example.com',
  port: null,
  hostname: 'www.example.com',
  hash: null,
  search: '?id=123&name=haha',
  query: { id: '123', name: 'haha' },
  pathname: '/hello',
  path: '/hello?id=123&name=haha',
  href: 'http://www.example.com/hello?id=123&name=haha'
}
```

#### url.format方法

url.format()方法用于将一个URL对象转化为一个URL地址。

以下是一个示例代码：

```javascript
const url = require('url');

const urlObj = {
  protocol: 'http:',
  slashes: true,
  host: 'www.example.com',
  hostname: 'www.example.com',
  hash: null,
  search: '?id=123&name=haha',
  query: { id: '123', name: 'haha' },
  pathname: '/hello',
  path: '/hello?id=123&name=haha',
  href: 'http://www.example.com/hello?id=123&name=haha'
};

const urlString = url.format(urlObj);

console.log(urlString);
```

### util模块

**util**模块是 Node.js 的内置模块之一，提供了一些实用函数的集合，用于辅助开发者进行常见的功能操作。**util**模块包含了各种实用函数的集合，方便开发者编写高效、可维护的代码。

#### util的继承功能

util 模块提供了一个 inherits 函数，用于实现对象间的继承关系。这个函数可以将一个构造函数从一个原型继承到另一个构造函数的原型链上。

下面是一个例子：

```javascript
const util = require('util');

function Parent() {
  this.name = 'Parent';
}

Parent.prototype.sayHello = function() {
  console.log('Hello!');
};

function Child() {
  this.name = 'Child';
}

util.inherits(Child, Parent);

const child = new Child();
child.sayHello(); // 输出 'Hello!'
```

使用 util.inherits 函数将 Child 构造函数继承自 Parent 构造函数。继承后，Child 对象就可以使用 Parent 原型链上的属性和方法。

#### 促进回调函数的使用

util 模块提供了一些函数，可以帮助处理回调函数的调用和处理。下面是一些常用的函数：

##### promisify

promisify 函数将一个基于回调的异步函数转换为返回 Promise 的函数。

##### callbackify

callbackify 函数将返回 Promise 的函数转换为接受回调函数的函数。

#### 类型判断和错误处理

##### util.inspect

util.inspect 方法返回一个对象的字符串表示，通常用于调试和日志输出。

##### util.types.is[typename]

util.types 提供了一系列函数，用于检查 JavaScript 内置类型的实例。

```javascript
const util = require('util');

console.log(util.types.isDate(new Date())); // true
console.log(util.types.isRegExp(/abc/)); // true
console.log(util.types.isError(new Error())); // true
```

##### util.throwDeprecation

当满足某个条件时，我们可以使用 util.throwDeprecation 方法抛出一个废弃警告错误。

```javascript
const util = require('util');

if (someCondition) {
  util.throwDeprecation('This feature is deprecated');
}
```

## 常用函数

### promise函数

#### 回调函数的问题

回调函数剥夺了我们使用 return 和 throw 这些关键字的能力。

因此，Promise应运而生，用来传递异步操作的消息。

#### promise对象的三种状态

pomise对象只有三种状态，分别是

1. 异步操作未完成（pending）

2. 异步操作成功（fulfilled）

3. 异步操作失败（rejected）

这三种的状态的变化途径只有两种：

1. 从“未完成”到“成功”
2. 从“未完成”到“失败”

一旦状态发生变化，就凝固了，不会再有新的状态变化，会一直保持这个结果，这时就称为 **resolved**

#### resolve方法

```javascript
Promise.resolve（'foo'）
```

等价于 

```javascript
new Promise(resolve => resolve('foo'))
```

resolve方法有四种参数如下：

##### 参数是一个 Promise 实例

如果参数是 Promise 实例，那么Promise.resolve将不做任何修改、原封不动地返回这个实例。

##### 参数是一个thenable对象

thenable对象指的是具有then方法的对象。

Promise.resolve方法会将这个对象转为 Promise 对象，然后就立即执行thenable对象的then方法。

然后该Pormise对象的状态变为resolved

##### 参数不是具有then方法的对象，或根本就不是对象

如果参数是一个原始值，或者是一个不具有then方法的对象，则Promise.resolve方法返回一个新的 Promise 对象，状态从一生成就是resolved。

Promise.resolve方法的参数，会同时传给回调函数。

##### 不带有任何参数

Promise.resolve方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。

#### reject方法

#### all方法

#### race方法

#### then方法

#### catch方法

考虑如下代码：

```javascript
let promise1 = new Promise((res, rej) => {
    //打印一个未定义的变量，这里会出错
    console.log(a)
    res()
})
promise.then(null, reason => console.log(reason)).catch(reason => console.log(reason))
```

这个catch捕捉的还是promsie1的错误，他的实现原理是最先捕捉第一个promise也就是promsie1，如果没出错则向下(返回的新的promise)捕捉，以此类推。而不是我们想象的返回的新的promise来调用catch，这个cath就会取捕捉这个返回的新的promise出现的错误。

## 常用符号

### 箭头符号 =>

```javascript
var site = () => {    var url="www.xttblog.com";    var title="塔灯网络";}
```

等价于

```javascript
var site = function(){    var url="www.xttblog.com";    var title="塔灯网络";}
```

## 常用概念

### 原型链

原型链是一种机制，指的是JavaScript每个对象包括原型对象都有一个内置的\_\_proto\_\_属性指向创建它的函数对象的原型对象，即prototype属性。

JavaScript规定：每个构造函数都有prototype，指向另外一个对象。这个prototype既是一个对象，又是属性，都是被构造函数拥有。

我们可以将那些不变的方法放在prototype上，那么这样就会节约资源，且所有实例都可以访问这个方法。

例如：

```javascript
function Person(name, age,sex) {
  this .name = name;
  this .age = age;
  this .sex = sex;
}
Person.prototype.eat = function (){
  console.log("I can eating");
}
let person1= new Person("阿飞"，22，"男");
person1.eat();
```

### let和var的区别

**let**的用法类似于**var**，但是所声明的变量，只在**let**命令所在的代码块内有效。

#### 基本用法

```javascript
{
  let a = 10;
  var b = 1;
}
a // ReferenceError: a is not defined.
b // 1
```

例如，在使用**for**循环的计数器时，就很适合使用**let**命令

### 静态成员和实例成员

静态成员：由构造函数直接调用的属性、方法叫静态成员。
实例成员：由构造函数创建出来对象才能调用的属性和方法就叫实例成员。

### 回调地狱

如下是一个通过回调函数异步执行的代码：

```javascript
setTimeout(function () {  //第一层
  console.log('武林要以和为贵');
  setTimeout(function () {  //第二程
    console.log('要讲武德');
    setTimeout(function () {   //第三层
      console.log('不要搞窝里斗');
    }, 1000)
  }, 2000)
}, 3000)
```

为实现代码顺序执行，这种回调函数中嵌套回调函数的情况就叫做回调地狱。它会造成我们的代码可读性非常差，后期不好维护。

解决回调地狱的方法有如下几种：

1. Promise
2. async/await

## 常用功能实现

### Get请求

我们可以使用 url.parse 方法来解析 URL 中的参数，代码如下：

```javascript
var http = require('http');
var url = require('url');
var util = require('util');
 
http.createServer(function(req, res){
    res.writeHead(200, {'Content-Type': 'text/plain'});
 
    // 解析 url 参数
    var params = url.parse(req.url, true).query;
    res.write("网站名：" + params.name);
    res.write("\n");
    res.write("网站 URL：" + params.url);
    res.end();
 
}).listen(3000);
```



### Post请求