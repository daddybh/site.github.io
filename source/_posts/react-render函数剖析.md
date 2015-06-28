title: react render函数剖析
date: 2015-06-28 17:42:55
tags:[react]
---

render
`render: function(nextElement, container, callback)`
src/renderers/dom/client/ReactMount.js#599

render参数：
nextElement 通过react.createElement创建的元素
container:  nextElement的容器
callback:   完成render后的回调函数


step1:

src\shared\vendor\core\invariant.js
invariant 当条件为false时，抛出异常
src\shared\vendor\core\waring.js
warnging  当条件为false时，在控制台打印第二个参数的文字

调用 _renderSubtreeIntoContainer

1. 判断nextElement是否为一个object，且有_isReactElement 属性，并抛出异常

2. 如果容器是body，输出warning信息

3. 检查容器是否已经渲染了其它的component

    如果已经渲染，判断nextElement跟缓存中的element 是否一致
        如果不一致，且缓存中id已经变化，则抛出异常
        如果是同个元素，则更新nodeCache->新传入的rootNode

    如果已经渲染，根据rootID（从getReactRootID获取）， 从instancesByReactRootID缓存中获取一个实例
    并根据实例的key判断是否需要更新，如果需要，则调用_updateRootComponent ，更新元素


    getReactRootID
    `获取容器的firstChild react-dataid 属性`


