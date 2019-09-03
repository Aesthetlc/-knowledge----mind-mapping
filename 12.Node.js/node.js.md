本文资源感谢   廖雪峰老师  https://www.liaoxuefeng.com/wiki/1022910821149312/1023025235359040

# 一.基本模块

### global

我们都知道，JavaScript有且仅有一个全局对象，在浏览器中，叫`window`对象。而在Node.js环境中，也有唯一的全局对象，但不叫`window`，而叫`global`，这个对象的属性和方法也和浏览器环境的`window`不同。

- console，我们在初体验时，使用了console，它可不是浏览器中的console对象，使用的是node中的console

- process，和进程相关的对象

- setInterval，同理，也是node中的，不是浏览器中的

- **require()**，它是全局对象global中的一个方法，用于在js文件中引入另外的文件
    - require() 方法可以在js文件中加载另外的js文件（模块）
    - require() 方法可以在js文件中加载json文件
    
- __dirname，当前执行文件的绝对路径（在js文件中使用）

- __filename，当前执行文件的绝对路径，包含文件名（在js文件中使用）

> 上述：__dirname、\_\_filename、exports、module、require 这5个不能通过global调用，但是可以直接使用

### process

`process`也是Node.js提供的一个对象，它代表当前Node.js进程。通过`process`对象可以拿到许多有用信息：

```js
> process === global.process;
true
> process.version;
'v5.2.0'
> process.platform;
'darwin'
> process.arch;
'x64'
> process.cwd(); //返回当前工作目录
'/Users/michael'
> process.chdir('/private/tmp'); // 切换当前工作目录
undefined
> process.cwd();
'/private/tmp'
```

JavaScript程序是由事件驱动执行的单线程模型，Node.js也不例外。Node.js不断执行响应事件的JavaScript函数，直到没有任何响应事件的函数可以执行时，Node.js就退出了。

如果我们想要在下一次事件响应中执行代码，可以调用`process.nextTick()`：

用Node执行上面的代码`node test.js`，你会看到，打印输出是：

```js
nextTick was set!
nextTick callback!
```

这说明传入`process.nextTick()`的函数不是立刻执行，而是要等到下一次事件循环。

Node.js进程本身的事件就由`process`对象来处理。如果我们响应`exit`事件，就可以在程序即将退出时执行某个回调函数：

```js
// 程序即将退出时的回调函数:
process.on('exit', function (code) {
    console.log('about to exit with code: ' + code);
});
```

### 判断JavaScript执行环境

有很多JavaScript代码既能在浏览器中执行，也能在Node环境执行，但有些时候，程序本身需要判断自己到底是在什么环境下执行的，常用的方式就是根据浏览器和Node环境提供的全局变量名称来判断：

```javascript
if (typeof(window) === 'undefined') {
    console.log('node.js');
} else {
    console.log('browser');
}
```

## 1.1 fs模块

### 读文件

#### 异步读文件

按照JavaScript的标准，异步读取一个文本文件的代码如下：

```js
var fs = require('fs');

fs.readFile('./123.html', 'utf-8', function (err, data) {
    if (err) console.log(err);
    console.log(data);
});
```

请注意，`sample.txt`文件必须在当前目录下，且文件编码为`utf-8`。

异步读取时，传入的回调函数接收两个参数，当正常读取时，`err`参数为`null`，`data`参数为读取到的String。当读取发生错误时，`err`参数代表一个错误对象，`data`为`undefined`。这也是Node.js标准的回调函数：第一个参数代表错误信息，第二个参数代表结果。后面我们还会经常编写这种回调函数。

由于`err`是否为`null`就是判断是否出错的标志，所以通常的判断逻辑总是：

```js
if (err) {
    // 出错了
} else {
    // 正常
}
```

如果我们要读取的文件不是文本文件，而是二进制文件，怎么办？

下面的例子演示了如何读取一个图片文件：

```js
var fs = require('fs');

fs.readFile('./timg.jpg', function (err, data) {
    if (err) {
        console.log(err);
    } else {
        console.log(data);
        console.log(data.length + ' bytes');
    }
});
```

当读取二进制文件时，不传入文件编码时，回调函数的`data`参数将返回一个`Buffer`对象。在Node.js中，`Buffer`对象就是一个包含零个或任意个字节的数组（注意和Array不同）。

`Buffer`对象可以和String作转换，例如，把一个`Buffer`对象转换成String：

```js
// Buffer -> String
var text = data.toString('utf-8');
console.log(text);
```

或者把一个String转换成`Buffer`：

```js
// String -> Buffer
var buf = Buffer.from(text, 'utf-8');
console.log(buf);
```

#### 同步读文件

