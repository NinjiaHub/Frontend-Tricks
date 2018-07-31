# 在 Chrome 中使用本地文件创建 Web Worker

* [解决办法](#ways)
	* [1、local-web-server](#local-web-server)
	* [2、--allow-file-access-from-files](#cli-arg)
	* [3、使用 ObjectURL](#object-url)
	* [4、FireFox?](#firefox)
* [参考链接](#ralated-links)
* [Author Info](#author)

最近在尝试使用 Web Worker 的最开始遇到了一个问题：Chrome 不能运行本地文件中创建 Web Worker 的代码，即 Chrome 中不能使用通过 `file://` 协议加载的 JavaScript 文件来创建一个 Web Worker，只能通过 `http://` 或者 `https://` 加载要创建 Web Worker 的文件。不能通过本地文件创建 Web Worker 实例的原因是 Worker 受浏览器同源策略([Same Origin Policy](http://en.wikipedia.org/wiki/Same_origin_policy))限制，但是同源策略在一些边缘条件中的规定不是太明确，维基百科提示如下：

> The behavior of same-origin checks and related mechanisms is not well-defined in a number of corner cases, such as for protocols that do not have a clearly defined host name or port associated with their URLs (file:, data:, etc.).

`file://` 协议因为没有明确地定义 host 名字和端口号，所以在浏览器的同源策略中被判定为不同源，所以浏览器会拒绝使用 `file://` 协议加载文件来创建 Web Worker 实例。

虽然在代码上线之后都会使用 `http://` 或者 `https://` 来加载文件，但是在本地写测试代码的时候可能并不会起一个 http 服务器(这里不考虑 API 接口跨域问题)，那有没有本法让 Chrome 通过运行本地加载的 JavaScript 文件来创建 Web Worker 呢？答案当然是有，要么我写这篇文章干嘛呢：

```emoj
（╯’ - ‘)╯︵ ┻━┻     （掀桌子） 

┬─┬ ノ( ‘ - ‘ノ)       {摆好摆好） 

(╯°Д°)╯︵ ┻━┻        (再他妈的掀一次）
```

尝试使用 Web Worker 的 Demo 如下：

```html
// index.html
<!doctype html>
<html>
  <head>
    <title>Web Worker</title>
  </head>
  <body>
    <p>web worker</p>
    <script src="./main.js"></script>
  </body>
</html>
```

```javascript
// main.js
let w = new Worker('./worker.js')
w.onmessage = e => {
  console.log(e.data)
}
w.postMessage('Hello Worker')
```

```javascript
// worker.js
onmessage = function foo (m) {
  console.log(m.data)
  postMessage('get it.')
}
```
当打开控制台查看时，发现如下报错信息：

```javascript
Uncaught DOMException: Failed to construct 'Worker': Script at
'file:///Users/taoxin/workspace/alpha/web-worker/worker.js'
cannot be accessed from origin 'null'.
at file:///Users/taoxin/workspace/alpha/web-worker/main.js:1:9
```

其实这里的报错信息与开头说的意思一样：不能通过“源”为 `null` 的 `main.js` 文件来获取 `file:///Users/taoxin/workspace/alpha/web-worker/worker.js` 文件，即 Web Worker 不能通过使用 `file://` 协议加载的 JS 文件来创建。

下面我们来看一下解决这个问题的办法：

## <span id="ways">解决办法</span>

### <span id="local-web-server">1、local-web-server</span>

只从有了 [NodeJS](https://nodejs.org/en/) 和 [npm](https://npmjs.com)，这个世界上就没有什么事情是用一个 npm 管理的插件解决不了的，如果有，那就用两个插件。

今天这个问题，也可以使用一个 npm 管理的插件来解决。[local-web-server](https://www.npmjs.com/package/local-web-server) 这个插件可以全局安装之后作为一个全局命令来用：

```cli
> npm i -g local-web-server // npm version: 5.6.0
```

该插件的主要功能就一个：在本地起一个 Web 服务器，用来访问本机的文件；但是其本身远没有这么简单，官网罗列的 features 如下：

* Modular, extensible and easy to personalise. Create, share and consume only plugins which match your requirements.
* Powerful, extensible command-line interface (add your own commands and options)
* HTTP, HTTPS and HTTP2 support (HTTP2 requires node v8.4.0 or above)
* URL Rewriting to local or remote destinations
* Single Page Application support
* Response mocking
* Configurable access log
* Route blacklisting
* HTTP Conditional and Range request support
* Gzip response compression, HTTP Basic Authentication and much more

最简单的使用方法如下：

```cli
> ws
> Serving at http://taoxindeMacBook-Pro.local:8000, http://127.0.0.1:8000, http://192.168.2.68:8000
```

该命令默认会以当前目录作为跟目录起一个 Web 服务器，还支持配置协议，并且会将 URL 输出。如此一来就可以通过 http 协议访问我们的 Demo 页面了，也就解决了上面 Chrome 不支持使用 `file://` 协议加载的 JavaScript 文件启动 Web Worker 的问题。

**eg：在 stackoverflow 上有很多诸如使用 Ruby、PHP 等启动 Web Server 的例子，原理与该方法相同，都是更改了加载文件的协议。**

### <span id="cli-arg">2、--allow-file-access-from-files</span>

可以通过命令启动 Chrome，在启动时加上 `--allow-file-access-from-files` 参数，以该模式启动的 Chrome 允许使用以 `file://` 协议加载的 js 文件创建 Web Worker，命令行启动 Chrome 的命令如下：

```
// Windows
> "C:\PathTo\Chrome.exe" --allow-file-access-from-files

// Mac OS
> /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --allow-file-access-from-files
```

**注：只有重新启动 Chrome 才有效；如果是已经打开的 Chrome 浏览器，使用上面的命令只会打开一个新的窗口，而使用 `file://` 协议加载文件启动 Worker 仍然会失败报错。**

### <span id="object-url">3、使用 ObjectURL</span>

这种方法使用 objectURL 作为文件的 url 来“欺骗”浏览器，让浏览器以为被加载的文件不是本地文件；其实这种方法只是修改了标识文件的方式，使用 objectURL 代替 `file://` 协议的 url 来标识要加载的文件。实现代码如下：

```javascript
// main.js
function worker_function () {
  onmessage = function foo (m) {
    console.log(m.data)
    postMessage('get it.')
  }
}

let w = new Worker(URL.createObjectURL(new Blob(["(" + worker_function.toString() + ")()"], { type: 'text/javascript' })))
w.onmessage = e => {
  console.log(e.data)
}
w.postMessage('Hello Worker')
```

上面的实现方式，先将 `worker_function` 转换为字符串，作为 `Blob()` 的构造函数的参数；然后将构建出来的 Blob 对象的实例作为 `window.URL.createObjectURL()` 的方法，创建一个标识 `worker_function` 实现内容字符串文件的 ObjectURL；最后使用该 ObjectURL 表示 JS 文件的引用来创建一个新的 Worker 对象。生成的 Blob 实例包含 `worker_function` 函数的所有实现，并且该函数被封装成了一个 IIFE(立即执行函数表达式)，被下载到 worker 中时会立即执行，在当前 worker 实例上注册一个 `onmessage` 回调。

上面的方式将 Web Worker 要执行的内容，转换存储在 Blob 文件，并使用 ObjectURL 来标识该 Blob 文件并使浏览器可以引用，从而避开浏览器的同源策略以达到使用本地文件创建 Web Worker 的目的。

不了解 Web 端文件操作的小伙伴请阅读下面的 [File Api](../HTML/file-api.md)。

**注：关于 Web 端文件操作相关的内容，请查阅我写的另外一篇文章：[File API](../HTML/file-api.md)。**

### <span id="firefox">4、FireFox?</span>

FireFox 浏览器目前没有该限制，可以直接在浏览器地址栏输入 url 后通过 `file://` 协议打开，并且可以顺利创建 Web Worker 实例。

FireFox 版本：`v58.0.2 `。

**注：支持 Web Worker 的 IE10+ 也可以，但是好像 stackoverflow 上有人不支持使用更换浏览器来实现这个目的，可能是因为更换浏览器不够 Geek，而且更换浏览器调试的体验不好。**

## <span id="related-links">参考链接 🔗</span>

* [The Basics of Web Workers](https://www.html5rocks.com/en/tutorials/workers/basics/#toc-introduction-jsthreading)
* [HTML5 Web Workers work in Firefox 4, but not in Chrome 12.0.742.122](https://stackoverflow.com/questions/6811398/html5-web-workers-work-in-firefox-4-but-not-in-chrome-12-0-742-122/6823683#6823683)
* [Chrome can't load web worker](https://stackoverflow.com/questions/21408510/chrome-cant-load-web-worker)
* [Web worker settings for chrome](https://stackoverflow.com/questions/21751775/web-worker-settings-for-chrome)
* [The Basics of Web Workers](https://www.html5rocks.com/en/tutorials/workers/basics/)

## <span id="author">Author Info 🦁</span>

* [GitHub](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>