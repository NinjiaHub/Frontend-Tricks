# rel=noopener

* [tabnabbing](#tabnabbing)
* [解决方式](#resolve-p)
	* [a 标签不使用 `target="_blank"` 属性](#no-blank)
	* [使用 `rel=noreferrer` 属性](#noreferrer)
	* [使用 `rel=noopener` 属性](#noopener)
	* [`window.opener = null`](#opener-null)
	* [iframe](#inject-iframe)
	* [解决该问题的库 - blankshield](#blankshield)
* [性能问题](#performance)
* [参考链接](#links)
* [作者信息](#author)

## <span id="tabnabbing">tabnabbing</span>

[tabnabbing](https://en.wikipedia.org/wiki/Tabnabbing) 是利用假冒页面修改一个打开 tab 的内容、用户对知名网站的信任心理实现的网络诱骗。该诱骗是利用 `<a href="xx" target="_blank">anchor</a>` 或者 `window.open` 打开的子页面可以通过 `window.opener.location.assgin()` 修改父页面所在 tab 的 URL 的特性来实现的。当用户通过点击链接在新 tab 打开一个新页面时，如果该页面是一个恶意页面，则该页面可以通过 `window.opener.location.assgin()` 方式在父 tab 中打开一个假冒知名网站的登陆页面，诱导用户填写自己在该网站的用户名和密码，从而实现网络诱骗。

对于同源的子页面，子页面可以通过 `window.opener` 获取父页面的所有属性，包括 location、document 等；而非同源的子页面只能访问父页面的 `location` 属性，不能获取 `document` 等其他属性。

## <span id="resolve-p">解决方式</span>

从上面的分析可知，tabnabbing 是通过访问父页面的 `window.opener` 来实现网络诱骗的，所以解决办法就从限制子页面的 `window.opener` 下手：

### <span id="no-blank">1、a 标签不使用 `target="_blank"` 属性</span>

通过 a 标签进行跳转时，如果 a 标签没有使用 `target="_blank"` 属性，则新的页面会在当前 tab 中使用新页面替换当前页面，旧的的 window 对象被替换了，所以 `window.opener` 为 `null`。

### <span id="noreferrer">2、使用 `rel=noreferrer` 属性</span>

可以在任何使用 `target="_blank"` 属性的链接上使用 `rel="noreferrer"` 属性，使用该属性打开的新页面中，`window.opener` 的值为 null。但是使用该属性有两个缺点：第一是使用该属性时 HTTP 请求头中的 referrer 不会传递，有的网站服务器会验证跳转来源，如果 referrer 为空或者不传递时，服务器会禁止响应；第二是 IE 和 Safari 浏览器不支持该属性。

### <span id="noopener">3、使用 `rel=noopener` 属性</span>

同 `rel=noreferrer`，在使用 a 标签打开新窗口时，也可以使用 `rel=noopener` 属性，这样在子窗口中 `window.opener` 的值为 null，从而可以避免 tabnabbing 的问题。而且使用该属性时 HTTP 请求头中的 referrer 会照常传递；缺点是不分浏览器不支持该属性，兼容性查询传送门：[caniuse](http://caniuse.com/#feat=rel-noopener)。

### <span id="opener-null">4、`window.opener = null`</span>

可以给跳转链接添加监听事件，当用户点击跳转链接时，在事件回调中通过 `e.preventDefault()` 禁止浏览器的默认跳转行为，然后通过调用如下代码实现跳转并置空 `window.opener`：

```javascript
let w = window.open()
w.opener = null
w.location.href = url
```

上面的代码通过手动置空 `window.opener` 的值来防止子窗口中可以访问父窗口的 window 对象。

**缺点：**Safari 浏览器不支持该方法。在 Safari 浏览器中，如果子窗口和父窗口不同源时，浏览器会阻止在子窗口中修改 `window.opener`；但是允许在自窗口中访问父窗口的 `window.opener.location`，所以该方法在 Safari 浏览器中是无效的。

### <span id="inject-iframe">5、iframe</span>

这是兼容性最好的办法。该方法的思路是先通过 JavaScript 在内存中创建一个新的 iframe；然后将 iframe 嵌入到 dom 树中，通过该 iframe 在新窗口中打开新页面；最后，将 iframe 从 dom 树中移除。

iframe 得到了绝大多数浏览器的支持，所以兼容性最好。在打开新页面后，将 iframe 从浏览器中移除之后，iframe 中的 dom 及 window 对象被销毁，所以子页面中 `window.opener` 的值为 null，无法再通过 `window.opener` 修改父页面实施 tabnabbing。而且这里创建的 iframe 大多会添加 `display: none;` 的样式，即使子页面可以访问 iframe，修改的内容也是 iframe 中的内容，并不户被用户看到。

### <span id="blankshield">解决该问题的库 - blankshield</span>

[blankshield](https://github.com/danielstjules/blankshield)：该插件使用 [内嵌 iframe 打开新窗口](#inject-iframe) 的办法实现在新窗口中打开子页面，规避了 tabnabbing 问题；并且对 IE、Safari 等浏览器做了兼融。如果自己做的产品对浏览器兼容有要求的话，推荐使用该插件。

## <span id="performance">性能问题</span>

除了安全方面的问题，使用 `target="_blank"`会引发性能方面的问题：

我们知道浏览器中 JavaScript 和页面渲染是通过一个线程来处理的，即平时我们所说的“单线程”(这里的“单线程”是指处理 JavaScript 和 渲染的线程，浏览器本身是多线程的)；如果一个任务(task)长时间占用这个主线程时，其他任务就得等待该任务执行完之后再执行，而且页面会卡住，会重新渲染。

而通过使用 `target="_blank"` 或者 `window.open()` 打开的的新页面(子窗口) A 和老页面(父窗口) B 公用同一个主线程，所以页面 A 和页面 B 中所有的 JS 任务和页面渲染都要在同一个线程中进行，如果其中一个页面的任务比较多时，另外一个的任务就被挂起；表现出来就是页面有卡顿。如果主线程被一个页面中的任务阻塞，另外一个页面中的文字都不能被选中。

所以如果两个页面是通过 `target="_blank"` 或者 `window.open()` 打开的，而且的任务都比较密集的话，虽然浏览器会挂起非活跃状态 tab 中的一部分任务，但是另外一个页面中的部分任务还是会被阻塞出现页面卡顿的情况，这种情况可以通过这个 [Demo](https://jakearchibald.com/2016/performance-benefits-of-rel-noopener/#demo) 查看。

虽然可以通过 [内嵌 iframe](#inject-iframe) 的方式解决 tabnabbing 的问题，但是能否解决性能问题是个未知数：如果内嵌的 iframe 和 宿主页面共享同一个主线程的话，则通过 iframe 在新 tab 中打开的页面应该也与 iframe 的宿主页面共享一个主线程，这种方法并不能解决性能问题；而如果内嵌的 iframe 与宿主页面不共享同一个主线程的话，则不会出现上面说的性能问题。内嵌 iframe 与宿主页面是否共享主线程暂时不知，如果有知道的小伙伴可以告诉我。

所以，在链接跳转时，还是推荐使用 `<a href="">anchor</a>` 这种加载新页面替换当前页面的方式跳转，不推荐使用 `target="_blank"` 属性。

## <span id="links">参考链接</span> 🖇

* [网站使用 rel="noopener" 打开外部锚](https://developers.google.com/web/tools/lighthouse/audits/noopener?hl=zh-cn)
* [The performance benefits of rel=noopener](https://jakearchibald.com/2016/performance-benefits-of-rel-noopener/)
* [About rel=noopener](https://mathiasbynens.github.io/rel-noopener/)
* [danielstjules / blankshield](https://github.com/danielstjules/blankshield#solutions)

## <span id="author">作者信息 🐯</span>

* [GitHub](hppts://github.com/Tao-Quixote)
* Email：<web.taox@gmail.com>