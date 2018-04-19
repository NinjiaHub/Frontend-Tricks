# JavaScript 中没有 `else if` 结构

是的，你没有看错，在 ECMA262 规范中，并不存在 `else if` 这种语法结构，虽然在开发中我们经常使用这种“解构”。`else if` 也不是一个保留字/关键字，而是由社区发明出来的 `else` 和 `if` 语句的结合体。

`if` 语句有两种使用方式：

**if 语句后使用花括号( {} )：**

```javascript
if (express) {
	express
	...
}
```

**if 语句后不使用花括号( {} )：**

```javascript
if (express) express
```

`else` 语句同 `if` 语句，在使用时也可以不使用花括号：

```javascript
if (false) {
	express
} else express
```

`else` 语句中可以书写任何任何符合规范的语句、表达式、代码块等，所以上例中 `else` 语句中的 `express` 可以任意合法的 JavaScript 代码；如果 `express` 部分是一个完整的代码块时，可以换行；所以，`else` 语句中的 `express` 部分可以是一个 `if` 语句：

```javascript
if (false) {
	...
} else {
	if (condition) {
		...
	}
}
```

上例中的解构，`else` 语句中的 `if` 语句可以看作是一个完整的代码块，所以可以将 `else` 语句的花括号删除；同时，在删除 `else` 语句的花括号时，应将其中的逻辑/代码书写在 `else` 之后，于是就有了 `else if` 结构：

```javascript
// 1、删除 else 部分的花括号
if (false) {
	...
} else
	if (condition) {
		...
	}

// 2、将 if 语句与 else 书写在同一行；if 语句是一个完整的代码块，允许换行
if (false) {
	...
} else if (condition) {
		...
	} // *
	
// 3、为了美观，将上面 else 中的 if 语句的结束花括号向前提
if (false) {
	...
} else if (condition) {
		...
}

// else if 结构就产生了
```

所以，`else if` 这种写法是社区根据 ECMA 规范发明出来的写法，并不存在于规范中，且规范中也没有 关于 `else if` 结构的定义；`else if` 不是 JavaScript 关键字/保留字。

日常开发中，推荐使用花括号包裹 `if` 和 `else` 的表达式部分。

## 作者信息 🐣

* [GitHub](https://github.com/Tao-Quixote)
* e-mail: <web.taox@gmail.com>