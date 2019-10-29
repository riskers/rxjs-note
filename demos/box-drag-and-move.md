[stackblitz](https://stackblitz.com/edit/rxjs-mouse-move?embed=1&file=index.ts)

这是一个复杂的 stream，用 marbles 表示:

```bash
move$:  -(m)--(m)--(m)--(m)--(m)--(m)----
             -----------------
            |    takeUntil    |
             -----------------
up$:    ---------------------------(u)---
                    |
endMove$: -(m)--(m)--(m)--(m)--(m)--|
```

```bash
down$:      -(d)-------------------------
                        map
endMove$:   -(m)--(m)--(m)--(m)--(m)--|
                         |
            -(o)-------------------------
              \
               (mouseEvent)
                      concatAll
            -(mouseEvent)----------------
                        map
            -(x, y)----------------------
```
