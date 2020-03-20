# High-resolution Timestamps

该时间戳是一个 double-precision floating-point，即双精度浮点数，用来存储以毫秒计数的时间。

规范中规定该浮点数应该精确到 5µs，但是如果浏览器不支持的话，浏览器应该返回一个精确到毫秒的时间戳。

注意，可以在浏览器设置中修改该时间戳的精度。

> The time, given in milliseconds, should be accurate to 5 µs (microseconds), with the fractional part of the number indicating fractions of a millisecond. However, if the browser is unable to provide a time value accurate to 5 µs (due, for example, to hardware or software constraints), the browser can represent the value as a time in milliseconds accurate to a millisecond. Also note the section below on reduced time precision controlled by browser preferences to avoid timing attacks and fingerprinting.

## Reduced time precision

为了防止 timing attack(时序攻击) & fingerprinting，该时间的精度可以手动修改。在 FireFox 的偏好设置中，`privacy.reduceTimerPrecision` 默认启用，在 v59 中为 20µs，在 v60 中默认为 2ms。

## Refs

* [DOMHighResTimeStamp](https://developer.mozilla.org/en-US/docs/Web/API/DOMHighResTimeStamp)