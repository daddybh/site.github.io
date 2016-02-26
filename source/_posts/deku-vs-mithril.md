title: deku vs mithril
date: 2016-02-26 14:13:46
tags: [deku,mithril,react,jsx,mvc]
---

deku 和 mithril都是新近前端框架，两个库都是基于virtual dom的轻量级库，下面就各方面对这两个库进行对比：

## 功能对比

|                 |     deku          |     mithril    |
|---------------- |-------------------|----------------|
| virtual dom     |    yes            |      yes       |
| size            |   <10kb gzip      |    <=7kb gzip  |
| router          |   no              |     yes        |
| jsx             |   yes             |    no(msx)     |
| controller      |   no              |    yes         |
| model/view-model|   no              |    yes         |
| docs            |   yes             |    yes         |
| flux            |   yes             |    no          |
| mvc             |   yes             |    no          |

总结deku是一个：
* 类react 的view库
* 支持jsx语法
* 单向数据流

mithril：
* 完整mvc框架
* 内置路由
* 文档完善


## 总结

* deku与react的jsx语法，熟悉react的很容易上手
* deku方便react之间切换
* 两库大小不相上下，但是deku需要引入路由库，另外解决路由问题
* deku文档没有mithril完善
* mithril为传统的mvc，而deku只是一个view层

总结：`按需选择`，熟悉react的可考虑用deku，熟悉backbone等mvc的可以考虑mithril


## 社区热度

github热度

|        | star  | fork  | contribute | Last Issue | 
|--------|-------|-------|------------|------------|
|deku    | 2434  |  99   |   40       |  5 days ago|
|mithril | 5561  |  420  |   114      |  1 days ago|