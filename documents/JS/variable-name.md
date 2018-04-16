# 变量命名


JavaScript 中变量命名规则如下：

* 1、允许字母和阿拉伯数字，但是**数字不能作为变量名的第一个字符**
* 2、**`$`** 和 **`_`**允许出现在变量名的任意位置，也可单独作为变量名
* 3、**非拉丁字母** 和 **象形文字** 也可以单独作为变量名或者作为变量名的一部分出现在任意位置

例：

```javascript
let $ = 1
let _num = 2
let 变量 = 3

console.log($) // 1
console.log(_num) // 2
console.log(变量) // 3
```

上面需要注意的是第 **`3`** 条：

> **非拉丁字母** 和 **象形文字** 也可以单独作为变量名或者作为变量名的一部分出现在任意位置

**非拉丁字母** 和 **象形文字** 在规范中是允许出现在变量名中的，但是为了通用和国际化的，最好还是不要在变量的命名中使用 **非拉丁字母** 和 **象形文字**。

## <span id="links">参考</span>

* [Variables](https://javascript.info/javascript-specials#variables)

## <span id="author">作者 🙉</span>

* [GitHub](https://github.com/Tao-Quixote)
* e-mail: <web.taox@gmail.com>