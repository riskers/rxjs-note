```js
import { of } from 'rxjs'

of('H', 'e', 'l', 'l', 'o')
  .subscribe(e => {
    console.log(e)
  })
```