除了标准的异步读取模式外，`fs`也提供相应的同步读取函数。同步读取的函数和异步函数相比，多了一个`Sync`后缀，并且不接收回调函数，函数直接返回结果。

用`fs`模块同步读取一个文本文件的代码如下：

```js
var fs = require('fs');

var data = fs.readFileSync('sample.txt', 'utf-8');
console.log(data);
```

可见，原异步调用的回调函数的`data`被函数直接返回，函数名需要改为`readFileSync`，其它参数不变。

如果同步读取文件发生错误，则需要用`try...catch`捕获该错误：

```js
try {
    var data = fs.readFileSync('sample.txt', 'utf-8');
    console.log(data);
} catch (err) {
    // 出错了
     console.log(err);
}
```

### 写文件

将数据写入文件是通过`fs.writeFile()`实现的：

```js
var fs = require('fs');

fs.writeFile('./write.txt', data, function (err) {
    if (err) console.log(err)
    console.log('写入完毕');
});
```

`writeFile()`的参数依次为文件名、数据和回调函数。如果传入的数据是String，默认按UTF-8编码写入文本文件，如果传入的参数是`Buffer`，则写入的是二进制文件。回调函数由于只关心成功与否，因此只需要一个`err`参数。

说到这里我想起了一个朋友问我的一个问题，细心的同学应该会发现这个writeFile会将原文本覆盖，所以，有没有什么好的方法能追加或者不覆盖呢？答案是肯定的，肯定会有的。

```js
//追加文本内容 方法1
const data = "hello,nihao"
fs.appendFile('./write.txt', data, function (err) {
    if (err) console.log(err)
    console.log('写入完毕');
});
```

```js
//追加文本内容 方法2
const data = "hello,nihao";
fs.open('ttt.txt', 'a', function (err, fd) {
    if (err) {
        console.log(err);
    } else {
        console.log('open');
        fs.writeFile(fd, data, function (err) {
            if (err) {
                console.log(err);
            } else {
                console.log('ok new');
            }
        });
    }
});
```



和`readFile()`类似，`writeFile()`也有一个同步方法，叫`writeFileSync()`：

```js
var fs = require('fs');

var data = 'Hello, Node.js';
fs.writeFileSync('output.txt', data);
```

### stat

如果我们要获取文件大小，创建时间等信息，可以使用`fs.stat()`，它返回一个`Stat`对象，能告诉我们文件或目录的详细信息：

```js
'use strict';

var fs = require('fs');

fs.stat('sample.txt', function (err, stat) {
    if (err) {
        console.log(err);
    } else {
        // 是否是文件:
        console.log('isFile: ' + stat.isFile());
        // 是否是目录:
        console.log('isDirectory: ' + stat.isDirectory());
        if (stat.isFile()) {
            // 文件大小:
            console.log('size: ' + stat.size);
            // 创建时间, Date对象:
            console.log('birth time: ' + stat.birthtime);
            // 修改时间, Date对象:
            console.log('modified time: ' + stat.mtime);
        }
    }
});
```

运行结果如下：

```js
isFile: true
isDirectory: false
size: 18100
birth time: Fri Dec 11 2019 09:43:41 GMT+0800 (CST)
modified time: Fri Dec 11 2019 12:09:00 GMT+0800 (CST)
```

`stat()`也有一个对应的同步函数`statSync()`

### 异步还是同步

在`fs`模块中，提供同步方法是为了方便使用。那我们到底是应该用异步方法还是同步方法呢？

由于Node环境执行的JavaScript代码是服务器端代码，所以，绝大部分需要在服务器运行期反复执行业务逻辑的代码，*必须使用异步代码*，否则，同步代码在执行时期，服务器将停止响应，因为JavaScript只有一个执行线程。

服务器启动时如果需要读取配置文件，或者结束时需要写入到状态文件时，可以使用同步代码，因为这些代码只在启动和结束时执行一次，不影响服务器正常运行时的异步执行。

##  1.2 path 模块

* 使用方法

  * 加载模块

    ```js
    // 使用核心模块之前，首先加载核心模块
    let path = require('path');
    // 或者
    const path = require('path');
    ```
    
   * 举例

     ```js
     const path = require('path');
     
     // extname -- 获取文件后缀
     console.log(path.extname('index.html')); // .html
     console.log(path.extname('index.coffee.md')); // .md
     
     // join -- 智能拼接路径
     console.log(path.join('/a', 'b', 'c')); // \a\b\c
     console.log(path.join('a', 'b', 'c')); // a\b\c
     console.log(path.join('/a', '/b/../c')); // \a\c
     console.log(path.join('/a', 'b', 'index.html')); // \a\b\index.html
     console.log(path.join(__dirname, 'a', 'index.html')); // 得到一个绝对路径
     ```

