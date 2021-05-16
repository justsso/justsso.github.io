
---
title: 聊聊react fiber架构
date: 2021-05-02
---


本篇文章的大纲也就是写作的思路如下：
1. react fiber架构是什么？
2. react fiber出现的时间节点
3. 为什么要有fiber架构，解决了什么问题？
4. react fiber架构是怎么做的？实现原理

react fiber是一种表示react节点的数据结构，现在我们所说的react fiber通常是指一种架构，react在16版本以后采用fiber架构

react fiber是在v16出现的，重写了v15中的底层，并且对外输出的api保持不变。

那么v15有什么问题呢，才会被推翻，被fiber架构取代。

v15中的render渲染是栈调和的，基于栈和深度递归，一直递归到最底层才会返回，如果执行的时间超过16ms(浏览器1s60帧)，就会出现卡顿现象。所以必须要解决这个问题。
我们希望react调和是怎样的呢，我们希望它可以暂停，在当前可用时间小于一帧的情况下，暂停，然后还能继续恢复。然后还有在有更高优先级的时候，我们希望它可以先去处理更高优先级，处理完了之后再来接着处理这个问题。
基于我们想要实现的效果，react采用了更先进的理念，纤程（比线程更小的单位），自己实现纤程，做到可以暂停，可以继续的架构，这底层的结构就叫fiber

那么fiber是怎么做的呢，可以实现可中断、可继续。fiber分为两部分 render和commit阶段，render阶段是可以停下来，再接着执行的，所以可能会被执行多次，而commit会和底层的dom操作有关，是不能被停下来的。

随着fiber架构思想的出现，fiber还带来”周边“的变化，那就是生命周期的变化，直接说是 react 15中的compomentWillDidMount componentWillUpdate componentWillRecivedProps 因为这三个生命周期是在render阶段，又可能会被重复执行，而开发者又有时会滥用，把一些副作用的代码写在这三个生命周期中。
所以react团队去掉了这三个生命周期，加入了getDerivedStateFromProps getSnapshotBeforUpdate 这两个静态方法，在静态方法中是拿不到this的




---
react hooks的设计动机与工作模式

hooks是只能在function组件中使用，到现在react 团队都是在推崇使用function组件的。那么为什么是function 组件了呢？
因为function组件更加符合react的理念，那就是  UI = f(data)，在function 中渲染可以读取到data。函数组件会捕获render内部的状态，这是和class组件最大的不同。

Hooks本质:一套能够使函数组件更强大、更灵活的”钩子“

函数组件比起类组件"少"了很多东西，比如生命周期、对state的管理。React-Hooks的出现，就是为了帮助函数组件补齐这些（相对于类组件来说，但生命周期这些也不是必须，只是完成一件事情的步骤代号而已）

