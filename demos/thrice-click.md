
```js
import { fromEvent } from 'rxjs';
import { map, buffer, debounceTime } from 'rxjs/operators';

const mouse$ = fromEvent(document, 'click')

const buff$ = mouse$.pipe(
  debounceTime(250),
)

const click$ = mouse$.pipe(
  buffer(buff$),
  map(list => {
    return list.length;
  }),
  filter(x => x === 2),   // 这里不判断就可以判断点击次数
)

click$.subscribe(() => {
  console.log('doubleclick')
})
```