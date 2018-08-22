# animation

```css
  animation-name: move;
  animation-duration: 10s;
  animation-timing-function: ease-in;
  animation-delay: .2s;
  animation-iteration-count:infinite;
  animation-direction:alternate;
```

上面这些 animation 相关的属性，一个都不说，要说的是下面这个：

## `animation-play-state`

该属性有两个值：paused 和 running，当值为 paused 时，动画暂停执行，停在当前位置；当值为 running 时，动画从当前停止的位置再次开始执行。这两个值主要用来暂停/开始动画的执行：

```css
.demo {
  animation-name: demo;
  animation-duration: 5s;
  animation-delay: 1s;
  animation-timing-function: ease;
  animation-direction: alternate;
  animation-iteration-count: infinite;
  animation-play-state: paused;
}

.demo:hover {
  animation-play-state: running;
}
```

上面的例子中，每次鼠标移入 `.demo` 元素时，动画会从上次停止的位置继续执行；当鼠标移出时，在当前位置停下。

该属性可以用来做可控起/停位置的动画。

## 参考

* [animation-play-state](https://www.imooc.com/code/1075)

## Author 🐂

* [Github](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>
