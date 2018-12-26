# rxjs 的优势

* Observable 标准化
* 多语言的支持

https://github.com/tc39/proposal-observable

# rxjs

* 初识 rxjs: https://stackblitz.com/edit/rxjs6-study-beginners
* cold Obvservable / hot Observable: 绝大多数的创建类操作符创建的都是 Cold Obvserable 对象(除了 ajax / fromEvent ...):
    * cold: 录像
    * hot: 直播
* Subject: https://stackblitz.com/edit/rxjs6-study-subject
    * 既是 Observable，也是 observer。主要解决多播问题。


----

# Observable

第一步，先了解 [Observer Pattern](http://www.jianshu.com/p/8051934077ef) 和 [Iterator Pattern](http://www.jianshu.com/p/f054f68508e1)，然后可以发现他们有个共通的特性，就是他们都是渐进式的 (progressive) 取数据，差别在于

* Observer 是生产者，push 数据
* Iterator 是消费者，pull 数据

![](http://7xlc2a.com1.z0.glb.clouddn.com/15064082339931.png)

第二步，Observable 其实就是这两个设计模式的结合实现，Observable 具有生产者 push 数据的特性，同时也像序列，拥有序列处理数据的方法（map、filter等）

```js
// Iterator Pattern
var observable = Rx.Observable.create(observer => {
  observer.next('Jerry')
  observer.next('Anna')
})

// Observer Pattern
observable.subscribe({
  next: x => console.log(x),
  error: err => console.log(err),
  complete: () => console.log('complete')
})
```

> 注意 Observable 的 Observer Pattern 实现与一般的 DOM 事件的 Observer Pattern 是不同的。
> DOM 事件的订阅会有一份清单
> Observable 的 Observer Pattern 实现就像是执行了一个 function


# rxjs

* 一个核心（Observable + Operators）
* 三个重点
    * Observer 一定会用到且最简单的
    * Subject 使用频率低很多，很多 RxJS 相关的 Library 或 Framework 用到，如 redux-observable
    * Schedulers

# Observable

## 创建 Observable

相当于创建数据源

* 方法一 `Rx.Observable.create`
* 方法二 creation operator `from`、`of`、`fromEvent`、`fromPromise` ...

> 虽然 Observable 可以被 create，但实际上我們通常都使用 creation operator 像是 from, of, fromEvent, fromPromise 等。

****

**Observable 可以同时处理同步与异步行为**

```js
var observable = Rx.Observable
	.create(function(observer) {
		observer.next('Jerry'); // RxJS 4.x 以前的版本用 onNext
		observer.next('Anna');
		
		setTimeout(() => {
			observer.next('RxJS 30 days!');
		}, 30)
	})
	
console.log('start');
observable.subscribe(function(value) {
	console.log(value);
});
console.log('end');
```

```js
start
Jerry
Anna
end
RxJS 30 days!
```

*****

**深入 Observable(与 Array 的区别)**

1. 延迟计算：Observable 一样要等到 subscribe 后才开始对元素运算
    
    ```js
    // 没有 subscribe ，下面代码不会运算
    var source = Rx.Observable.from([1,2,3,4,5]);
    var example = source.map(x => x + 1);
    
    // 而数组就直接运算了
    var source = [1,2,3,4,5];
    var example = source.map(x => x + 1); 
    ```
    
2. 渐进式取值：一个元素运算到底，而不是像数组运算完全部元素再返回

    ![](http://7xlc2a.com1.z0.glb.clouddn.com/15064984543104.gif)
    
    ![](http://7xlc2a.com1.z0.glb.clouddn.com/15064984665738.gif)




## Observer

Observable 可以被订阅，订阅 Observable 的对象就是 Observer。Observer 有三个方法，每当 Observable 发生事件时，便会执行对应的 Observer 方法：

* next：每当 Observable 发出新值时执行
* complete
* error

```js
var observable = Rx.Observable.create(observer => {
  observer.next('Jerry')
  observer.error('xx')
  observer.next('Anna')
})

observable.subscribe({
  next: x => console.log(x),
  error: err => console.log(err),
  complete: () => console.log('complete')
})
```

****

**取消订阅 unsubscribe()**

```js
let source$ = observable.subscribe(...)

source$.unsubscribe()
```

## Operator

每个 operator 都会回传一个新的 observable

****

**Marble diagrams**

* `-`： 一串 `-` 代表一个 observable
* `|`： 代表 observable 结束
* `X`： 代表有错误发生
* `()`： 同步

```js
// creation operation
/*
 *  -----0-----1-----2-----3---...
**/
Rx.Observable.interval(1000)

/*
 * (1234)|
**/
Rx.Observable.of(1,2,3,4) // 同步
```

```js
// filter operation
/*
    source: -----0-----1-----2-----3--...
                map(x => x + 1)
    newest: -----1-----2-----3-----4--...
    
    source : -----0-----1-----2-----3--..
                take(3)
    example: -----0-----1-----2|
**/
```

****

[RxJS operators](https://gist.github.com/riskers/c6c8fa7cf51b0d9cb39b443aaf0d48b4) 看这里

// concatAll http://ithelp.ithome.com.tw/articles/10187333
// combineLatest http://ithelp.ithome.com.tw/articles/10187638
// scan buffer http://ithelp.ithome.com.tw/articles/10187882


# Subject

## Subject 是什么？

* Subject 是 Observable 又是 Observer
    
    ```js
    var source = Rx.Observable.interval(1000).take(10)
    var observerA = {
      next: x => console.log('A:', x),
      error: err => console.log(err),
      complete: () => console.log('complete')
    }
    
    var observerB = {
      next: x => console.log('B', x),
      error: err => console.log(err),
      complete: () => console.log('complete')
    }
    
    var subject = new Rx.Subject()
    
    source.subscribe(subject) // subject 作为 observer
    
    subject.subscribe(observerA) // subject 作为 observable
    
    setTimeout(() => {
      subject.subscribe(observerB) // subject 作为 observable
    }, 1000)
    ```

* Subject 会对内部的 observers 清单进行组播 multicast（第二次订阅 source 不會從头开始接收元素，而是从第一次订阅到当前处理的元素开始发送）

## BehaviorSubject / ReplaySubject / AsyncSubject

* BehaviorSubject 用来呈现当前的值，会记住最新一次发送的元素，并把该元素当做目前的值
* ReplaySubject 能在新订阅时重新发送最后的几个元素
* AsyncSubject 在 subject 结束后送出最后一个值

## multicast / refCount / publish / share

略

## Subject 的用处

用在不知道如何建立 Observable 的状况，比如 React

![](http://7xlc2a.com1.z0.glb.clouddn.com/30_天精通_RxJS_25_：Subject_總結_-_iT_邦幫忙__一起幫忙解決難題，拯救_IT_人的一天.png)

因为 React API 的关系，只能通过 Subject 帮助我们把 React Event 转化为 Observable。但绝大多数的情況我們是可以通過 Observable.create 來做到這件事，像下面這樣

![](http://7xlc2a.com1.z0.glb.clouddn.com/30_天精通_RxJS_25_：Subject_總結_-_iT_邦幫忙__一起幫忙解決難題，拯救_IT_人的一天-1.png)


## Subject 与 Observable 区别

永远记得 Subject 其实是 Observer Design Pattern 的实作，所以当 observer 订阅到 subject 时，subject 会把订阅者塞到一份订阅者清单，在元素发送时就是在遍历这份清单，并把元素一一送出，这跟 Observable 像是一个 function 执行是完全不同的(请参考 05 篇)。

Subject 之所以具有 Observable 的所有方法，是因为 Subject 继承了 Observable 的型别，其实 Subject 型别中主要实做的方法只有 next、error、 complete、subscribe 及 unsubscribe 这五个方法，而这五个方法就是依照 Observer Pattern 下去实作的。

总而言之，Subject 是 Observable 的子类别，这个子类别当中用上述的五个方法实作了 Observer Pattern，所以他同时具有 Observable 与 Observer 的特性，而跟 Observable 最大的差异就是 Subject 是具有状态的，也就是储存的那份清单！


# Scheduler

RxJS 可以同时处理同步和异步，所以我们经常搞不清楚 `from`、`range` 这些是同步还是异步送出数据。`Scheduler` 就是解决这个问题的。

## 什么是 Scheduler

Scheduler 控制一个 subscription 的订阅何时开始和何时发送

1. Scheduler 是一个数据结构
2. Scheduler 是执行上下文
3. Scheduler 有一个（虚拟的）时钟

------

RxJS 是通过使用可观察序列来编写异步和基于事件的库

> 把 RxJS 当作事件的 lodash

响应式编程继承自函数式编程：

> RxJS是结合了观察者模式(Observer)，迭代器模式(Iterator)和函数式编程结合，以满足管理事件序列的理想方式的需要

* 核心类型 Observable：表示未来值或事件的可调用集合的思想
* 周边类型 Observer Scheduler Subject
    * Observer：回调函数的集合，这些回调函数知道如何监听 Observable 传递的值
    * Subscribe：表示 Observable 的执行，主要用于取消回调
    * Subject：等同于 EventEmitter，向多个 Observer 广播数值或者事件的唯一方法
    * Schedule：控制并发的集中式调试程序，允许我们当运算在 setTimeout 等发生时进行协调
* 操作符 Operators：纯函数，用于实现函数式编程风格，使用 map、filter、concat 等来处理集合

## Observable

推送多个数值集合的惰性计算


|   | 单值 | 多值 |
| --- | --- | --- |
| 拉 | 函数 | 迭代器 |
| 推 | Promise | Observable |


### 创建 Observable

* 方法一 Rx.Obsevable.create

```js
let source$ = Rx.Observable.create(observer => {
  observer.next(1)
  observer.next(2)
  observer.next(3)

  setTimeout(()=>{
    observer.next(4)
    observer.complete()
  }, 1000)
})

// subscribe: 提供回调处理传递的数据
// 仅当一个 Observable 被订阅时才运行的惰性计算
source$.subscribe({
  next: x => {console.log(x)}
})
```

* 方法二 创建操作符（静态方法） from、of、interval、fromEvent

```js
Rx.Observable.of(1,2,3).subscribe(console.log)

Rx.Observable.from([1,2,3]).subscribe(console.log)
```

### 注销

```js
$source.unsubscribe()
```

## Observer

Observable 推送的数值的消费者，包含一系列回调函数

```js
var observer = {
    next: x => {},
    complete: () => {},
    error: () => {}
}

source$.subscribe(observer)
```

## Subscription

表示 Observable 的执行的一次性资源

```js
let subscription = source$.subscribe(observer)

// leter
subscription.unsubscribe()
```

```js
var source1$ = Rx.Observable.interval(400)
var source2$ = Rx.Observable.interval(300)

var subscription = source1$.subscribe( x => console.log(x))
var childSubscription = source2$.subscribe( x => console.log(x) )

subscription.add(childSubscription)

/*
setTimeout(()=>{
  subscription.remove(childSubscription)
}, 1000)
*/

setTimeout(()=>{
  subscription.unsubscribe()
}, 1200)
```

## Subject

一种特殊的 Observable，允许将数值组播给多个 Observer

Subject 是 Observable ，也是 Observer

```js
var subject = new Rx.Subject()

subject.subscribe({
  next: v => console.log('A', v)
})

subject.subscribe({
  next: v => console.log('B', v)
})

subject.next(1)
subject.next(2)
```

```js
var subject = new Rx.Subject()
subject.subscribe({
  next: v => console.log('A', v)
})
subject.subscribe({
  next: v => console.log('B', v)
})

var observable = Rx.Observable.from([1,2,3])
observable.subscribe(subject)

// 等同于, subject 是组播
var a$ = Rx.Observable.from([1,2,3])

a$.subscribe(v => console.log('A', v))
a$.subscribe(v => console.log('B', v))
```

* 组播 Observable，使用 `multicast` operator 生成一个拥有 conncet 方法 的 ConnectableObservable
* BehaviorSubject - replays one, only before completion
* ReplaySubject - replays many, before or after completion
* AsyncSubject - replays one, only if completed


## Operator

[rxmarbles](http://rxmarbles.com/)

[rxfiddle](http://rxfiddle.net/)

返回一个新的 Observable 的 Observable 方法

* 静态方法
    * create
    * empty
    * of
    * from
    * fromEvent
    * interval
    * timer
* 实例方法 

## Scheduler

控制 Observable 何时执行，通知何时发送


