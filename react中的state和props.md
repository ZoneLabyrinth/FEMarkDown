前面提过react中的state和props是react组件中的两大部分，有很多人分不清state和props，这节我们就来区分一下他们之间的关系。

很多人觉得react难学就在于它的数据驱动以及它的单向数据流，很多人习惯了jquery时代，习惯了选取元素然后进行dom操作，可现在我们只能操纵props和state来完成想要的效果，这着实让人头疼，可能有人非说react中也可以用jquery，可以用refs获取dom，可这不是react推荐的，只在某一些情形才适合获取dom，大多数情况都是只推荐同state和props来获得我们想要的效果。

### state

state顾名思义就是状态，它只是用来控制这个组件本身自己的状态，我们可以用state来完成对行为的控制、数据的更新、界面的渲染，由于组件不能修改传入的props，所以需要记录自身的数据变化。

#### setState

很多新人开始学习的时候不知道怎么更新state，就直接给state赋值，往往没达到自己想要的效果，其实state的更新需要运用到setState，给state赋值的确会更改state，但是往往我们看电脑屏幕却没达到我们想要的效果。我们来看官网：
 `setState() schedules an update to a component’s state object. When state changes, the component responds by re-rendering`，当我们调用这个函数的时候，React.js 会更新组件的状态 state ，并且重新调用 render 方法，然后再把 render 方法所渲染的最新的内容显示到页面上。我们如果google一下，会搜到大量关于setState的文章

- 当我们调用这个函数的时候，React.js 会更新组件的状态 state ，并且重新调用 render 方法，然后再把 render 方法所渲染的最新的内容显示到页面上
- React.js 并不会马上修改 state。而是把这个对象放到一个更新队列里面，稍后才会从队列当中把新的状态提取出来合并到 state 当中，然后再触发组件更新。
- React setState 更新是异步的

可是怎么更新却没有提及，有的人会似懂非懂。直到我看了好多文章才找到了自己想要的解答。

> setState 方法与包含在其中的执行是一个很复杂的过程，从 React 最初的版本到现在，也有无数次的修改。它的工作除了要更动 this.state 之外，还要负责触发重新渲染，这里面要经过 React 核心 diff 算法，最终才能决定是否要进行重渲染，以及如何渲染。而且为了批次与效能的理由，多个 setState 呼叫有可能在执行过程中还需要被合并，所以它被设计以延时的来进行执行是相当合理的。



