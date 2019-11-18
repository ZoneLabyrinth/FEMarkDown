#### TypeScript原始类型

- `boolean`
- `number`
- `string`
- `void`
- `null/undefined`
- `symbol`
- `bigint` 操作大整数

### 其他类型  any/unkown/array/tuple/object

- `any`  相当于跳过检查

- `unknown`   是`any`对应的安全类型

  `unknown`和`any`主要区别是`unknown`类型会更加严格：再对`unknown`类型执行大多数操作之前，我们必须进行某种形式的检查，而对`any`类型的值执行操作前，不必进行任何检查。

- `never` 表示用不存在值的类型（异常，空数组，而且永远为空），可以是任何类型的子类，也可以赋值给任何类型，但没有类型是`never`的子类型或可以赋值给`never`

- 数组

  - 泛型： `const list: Array[number] = [1,2,3]`
  - 元素后直接 `[]`: `const list: number[] = [1,2,3]`

- 元组 `Tuple`  表示一个已知元素数量和类型的数组

  - `let arr: [string, number]` 2个元素，多或少都会报错
  - 可以当成严格版数组 `ineterface Tuple extends Array<string | number> { 0: stiring;1:number;length:2}`
  - 元组越界：允许push添加，但不允许访问；` arr.push(2); arr[2]//error`

-  `Object` 非原始类型，

### 枚举 emnu

#### 数字枚举

声明一个枚举类型，没有赋值，但值是默认的数字类型，而且默认从0开始累加，也可以指定第一个值，后面根据其累加：

```typescript
emnu Duck {
	first = 20,
	second,
	third
}
Duck.first === 20;
Duck.first === 21;
Duck.first === 22;
```

#### 字符串枚举

```typescript
emnu Duck {
	first = 'tom',
	second = 'jack'
}
Duck['jack'] ,Duck.first
```

#### 异构枚举

```typescript
// 混合使用
emnu Duck {
	first = 20,
	Name = 'Tom'
}
```

### 添加静态方法

### 接口 interface

```typescript
interface User{
	name: string,
	age?: number //可以没有
    readonly isMale: boolean  //只读，不可修改
	say: (sentence: string) => string //函数类型 user.say = (sentence) =>{return 'hello'}
 
}
// 继承
interface root extends User, SuperUser {
  	say:() => void
}

```

### 类

#### 抽象类 abstract

抽象类作为其他派生类的基类使用，一般不会直接被实例化，不同于接口，抽两类可以包含成员的实现细节。

```typescript
abstract class User {
    abstract handsome(): void;
    say(): {
        console.log('Hello world')
    }
}
new User() //报错
class root extends User{
    handsome(){
        console.log('so handsome');
    }
};
root.handsome(); //so handsome
root.say(); //hello world;


```

#### 访问限定符 public/private/protected

同传统面向对象语言一样，三类访问限定符的用法类似；

```typescript
class User {
	public name(n){
		console.log(`name is ${n}`)
	}
    private money(){
        console.log('total a billion')
    }
    protected creditCard() {
        console.log('8888888888');
    }
}
class Root extends User {
	init() {
        this.creditCard();
    }
}

const carl = new User();
carl.name('carl') // root;
carl.money() //受保护
carl.creditCard()  //受保护

const root = new Root();
root.init() //888888888

```

### 函数

定义函数类型

```typescript
const add: (a:number, b:number) => number =(a:number,b:number) => a + b
// 重载
```

