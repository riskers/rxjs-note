How?

```js
// const m = (cb) => {
//   return Observable.create((observer) => {
//     return this.subscribe(
//       value => {
//         observer.next(cb(value))
//       }
//     )
//   })
// }

// from(['H', 'e', 'l', 'l', 'o'])
//   .pipe(
//     map(item => item + '~~~~')
//   )
//   .subscribe(e => {
//     console.log(e)
//   })
```

每個 operator 都會回傳一個新的 observable，而我們可以透過 create 的方法建立各種 operator。

