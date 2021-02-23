# domain sharding

domain sharding 是指域名分片，其实也算不上是一种新技术，只是基于现有技术对网络请求限制的一种解决方案。浏览器在请求网络资源时，会对同一域名下的请求数量做限制，Chrome 中对请求的限制为 6，当同一域名的请求数 > 6 时，多出来的请求会被缓存到队列中，等前面的请求结束之后，再从队列中取出新的请求并执行。

如果统一域名下的请求数过多的话，会导致资源加载变慢，这时就可以使用域名分片的解决方案，将资源放在两个不同的域名下，这样就不会触发上述限制。

同一域名是指子域名，即 `a.xx.com` 和 `b.xx.com` 在浏览器中会判定为不同的域名，所以域名分片技术可以在一台服务器上实现，只要域名解析到同一台服务器并且 Nginx 配置支持即可。

域名分片并不是越多越好，只要够用即可，因为对于不同的域名，需要不同的 DNS 解析，如果域名过多，DNS 解析的时间可能会比队列等待的时间还要久，得不偿失。

## HTTP/2

如果服务器支持了 HTTP/2 的话，不要使用 domain sharding 方案，因为 HTTP/2 默认支持无限网络请求，同 domain sharding 解决的问题一致。在支持 HTTP/2 的地方使用 domain sharding 是多余的。在老的浏览器上使用 ds，在新的浏览器上直接使用 HTTP/2 请求。

> Domain sharding can boost a website’s performance without adding a substantial amount of complexity.

## Refs

* [What is Domain Sharding?](https://blog.stackpath.com/glossary-domain-sharding/)