# csso - CSS Optimizer

> CSSO (CSS Optimizer) is a CSS minifier. It performs three sort of transformations: cleaning (removing redundant), compression (replacement for shorter form) and restructuring (merge of declarations, rulesets and so on). As a result your CSS becomes much smaller.

* cleaning
* compression
* restructuring

CSSO is based on CSSTree to parse CSS into AST, AST traversal and to generate AST back to CSS. 

CSSTree API Map:

![](../../images/csstree-api-map.svg)

注：上图来自 csstree [GitHub Repo](https://github.com/csstree/csstree)。

## csso-webpack-plugin

在构建前端项目时可以通过该插件做 css modules 的优化或者单独做 css 优化。

[GitHub Repo](https://github.com/zoobestik/csso-webpack-plugin)

如需在 gulp、grunt 或者 postcss 等中使用，可以[参阅](https://github.com/css/csso#ready-to-use)。

## example

可以通过这个网站来体验 csso 的优化。css modules 中会在使用 `composes` 组合的时候，有可能会产生空的类：

```css
// index.css
.a {
	color: red;
	font-size: 12px;
}
.b {
	composes: a;
}
```

===> 编译生成的 css：

```css
.index_a_39fe1 {
	color: red;
	font-size: 12px;
}
.index_b_ea215 {
}
```

**注：这里的 hash 是随便写的，不是真的。**

```jsx
// a.jsx
import cls from './index.css';

<p className={cls.b}></p>
```

===> 编译生成的 HTML：

```html
<p class="index_a_39fe1 index_b_ea215"></p>
```

虽然生成的 css 中类 ` index_b_ea215` 中没有内容，但是在构建工具中一般会集成 csso，在压缩阶段会把 `index_b_ea215` 过滤掉，即生成的 css 文件中并没有 ` ndex_b_ea215`。

===> 经过 csso 之后的 css：

```css
.index_a_39fe1 {
	color: red;
	font-size: 12px;
}
```

[https://css.github.io/csso/csso.html](https://css.github.io/csso/csso.html)

## Links

* [csso - GitHub Repo](https://github.com/css/csso)
* [csstree - GitHub Repo](https://github.com/csstree/csstree)