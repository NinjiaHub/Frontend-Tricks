# Fetch

## cookieless default

```js
fetch(
    'http://domain/service',
    {
      method: 'GET',
      credentials: 'same-origin' // cookie with this
    }
  )
```

## timeout

```js
// fetch with a timeout
function fetchTimeout(url, init, timeout = 3000) {
  return new Promise((resolve, reject) => {
    fetch(url, init)
      .then(resolve)
      .catch(reject);
    setTimeout(reject, timeout);
  }
}
```

set timeout with `Promise.race()`:

```js
Promise.race([
  fetch('http://url', { method: 'GET' }),
  new Promise(resolve => setTimeout(resolve, 3000))
])
  .then(response => console.log(response))
```

## Abort

```js
// XMLHttpRequest
const xhr = new XMLHttqRequest()
xhr.abort()
```

```js
// fetch
const controller = new AbortController();

fetch(
  'http://domain/service',
  {
    method: 'GET'
    signal: controller.signal
  })
  .then( response => response.json() )
  .then( json => console.log(json) )
  .catch( error => console.error('Error:', error) );
```

Fetch can be aborted by calling controller.abort();. The Promise rejects so the .catch() function is called.

## no progress

```js
// xhr
// upload progress
xhr.upload.onprogress = p => {
  console.log( Math.round((p.loaded / p.total) * 100) + '%') ;
}
```

## Refs

* [XMLHttpRequest vs the Fetch API: What’s Best for Ajax in 2019?](https://www.sitepoint.com/xmlhttprequest-vs-the-fetch-api-whats-best-for-ajax-in-2019/)