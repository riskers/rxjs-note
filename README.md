# rxjs note

目前 rxjs 最新版本是 v6，v4 之前的仓库是 https://github.com/Reactive-Extensions/RxJS，v5 迁移到了 https://github.com/ReactiveX/rxjs。

## rxjs 的优势

* Observable 标准化: https://github.com/tc39/proposal-observable
* 多语言的支持: http://reactivex.io/ 中可以看到支持的众多语言，js 只是其中一种

## 学习笔记

rxjs 的知识体系为:

```
* 两个基本点
  * observable
  * observer
* 三大核心
  * operators
  * subject
  * schedule
```

这里是我在学习 rxjs 时记录的概念和有趣的 demo:

* 概念
  * [observable](./concepts/observable.md)
  * [observer](./concepts/observer.md)
  * operators
  * [subject](./concepts/subject.md)
  * schedule
* 操作符
* 与框架的结合
  * React
  * Angular
* 调试
