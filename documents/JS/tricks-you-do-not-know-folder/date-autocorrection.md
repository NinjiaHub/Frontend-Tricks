# 自动纠正（Autocorrection）

> The autocorrection is a very handy feature of Date objects. We can set out-of-range values, and it will auto-adjust itself.

**自动纠正** 是 `Date` 对象中一个非常方便的特性。我们可以设置超出范围的值，`Date` 对象及其实例会自动纠正为正确的日期。

例如：

```javascript
let date = new Date(2013, 0, 32); // 32 Jan 2013 ?!? 1 月份没有 32 号
alert(date); // ...is 1st Feb 2013! 纠正为 2 月 1 号
```

> Out-of-range date components are distributed automatically.

超出范围的日期部分将会自动分发。

> Let’s say we need to increase the date “28 Feb 2016” by 2 days. It may be “2 Mar” or “1 Mar” in case of a leap-year. We don’t need to think about it. Just add 2 days. The Date object will do the rest:

比如我们要将日期 “2016-02-28” 增加 2 天。因为存在**闰年**的问题，所以结果可能为 “2016-03-02” 或者 “2016-03-01”。我们不必考虑这些，只需要直接增加 2 天，剩下的事情交给 `Date` 对象来处理：

```javascript
let date = new Date(2016, 1, 28);
date.setDate(date.getDate() + 2);

alert( date ); // 2016-03-01
```

> That feature is often used to get the date after the given period of time. For instance, let’s get the date for “70 seconds after now”:

该特性经常用来获取指定时间段之后日期。例如，我们来获取 “**70 秒之后**” 的日期：

```javascript
let date = new Date();
date.setSeconds(date.getSeconds() + 70);

alert( date ); // shows the correct date
```

> We can also set zero or even negative values. For example:

我们可以设置 **`0`** 甚至**负数**。例如：

```javascript
let date = new Date(2016, 0, 2); // 2016-01-02

date.setDate(1); // set day 1 of month
alert( date );

date.setDate(0); // date 的最小值为 1，所以 date 被设置为上个月的最后一天
alert( date ); // 2015-12-31
```

## 原文链接

[Date and time - Autocorrection](https://javascript.info/date#autocorrection)

## 译者 🐒

* [GitHub](https://github.com/Tao-Quixote)
* e-mail: <web.taox@gmail.com>