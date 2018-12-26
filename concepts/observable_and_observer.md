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
const observer = (e) => {
  console.log(e)
}
o$.subscribe(observer)
```