react

生命周期

伪类伪元素 :empty

redux

dangerouslyinserthtml

fiber

hooks(实现类组件)

排序

redux

排序

语义化

网页渲染

事件循环

setState()更新

refs

纯组件和类组件/高阶组件

connect

generator

promise/all/race

节流防抖

箭头函数

box-sizing

em rem vw 

umi

### 简述 flux 思想

Flux 的最大特点，就是数据的"单向流动"。

1. 用户访问 View
2. View 发出用户的 Action
3. Dispatcher 收到 Action，要求 Store 进行相应的更新
4. Store 更新后，发出一个"change"事件
5. View 收到"change"事件后，更新页面