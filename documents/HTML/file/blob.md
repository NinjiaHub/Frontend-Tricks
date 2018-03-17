# BLOB(Binary Large Object)

* [使用方式](#how)
* [使用场景](#usages)
* [参考链接](#links)
* [作者信息](#author)

> In computers, a BLOB (binary large object), pronounced **BLAHB** and sometimes spelled in all lower case, is a large file, typically an image or sound file, that must be handled (for example, uploaded, downloaded, or stored in a database) in a special way because of its size. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; -- [http://searchsqlserver.techtarget.com/](http://searchsqlserver.techtarget.com/definition/BLOB)

中国的小伙伴要注意了，BLOB 的发音为 **BLAHB**。由于其体积一般较大，所以在传输过程中需要使用特殊的上传方式，需要考虑断点续传、存储空间等问题。

在前端开发中，在个别情况下会使用到 [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)，例如 [OffscreenCanvas.toBlob()](https://developer.mozilla.org/en-US/docs/Web/API/OffscreenCanvas/toBlob) 或者 [在 Chrome 中使用本地文件创建 Web Worker](https://github.com/NinjiaHub/Frontend-Tricks/blob/master/documents/JS/create-worker-from-local-file-in-chrome.md#user-content-3使用-objecturl)；Blob 在前端的使用场景比较少，在客户端，例如 database 或者 Git 中使用的比较多，database 中多会用 blob 来存储多媒体文件的二进制文件，而 Git 中使用 Blob 存储 **签名(即 Git 写在 Blob 文件开头的信息)** 后的代码文件。

下面是 [webopedia](https://www.webopedia.com) 对 Blob 格式文件的定义：

> Short for binary large object, a collection of binary data stored as a single entity in a database management systems (DBMS). BLOBs are used primarily to hold multimedia objects such as images, videos, and sound, though they can also be used to store programs or even fragments of code. Not all DBMSs support BLOBs. &nbsp; -- webopedia

维基百科上将 BLOB 翻译为 “二进制大型对象”；从上面对于 BLOB 的定义可以看出，BLOB 主要用于将一系列相关多媒体文件的二进制数据存储在一个完整的文件中，且该文件正常情况下是不可变的。除了可以存储多媒体文件，还可用于存储程序文件(例如 Git 在执行 `git add` 命令时，就会将文件签名后存放在一个 BLOB 文件中)，甚至是可执行的二进制代码片段。

BLOB 的主要作用是存储多媒体文件的二进制数据，重点在于存储。在前端领域，BLOB 多用于存储单个多媒体文件的数据或者 JavaScript 代码，从而实现传输或者作为 hack 的方式展示、执行源内容。为了方便使用 BLOB 格式的文件，前端领域的 [File](https://developer.mozilla.org/en-US/docs/Web/API/File) 和 [FileList](https://developer.mozilla.org/en-US/docs/Web/API/FileList) 都继承了 BLOB 接口。

BLOB 就像是一个透明的装二进制数据容器的盒子，我们可以通过构造函数往里面塞各种二进制的数据。因为其本身不允许修改的特性，所以如果要修改一个 BLOB 实例，只能通过构造器构造另外一个新的 BLOB 实例，不能直接修改已生成 BLOB 实例的内容。

## <span id="how">使用方式</span>

详情可见 MDN 上关于 `Blob()` 构造函数的文档：

* [英文版](https://developer.mozilla.org/en-US/docs/Web/API/Blob/Blob)
* [中文版](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/Blob)

**注：`Blob()` 构造函数接受 DomString 类型的参数。DomString 是指 UTF-16 编码的字符串，而 JavaScript 几乎是在一开始就使用类似的字符串，所以 JavaScript 中直接将 DomString 映射为普通的字符串。**

## <span id="usages">使用场景</span>

### <span id="u-canvas">HTMLCanvasElement.toBlob()</span>

在浏览器中可以通过 **canvas** 元素对图片进行裁剪、绘制、打码等多种操作，操作的结果如何保存呢？这时候就用到了 BLOB。canvas 元素对外暴露了 `toBlob()` 接口，可以通过该接口将 cavnas 元素内绘制的内容生成一个 BLOB 文件，有了这个生成的 BLOB 实例，就可以进行展示、下载等操作。

```javascript
// 创建 canvas 元素并绘制一条线段
let canvas = document.createElement('canvas')
canvas.width = 200
canvas.height = 200

let ctx = canvas.getContext('2d')
ctx.moveTo(0, 0)
ctx.lineTo(cavnas.width, canvas.height)
ctx.stroke()
```

**1、将 canvas 内容在 img 标签中渲染**

```javascript
// 渲染到页面中
function render (blob) {
	let img = document.createElement('img'),
		url = URL.createObjectURL(blob)
		
	img.onload = function () {
		URL.revokeObjectURL(url)
	}
	
	document.body.appendChild(img)
}

canvas.toBlob(render, 'image/png')
```

**2、作为图片下载到本地**

```javascript
function download (blob) {
	let a = document.createElement('a'),
		url = URL.createObjectURL(blob)
	a.href = url
	a.download = 'filename'
	
	a.click()
}

canvas.toBlob(download, 'image/png')
```

### 解决 Web Worker 不能通过 `file://` 协议加载 JS 文件的问题

这个问题在另外一篇文章 [在 Chrome 中使用本地文件创建 Web Worker](https://github.com/NinjiaHub/Frontend-Tricks/blob/master/documents/JS/create-worker-from-local-file-in-chrome.md) 有详细的描述，请移步 [使用 ObjectURL](https://github.com/NinjiaHub/Frontend-Tricks/blob/master/documents/JS/create-worker-from-local-file-in-chrome.md#3%E4%BD%BF%E7%94%A8-objecturl) 看。

## <span id="links">参考链接</span> 🖇

* [http://searchsqlserver.techtarget.com/](http://searchsqlserver.techtarget.com/definition/BLOB)
* [BLOB](https://www.webopedia.com/TERM/B/BLOB.html)
* [MDN BLOB](https://developer.mozilla.org/en-US/docs/Web/API/Blob)
* [DOMString](https://developer.mozilla.org/en-US/docs/Web/API/DOMString)

## <span id="author">作者信息 🐮</span>

* [GitHub](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>
