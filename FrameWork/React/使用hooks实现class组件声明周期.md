### constructor

函数组件不需要构造函数，可以通过`useState()`来初始化。

### getDerivedStateFromProps

此生命周期尽量少使用。容易造成`props`和`state`混乱。

你可以在渲染过程中更新 state 。React 会立即退出第一次渲染并用更新后的 state 重新运行组件以避免耗费太多性能。

```js
function ScrollView({row}) {
  let [isScrollingDown, setIsScrollingDown] = useState(false);
  let [prevRow, setPrevRow] = useState(null);

  if (row !== prevRow) {
    // Row 自上次渲染以来发生过改变。更新 isScrollingDown。
    setIsScrollingDown(prevRow !== null && row > prevRow);
    setPrevRow(row);
  }

  return `Scrolling down: ${isScrollingDown}`;
}
```



### shouldComponentUpdate

类组件中有`PureComponent`实现的浅比较以及`shouldComponentUpdate()`周期函数可以用于性能优化。在`hooks`中，可以使用`React.memo`来做优化。

`React.memo`不是一个Hook，它等同于`PureComponent`，但它只比较`props`，因为没有单一的`state`可以比较。

`React.memo`接收第二个参数来指定自定义比较`props`的函数。如果返回true,则跳过更新。

```js
function App() {

  const [count,setCount] = useState(0);
  const [val,setVal] = useState('');


  return (
    <div className="App">
      {count}
      <Child count={count}/>
      <button onClick={()=>setCount(count+1)}>点击+1</button>
      <input value={val} onChange={e=> setVal(e.target.value)} />
    </div>
  );
}
//一般情况
function Child(props){
    console.log('Child');

    return (
        <div>
            {props.count}
        </div>
    )

}

```

![no](https://tva1.sinaimg.cn/large/006y8mN6ly1g8qz14ic43g30i80beacx.gif)

```js
//使用memo
const Child = React.memo(function(props){
    console.log('Child');

    return (
        <div>
            {props.count}
        </div>
    )
})
```

![yes](https://tva1.sinaimg.cn/large/006y8mN6ly1g8qz15txnxg30i80bego2.gif)

使用了`React.memo`之后，父组件传入子组件的props不改变时，子组件不会发生渲染。

或者使用useMemo同样能达到优化效果

```js
function App() {
  const [count, setCount] = useState(0);
  const [val, setVal] = useState('');
  const child = useMemo(() => <Child count={count} /> , [count]) //使用useMemo缓存组件
  return (
    <div className="App">
      {count}
      {child} //调用
      <button onClick={() => setCount(count + 1)}>点击+1</button>
      <input value={val} onChange={e => setVal(e.target.value)} />
    </div>
  );
}
```



### forceUpdate

在`hooks`中，每当`state`更新都会发生一次渲染，可以利用如此特性来实现`forUpdate`。

```js
function App(){
	const [forceUpdate, setForceUpdate] = useState(false);
	
	function handlerUpdate(){
		setForceUpdate(!forceUpdate);
	}
		
  return (
  	 //....组件
  ) 
}
```

注意检查逻辑，一般逻辑没有错误的情况下不需要，`hooks`各个依赖还是很明确的。

### componentDidMount/componentDidUpdate/componentWillUnMount

`useEffect`是`componentDidMount/componentDidupdate/componentWillUnMount`三个周期函数的组合，

可以使用`useEffect`实现这三个声明周期

```js
//componentDidMount
const useDidMount = (fn) => {
		return useEffect(()=> {
      if(fn){
         fn();
      }
    },[]);
}
//componentDidUpdate
const useDidUpdate = (fn,dependency) => {
		return useEffect(()=> {
      if(fn){
         fn();
      }
    },[...dependency]);
}
//componentWillUnmount
const useUnmount = (fn) => {
		return useEffect(()=>{
      return fn && fn(),   //useEffect返回函数会在销毁时执行
    },[])
}

```



### componentDidCatch / getDerivedStateFromError

目前还没有实现此方法，很快会加入。

