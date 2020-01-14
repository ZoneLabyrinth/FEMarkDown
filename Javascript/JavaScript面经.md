### 原型 / 构造函数 / 实例

- 原型`(prototype)`: 一个简单的对象，用于实现对象的 **属性继承**。可以简单的理解成对象的爹。在 Firefox 和 Chrome 中，每个`JavaScript`对象中都包含一个`__proto__` (非标准)的属性指向它爹(该对象的原型)，可`obj.__proto__`进行访问。
- 构造函数: 可以通过`new`来 **新建一个对象** 的函数。
- 实例: 通过构造函数和`new`创建出来的对象，便是实例。 **实例通过\_\_proto\_\_指向原型，通过constructor指向构造函数**。

说了一大堆，大家可能有点懵逼，这里来举个栗子，以`Object`为例，我们常用的`Object`便是一个构造函数，因此我们可以通过它构建实例。

```
// 实例
const instance = new Object()
复制代码
```

则此时， **实例为instance**, **构造函数为Object**，我们知道，构造函数拥有一个`prototype`的属性指向原型，因此原型为:

```js
// 原型
const prototype = Object.prototype
复制代码
```

这里我们可以来看出三者的关系:

```js
实例.__proto__ === 原型

原型.constructor === 构造函数

构造函数.prototype === 原型

// 这条线其实是是基于原型进行获取的，可以理解成一条基于原型的映射线
// 例如: 
// const o = new Object()
// o.constructor === Object   --> true
// o.__proto__ = null;
// o.constructor === Object   --> false
实例.constructor === 构造函数
复制代码
```

放大来看，我画了张图供大家彻底理解:

