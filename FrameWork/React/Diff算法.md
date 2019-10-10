## React的Diff算法

当比较两棵树时，React首先会比较两个根元素。接下的比较会随着根元素的类型而异

### 元素的不同类型

每当根元素具有不同类型时，React都会拆开旧的树并从头开始构建新树。

当拆开树时，旧的DOM节点旧会被销毁。组件实例会接收 `componentWillUnmount()`生命周期函数。当建立一颗新树时，新的DOM节点会被插入到DOM中。组件实例会重新运行`componentWillMount`和`componentDidMount()`。和旧的树关联的任何`state`都会丢失。

### 相同类型的DOM元素

当比较两个相同类型的React DOM元素时，React会关注两者的属性，保留相同的基础DOM节点，然后仅更新更改过的属性。

处理过DOM节点后，React会继续在子节点上递归。

### 相同类型的组件元素

当一个组件更新时，实例保持不变，因此，`state`在渲染之间仍然保持。React更新了基础组件实例的`props`来匹配新的元素，并且在基础实例上调用`comopnentWillReceieveProps()`和`componentWillUpdate()`方法。

接下来，`render()`方法被调用然后diff算法根据先前的结果和新的结果进行递归。

### 递归子节点

默认情况下，当递归DOM的子节点时，react只是同时遍历两个子节点列表，并在存在差异时产生一个突变。

#### Keys

为了解决列表问题，React提供了`key`属性。当子节点有`key`时，react使用key来匹配在原树中和新树中的自带进行匹配。