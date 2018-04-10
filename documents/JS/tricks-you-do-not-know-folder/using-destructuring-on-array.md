# 在数组上应用对象解构

> 写这篇文章的起因在于查看 [justjavac/the-front-end-knowledge-you-may-not-know - 对象解构可以应用在数组上](https://github.com/justjavac/the-front-end-knowledge-you-may-not-know/issues/20#issuecomment-379605209) 这个知识点时回复用户 **@jjvein** 的回复；恰好最近在看相关的知识点，于是写了这篇文章。

在 [对象解构可以应用在数组上](https://github.com/justjavac/the-front-end-knowledge-you-may-not-know/issues/20#issuecomment-379605209) 这篇中，题主列出了一些解构应用在数组上的一些应用：

1、获取数组的长度：

```javascript
const {0:a, 2:b, length:l} = ['foo', 'bar', 'baz']
a === 'foo'
b === 'baz'
l === 3
```

2、还可以使用此技巧获取数据最后一个元素：

```javascript
const { length: l, [l-1]: last, ...rest } = [1, 2, 3]
l === 3
last === 3
```

只是 rest 变成了对象 {0: 1, 1: 2}。

3、将数组转换为对象：

```javascript
> const { ...obj } = [1,2,3]
> obj
{0: 1, 1: 2, 2: 3}
```

****

其实上面这些用法一点都不神奇，数组构造函数（Array）本身就继承自 **Object**；而且，数组具有对象(Object)的所有方法，可以通过下面的方式验证：

```javascript
> Object
ƒ Object() { [native code] }
> Array.prototype.__proto__ === Object.prototype
true
```
从上面的代码可知，Object 上所有的方法都存储在其原型（Object.prototype）上；Array.prototype 对象的 `原型（__proto__）` 其实就是指向 Object.prototype，所以，Array 及其 “实例” 拥有 Object 所有属性及特性，所以，解构自然而然地可以应用在数组上。

## 索引

JavaScript 中的数组是 **Array** 的实例，**Array** 继承自 **Object** 和 **Function**，数组本质上就是对象，其索引(下标)其实就是不同的属性名称；所以，通过数组的索引(下标)对数组进行解构时，可以看作是取对象对应索引的属性的值。

## length

```javascript
> []
[]
length: 0
```
将上面的代码放在控制台中运行，输出包括三部分：`[]`、`length: 0` 以及 `__proto__` 部分。

我们知道，**length** 是“数组对象”的一个特殊属性，该属性的值通常由引擎在执行增/删数组元素的时候自行维护。所以当数组被放在解构表达式的右边时，数组是被当成一个对象来处理的，所以 `let {length: l} = []` 其实就是从 “数组对象 `[]`” 中解构出属性 `length` 的值并存储在变量 `l` 中。

## 不只是 `join`

解构同操作符 `in`，会查找解构表达式右边对象的原型链。但是其与 `in` 操作符有一个不同的地方：`in` 操作符不会匹配 `enumerable: false` 的属性；而解构不理会描述符(descriptor)，只要要解构的属性在被解构的对象或者原型链上，该属性的值就会被解构然后赋值给对应的变量。所以，不只是 `join` 属性，其他在 `Array.prototype` 上的方法如 `concat`、`map`、`forEach`、`pop`、`push`、`shift`、`unshift` 等也可以被解构：

```javascript
let {map: m, push: p} = []
m === Array.prototype.map // true
m === [].__proto__.map // true
p === Array.prototype.push // true
p === [].__proto__.push // true
```

通过 Array 的原型链解析 `Object.prototype` 上的属性：

```javascript
Array.prototype.__proto__.hasOwnProperty === Object.prototype.hasOwnProperty // true
let {hasOwnProperty: hop} = []
hop === Object.prototype.hasOwnProperty // true
```

从上面的例子可以看出，解构操作会沿着当前对象及其原型链一直向上查找，直到 `Object.prototype.__proto__` 对象。如果被解构对象及其原型链上不存在该属性，则返回 `undefined`。

## 不只是对象

在 [迷渡(justjavac)](https://github.com/justjavac) 的提点下，看了一下 ecma262 规范中 [Runtime Semantics: DestructuringAssignmentEvaluation](https://tc39.github.io/ecma262/#sec-runtime-semantics-destructuringassignmentevaluation) 部分的定义：

> 1、Perform ? **RequireObjectCoercible(value)**.
> 
> 2、Return **NormalCompletion(empty)**.
> 
	ObjectAssignmentPattern:
		{AssignmentPropertyList}
		{AssignmentPropertyList,}
> 1、Perform ? **RequireObjectCoercible(value)**.
> 
> 2、Perform ? PropertyDestructuringAssignmentEvaluation for AssignmentPropertyList using value as the argument.
> 
> 3、Return **NormalCompletion(empty)**.

在运行时，引擎会先执行 **RequireObjectCoercible(value)** 操作，而这一步操作决定了对象解构不止可以用于对象(包括 Object、Function、Array、RegExp、Date...)，而且可用于基本数据类型(Number、String、Boolean、Symbol)。由于 **undefined** 和 **null** 没有对应的包装类型，所以对这两种数据进行对象解构会报错。

**RequireObjectCoercible(value)：**

> The abstract operation **RequireObjectCoercible** throws an error if argument is a value that cannot be converted to an Object using **ToObject**.
> 
> 对于不能通过 **ToObject** 转换为对象的 argument，抽象操作 **RequireObjectCoercible** 应抛出异常。

所以 **RequireObjectCoercible(value)** 这部分会通过 **ToObject** 将 argument 转换为对象，基本数据类型转换为对应的包装对象，然后再进行后续的解构操作。

所以，对象解构不止可以用于对象，也可以用于部分基本数据类型(Number、String、Boolean、Symbol)。

**例：**

```javascript
let {__proto__: p} = 1
p === Number.prototype // true
```

## <span id="author">作者 🙉</span>

* [GitHub](https://github.com/Tao-Quixote)
* e-mail: <web.taox@gmail.com>
