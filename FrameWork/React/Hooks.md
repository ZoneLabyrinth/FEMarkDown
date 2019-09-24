## 为什么要使用添加hooks

### 组件之间复用状态逻辑很难 （复用状态逻辑）

React 没有提供将可复用性行为“附加”到组件的途径（例如，把组件连接到 store）。如果你使用过 React 一段时间，你也许会熟悉一些解决此类问题的方案，比如 [render props](https://zh-hans.reactjs.org/docs/render-props.html) 和 [高阶组件](https://zh-hans.reactjs.org/docs/higher-order-components.html)。但是这类方案需要重新组织你的组件结构，这可能会很麻烦，使你的代码难以理解。如果你在 React DevTools 中观察过 React 应用，你会发现由 providers，consumers，高阶组件，render props 等其他抽象层组成的组件会形成“嵌套地狱”。尽管我们可以[在 DevTools 过滤掉它们](https://github.com/facebook/react-devtools/pull/503)，但这说明了一个更深层次的问题：React 需要为共享状态逻辑提供更好的原生途径。

你可以使用 Hook 从组件中提取状态逻辑，使得这些逻辑可以单独测试并复用。**Hook 使你在无需修改组件结构的情况下复用状态逻辑。** 这使得在组件间或社区内共享 Hook 变得更便捷。

### 复杂组件变得难以理解（状态逻辑混乱）

我们经常维护一些组件，组件起初很简单，但是逐渐会被状态逻辑和副作用充斥。每个生命周期常常包含一些不相关的逻辑。例如，组件常常在 `componentDidMount` 和 `componentDidUpdate` 中获取数据。但是，同一个 `componentDidMount` 中可能也包含很多其它的逻辑，如设置事件监听，而之后需在 `componentWillUnmount` 中清除。相互关联且需要对照修改的代码被进行了拆分，而完全不相关的代码却在同一个方法中组合在一起。如此很容易产生 bug，并且导致逻辑不一致。

在多数情况下，不可能将组件拆分为更小的粒度，因为状态逻辑无处不在。这也给测试带来了一定挑战。同时，这也是很多人将 React 与状态管理库结合使用的原因之一。但是，这往往会引入了很多抽象概念，需要你在不同的文件之间来回切换，使得复用变得更加困难。

为了解决这个问题，**Hook 将组件中相互关联的部分拆分成更小的函数（比如设置订阅或请求数据）**，而并非强制按照生命周期划分。你还可以使用 reducer 来管理组件的内部状态，使其更加可预测。

我们将在[使用 Effect Hook](https://zh-hans.reactjs.org/docs/hooks-effect.html#tip-use-multiple-effects-to-separate-concerns) 中对此展开更多讨论。

### 难以理解的 class（this绑定，class压缩）

除了代码复用和代码管理会遇到困难外，我们还发现 class 是学习 React 的一大屏障。你必须去理解 JavaScript 中 `this` 的工作方式，这与其他语言存在巨大差异。还不能忘记绑定事件处理器。没有稳定的[语法提案](https://babeljs.io/docs/en/babel-plugin-transform-class-properties/)，这些代码非常冗余。大家可以很好地理解 props，state 和自顶向下的数据流，但对 class 却一筹莫展。即便在有经验的 React 开发者之间，对于函数组件与 class 组件的差异也存在分歧，甚至还要区分两种组件的使用场景。

另外，React 已经发布五年了，我们希望它能在下一个五年也与时俱进。就像 [Svelte](https://svelte.dev/)，[Angular](https://angular.io/)，[Glimmer](https://glimmerjs.com/)等其它的库展示的那样，组件[预编译](https://en.wikipedia.org/wiki/Ahead-of-time_compilation)会带来巨大的潜力。尤其是在它不局限于模板的时候。最近，我们一直在使用 [Prepack](https://prepack.io/) 来试验 [component folding](https://github.com/facebook/react/issues/7323)，也取得了初步成效。但是我们发现使用 class 组件会无意中鼓励开发者使用一些让优化措施无效的方案。class 也给目前的工具带来了一些问题。例如，class 不能很好的压缩，并且会使热重载出现不稳定的情况。因此，我们想提供一个使代码更易于优化的 API。

为了解决这些问题，**Hook 使你在非 class 的情况下可以使用更多的 React 特性。** 从概念上讲，React 组件一直更像是函数。而 Hook 则拥抱了函数，同时也没有牺牲 React 的精神原则。Hook 提供了问题的解决方案，无需学习复杂的函数式或响应式编程技术。

### 使用规则

- 只能在 **函数最外层** 调用Hook。不要在循环、条件判断或者子函数中调用（确保顺序）
- 只能在 **React的函数组件** 中调用Hook。



## 底层原理

### React 是如何把对 Hook 的调用和组件联系起来的？

React 保持对当先渲染中的组件的追踪。多亏了 [Hook 规范](https://zh-hans.reactjs.org/docs/hooks-rules.html)，我们得知 Hook 只会在 React 组件中被调用（或自定义 Hook —— 同样只会在 React 组件中被调用）。

每个组件内部都有一个「记忆单元格」列表。它们只不过是我们用来存储一些数据的 JavaScript 对象。当你用 `useState()` 调用一个 Hook 的时候，它会读取当前的单元格（或在首次渲染时将其初始化），然后把指针移动到下一个。这就是多个 `useState()` 调用会得到各自独立的本地 state 的原因。

### 

### Hooks

#### useState （状态）

通过函数组件调用给函数组件添加一些内部的state。React会在重复渲染时保留这个state。`useState`会返回一对值： **当前**状态和一个让你更新它的函数（`const [count,setCount] =useState(0)`），可以在事件处理函数中或其他一些地方调用这个函数。类似`class`中的`this.setState`，但是它 **不会把新的state和旧的state进行合并**。

- 做了什么：定义了一个“state"变量。一般函数退出后变量就会消失，而state中的变量则会被React保留。
- 参数：唯一的参数就是state初始值。不同于class的是，可以不一定是对象。存储不同变量多次调用即可
- 返回值：当前state以及更新state的函数。







#### useEffect （副作用）

`useEffect`给函数组件增加操作副作用的能力。它跟 `class` 组件中的 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 具有相同的用途，只不过被合并成了一个 API

当调用`useEffect`时，React会在完成DOM更改后运行 ”副作用“ 函数。由于副作用函数是在组件内部声明的，它可以访问到组件的 **props**和 **state**。默认情况下，React会在每次渲染后调用副作用函数--- **包括第一次渲染的时候**



副作用函数可以通过返回一个函数来指定“清除“副作用。

```js
 useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);

    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```



- 做了什么：使用该hook，React组件会在渲染后执行某些操作。React会保存你传递的函数，并在 **执行DOM更新之后调用它**。可以执行数据获取或调用其他命令式的API
- 为什么在组件内部调用：放在组件内可以在effect函数中直接访问 state变量（或者其他props）。不需要其他特殊API来读取它——它已经保存在作用域中了。Hook使用了js闭包机制，而不用在 JavaScript 已经提供了解决方案的情况下，还引入特定的 React API。
- 执行：**每次渲染后都会执行**。在**第一次渲染后和每次更新之前**都会执行。React保证了每次运行`effect`同时，DOM都已经更新完毕

与 `componentDidMount` 或 `componentDidUpdate` 不同，使用 `useEffect` 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。大多数情况下，effect 不需要同步地执行。在个别情况下（例如测量布局），有单独的 [`useLayoutEffect`](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/hooks-reference.html#uselayouteffect) Hook 供你使用，其 API 与 `useEffect` 相同。

- 为什么返回一个函数：讲事件逻辑放在一起
- 何时清除Effect：在组件卸载时执行清除操作。每次执行effect时，会对上一个effect进行清除，保证props等发生改变而不更新ui的bug。



##### 跳过清除：判断第二个参数

```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新
```

如果你要使用此优化方式，请确保数组中包含了**所有外部作用域中会随时间变化并且在 effect 中使用的变量**，否则你的代码会引用到先前渲染中的旧变量

如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（`[]`）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。这并不属于特殊情况 —— 它依然遵循依赖数组的工作方式。

如果你传入了一个空数组（`[]`），effect 内部的 props 和 state 就会一直拥有其初始值。尽管传入 `[]` 作为第二个参数更接近大家更熟悉的 `componentDidMount` 和 `componentWillUnmount` 思维模式，但我们有[更好的](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/hooks-faq.html#is-it-safe-to-omit-functions-from-the-list-of-dependencies)[方式](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often)来避免过于频繁的重复调用 effect。除此之外，请记得 React 会等待浏览器完成画面渲染之后才会延迟调用 `useEffect`，因此会使得额外操作很方便。







