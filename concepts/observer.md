## Observer

`Observer` 是 Observable 发送的值的消费者。

```js
// 观察者只是有三个回调函数的对象，每个回调函数对应一种 Observable 发送的通知类型:
const observer = {
  next: (value) => console.log(value),
  error: err => console.log(err),
  complete: () => console.log('complete'),
}

// 要使用观察者，需要把它提供给 Observable 的 subscribe 方法:
observable$.subscribe(observer)
```

subscribe 也可以是一个严格按照顺序的参数:

```js
observable$.subscribe(
  (val) => {console.log(val)},
  err => console.log(err),
  () => console.log('complete'),
)
```