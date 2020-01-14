### Symbol使用

es6新类型

不能使用`new`，不是对象类型，不能添加属性，本质上类似于字符串的原始类型

Symbol函数可以接受一个字符串作为参数，表示对Symbol实例的描述。这个参数只是表示**对当前Symbol值的描述**，因此**相同参数的Symbol函数的返回值是不同的Symbol实例。**

```js
//实例唯一，以下生成两个symbol实例
var s1 = Symbol('foo')
var s2 = Symbol('foo')
s1 === s2 //false
```

#### `Symbol.for() Symbol().keyfor`

```js
//使用for则会进行全局搜索
let s1= Symbol.for('foo');
let s2 = Symbol.for('foo');
s1 === s2 // true

//Symbol.keyFor方法返回一个已登记的Symbol类型值的key。
var s1 = Symbol.for("foo");
Symbol.keyFor(s1) // "foo"
 
var s2 = Symbol("foo");
Symbol.keyFor(s2) // undefined 未定义
```

`Symbol.for()`与`Symbol()`这两种写法，都会生成新的 Symbol。它们的区别是，**前者会被登记在全局环境中供搜索，后者不会**。`Symbol.for()`不会每次调用就返回一个新的 Symbol 类型的值，而是会先检查给定的`key`是否已经存在，如果不存在才会新建一个值。比如，如果你调用`Symbol.for("cat")`30 次，每次都会返回同一个 Symbol 值，但是调用`Symbol("cat")`30 次，会返回 30 个不同的 Symbol 值。



### 作为属性名的Symbol

```js
var mySymbol = Symbol();
//第一种写法
var a = {};
a[mySymbol] = "Hello!";
//第二种写法 再对象内部，必须用方括号
var a = {
     [mySymbol]: "Hellow!"
}
//第三种写法
var a = {};
Object.defineProperty(a, mySymbol, { value: "Hellow!" });
//以上写法的结果都相同
a[mySymbol] // "Hellow!"
```

​	Symbol作为对象属性名时，不能用扩展运算符

#### 遍历

Symbol作为属性名，该属性不会出现在`for...in、for...of`循环中，也不会被`Object.keys()、Object.getOwnPropertyNames()`返回。但是，它也不是私有属性，有一个`Object.getOwnPropertySymbols`方法，可以获取指定对象的所有Symbol属性名。

`Object.getOwnPropertySymbols`方法返回一个数组，成员是当前对象的所有用作属性名的Symbol值。