# BEM

> The **Block**, **Element**, **Modifier** methodology (commonly referred to as BEM) is a popular naming convention for classes in HTML and CSS. Developed by the team at Yandex, its goal is to help developers better understand the relationship between the HTML and CSS in a given project.

BEM 不是一种前端技术，而是一种用来帮助更好地理解 HTML 和 CSS 之间关系的命名规范。示例：

```css
/* Block component */
.btn {}

/* Element that depends upon the block */ 
.btn__price {}

/* Modifier that changes the style of the block */
.btn--orange {} 
.btn--big {}
```
BEM 将 CSS 类名根据其在 HTML 中的应用位置分为三类：

* BLOCK - 单独作为一个单元的块
* ELEMENT - 依赖 BLOCK，作为 B 的子元素
* MODIFIER - 依赖 B 但又对 BLOCK 的样式做出部分修改

对于这三部分，其命名规范如下：

* BLOCK - 常见的 css 命名，说明该样式块要修饰的部分的用途
* ELEMENT - BLOCK__ELEMENT，即 block 的名字 + 两个下划线 + element 要修饰的部分
* MODIFIER - BLOCK--MODIFIER，即 block 的名字 + 两个短横线 + modifier 要做出的修改

上面的 BEM 例子对应的 HTML 应该如下：

```html
<a class="btn btn--big btn--orange" href="https://css-tricks.com">
  <span class="btn__price">$9.99</span>
  <span class="btn__text">Subscribe</span>
</a>
```

## 例子

```css
.accordion {
  max-width: 600px;
  margin: 4rem auto;
  width: 90%;
  font-family: "Raleway", sans-serif;
  background: #f4f4f4;
}
.accordion__title {
  font-size: 0.7rem;
  text-transform: uppercase;
  letter-spacing: 2px;
  padding: 1rem 1.5rem;
  background: #5B5F97;
  color: white;
  cursor: pointer;
  transition: .3s ease;
}
.accordion__title:hover {
  background: #484b77;
}
.accordion__copy {
  display: none;
  padding: 1rem 1.5rem 2rem 1.5rem;
  color: gray;
  line-height: 1.6;
  font-size: 14px;
  font-weight: 500;
}
.accordion__copy--open {
  display: block;
}


body {
  background: #222;
}
```
```html
<div class="accordion">
  <div class="accordion__item">
    <h3 class="accordion__title js-title">Real Lorem Ipsum by 37signals</h3>
    <p class="accordion__copy accordion__copy--open js-copy">By not having the imagination to imagine what the content "might" be, a design consideration is lost. Meaning becomes obfuscated because "it's just text", understandability gets compromised because nobody realized that this text stuff was actually meant to be read. Opportunities get lost because the lorem ipsum garbage that you used instead of real content didn't suggest opportunities. The text then gets made really small, because, it's not meant to be used, we might as well create loads of that lovely white space.</p>
  </div>
</div>
```
## 其他

在 BEM 的理念中，使用了 BEM 的命名规范之后，是不推荐再使用嵌套的，一切最好写在文件的最外层，即使使用了 SASS 等预处理语言编写 CSS，在编译生成的 CSS 中，BEM 的每个部分也应该是在文件的最外层的，不应该再出现 `.a .b>.c` 这种 class 命名。

如果想在 SASS 中书写时使用嵌套，但是编译生成的 css 中不嵌套，可以使用如下的方法：

```sass
.block {
  @at-root #{&}__element {
  }
  @at-root #{&}--modifier {
  }
}
```
===>

```css
.block {
}
.block__element {
}
.block--modifier {
}
```

这个写法看起来很蠢。。。下面的链接会指向一个牛批的实现：

[Expressive BEM with Sass: a different approach](https://codepen.io/andersschmidt/post/expressive-bem-with-sass-a-different-approach)

## Links

* [BEM 101](https://css-tricks.com/bem-101/)