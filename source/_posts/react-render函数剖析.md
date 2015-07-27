title: react render函数剖析
date: 2015-06-28 17:42:55
tags: [react]
---

react通过一个React.render函数来渲染启动一个app，具体这个函数都做了些什么事情？其实jsx编译过后，render函数中的html都转换成一堆`React.createElement`的代码，最终生成一个`ReactElement`传入到render函数

## ReactElement数据结构

`React.createElement` 接收函数接收两个及以上参数:
* type 通过React.createClass创建的类型
* props 元素的属性值
* 其它参数： 该元素的直接子级元素

最终生成ReactElement是一个树形结构，子元素保存在`_store.props`属性中

{% asset_img ReactElement.png [ReactElement树形结构] %}

## render函数

ReactElement树创建之后，通过React.render方法，将树生成具体的html，并渲染到对应的容器中。

#### 参数

* nextElement ReactElement对象，通过React.createElement创建
* container 页面容器
* callback 回调函数

## render主要流程

1. 对nextElement的合法性进行校验
2. 检查container是否已经渲染过React组件
3. 开始调用`_renderNewRootComponent`渲染

#### nextElement 合法性校验

主要通过ReactElement的`_isReactElement` 属性判断，如果非法则抛出异常


#### 检查container是否已经渲染过React组件

1. 根据ReactElement，从缓存查找是否存在preComponent
2. 调用shouldUpdateReactComponent，检查是更新，还是替换原来节点

    如果需要更新，调用ReactMount._updateRootComponent更新原来节点(具体细节后续再补充，本文先针对正常流程)

    如果不需要更新，则调用unmountComponentAtNode，删除旧节点，然后执行一个全新的mount操作


#### _renderNewRootComponent内部调用流程图

*Transaction概念*

```
从源代码的注释可以看到，Transaction.perform 方法提供了一个执行函数的黑盒，在函数开始时执行wrapper的`initialize`方法，结束后调用`close`方法。Transaction作为一个基类，其它Transaction可以从它继承perform方法。
```

TransactionWrapper，是一个提供`initialize`和`close`方法的简单对象

Transaction 可以通过重写`getTransactionWrappers`来返回wrapper数组

Transaction 主要用途：

* 恢复input的选中状态
* 在渲染时，禁用类似blur/focus事件，渲染结束后，重新启用
* 批量更新DOM元素的修改
* 渲染新内容后，调用所有的`componentDidUpdate`函数


_renderNewRootComponent 使用两个Transaction：

* ReactDefaultBatchingStrategyTransaction
* ReactReconcileTransaction

下面是一个渲染全新React组件的核心流程:

{% asset_img _renderNewRootComponent.png [render内部函数调用关系] %}

