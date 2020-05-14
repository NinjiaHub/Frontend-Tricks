# nghttp2

HTTP/2 工具，既可以作为服务器，也可以作为请求测试工具。

## 安装

Mac:

```bash
> brew install nghttp2
```

## options

```bash
Options:
  -v, --verbose
              Print   debug   information   such  as   reception   and
              transmission of frames and name/value pairs.  Specifying
              this option multiple times increases verbosity.
  -n, --null-out
              Discard downloaded data.
  -O, --remote-name
              Save  download  data  in  the  current  directory.   The
              filename is  derived from  URI.  If  URI ends  with '/',
              'index.html'  is used  as a  filename.  Not  implemented
              yet.
  -t, --timeout=<DURATION>
              Timeout each request after <DURATION>.  Set 0 to disable
              timeout.
  -w, --window-bits=<N>
              Sets the stream level initial window size to 2**<N>-1.
  -a, --get-assets
              Download assets  such as stylesheets, images  and script
              files linked  from the downloaded resource.   Only links
              whose  origins are  the same  with the  linking resource
              will be downloaded.   nghttp prioritizes resources using
              HTTP/2 dependency  based priority.  The  priority order,
              from highest to lowest,  is html itself, css, javascript
              and images.
  -H, --header=<HEADER>
              Add a header to the requests.  Example: -H':method: PUT'
  -d, --data=<PATH>
              Post FILE to server. If '-'  is given, data will be read
              from stdin.
  -u, --upgrade
              Perform HTTP Upgrade for HTTP/2.  This option is ignored
              if the request URI has https scheme.  If -d is used, the
              HTTP upgrade request is performed with OPTIONS method.
  --version   Display version information and exit.
  -h, --help  Display this help and exit.
```

## Refs

* [nghttp2官网](https://nghttp2.org/documentation/nghttp.1.html)