# RxJS学习资料



[TOC]



## RxJS基础概念

可以把 RxJS 当做是用来处理事件的 [Lodash](https://lodash.com/) 。

在 RxJS 中用来解决异步事件管理的的基本概念是：

- **Observable (可观察对象):** 表示一个概念，这个概念是一个可调用的未来值或事件的集合。
- **Observer (观察者):** 一个回调函数的集合，它知道如何去监听由 Observable 提供的值。
- **Subscription (订阅):** 表示 Observable 的执行，主要用于取消 Observable 的执行。
- **Operators (操作符):** 采用函数式编程风格的纯函数 (pure function)，使用像 `map`、`filter`、`concat`、`flatMap` 等这样的操作符来处理集合。
- **Subject (主体):** 相当于 EventEmitter，并且是将值或事件多路推送给多个 Observer 的唯一方式。
- **Schedulers (调度器):** 用来控制并发并且是中央集权的调度员，允许我们在发生计算时进行协调，例如 `setTimeout` 或 `requestAnimationFrame` 或其他。

## RxJS特性

### 纯净性(Purity)

使得 RxJS 强大的正是它使用纯函数来产生值的能力。这意味着你的代码更不容易出错。

通常你会创建一个非纯函数，在这个函数之外也使用了共享变量的代码，这将使得你的应用状态一团糟。

```js
var count = 0;
var button = document.querySelector('button');
button.addEventListener('click', () => console.log(`Clicked ${++count} times`));
```

使用 RxJS 的话，你会将应用状态隔离出来。

```Js
var button = document.querySelector('button');
Rx.Observable.fromEvent(button, 'click')
  .scan(count => count + 1, 0)
  .subscribe(count => console.log(`Clicked ${count} times`));
```

**scan** 操作符的工作原理与数组的 **reduce** 类似。它需要一个暴露给回调函数当参数的初始值。每次回调函数运行后的返回值会作为下次回调函数运行时的参数。

### 流动性(Flow)

RxJS 提供了一整套操作符来帮助你控制事件如何流经 observables 。

下面的代码展示的是如何控制一秒钟内最多点击一次，先来看使用普通的 JavaScript：

```js
var count = 0;
var rate = 1000;
var lastClick = Date.now() - rate;
var button = document.querySelector('button');
button.addEventListener('click', () => {
  if (Date.now() - lastClick >= rate) {
    console.log(`Clicked ${++count} times`);
    lastClick = Date.now();
  }
});
```

使用 RxJS：

```js
var button = document.querySelector('button');
Rx.Observable.fromEvent(button, 'click')
  .throttleTime(1000)
  .scan(count => count + 1, 0)
  .subscribe(count => console.log(`Clicked ${count} times`));
```

public throttleTime(duration: number, scheduler: Scheduler): Observable

从源 Observable 中发出一个值，然后在 `duration` 毫秒内忽略随后发出的源值， 然后重复此过程。

## Observables 作为函数的泛化

### Observables 像是没有参数, 但可以泛化为多个值的函数。

考虑如下代码：

```js
function foo() {
  console.log('Hello');
  return 42;
}

var x = foo.call(); // 等同于 foo()
console.log(x);
var y = foo.call(); // 等同于 foo()
console.log(y);
```

我们期待看到的输出：

```none
"Hello"
42
"Hello"
42
```

你可以使用 Observables 重写上面的代码：

```js
var foo = Rx.Observable.create(function (observer) {
  console.log('Hello');
  observer.next(42);
});

foo.subscribe(function (x) {
  console.log(x);
});
foo.subscribe(function (y) {
  console.log(y);
});
```

输出是一样的:

```none
"Hello"
42
"Hello"
42
```

因为Observable都是惰性运算，只有在subscribe订阅之后才会执行里面的内容。

### 订阅 Observable 类似于调用函数。

一些人声称 Observables 是异步的。那不是真的。如果你用日志包围一个函数调用，像这样：

```js
console.log('before');
console.log(foo.call());
console.log('after');
```

你会看到这样的输出:

```none
"before"
"Hello"
42
"after"
```

使用 Observables 来做同样的事：

```js
console.log('before');
foo.subscribe(function (x) {
  console.log(x);
});
console.log('after');
```

输出是：

```none
"before"
"Hello"
42
"after"
```

这证明了 `foo` 的订阅完全是同步的，就像函数一样。

### Observables 传递值可以是同步的，也可以是异步的。

那么 Observable 和 函数的区别是什么呢？**Observable 可以随着时间的推移“返回”多个值**，这是函数所做不到的。你无法这样：

```js
function foo() {
  console.log('Hello');
  return 42;
  return 100; // 死代码，永远不会执行
}
```

函数只能返回一个值。但 Observables 可以这样：

```js
var foo = Rx.Observable.create(function (observer) {
  console.log('Hello');
  observer.next(42);
  observer.next(100); // “返回”另外一个值
  observer.next(200); // 还可以再“返回”值
});

console.log('before');
foo.subscribe(function (x) {
  console.log(x);
});
console.log('after');
```

同步输出：

```none
"before"
"Hello"
42
100
200
"after"
```

但你也可以异步地“返回”值：

```js
var foo = Rx.Observable.create(function (observer) {
  console.log('Hello');
  observer.next(42);
  observer.next(100);
  observer.next(200);
  setTimeout(() => {
    observer.next(300); // 异步执行
  }, 1000);
});

console.log('before');
foo.subscribe(function (x) {
  console.log(x);
});
console.log('after');
```

输出：

```none
"before"
"Hello"
42
100
200
"after"
300
```

结论:

- `func.call()` 意思是 "*同步地给我一个值*"
- `observable.subscribe()` 意思是 "*给我任意数量的值，无论是同步还是异步*"

## Observable 剖析

Observable 的核心关注点：

- **创建** Observables
- **订阅** Observables
- **执行** Observables
- **清理** Observables

### 创建 Observables

`Rx.Observable.create` 是 `Observable` 构造函数的别名，它接收一个参数：`subscribe` 函数。

下面的示例创建了一个 Observable，它每隔一秒会向观察者发送字符串 `'hi'` 。

```js
var observable = Rx.Observable.create(function subscribe(observer) {
  var id = setInterval(() => {
    observer.next('hi')
  }, 1000);
});
```

Observables 可以使用 `create` 来创建, 但通常我们使用所谓的[创建操作符](https://cn.rx.js.org/manual/overview.html#creation-operators), 像 `of`、`from`、`interval`、等等。

### 订阅 Observables

示例中的 Observable 对象 `observable` 可以**订阅**，像这样：

```js
observable.subscribe(x => console.log(x));
```

订阅 Observable 像是调用函数, 并提供接收数据的回调函数。

`subscribe` 调用是启动 “Observable 执行”的一种简单方式， 并将值或事件传递给本次执行的观察者。

### 执行 Observables

Observable 执行可以传递三种类型的值：

- "Next" 通知： 发送一个值，比如数字、字符串、对象，等等。
- "Error" 通知： 发送一个 JavaScript 错误 或 异常。
- "Complete" 通知： 不再发送任何值。

***在 Observable 执行中, 可能会发送零个到无穷多个 "Next" 通知。如果发送的是 "Error" 或 "Complete" 通知的话，那么之后不会再发送任何通知了。***

### 清理 Observable 执行

当调用了 `observable.subscribe` ，观察者会被附加到新创建的 Observable 执行中。这个调用还返回一个对象，即 `Subscription` (订阅)：

```js
var subscription = observable.subscribe(x => console.log(x));
```

为了避免内存泄漏和浪费计算能力，在订阅执行之后都要记得取消执行。

```js
var observable = Rx.Observable.from([10, 20, 30]);
var subscription = observable.subscribe(x => console.log(x));
// 稍后：
subscription.unsubscribe();
```

当你订阅了 Observable，你会得到一个 Subscription ，它表示进行中的执行。只要调用 `unsubscribe()` 方法就可以取消执行。