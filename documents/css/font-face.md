# @font-face

现代网页中的 icon 一般使用 icon font 来制作，因为相比于多个图片或者雪碧图来说，icon font 轻量级、灵活、兼容性好；而且 icon font 是矢量图，在缩放时不会失真。

使用 icon font 时，除了素材制作之外，使用 icon font 的第一步就是声明 font-family：

## 声明 font-family

```css
@font-face {
  font-family: "imooc-icon";
  src: url("../fonts/icomoon.eot"); /* IE9 兼容模式 */
  src: url("../fonts/icomoon.eot?#iefix") format("embedded-opentype"), /* IE6-IE8 */
       url("../fonts/icomoon.woff") format("woff"), /* 现代浏览器 */
       url("../fonts/icomoon.ttf") format("truetype"), /* Safari、Android、IOS */
       url("../fonts/icomoon.svg") format("svg"); /* Legacy IOS */
  font-weight: normal;
  font-style: normal;
}

在上面的 font-family 声明中，src 的声明比较奇怪，`src: url("../fonts/icomoon.eot");` 这句是针对 IE9 兼容模式进行的特殊处理，IE 9 会根据 url 区加载 eot 格式的字体文件；下面的 src 部分是针对不同浏览器声明的 url 路径，用于告诉不同的浏览器加载不同的字体文件。

`url("../fonts/icomoon.eot?#iefix") format("embedded-opentype"), /* IE6-IE8 */` 这行是针对 IE6 - IE8 的声明，原因如下：

> IE8 and the older have a bug in their parsers for the src attribute. So if you include more than 1 font format in the SRC, IE fails to load it and reports a 404 error.
> The question mark solves that problem as it fools IE into thinking the rest of the string (other src) is a query string, and therefore loading just the EOT file...
> Other browsers will follow the specification and load just their required font type ...

简单来说就是 IE8 及更早的浏览器在解析有多个 url 的 src 时会出错，所以如果 src 中出现了多于 1 个字体格式时，IE 会加载失败并返回一个 404 错误。所以这里的 `?` 是为了欺骗 IE 浏览器，让其以为后面的字符串是查询字符从而加载 EOT 文件。其他浏览器可以遵循规范正确的加载自己可以解析的字体文件。关于这个问题的详情可以跳转 [How does ?#iefix solve web fonts loading in IE6-IE8?](https://stackoverflow.com/questions/8050640/how-does-iefix-solve-web-fonts-loading-in-ie6-ie8)。

.example {
   font-family: "imooc-icon";
   font-size: 12px;
   font-style: normal;
   font-weight: normal;
   -webkit-font-smoothing: antialiased; /* webkit 内核字体抗锯齿 */
   -moz-osx-font-smoothing: grayscale; /* moz/osx 字体抗锯齿 */
}
```

## 在 HTML 中直接使用 @font-face 定义的 icon fonts

如果在 html 中直接输入字体中标示 icon 的十六进制数字是没有效果的，这个十六进制的字符串会直接渲染出来；想在 html 中直接使用 icon font，需要在十六进制的数字前加上 `&#x` 前缀，例如：

假设一个 icon font 中的一个图标的十六进制码为 `6d3e` 的话，要在 html 中直接使用该图标：

```html
<style>
  li {
    font-family: "imooc-icon";
    font-size: 18px;
    font-weight: normal;
    font-style: normal;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
<ul>
  <li>&#x6d3e;</li>
</ul>
```

该方法支持 IE6 及其以上版本。

## 在 CSS 中使用 @font-face 定义的 icon-fonts

在 css 中使用 icon fonts 中定义的图标字体文件，需要使用 css 中的伪类来实现，例：

```html
<style>
  .li {
    font-family: "imooc-icon";
    font-size: 18px;
    font-weight: normal;
    font-style: normal;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
  .li::before {
    content: '\6d3e'
  }
<ul>
  <li></li>
</ul>
```

css 伪元素的`content` 属性中也不能直接书写十六进制码，需要使用 `\\` 进行转义告诉浏览器这是一个 icon font。

**注：IE8 及其以上版本才支持 icon font，IE8 一下版本不支持 icon font。所以在 HTMl 中使用十六进制码的兼容性更好。**

## 参考

* [HTML 画小图标实例](https://www.imooc.com/video/5126/0)
* [Bulletproof @font-face Syntax](https://www.paulirish.com/2009/bulletproof-font-face-implementation-syntax/)

## Author Info 🐬

* [GitHub](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>
