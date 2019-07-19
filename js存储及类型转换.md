```js
function test(person) {
  person.age = 20
  
  //重新分配对象
  person = {
    name: 'lll',
    age: 30
  }

  return person
}
const p1 = {
  name: 'zzz',
  age: 18
}
const p2 = test(p1)
console.log(p1) // -> zzz,20 
console.log(p2) //->  lll,30
```

![img](http://ww3.sinaimg.cn/large/006tNc79ly1g3h4tb06ibj30ia0bi74c.jpg)

# 对象类型转换

会调用内置`[[Toprimitive]]`函数，该函数的一般逻辑为下：

1. 如果已经是原始类型，则不变；
2. **如果需要转字符类型就调用****`x.toString()`**，若转换为基础类型则返回转换的值，否则就先调用`valueof()`,结果不是基础类型再调用`toString()`
3. 调用`x.valueof()`，如果转为基础类型，则返回转换的值
4. 如果最后都没有返回原始值，则报错

也可以重写`Symbol.toPrimitive`方法，该方法优先级最高。

```javascript
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  },
  [Symbol.toPrimitive]() {
    return 2
  }
}
1 + a // => 3 优先级 1、toprimitive 2、valueof() 3、tostring()
```



# ==和===

x == y

1、如果两个值类型相同，进行 === 比较。 
2、如果两个值类型不同，他们可能相等。根据下面规则进行类型转换再比较： 
（1）如果一个是null、一个是undefined，那么[相等]。 
（2）如果任一值是字符串，另一个值是数值，在比较相等性之前先将字符串转换为数值；即是调用Number()函数。 
（3）如果任一值时布尔值，则在比较相等性之前先将其转换为数值，即是调用Number()函数。 
（4）如果一个是对象，另一个是数值或字符串，把对象转换成基础类型的值再比较。对象转换成基础类型，利用它的toString或者valueOf方法。 js核心内置类，会尝试valueOf先于toString；例外的是Date，Date利用的是toString转换。

![img](http://ww2.sinaimg.cn/large/006tNc79ly1g3h4wef51xj30rx0buglx.jpg)

### 创建特定大小的数组

方便快捷创建特定大小的数组

```
[...Array(3).keys()]
// [0, 1, 2]
```



### 声明提升

声明优先级，**函数 > 变量**

```js
foo();  // foo2
var foo = function() {
    console.log('foo1');
}

foo();  // foo1，foo重新赋值

function foo() {
    console.log('foo2');
}

foo(); // foo1
```