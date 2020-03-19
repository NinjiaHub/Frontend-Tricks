# Resource Timing API

通常，我们认为所有的网络请求都是资源。在网络请求的过程中，资源都有明显的生命周期；Resource Timing API定义了网络请求的生命周期。

同时，浏览器的 Network Panel 中也使用了 Resource Timing API，可以供开发者使用。

Resource Timing API 提供了检查、分析一个资源在请求过程中详细网络时间线的方法。该接口的属性使用  [high-resolution timestamps](https://github.com/NinjiaHub/Frontend-Tricks/blob/master/documents/browser/high-resolution-timestamps.md) 生成了资源加载时间线，该资源加载时间线包含了很多网络事件：例如重定向的开始、结束时间，fetchStart，DNS 解析的开始、结束时间等。该接口同时包含了资源的其他信息，例如资源的大小、类型等。

网络请求声明周期的主要阶段如下：

* Redirect
	* Immediately begins `startTime`.
	* If a redirect is happening, `redirectStart` begins as well.
	* If a redirect is occurring at the end of this phase then `redirectEnd` will be taken.
* App Cache
	* If it’s application cache fulfilling the request, a `fetchStart` time will be taken.
* DNS
	* `domainLookupStart` time is taken at the beginning of the DNS request.
	* `domainLookupEnd` time is taken at the end of the DNS request.
* TCP
	* `connectStart` is taken when initially connecting to the server.
	* If `TLS` or `SS`L are in use then `secureConnectionStart` will start when the handshake begins for securing the connection.
	* `connectEnd` is taken when the connection to the server is complete.
* Request
	* `requestStart` is taken once the request for a resource has been sent to the server.
* Response
	* `responseStart` is the time when the server initially responds to the request.
	* `responseEnd` is the time when the request ends and the data is retrieved.

![](../../images/resource-timing-api.png)

## 通过 js 获取

可以在 DevTools 中的 console 中使用下面的代码，performance 可以使用 Resource Timing API 追踪所有资源：

```js
performance.getEntriesByType('resource').filter(item => item.name.includes('resourceName'));
```
上面的 resourceName 是指要查看的具体请求，例如 `a.css` `b.js` `c.png` 这种。

## 其他

Resource Timing API 依赖 [high-resolution timestamps](https://github.com/NinjiaHub/Frontend-Tricks/blob/master/documents/browser/high-resolution-timestamps.md)，详细可以点击链接查看 high-resolution timestamps。

## Refs

* [Understanding Resource Timing](https://developers.google.com/web/tools/chrome-devtools/network/understanding-resource-timing)
* [Using the Resource Timing API](https://developer.mozilla.org/en-US/docs/Web/API/Resource_Timing_API/Using_the_Resource_Timing_API)