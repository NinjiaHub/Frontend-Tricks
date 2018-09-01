# 多列布局

为了能在Web页面中方便实现类似报纸、杂志那种多列排版的布局，W3C特意给CSS3增加了一个多列布局模块，用于将容器中的内容按照指定的列数/列的宽度来展示文本内容。

* column-width: 用法同 width，用于指定每一列的宽度；如果没有设置，则会按照容器宽度和 column-count 来自动计算每一列的宽度；
* column-count：用于指定当前容器中分成多少列来展示内容；
* column-gap：用于设置列与列之间的间距；
* column-span：主要用来定义一个分列元素中的子元素能跨多少列；
* column-rule：用于设置列与列之间边框的宽度、样式和颜色，类似 border，但是不占用空间；

example:

```css
div {
  width: 430px;
  column-width: 100px;
  column-gap: 10px;
  column-rule: 3px dashed #f00;
}

div p:first-of-type {
  column-span: all;
}
```

## column-rule

```css
column-rule:<column-rule-width>|<column-rule-style>|<column-rule-color>
```

* column-rule-width: 类似于border-width属性，主要用来定义列边框的宽度，其默认值为“medium”，column-rule-width属性接受任意浮点数，但不接收负值。但也像border-width属性一样，可以使用关键词：medium、thick和thin。
* column-rule-style: 类似于border-style属性，主要用来定义列边框样式，其默认值为“none”。column-rule-style属性值与border-style属值相同，包括none、hidden、dotted、dashed、solid、double、groove、ridge、inset、outset。
* column-rule-color: 类似于border-color属性，主要用来定义列边框颜色，其默认值为前景色color的值，使用时相当于border-color。column-rule-color接受所有的颜色。如果不希望显示颜色，也可以将其设置为transparent(透明色)。

## Author 🐖

* [Github](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>
