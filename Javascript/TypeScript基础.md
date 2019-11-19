#### TypeScript原始类型

- `boolean`
- `number`
- `string`
- `void`
- `null/undefined`
- `symbol`
- `bigint` 操作大整数

### 其他类型  any/unkown/never/array/tuple/object

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

### 泛型 T

```typescript
//捕获开发者传入参数类型，然后使用T做参数类型和返回值的类型
function returnItem<T>(para: T): T {
	return para
}
//多个类型参数
function swap<T,U>(tuple:[T,U]:[U:T]){
    return [tuple[1],tuple[0]]
}
swap([1,'one'])
//泛型变量
function getLength<T>(arg: Array<T>):T[] {  //arg: T[]
    console.log((arg as Array<any>).length)
    return arg
}
//泛型接口
interface ReturnItemFn<T> {
    (para:T):T
}
//泛型约束
type Params = number | string
class Stack<T extends Params> {
    private arr: T[] =[]
    public: push(item:T){
        this.arr.push(item)
    }
}
// 泛型约束与索引类型
// 获取键值时
function getProperty(obj: T, key: K) {
    return obj[key];  //当key不存在obj上时报错
}
// 使用keyof
function getValue<T extends object, U extends keyof T>(obj: T, key: U) {
    return obj[key]; // ✔
}
//在泛型中使用类类型
//创建工厂函数时 new ()
function create<T>(c:{new ():T}):T{
    return new c();
}

```

### 装饰器

### Refelct MetaData

### 明确赋值断言 !

```typescript

let x: number;
init();
console.log(x! + x!);  //不加!提示未赋值
function init() {
    x = 10;
}
```

### is关键字

```typescript
// 用于判断返回类型 真假
function isString(test: any) test is string{
	return test === 'string'
}
//限定了参数类型
```

### 可调用类型注解

### Omit

### 索引类型

```typescript
// 获取键值
class Image = {
    public src: string = 'https'
    public alt: string = 'img'
    public width: number = 500
}
type propsname = keyof Image  // src | alt | width
type propsType = Image[propsname] //string | number
// 获取键值函数
function pick<T, K extends keyof T>(o: T, names: K[]): T[K][]{
	return names.map(n=> o[n])
}
```

### 映射类型

```typescript
// 将User属性变为全部可选
interface User {
    username: string
    id: number
    token:string
    role: sting
}
// [K in keys] K 类型变量，keys 字符串字面量构成的联合类型，表示一组属性名
// T[K] 索引访问
type partial<T> = {[K in typeof T]?: T[K]} // username?: string

```

### 条件类型

```typescript
// 接收Bool值，ture返回string类型，false返回number类型。类似三目
declare function f<T extends boolean>(x: T): T extends true ? string: number;
// 裸类型参数 分布式有条件类型，不能包裹其他类型
type NakedUsage<T> = T extends boolean? "yes": "no"
// 类型参数被包裹在元组内
type WrappedUsage<T> = [T] extends [boolean] ? "YES":"NO"

// 分布式有条件类型在实例化时会自动分发成联合类型 相当于map
type Distributed = NakedUsage<number | boolean> //  = NakedUsage<number> | NakedUsage<boolean> =  "NO" | "YES"
type NotDistributed = WrappedUsage<number | boolean > // "NO"

// 找出两部分不同
type R = Diff<"a" | "b" | "c" | "d", "a" | "c" | "f">;  // "b" | "d"
//
type Diff<T, U> = T extends U ? never : T;


```

### infer 推断

```typescript
type ParmaType<T> = T extends (param: infer P) => any ? P : T
// 如果T能赋值给(param: infer P) => any,则结果是（param：infer P) => any中的P,否则返回T
```

#### 应用

```typescript
// tuple 转 union, 比如 [string,number] -> string | number:
type ElementOf<T> = T extends Array<infer E>? E : never;
type ATuple = [string,number];
type ToUnion = ElementOf<ATuple>;
        
```

### 工具类型

```typescript
//ReturnType、Partial、ConstructorParameters、Pick

// 关键字 + - -?变为必选 -readonly变为非只读
type Required<T> = { [P in keyof T]-?: T[p]};

// Exclude<T>  T中排除可分配给U的元素
type Exclude<T,U> = T extends U ? never : T;
type T = Exclude<1 | 2, 1 | 3> // 2

// Omit 忽略T中某些属性
type Omit<T, K> = Pick<T, Exclude<keyof T,K>>
type Foo = Omit<{name: string,age: number},'name'> // {age:number}

// Merge 合并 Compute将交叉类型合并
type Compute<A extends any> = A extends Function ? A :{[K in keyof A]: A[k]}
type R1 = Compute<{x:'x' & {y:"y"}}
// Merge 合并
type Merge<01 extends object, 02 extends object> = Compute<01 & Omit<02,keyof 01>>

// Intersection<T,U> 取T属性和U的交集
type Props = { name: string; age: number; visible: boolean };
type DefaultProps = { age: numebr }
type DuplicateProps = Intersection<Props, DefalutProps>; //{age:number}

// Intersection是 Extract和Pick的结合
Intersection<T,U> = Extract<T,U> + Pick<T,U>

//Overwrite<T,U> 用U覆盖T相同的属性
type Props = {name: string; age: number; visible: boolean };
type NewProps = {age: string; other:string};
type ReplaceProps = Overwrite<props,NewProps> // {name:string,age:string; visable:boolean}
//即
type Overwrite<T extends object, U extends object, I = Diff<T,U>& Intersection<U,T>> = Pick<I, keyof I>;

// Mutable<T> 移除T所有属性的readonly
type Mutable<T> = {
	-readonly [P in keyof T]:T[P]
}

```