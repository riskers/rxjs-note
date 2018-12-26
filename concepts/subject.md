subject: [stackblitz](https://stackblitz.com/edit/rxjs-tm5sj9?embed=1&file=index.ts)

* Subject 同時是 Observable 又是 Observer [stackblitz](https://stackblitz.com/edit/rxjs-coom7d?file=index.ts)
* Subject 會對內部的 observers 清單進行組播(multicast)

-----

Subject 既是 Observable ，又是 Observer

Subject 充当代理和桥梁的作用，正因为如此，才只有一个执行。从 `interval$` Observable 那得到一个新值，然后我将这个值传递给我的所有观察者 (监听者)

[subject demo](https://jsbin.com/nuciruzexe/edit?js,console,output)

何时使用 Subject:

* 需要共享相同的 observable 执行。
* 当需要决定观察者迟来时该怎么做，是否使用 ReplaySubject、BehaviorSubject？
* 需要完全控制 next()、error() 和 completed() 方法。

-----

很多人會直接把這個特性拿來用在 不知道如何建立 Observable 的狀況:

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

因為在 React API 的關係，如果我們想要把 React Event 轉乘 observable 就可以用 Subject 幫我們做到這件事；但絕大多數的情況我們是可以透過 Observable.create 來做到這件事，像下面這樣

```js
const example = Rx.Observable.creator(observer => {
    const source = getSomeSource(); // 某個資料源
    source.addListener('some', (some) => {
        observer.next(some)
    })
});
```

大概就會像上面這樣，如果沒有合適的 creation operators 我們還是可以利用 Observable.create 來建立 observable，除非真的因為框架限制才會直接用 Subject。

## BehaviorSubject

[stackblitz](https://stackblitz.com/edit/rxjs-okmhrl?embed=1&file=index.ts)

## ReplaySubject

[stackblitz](https://stackblitz.com/edit/rxjs-jwa7n9?embed=1&file=index.ts)

可能會有人以為 ReplaySubject(1) 是不是就等同於 BehaviorSubject，其實是不一樣的，BehaviorSubject 在建立時就會有起始值，比如 BehaviorSubject(0) 起始值就是 0，BehaviorSubject 是代表著狀態而 ReplaySubject 只是事件的重放而已

## AsyncSubject

[stackblitz](https://stackblitz.com/edit/rxjs-hweoev?embed=1&file=index.ts)

不常用

## multicast

[stackblitz](https://stackblitz.com/edit/rxjs-afv7vh)