![img](https:////upload-images.jianshu.io/upload_images/1559594-2aabb5d371209c7f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600/format/webp)

侵删

```
componentDidMount() {
  document.querySelector('#btn-raw').addEventListener('click', this.onClick);
}
onClick() {
  this.setState({count: this.state.count + 1});
  console.log('# this.state', this.state);
}
// ......
render() {
  console.log('#enter render');
  return (
    <div>
      <div>{this.state.count}
        <button id="btn-raw">Increment Raw</button>
      </div>
    </div>
  )
}
```

代码和示例来源于知乎的[程墨Morgan](https://link.jianshu.com?t=https://www.zhihu.com/people/29448cc28212a543bea56b9492f82e25)和[Lucas HC](https://link.jianshu.com?t=https://www.zhihu.com/people/lucas-hc)

你会发现上面的代码会实时更新，这跟平时说的将state放在队列里不一样。深入源码你会发现：

> 在 React 的 setState 函数实现中，会根据一个变量 isBatchingUpdates 判断是直接更新 this.state 还是放到队列中回头再说，而 isBatchingUpdates 默认是 false，也就表示 setState 会同步更新 this.state，但是，有一个函数 batchedUpdates，这个函数会把 isBatchingUpdates 修改为 true，而当 React 在调用事件处理函数之前就会调用这个 batchedUpdates，造成的后果，就是由 React 控制的事件处理过程 setState 不会同步更新 this.state。

那么我们可以总结一下：

- 由 React 控制的事件处理过程 setState 不会同步更新 this.state！
- React 控制之外的情况， setState 会同步更新 this.state！

但大部份的使用情况下，我们都是使用了 React 库中的表单组件，例如 select、input、button 等等，它们都是 React 库中人造的组件与事件，是处于 React 库的控制之下，比如组件原色 onClick 都是经过 React 包装。在这个情况下，setState 就会以异步的方式执行。

所以一般来说，很多人会误认为 setState 就是异步执行，实际上，绕过 React 通过 JavaScript 原生 addEventListener 直接添加的事件处理函数，还有使用 setTimeout/setInterval 等 React 无法掌控的 APIs情况下，就会出现同步更新 state 的情况。

所以下次遇到这种react面试题你应该就知道答案是什么了吧！

```
import React, { Component } from 'react';

class App extends Component{
  constructor(props){
    super(props)
    this.state = {
      val:0
    }
  }

  componentDidMount(){
    this.setState({val:this.state.val+1})
    console.log(this.state.val)//0

    this.setState({val:this.state.val+1})
    console.log(this.state.val)//0

    this.timer = setTimeout(() => {
      console.log(this.state.val)//1
      this.setState({val:this.state.val+1})
      console.log(this.state.val)//2

      this.setState({val:this.state.val+1})
      console.log(this.state.val)//3
    },0)
  }

  componentWillUnmount(){
    clearTimeout(this.timer)
  }

  render(){
    return <div>{this.state.val}</div>
  }
}


export default App
```

有的人看了一下代码，然后立马说我骗人，明明加了4次，为什么最后的结果没变成4？其实这个setState放在了componentDismount这个生命周期有关，我们将上面代码关于setTimeout的都删除掉会发现我们页面的结果不是0而是1，这就是为什么我们的结果是3了，至于为什么它会吞掉一个1，由于setState不会立即改变React组件中state的值，所以两次setState中this.state.val都是同一个值0，故而，这两次输出都是0。因而val只被加1

最后说个题外话，那么怎么让react强制不用异步更新呢？有三个方法

- 常见的一种做法便是将一个回调函数传入 setState 方法中。即 setState 著名的函数式用法。这样能保证即便在更新被 batched 时，也能访问到预期的 state 或 props。
- 另外一个常见的做法是需要在 setState 更新之后进行的逻辑（比如上述的连续第二次 count + 1），封装到一个函数中，并作为第二个参数传给 setState。这段函数逻辑将会在更新后由 React 代理执行。即：`setState(updater, [callback])`
- 把需要在 setState 更新之后进行的逻辑放在一个合适的生命周期 hook 函数中，比如 componentDidMount 或者 componentDidUpdate 也当然可以解决问题。也就是说 count 第一次 +1 之后，出发 componentDidUpdate 生命周期 hook，第二次 count +1 操作直接放在 componentDidUpdate 函数里面就好啦。

### props

react中说的单向数据流值说的就是props，根据这一特点它还有一个作用：组件之间的通信。props本身是不可变的，但是有一种情形它貌似可变，即是将父组件的state作为子组件的props，当父组件的state改变，子组件的props也跟着改变，其实它仍旧遵循了这一定律：props是不可更改的。

props一定来源于默认属性或者通过父组件传递而来，以下是这个写法：

```
static propProps = {
        val:PropTypes.string.isRequired
}
static defaultProps = {
        val:0
}
```

如果你觉得还是搞不清 state 和 props 的使用场景，那么请记住一个简单的规则：尽量少地用 state，尽量多地用 props。

没有 state 的组件叫无状态组件（stateless component），设置了 state 的叫做有状态组件（stateful component）。因为状态会带来管理的复杂性，我们尽量多地写无状态组件，尽量少地写有状态的组件。这样会降低代码维护的难度，也会在一定程度上增强组件的可复用性。函数式组件就只有props没有state，而react也非常鼓励我们编写函数式组件。

以下给props和state做一个总结：

- props用于定义外部接口，state用于记录内部状态
- props的赋值在于外部世界使用组件，state的赋值在于组件内部
- 组件不应该改变props的值，而state存在的目的就是让组件来修改的

组件的state相当于组件的记忆，其存在的意义就是被修改，每一次通过setState函数修改state就改变了组件的状态，然后通过把渲染过程体现出来。但是组件的props绝不应该被修改，我们可以想象一个场景，一个父组件将同一个props传给几个子组件，若有一个子组件将props修改，那么其他的组件就不一定能得到自己想到的值，此时的情况将混乱不堪。

作者：DCbryant

链接：https://www.jianshu.com/p/2f6d81a15d81

来源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。