![img](https://user-gold-cdn.xitu.io/2019/2/14/168e9d9b940c4c6f?imageslim)

### 原型链：

**原型链是由原型对象组成**，每个对象都有 `__proto__` 属性，指向了创建该对象的构造函数的原型，`__proto__` 将对象连接起来组成了原型链。是一个用来**实现继承和共享属性**的有限的对象链。

- **属性查找机制**: 当查找对象的属性时，如果实例对象自身不存在该属性，则沿着原型链往上一级查找，找到时则输出，不存在时，则继续沿着原型链往上一级查找，直至最顶级的原型对象`Object.prototype`，如还是没找到，则输出`undefined`；
- **属性修改机制**: 只会修改实例对象本身的属性，如果不存在，则进行添加该属性，如果需要修改原型的属性时，则可以用: `b.prototype.x = 2`；但是这样会造成所有继承于该对象的实例的属性发生改变。

### New操作符

**new 运算符**创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。**new** 关键字会进行如下的操作：

1. 创建一个空的简单JavaScript对象（即`{}`）；
2. 链接该对象（即设置该对象的构造函数）到另一个对象；`obj.__proto__  = Con.prototype`
3. 将新创建的对象作为`this`的上下文 ； apply
4. 如果该函数没有返回对象，则返回`this`。

##### 实现

```js
function New() {
        // if (typeof fn !==)
        let obj = {};
        let fn = [].shift.call(arguments);
        console.log(arguments);
        obj.__proto__ = fn.prototype;
        let ret = fn.apply(obj,[].slice.call(arguments));
        console.log(ret);
        console.log(typeof ret);
        console.log(arguments);
        if ((typeof ret === 'object' || typeof ret ==='function') && ret !==null){
            return ret
        }

        return obj
    }
```

### ES6/ES7

声明

- `let / const`: 块级作用域、不存在变量提升、暂时性死区、不允许重复声明
- `const`: 声明常量，无法修改

- 解构赋值

- `class / extend`: 类声明与继承
- `Set / Map`: 新的数据结构
- 异步解决方案:
- - `Promise`的使用与实现
  - `generator`:
    - `yield`: 暂停代码
    - `next()`: 继续执行代码

```js
function* helloWorld() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

const generator = helloWorld();

generator.next()  // { value: 'hello', done: false }

generator.next()  // { value: 'world', done: false }

generator.next()  // { value: 'ending', done: true }

generator.next()  // { value: undefined, done: true }

复制代码
```

- `await / async`: 是`generator`的语法糖， babel中是基于`promise`实现。

```js
async function getUserByAsync(){
   let user = await fetchUser();
   return user;
}

const user = await getUserByAsync()
console.log(user)
```

### 函数柯里化

在一个函数中，首先填充几个参数，然后再返回一个新的函数的技术，称为函数的柯里化。通常可用于在不侵入函数的前提下，为函数 **预置通用参数**，供多次重复调用。

```js
const add = function add(x) {
	return function (y) {
		return x + y
	}
}

const add1 = add(1)

add1(2) === 3
add1(20) === 21
```

##### 实现：

```js
	function add(...args) {
            return args.reduce((a, b) => a + b)
        }
		
		//判断参数是否足够，足够就调用apply，否则用bind返回延迟执行，并减少参数个数
        function currying(fn, length) {
            length = length || fn.length;
            return function (...args) {
                return args.length >= length
                    ? fn.apply(this, args)
                    : currying(fn.bind(this, ...args), length - args.length)
            }
        }

        const fn = currying(function (a, b, c) {
            console.log([a, b, c])
        })

        const sum = currying(add, 3)
```





### 数组(array)

- `map`: 遍历数组，返回回调返回值组成的新数组
- `forEach`: 无法`break`，可以用`try/catch`中`throw new Error`来停止
- `filter`: 过滤
- `some`: 有一项返回`true`，则整体为`true`
- `every`: 有一项返回`false`，则整体为`false`
- `join`: 通过指定连接符生成字符串
- `push / pop`: 末尾推入和弹出，改变原数组， 返回推入/弹出项
- `unshift / shift`: 头部推入和弹出，改变原数组，返回操作项
- `sort(fn) / reverse`: 排序与反转，改变原数组
- `concat`: 连接数组，不影响原数组， 浅拷贝
- `slice(start, end)`: 返回截断后的新数组，不改变原数组
- `splice(start, number, value...)`: 返回删除元素组成的数组，value 为插入项，改变原数组
- `indexOf / lastIndexOf(value, fromIndex)`: 查找数组项，返回对应的下标
- `reduce / reduceRight(fn(prev, cur)， defaultPrev)`: 两两执行，prev 为上次化简函数的`return`值，cur 为当前值(从第二项开始)
- 数组乱序：

```
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
arr.sort(function () {
    return Math.random() - 0.5;
});
复制代码
```

- 数组拆解: flat: [1,[2,3]] --> [1, 2, 3]

```
Array.prototype.flat = function() {
    return this.toString().split(',').map(item => +item )
}
复
```



- `clientHeight`：表示的是可视区域的高度，不包含 border 和滚动条。
- `offsetHeight`：表示可视区域的高度，包含了 border 和滚动条。
- `scrollHeight`：表示了所有区域的高度，包含了因为滚动被隐藏的部分。
- `clientTop`：表示边框 border 的厚度，在未指定的情况下一般为 0。
- `scrollTop`：滚动后被隐藏的高度，获取对象相对于由 `offsetParent` 属性指定的父坐标（`css` 定位的元素或 body 元素）距离顶端的高度。



### `requestAnimationFrame`

与`setInterval`相比，`requestAnimationFrame`最大的优势是**由系统来决定回调函数的执行时机。**具体一点讲，如果屏幕刷新率是60Hz,那么回调函数就每16.7ms被执行一次，如果刷新率是75Hz，那么这个时间间隔就变成了1000/75=13.3ms，换句话说就是，`requestAnimationFrame`的步伐跟着系统的刷新步伐走。**它能保证回调函数在屏幕每一次的刷新间隔中只被执行一次**，这样就不会引起丢帧现象，也不会导致动画出现卡顿的问题。

除此之外，`requestAnimationFrame`还有以下两个优势：

- **CPU节能**：使用`setTimeout`实现的动画，当页面被隐藏或最小化时，`setTimeout` 仍然在后台执行动画任务，由于此时页面处于不可见或不可用状态，刷新动画是没有意义的，完全是浪费CPU资源。而`requestAnimationFrame`则完全不同，**当页面处理未激活的状态下，该页面的屏幕刷新任务也会被系统暂停，**因此跟着系统步伐走的`requestAnimationFrame`也会停止渲染，当页面被激活时，动画就从上次停留的地方继续执行，有效节省了CPU开销。

- **函数节流**：在高频率事件(resize,scroll等)中，为了防止在一个刷新间隔内发生多次函数执行，使用`requestAnimationFrame`可保证每个刷新间隔内，函数只被执行一次，这样既能保证流畅性，也能更好的节省函数执行的开销。一个刷新间隔内函数执行多次时没有意义的，因为显示器每16.7ms刷新一次，多次绘制并不会在屏幕上体现出来。

### call/apply/bind原理实现

```js

        Function.prototype._call = function (fn, ...args) {
            if (typeof this !== 'function') {
                throw new Error('this is not a function')
            }
            let context = fn || window;
            // args = [...arguments].slice(1)
            context.fn = this;
            var result = context.fn(...args)
            delete context.fn
            return result
        }


        let a = [1, 2, 3, 4, 6, 7]
        let b = Math.max._call(null, ...a)
        console.log(b)

        Function.prototype._apply = function (fn) {
            if (typeof this !== 'function') {
                throw new Error('this is not a function')
            }
            let context = fn || window;
            context.fn = this;
            let result;
            if (arguments[1]) {
                result = context.fn(...arguments[1])
            } else {
                result = context.fn()
            }
            delete context.fn
            return result
        }

        let c = Math.max._apply(null, a)
        console.log(c)

        Function.prototype._bind = function (fn) {
            if (typeof this !== 'function') {
                throw new TypeError('Error')
            }
            let args = [...arguments].slice(1)
            const context = this
            return function F() {
                let arg = args.concat([...arguments])
                let result;
                if (this instanceof F) {
                    return new context(...arg)
                } 
                return context.apply(fn,arg)
            }
        }
```

### New实现

1. 创建一个空对象,这个对象将会是`new Person()`返回的对象实例;
2. 将这个空对象的原型指向`构造函数`的`prototype`属性;
3. 将`构造函数`的`this`指向空对象,并运行`构造函数`;
4. 判断`构造函数`返回的是不是`对象`,是的话返回`默认对象`,不是的话返回之前创建的`空对象`,没有返回值默认返回`空对象`



```js
function _new(fn,...args){
  let obj = Object.create(fn.prototype)
  const ret = fn.apply(obj,args)
  return ret instanceof Object? ret : obj
}
```

### Curry

```js
function curry(fn,...args) {
    let len = fn.length;

    return function (){
        console.log(this)
        let _args = [...arguments];
        args = args.concat(_args);
        if(args.length < len){
            return curry.call(this, fn, ...args)
        }else{
            return fn.apply(this, args)
        }
    }
}

//test
function add(a,b,c,d) {
    console.log(a+b+c+d);
}

let adds = curry(add,6);
adds(2)(3,4)
adds(2)(3)(4)


```

