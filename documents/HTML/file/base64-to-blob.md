# Base64 to Blob

* [Data URLs](#data-urls)
	* [Data URLs 的组成](#data-url-parts)
* [base64 编/解码](#base64-encode-decode)
* [Blob](#blob)
* [转换](#steps)
	* [step1: decode base64](#decode-base64)
	* [step2: 生成 ArrayBuffer 数组](#gen-array-buffer)
	* [step3: 生成 BLOB](#gen-blob)
	* [step4: 生成 object URL](#gen-object-url)
* [NPM 包 - b64-to-blob](#npm-package)
* [参考链接](#links)
* [作者信息](#author)

**起因**：Dom 元素的背景图片是使用 [canvas]() 绘制出来的，而使用 canvas 导出的图片内容的 base64 作为背景的话特别长；而 `window.URL.createObjectURL()` 可以生成 blob 文件的 ObjectURL，可直接用作 CSS 中的 `background-image` 属性的值。

**问题来了**：如何将 base64 格式的图片转换为 blob 文件呢？

**注：canvas 可以直接导出 BLOB 文件然后使用 `window.URL.createObjectURL()` 方法生成 ObjectURL 供 Dom 元素使用，但是这里不准备这么做（😄**

在进行转换之前，先来了解一下 base64 和 Blob 相关的知识点。

## <span id="data-urls">Data URLs</span>

先通过下面的代码生成一个表示 canvas 内容的 base64 字符串：

```javascript
// 生成 & 设置 canvas
let canvas = document.createElement('canvas')
canvas.width = 100
canvas.height = 100

let ctx = canvas.getContext('2d')
// 填充底色
ctx.fillStyle = '#fff'
ctx.fillRect(0, 0, 100, 100)
// 绘制一条对角线
ctx.strokeStyle = '#000'
ctx.moveTo(0, 0)
ctx.lineTo(100, 100)
ctx.stroke()

// 导出 data URL
let r = canvas.toDataURL()
```
生成的表示 canvas 内容的 **Data URL** (部分，没有完全截取)：

```data-url
"data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAABkCAYAAABw4pVUAAACIklEQVR4Xu3bW3KEMAwFUc/+F51Uk"
```

从 base64 字符串的组成可以看出，其实 base64 格式表示的图片是采用的 **Data URLs** 这种格式来定位资源的，像 **URI** 一样用来标识资源的位置；不同的是 **URI** 只是标识资源的位置，而 **Data URLs** 这种格式包含资源本身，且资源内容使用 **base64** 格式而不是传统的二进制数据来表示。

**Data URLs** 的主要作用并不是用来标识资源的位置，而是允许内容的创造者在文档中内联嵌入一些 **体积标较小** 的文件。在开发中不应该使用 Data URLs 在文档中嵌入体积比较大的文件，因为 Data URLs 多用于嵌入图片文件，而图片文件是被编码为 base64 格式的字符串；在普通文件从二进制转换为 base64 格式的过程中，体积会增大约 `1/3`，这是[编码的规则](https://github.com/NinjiaHub/Frontend-Tricks/blob/master/documents/CHAOS/covert-data-2-base64.md)造成的。因此，在使用中，切忌使用 Data URLs 在 HTML 中嵌入体积比较大的文件。

### <span id="data-url-parts">Data URLs 的组成</span>

Data URLs 由以下 4 个部分组成：

```
data:[<mediaType>][;base64],<data>
```

* **data:** scheme 标识 Data URL 的开始；
* **meidaType** 是一个表示 MIME 类型的字符串；如果该参数省略，则默认为 `text/plain;charset=US-ASCII`；
* **base64** 如果 data 的内容是编码为 base64 格式的字符串的话，则需要填写该参数；如果为普通字符该部分可以省略；
* **data** 资源内容（可以是普通字符串或编码为 base64 格式的字符串）

**注：** 不是所有浏览器都支持任意长度的 **Data URLs**，例如：FireFox 支持任意长度的 Data URLs，而 `Opera 11` 仅支持长度为 65535 个字符长度的 Data URLs。

## <span id="base64-encode-decode">base64 编/解码</span>

**注：这里的编解码不涉及具体规则，详情请移步 [base64 编码规则](https://github.com/NinjiaHub/Frontend-Tricks/blob/master/documents/CHAOS/covert-data-2-base64.md)。**

在 web 端，为了将二进制数据和 base64 格式的数据进行相互转换，浏览器实现了如下两个方法：

* **[WindowOrWorkerGlobalScope.btoa()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/btoa)**：该方法用于将字符串转换为 base64 格式，只接受 UTF-8 编码的字符串，传入 Unicode 字符作为参数会抛出异常。
* **[WindowOrWorkerGlobalScope.atob()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/atob)**：该方法用于将 base64 格式的字符串转换为 UTF-8 编码的普通字符串；如果传入的字符串长度不是 4 的整数倍，或者不是合法的 base64 字符，该方法会抛出异常。

注意上面两个方法的 `作用域（scope）` 为 **WindowOrWorkerGlobalScope**，这表明这两个方法在 window 对象或者 Web Worker 的全局作用域中都是可用的。

## <span id="blob">Blob</span>

**注：BLOB 详情请移步 [Blob](https://github.com/NinjiaHub/Frontend-Tricks/blob/master/documents/HTML/file/blob.md)。**

Blob 构造函数如下：

```javascript
let blob = new Blob(array[, options])
```

`array` 为以下数据类型的数组：

* [ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
* [ArrayBufferView](https://developer.mozilla.org/en-US/docs/Web/API/ArrayBufferView)
* [blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)
* [DomString](https://developer.mozilla.org/en-US/docs/Web/API/DOMString)

在将 base64 字符串表示的内容转换为 blob 的实现中，我们需要的是第一种参数，即将 base64 字符串转换为包含 [ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) 的数组。

## <span id="steps">转换</span>

我们使用上面生成的 base64 字符串 `r`：

### <span id="decode-base64">step1: decode base64</span>

```javascript
// 截取 base64 部分：
let b64String = r.split(',')[0]
// decode
let byteCharacters = window.atob(b64String)
```

### <span id="gen-array-buffer">step2: 生成 ArrayBuffer 数组</span>

```javascript
let bytesNumbers = new Array(byteCharacters.length) // 生成字符串长度的数组
// 构造单个字符组成的数组
for (let i = 0; i < byteCharacters.length; i++) {
	bytesNumbers[i] = byteCharacters.charCodeAt(i)
}
// 调用 ArrayBuffer 构造函数
// 从 base64 字符串 decode 后的字符都是 UTF-8 编码的字符
// 所以使用 Uint8Array 构造 ArrayBuffer 最合适
let byteArray = new Uint8Array(byteNumbers)
```

### <span id="gen-blob">step3: 生成 BLOB</span>

```javascript
let blob = new Blob([byteArray], {type: contentType})
```

### <span id="gen-object-url">step4: 生成 object URL</span>

到 **step3**，base64 转 blob 就算是完成了，但是要想在页面中使用刚刚生成的 blob，还得借助于 `window.URL.createObjectURL()` 函数：

```javascript
let ourl = URL.createObjectURL(blob)
```

这样，上面从 base64 转换的 blob 就可以在 HTML 中使用了。

## <span id="npm-package">NPM 包 - b64-to-blob</span>

在 NodeJS 和 npm 这么流行的现在，解决这种问题怎么少得了 NPM 包呢？文档请戳👉 [b64-to-blob](https://www.npmjs.com/package/b64-to-blob)。

## <span id="links">参考链接 🔗</span>

* [Creating a Blob from a base64 string in JavaScript](https://stackoverflow.com/questions/16245767/creating-a-blob-from-a-base64-string-in-javascript)
* [Data URLs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Data_URIs)
* [Base64 encoding and decoding](https://developer.mozilla.org/en-US/docs/Web/API/WindowBase64/Base64_encoding_and_decoding)
* [WindowOrWorkerGlobalScope.atob()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/atob)
* [WindowOrWorkerGlobalScope.btoa()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/btoa)

## <span id="author">作者信息 🐷</span>

* [GitHub](hppts://github.com/Tao-Quixote)
* Email：<web.taox@gmail.com>