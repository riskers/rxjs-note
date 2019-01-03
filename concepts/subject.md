## Subject

首先确定一个概念，**Subject 即是 Observable 也是 Observer**:

[stackblitz](https://stackblitz.com/edit/rxjs-tm5sj9)

Subject 充当代理和桥梁的作用，正因为如此，才只有一个执行。从 `interval$` Observable 那得到一个新值，然后我将这个值传递给我的所有观察者 (监听者)。

<!-- 何时使用 Subject:

* 需要共享相同的 observable 执行。
* 当需要决定观察者迟来时该怎么做，是否使用 ReplaySubject、BehaviorSubject？
* 需要完全控制 next()、error() 和 completed() 方法。 -->

## Cold Observable 的问题

cold ovservable 是无法多播的，因为数据不同步: [stackblitz](https://stackblitz.com/edit/rxjs-rjua2a)

## 多播

而将 cold -> hot 后，就可以多播了: [stackblitz](https://stackblitz.com/edit/rxjs-g62yrj)

[Subject 实现的多播](https://stackblitz.com/edit/rxjs-jzb8d8)

## subject 不能重复使用

[stackblitz](https://stackblitz.com/edit/rxjs-3gkwka)

很多人會直接把這個特性拿來用在不知道如何建立 Observable 的狀況:

```js
class MyButton extends React.Component {
    constructor(props) {
        super(props);
        this.state = { count: 0 };
        this.subject = new Rx.Subject();

        this.subject
            .mapTo(1)
            .scan((origin, next) => origin + next)
            .subscribe(x => {
                this.setState({ count: x })
            })
    }
    render() {
        return <button onClick={event => this.subject.next(event)}>{this.state.count}</button>
    }
}
```

因为 React API 的关系，如果我們想要把 React Event 转换成 observable 就可以用 Subject 幫我們做到這件事；但绝大多数的情況我們是可以透過 `Observable.create` 來做到這件事，像下面这样:

```js
import { Observable } from 'rxjs'

const example = Observable.create(observer => {
    const source = getSomeSource(); // 某個数据源
    source.addListener('some', (some) => {
        observer.next(some)
    })
});
```

大概就會像上面這樣，如果沒有合適的 creation operators 我們還是可以利用 Observable.create 來建立 observable，除非真的因為框架限制才會直接用 Subject。

## BehaviorSubject

很多時候希望 Subject 能代表當下的状态，也就是說如果今天有一個新的 subscribe，我們希望 Subject 能立即給出最新的值，而不是沒有值:

[stackblitz](https://stackblitz.com/edit/rxjs-okmhrl)

## ReplaySubject

希望 Subject 代表事件，但又能在新 subscribe 时重新发送最后的几個元素

[stackblitz](https://stackblitz.com/edit/rxjs-jwa7n9)

> 可能會有人以為 ReplaySubject(1) 是不是就等同於 BehaviorSubject，其實是不一樣的，BehaviorSubject 在建立時就會有起始值，比如 BehaviorSubject(0) 起始值就是 0，BehaviorSubject 是代表著狀態而 ReplaySubject 只是事件的重放而已

## AsyncSubject

AsyncSubject 会在 subject 结束后送出最后一個值，其实这个行为跟 Promise 很像，都是等到事情结束后送出一個值，但实际上我們非常非常少用到 AsyncSubject，絕大部分的時候都是使用 BehaviorSubject 跟 ReplaySubject 或 Subject。

[stackblitz](https://stackblitz.com/edit/rxjs-hweoev)


## 多播操作符

### multicast

refCount: 建立自动 connect 的 Observable

[stackblitz](https://stackblitz.com/edit/rxjs-afv7vh)

[multicast operator 实现的多播](https://stackblitz.com/edit/rxjs-3aaggb)

### publish

`multicast` 的简写:

```js
var source = interval(1000).pipe(
    publish(),
    refCount()
)

// 等同于:
/* var source = interval(1000).pipe(
    multicast(new Rx.Subject()),
    refCount()
) */
```

变体:

* publishBehavior

    ```js
    var source = interval(1000).pipe(
        publishBehavior(0),
        refCount()
    )

    // 等同于:
    /* var source = interval(1000).pipe(
        multicast(new Rx.BehaviorSubject(0)),
        refCount()
    ) */
    ```

* publishReplay

    ```js
    var source = interval(1000).pipe(
        publishReplay(1),
        refCount()
    )

    // 等同于:
    /* var source = interval(1000).pipe(
        multicast(new Rx.ReplaySubject(1)),
        refCount()
    ) */
    ```

* publishLast

    ```js
    var source = interval(1000).pipe(
        publishLast(),
        refCount()
    )

    // 等同于:
    /* var source = interval(1000).pipe(
        multicast(new Rx.AsyncSubject(1)),
        refCount()
    ) */
    ```

### share

`publish` + `refCount` 的简写:

```js
var source - interval(1000).pipe(
    share(),
)

/* var source = interval(1000).pipe(
    publish(),
    refCount()
) */

/* var source = interval(1000).pipe(
    multicast(new Rx.Subject()),
    refCount()
) */
```

[share operator 实现的多播](https://stackblitz.com/edit/rxjs-3nhqpt)