```js
// observable:
const o$ = Observable.create(observer => {
  observer.next('hi')
  observer.next('riskers')
  observer.next('hello world')

  setTimeout(() => {
    observer.next('ee')
  }, 2000)

  observer.next('zz')
})

// subscribe observable 的对象称为 observer,
// 具有 next / error / complete 三个方法:
const observer = {
  next: (value) => console.log(value),
  error: err => console.log(err),
  complete: () => console.log('complete'),
}
o$.subscribe(observer)
```

subscribe 可以一个严格按照顺序的参数:

```js
o$.subscribe(
  (val) => {console.log(val)},
  err => console.log(err),
  () => console.log('complete'),
)
```