### 生命周期方法

一些最重要的生命周期方法是：

1. **componentWillMount()** – 在渲染之前执行，在客户端和服务器端都会执行。

2. **componentDidMount()** – 仅在第一次渲染后在客户端执行。

3. **componentWillReceiveProps(nextProps)** – 当从父类接收到 props 并且在调用另一个渲染器之前调用。

   **componentWillReceiveProps**  在**初始化render的时候不会执行**，它会在**Component**接受到新的状态(**Props**)时被触发，一般用于父组件状态更新时子组件的重新渲染。这个东西十分好用，但是一旦用错也会造成十分严重的后果，避免修改父组件传入的props值造成的死循环。

   ```js
   componentWillReceiveProps(nextProps){
           if(nextProps.collapseAll !== this.props.collapseAll){
               this.setState({ collapse: false });
               this.setState({data:this.props.titles})
               this.span.current.style.transform = "rotate(0deg)";
           }
       }
   ```

4. **shouldComponentUpdate()** – 根据特定条件返回 true 或 false。如果你希望更新组件，请返回**true** 否则返回 **false**。默认情况下，它返回 false。

5. **componentWillUpdate()** – 在 DOM 中进行渲染之前调用。

6. **componentDidUpdate()** – 在渲染发生后立即调用。

7. **componentWillUnmount()** – 从 DOM 卸载组件后调用。用于清理内存空间。

### Refs

以下是应该使用 refs 的情况：

- 需要管理焦点、选择文本或媒体播放时
- 触发式动画
- 与第三方 DOM 库集成

### **什么是高阶组件（HOC）？**

高阶组件是重用组件逻辑的高级方法，是一种源于 React 的组件模式。 HOC 是自定义组件，在它之内包含另一个组件。它们可以接受子组件提供的任何动态，但不会修改或复制其输入组件中的任何行为。你可以认为 HOC 是“纯（Pure）”组件。

### **你能用HOC做什么？**

HOC可用于许多任务，例如：

- 代码重用，逻辑和引导抽象

- 渲染劫持

- 状态抽象和控制

- Props 控制

  

  ### 你对受控组件和非受控组件了解多少？

  | **受控组件**                                   | **非受控组件**           |
  | :--------------------------------------------- | :----------------------- |
  | 1. 没有维持自己的状态                          | 1. 保持着自己的状态      |
  | 2.数据由父组件控制                             | 2.数据由 DOM 控制        |
  | 3. 通过 props 获取当前值，然后通过回调通知更改 | 3. Refs 用于获取其当前值 |



### **React 中 key 的重要性是什么？**

key 用于识别唯一的 Virtual DOM 元素及其驱动 UI 的相应数据。它们通过回收 DOM 中当前所有的元素来帮助 React 优化渲染。这些 key 必须是唯一的数字或字符串，React 只是重新排序元素而不是重新渲染它们。这可以提高应用程序的性能。

### **MVC框架的主要问题是什么？**

- 对 DOM 操作的代价非常高
- 程序运行缓慢且效率低下
- 内存浪费严重
- 由于循环依赖性，组件模型需要围绕 models 和 views 进行创建

###  **解释一下 Flux**

![img](https://tva1.sinaimg.cn/large/006y8mN6ly1g73u2qufabj30m407awer.jpg)

Flux 是一种强制单向数据流的架构模式。它控制派生数据，并使用具有所有数据权限的中心 store 实现多个组件之间的通信。整个应用中的数据更新必须只能在此处进行。 Flux 为应用提供稳定性并减少运行时的错误。




Redux 由以下组件组成：

1. **Action** – 这是一个用来描述发生了什么事情的对象。
2. **Reducer** – 这是一个确定状态将如何变化的地方。
3. **Store** – 整个程序的状态/对象树保存在Store中。
4. **View** – 只显示 Store 提供的数据。



## DIFF算法

diff算法用于计算出两个virtual dom的差异，是react中开销最大的地方。

传统diff算法通过循环递归对比差异，算法复杂度为O(n3)。

react diff算法制定了三条策略，将算法复杂度从 O(n3)降低到O(n)。

- WebUI中DOM节点跨节点的操作特别少，可以忽略不计。
- 拥有相同类的组件会拥有相似的DOM结构。拥有不同类的组件会生成不同的DOM结构。
- 同一层级的子节点，可以根据唯一的`ID`来区分。

针对这三个策略，react diff实施的具体策略是:

1. diff对树进行分层比较，只对比两棵树同级别的节点。跨层级移动节点，将会导致节点删除，重新插入，无法复用。
2. diff对组件进行类比较，类相同的递归diff子节点，不同的直接销毁重建。diff对同一层级的子节点进行处理时，会根据key进行简要的复用。两棵树中存在相同key的节点时，只会移动节点。

另外，在对比同一层级的子节点时:

diff算法会以新树的第一个子节点作为起点遍历新树，寻找旧树中与之相同的节点。

如果节点存在，则移动位置。如果不存在，则新建一个节点。

在这过程中，维护了一个字段lastIndex，这个字段表示已遍历的所有新树子节点在旧树中最大的index。
在移动操作时，只有旧index小于lastIndex的才会移动。

这个顺序优化方案实际上是基于一个假设，大部分的列表操作应该是保证列表基本有序的。
可以推倒倒序的情况下，子节点列表diff的算法复杂度为O(n2)

![clipboard.png](http://ww3.sinaimg.cn/large/006tNc79ly1g3gyx5eag5j30m80d7ab6.jpg)

![clipboard.png](http://ww3.sinaimg.cn/large/006tNc79ly1g3gyxajknej30lk0bwjsa.jpg)

![clipboard.png](http://ww1.sinaimg.cn/large/006tNc79ly1g3gyxdqmfvj30ju0c0jsb.jpg)

