# rxjs note

目前 rxjs 最新版本是 v6，v4 之前的仓库是 https://github.com/Reactive-Extensions/RxJS， v5 迁移到了 https://github.com/ReactiveX/rxjs。

## rxjs 的优势

* Observable 标准化: https://github.com/tc39/proposal-observable
* 多语言的支持: http://reactivex.io/ 中可以看到支持的众多语言，js 只是其中一种

## 学习笔记

rxjs 的重点是 `observable` 和 `observer`，核心知识有 `operators`、`subject` 和 `schedule`。这里是我在学习 rxjs 时记录的概念和有趣的 demo。

> 本书发布在 https://riskers.github.io/rxjs-note/ ，感谢 github pages / gitbook / travis 提供的便利。