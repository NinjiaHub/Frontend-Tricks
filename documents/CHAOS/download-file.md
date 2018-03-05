# 前端下载文件遇到的一些问题

* [前端下载文件的方式](#ways)
	* [使用 `window.open` 方法下载](#window-open)
	* [使用 a 标签下载](#a-tag)
	* [使用虚拟 a 标签下载](#virtual-a-tag)
* [参考链接](#links)
* [作者信息](#author)

在前段时间的开发中，有一个下载分片视频的需求，在做这个需求的过程中遇到了几个坑，这里来记录一下。

在这个需求中，同一个视频被分成了一到多个视频片段，如果有一个视频片段在点击下载按钮时直接下载；如果有多个视频片段则需要选择后点击一次下载按钮时完成对所有视频片段的下载，即不能将视频的下载链接写成 `<a href="xx" download>video href</a>` 这种形式逐个点击后下载。接下来，我们就来看一看前端中到底有多少种方式可以用来下载文件。

## <span id="ways">前端下载文件的方式</span>

### <span id="a-tag">1、使用 a 标签下载<span>

通常情况下我们使用 `a` 标签进行页面之间的跳转，但是 `a` 标签还有一个隐藏功能，那就是 **文件下载**。默认情况下，如果 `a` 标签的 `href` 属性的值是一个指向浏览器可以打开的 MIME 中的一种时，浏览器会加载该 URI 指向的文件的并展示出来；如果 URI 指向的文件并不能被浏览器展现时，则会被下载到本地。

而在 HTML5 中，`a` 标签新增来一个 `download` 属性，如果一个 `a` 标签在使用时添加了 `download` 属性的话，在点击时，浏览器会将 `href` 指向的文件下载到本地。如果 `download` 属性设置了值的话，该属性的值会作为下载到本地文件的名字。

但是，如果 `a` 标签的 `href` 是指向的一个接口，通过接口下载文件的话，`download` 属性即使设置了值，也不能更改下载到本地的文件的名字；同样，下载 OSS 上的文件，也不能通过设置 `download` 属性来改变下载到本地文件的名字。所以，如果使用 `a` 标签下载文件并且想修改下载到本地的文件名时，需要服务端配合修改 HTTP 的协议头 `Content-Disposition`。

### <span id="virtual-a-tag">2、使用虚拟 a 标签下载</span>

由于产品形态或者其他原因，有时候产品不允许在页面中出现 **实体的 a 标签**，这里所谓的实体的 a 标签是指真实存在于 dom 树中的 a 标签，这种时候可以借助存在于内存中的 **虚拟 a 标签** 来实现下载的功能。

在现代浏览器中，绝大部份浏览器都支持在内存中创建 HTML 标签，并可以在虚拟标签上触发该标签所允许的事件，例如 `click` 等事件。

使用虚拟 a 标签实现下载功能的代码如下：

```javascript
function download () {
	let a = document.createElement('a')
	a.href = 'uri/to/file'
	a.download = 'filename'
	// a.style.display = 'none' # 设置 a 标签不可见
	// document.body.append(a) # 添加到 dom 树中
	a.click()
	// document.body.remove(a) # 从 dom 树中移除
}

download()
```

上面的代码现在内存中创建了一个 a 标签，设置 `href` 和 `download` 属性之后，添加到 dom 树中；然后通过 `a.click()` 触发 a 标签的点击事件，由于 a 标签设置了 `download` 属性，所以浏览器会解析 URI 并将文件下载到本地；最后通过 HTML 向外暴露的 `remove` 方法将 a 标签从 dom 树中移除。

其实即使不将 a 标签添加到 dom 树中，也可以通过 `a.click()` 触发 a 标签的点击事件实现下载；所以，在使用的过程中，可以省略上面代码中被注释的部分，少改变一次 dom 树，毕竟任何的操作都是有消耗的。

### <span id="window-open">3、使用 `window.open` 方法下载</span>

同没有添加 `download` 属性的 a 标签一样，可以通过 `window.open` 方法下载部分文件，这些可以下载的文件是不能被浏览器展现出来的文件；对于可以被浏览器解析并展现的文件，`windown.open` 方法只会在新打开的窗口渲染文件内容，并不会下载到本地。

除了以上的问题外，使用 `window.open` 还会出现以下几个问题：

* `window.open` 方法还会先打开一个空白的页面，然后在新打开的页面中实现下载，体验不是很好；
* 新打开的页面不会自己关闭，需要开发者自己手动关闭新打开的页面，这里就会出现一个问题：如果关闭新窗口的代码执行的太早，下载的请求链接没有传输完成时，则该下载会被中断。而且开发者没有办法知道下载请求链接是否完成，所以要么不关闭新打开的窗口，由用户关闭；或者设置一个比较大的定时器，由定时器来关闭新打开的空白页面。
* 对于异步获取的下载 **url**，通过 `window.open` 打开新页面时会被浏览器拦截，即该页面不会被打开，会被浏览器折叠在地址栏的最右边，需要用户手动信任后才能下载；

所以不建议通过 `window.open` 方法下载文件。

**注：**对于使用 `window.open` 打开异步获取的 url 被浏览器拦截的问题，可以通过先创建新的空白页面，然后设置 url 的方式打开：

```javascript
let w = window.open()
let url = (async function () {
	return await getUrlAsync()
})()
w.location = url
```

## <span id="links">参考链接</span> 🖇

* [How to set name of file downloaded from browser?
](https://stackoverflow.com/questions/3102226/how-to-set-name-of-file-downloaded-from-browser "How to set name of file downloaded from browser?
")
* [FileSaver](https://github.com/eligrey/FileSaver.js)
* [JavaScript 实现文件下载并重命名](https://www.jianshu.com/p/6545015017c4)

## <span id="author">作者信息 🐻</span>

* [GitHub](hppts://github.com/Tao-Quixote)
* Email：<web.taox@gmail.com>