## 1.3 Stream模块

`stream`是Node.js提供的又一个仅在服务区端可用的模块，目的是支持“流”这种数据结构。

示例：

```js
var fs = require('fs');

// 打开一个流:
var rs = fs.createReadStream('sample.txt', 'utf-8');

rs.on('data', function (chunk) {
    console.log('DATA:')
    console.log(chunk);
});

rs.on('end', function () {
    console.log('END');
});

rs.on('error', function (err) {
    console.log('ERROR: ' + err);
});
```

要注意，`data`事件可能会有多次，每次传递的`chunk`是流的一部分数据。

要以流的形式写入文件，只需要不断调用`write()`方法，最后以`end()`结束：

```js
var fs = require('fs');

var ws1 = fs.createWriteStream('output1.txt', 'utf-8');
ws1.write('使用Stream写入文本数据...\n');
ws1.write('END.');
ws1.end();

var ws2 = fs.createWriteStream('output2.txt');
ws2.write(new Buffer('使用Stream写入二进制数据...\n', 'utf-8'));
ws2.write(new Buffer('END.', 'utf-8'));
ws2.end();
```

所有可以读取数据的流都继承自`stream.Readable`，所有可以写入的流都继承自`stream.Writable`。

### pipe

就像可以把两个水管串成一个更长的水管一样，两个流也可以串起来。一个`Readable`流和一个`Writable`流串起来后，所有的数据自动从`Readable`流进入`Writable`流，这种操作叫`pipe`。

在Node.js中，`Readable`流有一个`pipe()`方法，就是用来干这件事的。

让我们用`pipe()`把一个文件流和另一个文件流串起来，这样源文件的所有数据就自动写入到目标文件里了，所以，这实际上是一个复制文件的程序：

```js
var fs = require('fs');

var rs = fs.createReadStream('sample.txt');
var ws = fs.createWriteStream('copied.txt');

rs.pipe(ws);
```

默认情况下，当`Readable`流的数据读取完毕，`end`事件触发后，将自动关闭`Writable`流。如果我们不希望自动关闭`Writable`流，需要传入参数：

```js
readable.pipe(writable, { end: false });
```

##  1.4 url模块

遗留的API

* 加载模块

   ```js
  const url = require('url');
  ```

* 使用方法：

   ```js
   let myURL = url.parse('/test.html?id=11&age=22'); // 返回一个包含url各个部分的对象
   ```

* 新方法（必须传递一个**完整的**url）

   ```js
   // 直接提供一个完整的url
   let myURL = new URL('http://www.xxx.com/test.html?id=11&age=22');
   // 或
   // 提供两个参数，一是文件路径及参数部分，二是域名，总之，二者组合必须是完整的url
   let myURL = new URL('/test.html?id=11&age=22', 'http://www.xxx.com');
   
   // 得到的myURL是一个对象，包含url中的各个部分
   // 如果需要解析参数部分，则使用querystring模块，或使用URL的一个子对象searchParams中的get方法
   let age = myURL.searchParams.get('age')； // 22
   ```

## 1.5 querystring模块

* 处理查询字符串（请求参数）的模块

* 使用方法：

  * 加载模块：

    ```js
    const querystring = require('querystring');
    ```

  * 调用querystring模块中的方法
  
    ```JS
    // parse -- 将查询字符串解析成JS对象
    console.log(querystring.parse('id=1&name=zs&age=20')); 
    // { id: '1', name: 'zs', age: '20' }
    
    // stringify -- 将JS对象转成查询字符串
    console.log(querystring.stringify({ id: '1', name: 'zs', age: '20' }));
    // id=1&name=zs&age=20
    ```
  
# 二.搭建一个服务器

## 流程：

```JS
// 1. 加载http模块
const http = require('http');

// 2. 创建服务对象，一般命名为server
const server = http.createServer(); // create创建、server服务器

// 3. 给server对象注册请求（request）事件，监听浏览器的请求。只要有浏览器的请求，就会触发该事件
server.on('request', () => {
    // 只要有浏览器的请求，就会触发该事件
    console.log('我发现你的请求了，但是不想搭理你');
});

// 4. 设置端口，开启服务
server.listen(3000, () => {
    console.log('服务器启动了');
});
```



* res形参：
  * res：response，响应；它是一个对象；它包含了所有和响应相关的信息
  * 响应对象，服务器给浏览器返回的响应内容，可以通过该对象设置
  * res.write()  设置响应体（返回给浏览器的内容）的内容，可以多次调用，但是只调用write不会做出响应，发送响应要调用 end()
  *  res.end()    把响应报文（响应行、响应头、响应体）发送给浏览器
  * res.setHeader()  设置响应头，比如设置响应体的编码
  * res.statusCode 设置状态码
  * res.writeHead(200, {响应头})
