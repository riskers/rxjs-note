# 操作符

[learn rxjs operators](https://github.com/RxJS-CN/learn-rxjs-operators) 已经很详细了，这里补充一些有意思的资料和总结。

* scan: 顧名思義【掃描】就是將傳入 RxJS 運算子的事件資料，一筆一筆處理過一遍，每次處理的過程都會累加，並且處理一筆就 emit 一筆新的事件資料出來。
* map: 顧名思義【對應】就是將一筆傳入的資料，對應到另一種格式的資料，通常用來轉換資料之用。
*concat: 顧名思義【串接】就是將一筆一筆的資料串接在一起，通常是把多個 Observable 物件串接成一個新的 Observable 物件。
* switch: 顧名思義【交換】就是將一筆資料 "交換成" 另一種資料，當連續交換的事件發生時，還沒交換的資料就會被放棄。
* merge: 顧名思義【合併】就是將多筆資料合併成一筆，通常是將多個 Observable 物件合併成一個 Observable 物件。
* flat: 其用途跟 merge 完全一樣，在語意上，通常代表把多個 Observable 物件「壓平」成一個 Observable 物件。
* exhaust: 顧名思義【耗盡】就是要把原本 Observable 物件送出的資料都跑完，才能繼續跑下一個。
* zip: 這個單字有【拉鍊】的意思，你要發揮一點想像力，假設衣服的兩端，拉鍊拉起來之後，每一節都會平整的被湊在一起。
* combine: 顧名思義【組合】就是將多筆資料組合在一起。
* forkJoin: 這裡的 fork 是【走進岔路】的意思，Join 則是【從不同的岔路合併回來】。通常意思代表多個 Observable 物件同時執行，但全部執行完之後，才會 emit 所有資料，等大家從岔路走回來的感覺。

-----

* https://zhuanlan.zhihu.com/p/39359316： 里面的图非常有意思，多观察观察。

![](https://github-riskers-blog.oss-cn-qingdao.aliyuncs.com/20181214220218.png)

* xxxAll: 顧名思義【全部】就是將所有傳入的 Observabe 物件，全部一起處理。
* xxxLatest: 顧名思義【最新的】就是取得最新資料的意思。
* xxxTo: 這裡的 To 有【表示結果】的意思，也就是直接把特定結果 emit 出去。
* xxxMap: 這裡的 Map 有【對應】的意思，但是在 RxJS 的領域中，通常代表著在 Operator 中會對應到另一個不同的 Observable 物件。
* xxxMapTo: 這裡就是 Map + To 的意思，但是在 RxJS 的領域中，通常代表著在 Operator 中會對應到另一個自行指定的 Observable 物件。
* xxxScan: 顧名思義【掃描】就是將傳入 RxJS 運算子的事件資料，一筆一筆處理過一遍，每次處理的過程都會累加，並且處理一筆就 emit 一筆新的事件資料出來。

> ---- https://blog.miniasp.com/post/2018/09/06/Clarify-some-confused-RxJS-operators.aspx

