* concatMap: 拼接
* mergeMap: 交错
* switchMap: 源 Observable 发出值时会取消内部 Observable 先前的所有订阅





concat / merge / switch 可以放在一起学习，每组都还有 concatAll / concatMap 这样的方法，

其中，concat 与 concatAll 的关系是：

![](https://github-riskers-blog.oss-cn-qingdao.aliyuncs.com/20181212170029.png)

-----

而 concatMap 其实就是 map + concatAll 的语法糖:

![](https://github-riskers-blog.oss-cn-qingdao.aliyuncs.com/20181212170106.png)
