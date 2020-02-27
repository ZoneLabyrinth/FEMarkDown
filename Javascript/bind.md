## bind

### 特点

- `bind`()方法回创建一个新函数。当这个新函数被调用时，`bind`()的第一个参数将作为它运行时的`this`，之后的一系列参数将回在传递的实参前传入作为它的参数。
- 一个绑定函数也能使用`new`操作符创建对象：这种行为就像把原函数当成构造器。提供的this值被略。同时调用时的参数被提供给模拟函数。

### 词法作用域

JavaScript采用的是词法作用域，函数的作用域在函数定义的时候就决定了。

而于词法作用域相对的是冬日太作用域，函数的作用域是在函数调用的时候才决定的。

### 变量对象

对于每个执行上下文，都有三个重要属性：

- 变量对象（variable object， VO）
- 作用域链（Scope chain）
- this

变量对象是于执行上下文相关的数据作用域，存储了在上下文中定义的变量和函数声明。

#### 全局上下文

全局上下文中的变量对象就是全局对象

#### 函数上下文：

- 在函数上下文中，用活动对象（`activation object, AO`）来表示变量对象。
- 活动对象和变量对象其实是一个东西，只是变量对象是规范上的或者说是引擎实现上的。不可在`JavaScript`环境中访问，只有当进入一个执行上下文中，这个执行上下文的变量才会被激活，所以才叫`activation object`，而只有被激活的变量对象，也就是活动那个独享上的各种属性才能被访问。
- 活动对象是在进入函数上下文时刻被创建的。它通过函数的`arguments`属性初始化。`arguments`属性值是`Arguments`对象

### 进入执行上下文

当进入执行上下文时，这时候还没有执行代码

变量对象回包括：

1. 函数的所有形参（如果是函数上下文）
   - 由名称和对应值组成的一个变量对象的属性被创建
   - 没有实参，属性值设为undefined
2. 函数声明
   - 由名称和对应值（函数对象（function-object）组成一个变量对象的属性被创建
   - 如果变量对象已经存在相同名称的属性，则完全替换这个属性
3. 变量生命
   - 由名称和对应值（undefined）组成一个变量独享的属性被创建
   - 如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性

### 作用域链

- 查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级（词法层面上父级）执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样有多个执行上下文的便令对象构成的链表就叫做作用域链。
- 函数的作用力在函数定义的时候就决定了
- 这是因为函数有一个内部属性`[[scope]]`，当函数创建的时候，就会保存所有父级变量对象到其中，你可以理解`[[scope]]`就是所有父级变量对象的层级链，但是注意：`[[scope]]`并不代表完整的作用域链

### 函数激活

当函数激活时，进入函数上下文，创建`VO/AO`后，就会将活动对象添加到作用域链的前端。这时候执行上下文的作用域链命名为`Scope chain`：

```js
ScopeChain = [AO].concat([[Scope]]);
```



没有prototype： bind；箭头函数；





### 深拷贝

```js
const isComplexDataType = obj => (typeof obj === 'object' || typeof obj === 'function') && (obj !== null)

const deepClone = function (obj, hash=new WeakMap()) {
    if(hash.has(obj)) return hash.get(obj)
    let type = [Date,RegExp,Set,Map,WeakMap,WeakSet]
    if(type.includes(obj.constructor)) return new obj.constructor(obj);
    //如果成环了，参数obj = obj.loop = 最初obj 会在weakMap中找到第一次放入的obj提前返回第一次放入weakMap的cloneObj
    
    let allDesc = Object.getOwnPropertyDescriptors(obj);
    let cloneObj = Object.create(Object.getPrototypeOf(obj),allDesc);
    hash.set(obj,cloneObj)
    
    for (let key of Reflect.ownKeys(obj)){ 
        //Reflect.ownKeys(obj) 可以拷贝不可枚举属性和符号类型
        //如果值是引用类型（非函数）则递归调用deepClone
        cloneObj[key] = 
            (isComplexDataType(obj[key]) && typeof obj[key] !== 'function') ?
            	deepClone(obj[key],hash) : obj[key];
    }
    return cloneObj;
}
```

