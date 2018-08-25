# 媒体查询

## 媒体类型

| 值 | 设备类型 |
|----|----------|
| All | 所有设备 |
| Braille | 盲人用点字法触觉回馈设备 |
| Embossed | 盲文打印机 |
| Handheld | 便携设备 |
| Print | 打印用纸或打印预览视图 |
| Projection | 各种投影设备 |
| Screen | 电脑显示器 |
| Speech | 语音或音频合成器 |
| Tv | 电视机类型设备 |
| Tty | 使用固定密度字母栅格的媒介，比如电传打字机和终端 |

## 引入媒体查询

### link方法

```html
<link rel="stylesheet" type="text/css" href="style.css" media="screen" />
<link rel="stylesheet" type="text/css" href="print.css" media="print" />
```

### @import

在 css 中引入：

```css
@importurl(reset.css) screen;
@importurl(print.css) print;
```

不建议在 css 中通过 `@importurl` 的方式引入媒体查询的 css 文件，因为这种方式可能会延长加载的时间，增加首屏的时间。

在 html 的 head 中引入：

```html
<head>
<style type="text/css">
    @importurl(style.css) all;
</style>
</head>
```

### @media

```css
@media screen {
   选择器{/*你的样式代码写在这里…*/}
}
```

```html
<head>
<style type="text/css">
    @media screen{
    选择器{/*你的样式代码写在这里…*/}
}
</style>
</head>
```

## 声明

该文章搬运自 大漠 老师的慕课网课程 [十天学会CSS](https://www.imooc.com/code/1389)。

## Author 🐄

* [Github](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>
