# 对象不能添加属性的情况

在 JavaScript 中，假设我们通过下面的方式创建了两个对象：

```javascript
let proto = { a: 3 };
let obj = Object.create(proto)
```

此时，对象 `obj` 的原型为 `proto` 对象；如果我们执行 `obj.a`，因为对象 `obj` 上没有 `a` 属性，所以 JavaScript 引擎会在其原型链查找，如果存在 `a` 属性则返回其值，如果没有的话返回 undefined。

如果我们通过 `obj.a = 4` 给 `a` 属性赋值的话，该操作不会覆盖原型上 `a` 属性的值，而是在对象 `obj` 上创建了一个新的属性 `a`，其值为 4。在此之后，对象 `obj` 上的属性 `a` 会对原型上的属性 `a` 进行屏蔽，后续的所有操作都是在 `obj.a` 上执行，除非手动通过原型链获取原型上的 `a` 属性。

看到这里，我们好像明白了，如果一个对象的原型上有某个属性 `a` 而该对象上没有此属性的话，那么执行 `get` 操作时会通过原型链获取到该属性的值；而如果执行 `set` 操作的话，会在该对象上创建一个同名的属性，并会对原型上的同名属性进行屏蔽。

事实真的像我们观察到的那样，如果对象 `obj` 上没有属性 `a`，通过赋值操作就可以在对象上创建一个 `a` 属性吗？答案是否定的。下面两种情况，对一个对象执行赋值操作时，并不会在该对象上创建一个同名属性：

## 原型链上的同名属性为 getter/setter 时

例：

```javascript
let proto = {
  get a () { return 3 },
  set b (v) {}
}
let obj = Object.create(proto) // set proto as obj's prototype
obj.a // 3
obj // {}

obj.a = 4 // 通过赋值操作在 obj 上创建属性 a 并赋值为 4
obj.a // 3，拿到的仍然是原型上 a 属性的值
obj // {}，obj 仍然是一个控对象，并没有创建 a 属性
```

由上可知，如果一个对象的原型上某个属性是通过 getter/setter 定义的话，则不能通过赋值操作在该对象上声明一个同名属性。

## 原型链上的同名属性的属性描述符中 `writable` 为 `false` 时

例：

```javascript
let proto = {}
Object.defineProperty(proto, 'a', {writable: false, configurable: true, enumerable: true, value: 3})
proto // { a: 3 }

let obj = Object.create(proto)
obj.a // 3
obj // {}

obj.a = 4
obj.a // 3
obj // {}
```

同上面的例子一样，如过一个对象的原型上某个属性的属性描述符 `writable` 为 `false` 时，则不能通过赋值操作在该对象上声明一个同名属性。

## 解决办法

如果原型上的某个属性是通过上面两种方式中的一种定义的，而我们又想在对象 `obj` 上声明一个同名的属性，是不是没有办法了呢？当然不是，我们可以通过 `Object.defineProperty()` 方法在 `obj` 上定义一个同名属性：

```javascript
Object.defineProperty(obj, 'a', {
  writable: true,
  configurable: true,
  enumerable: true,
  value: 3
})
```

在原生的 JavaScript 中，绝大部分属性都不是通过上面两种方式定义的，所以基本不存在无法在自定义的对象上通过赋值声明属性；如果出现了无法通过赋值声明属性的情况，那应该是自己写出了坑。在日常的开发中，不推荐在原型上使用上面两种方式声明属性，因为这会给后面使用该原型的人造成很大的麻烦：第一，后面使用该原型的人如果遇到了这个问题，可能会花很多时间才能查处问题所在；第二，查到问题之后，通过 `Object.defineProperty()` 方法声明变量写起来很麻烦。

## Author 🐊

* [Github](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>
