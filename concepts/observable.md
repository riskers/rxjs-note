## Observable

`Observable` 是一个可以推送多个值的惰性集合。

### Pull VS Push && 单值 VS 多值

|  | 单个值 | 多个值 |
| :---- | :---- | :---- |
| 拉取 | Function | Iterator |
| 推送 | Promise | Observable |

* 拉取 VS 推送
  * 所谓『拉取』，就是**生产者被请求时产生数据，消费者决定何时请求数据**: 在拉取体系中，由消费者来决定何时从生产者那里接收数据，生产者本身不知道数据是何时交付到消费者手中的
  * 所谓『推送』，就是**生产者按自己的节奏产生数据，消费者对收到的数据做出反应**: 在推送体系中，由生产者来决定何时把数据发送给消费者。消费者本身不知道何时会接收到数据
* 单值 VS 多值
  * 所谓单值，就是**只能返回一个值**
  * 所谓多值，就是**可以随着时间的推移“返回”多个值**

### Function

> 单值 + 拉取

```js
/*
 * 函数声明就是生产者，产生数据
 * 函数调用就是消费者，消费数据
 */

function foo() {
  return 42;
}

foo() // 只能得到一个值，永远不会是第二个值
```

### Iterator

> 多值 + 拉取

```js
/*
 * iter 就是生产者
 * next() 是消费者，决定了何时消费
 */

let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();

// 每一次 next() 调用得到的值都不一样，所以是多值
iter.next() // { value: 'a', done: false }
iter.next() // { value: 'b', done: false }
iter.next() // { value: 'c', done: false }
iter.next() // { value: undefined, done: true }
```

### Promise

> 单值 + 推送

```js
// 完全由 Promise 对象控制何时发送数据: then()
new Promise((resolve, reject) => {
  // ...
})
.then(data => {
  // ...  // 只能产生一个值
})
```

### Observable

> 多值 + 推送

对于 Observable 来说这个反应是 `subscribe`

```js
// 完全由 Observable 对象控制何时发送数据: subscribe
const observable$ = Observable.create(observer => {

  // 每一次 next() 输出的值都不一样，所以是多值
  observer.next('hi')
  observer.next('riskers')
  observer.next('hello world')

  setTimeout(() => {
    observer.next('ee')
  }, 2000)

  observer.next('last')
})

observable$.subscribe({
  next: (value) => console.log(value),
  error: err => console.log(err),
  complete: () => console.log('complete'),
})
```