* req形参：
  *  req：request，请求；它是一个对象；它包含了所有和请求相关的信息 
  * req.url ----  获取请求的url
  * req.method --- 获取请求的方式，值为GET或POST
  * req.headers -- 所有的请求头
## 处理post请求方式： 

思路：创建连接方式与之前的思路是一样的，区别在于服务器接收数据的时候：

```js
// 有请求过来，就会触发这个事件。请求包含GET和POST两种请求
server.on('request', (req, res) => {
    // 当有数据提交给服务器之后。
    // 服务器接收提交过来的数据；并且保存（json文件中，或者数据库中）
    /**
     * 1. 创建一个空字符串，用于保存提交过来的数据
     * 2. 给req注册data事件，只要有数据提交过来，就会触发；用于接收提交过来的数据；数据比较大的话，是分块接收的
     * 3. 给req注册end事件，当完全接收了提交过来的数据，就会触发
     */
    let str = ''; // 定义一个用于保存数据的空字符串
    req.on('data', (chunk) => {
        // chunk -- 块
        str += chunk;
    });
    req.on('end', () => {
        console.log(str); // id=1&name=zs&age=43
    });
});
```

## 处理静态资源：

因为在html的页面中存在着多样的数据类型，我们在处理的时候需要对多种类型的数据进行设置`Content-Type`

举例：

````JS
response.setHeader('Content-Type', 'text/css');
````

可能存在的类型：

* .html：text/html
* .css：text/css
* .js：application/javascript
* .jpg：image/jpg

那么问题来了 ，这些类型我们不可能一个一个的设置，引入第三方模块：

```js
 const mime = require('mime'); // mime的作用，可以根据文件名生成合理的类型
 let type = mime.getType(req.url);
 res.setHeader('Content-Type', type);
```

## 第三方模块

在"楼上"处理静态资源处，提到了第三方模块，那究竟什么是第三方模块呢，又能做什么呢？

说白了：

* 使用第三方模块，多数都是为了简化开发，将复杂的代码封装后，形成的模块

安装方式：

* npm（node package manager）node包管理器

* 全局安装：

  * ```bash
    # 安装语法
    npm install -g 模块名
    # 新版Mac系统，如果提示权限不足，可以使用 sudo su npm install -g 模块名
    ```

* 局部（本地）安装

  * ```bash
    与全局安装语法一致，将-g取消即可，
    # 安装语法
    npm install 模块名
    ```

### 比较有用的第三方模块

```bash
npm install mime        // mime的作用，可以根据文件名生成合理的类型
npm install -g nodemon  //用于自动启动服务的工具
npm install -g express  //基于Node.js快速开发的web框架
```

# 三.express框架

##  介绍



什么是express:Express 是一个基于 Node.js 平台，快速、开放、极简的 **web 开发框架**。

## 创建一个服务器的基本流程：

文字描述：

* 加载 express 模块
* 创建 express 服务器
* 开启服务器
* 监听浏览器请求并进行处理

代码描述：

```JS
// 使用express 搭建web服务器
// 1) 加载 express 模块
const express = require('express');

// 2) 创建 express 服务器
const app = express();

// 3) 开启服务器
app.listen(4000, () => console.log('express服务器开始工作了'));

// 4) 监听浏览器请求并进行处理

app.get('GET请求的地址', 处理函数);
或
app.post('POST请求的地址', 处理函数);
```

## express新增的API

 1) express框架封装了一些额外的API（例如:send），可以让我们更方便的构造Web服务器

​	1.1 ）使用send方法响应数据的话，会自动设置content-type。但有时候会错误设置

​	1.2 ）注意send不能直接响应数字，需要加引号，否则会将数字当做响应状态码处理

 2) 浏览器请求的每一个url地址都会由一个独立方法接收并处理，没有了 if ... else if ... else 这样的分支，程序结构
     更加清晰

3）sendFile(文件路径); -- 功能是读取文件，并将读取到的结果响应给浏览器。它的参数必须是绝对路径。

## 中间件

介绍与使用:

* 中间件就是一个函数，中间件函数要当做 `app.use();` 的参数，这样来使用
* 中间件函数中有三个基本参数， req、res、next
* req就是请求相关的对象，它和后面用到的req对象是一个对象
* res就是响应相关的对象，它和后面用到的res对象也是一个对象
* next：它是一个函数，调用它将会跳出当前的中间件函数，执行后续代码；如果不调用next，则会在当前中间件卡住

中间件处理静态资源：

​	

