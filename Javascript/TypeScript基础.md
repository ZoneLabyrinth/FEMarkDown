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

### Record 强制匹配元素

### tsconfig.json

```json
{
	// 包含指定文件的相对或绝对路径，用来指定待编译文件，包含依赖
    // 若不指定files，则按include中文件，若没有include，则编译根目录及子目录所有文件
   	// 优先级 files > exclude > include 
    "files": [], // 只能指定文件
    //  include/exclude支持通配符，可以指定文件或目录
	"include":[],
    // exclude 默认值：node_modules/bower_components/jspm_package/outDir选项指定路径
	"exclude": [],
    // 保存文件时根据tsconfig.json重新生成文件
	"compileOnSave": false,
    // 配置复用，继承其他配置文件 './config.js' 同名覆盖原文件
	"extends": "",
	"compilerOptions": {}
}

// compilerOptions选项 六大类:基础、类型检查、额外检测、模块解析、Source Map、实验
{
  "compilerOptions": {
    /* Basic Options */
    "target": "es5" /* target用于指定编译之后的版本目标: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'. */,
    "module": "commonjs" /* 用来指定要使用的模块标准: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */,
    "lib": ["es6", "dom"] /* lib用于指定要包含在编译中的库文件 */,
    "allowJs": true,                       /* allowJs设置的值为true或false，用来指定是否允许编译js文件，默认是false，即不编译js文件 */
    "checkJs": true,                       /* checkJs的值为true或false，用来指定是否检查和报告js文件中的错误，默认是false */
    "jsx": "preserve",                     /* 指定jsx代码用于的开发环境: 'preserve', 'react-native', or 'react'. */
    "declaration": true,                   /* declaration的值为true或false，用来指定是否在编译的时候生成相应的".d.ts"声明文件。如果设为true，编译每个ts文件之后会生成一个js文件和一个声明文件。但是declaration和allowJs不能同时设为true */
    "declarationMap": true,                /* 值为true或false，指定是否为声明文件.d.ts生成map文件 */
    "sourceMap": true,                     /* sourceMap的值为true或false，用来指定编译时是否生成.map文件 */
    "outFile": "./dist/main.js",                       /* outFile用于指定将输出文件合并为一个文件，它的值为一个文件路径名。比如设置为"./dist/main.js"，则输出的文件为一个main.js文件。但是要注意，只有设置module的值为amd和system模块时才支持这个配置 */
    "outDir": "./dist",                        /* outDir用来指定输出文件夹，值为一个文件夹路径字符串，输出的文件都将放置在这个文件夹 */
    "rootDir": "./",                       /* 用来指定编译文件的根目录，编译器会在根目录查找入口文件，如果编译器发现以rootDir的值作为根目录查找入口文件并不会把所有文件加载进去的话会报错，但是不会停止编译 */
    "composite": true,                     /* 是否编译构建引用项目  */
    "removeComments": true,                /* removeComments的值为true或false，用于指定是否将编译后的文件中的注释删掉，设为true的话即删掉注释，默认为false */
    "noEmit": true,                        /* 不生成编译文件，这个一般比较少用 */
    "importHelpers": true,                 /* importHelpers的值为true或false，指定是否引入tslib里的辅助工具函数，默认为false */
    "downlevelIteration": true,            /* 当target为'ES5' or 'ES3'时，为'for-of', spread, and destructuring'中的迭代器提供完全支持 */
    "isolatedModules": true,               /* isolatedModules的值为true或false，指定是否将每个文件作为单独的模块，默认为true，它不可以和declaration同时设定 */

    /* Strict Type-Checking Options */
    "strict": true /* strict的值为true或false，用于指定是否启动所有类型检查，如果设为true则会同时开启下面这几个严格类型检查，默认为false */,
    "noImplicitAny": true,                 /* noImplicitAny的值为true或false，如果我们没有为一些值设置明确的类型，编译器会默认认为这个值为any，如果noImplicitAny的值为true的话。则没有明确的类型会报错。默认值为false */
    "strictNullChecks": true,              /* strictNullChecks为true时，null和undefined值不能赋给非这两种类型的值，别的类型也不能赋给他们，除了any类型。还有个例外就是undefined可以赋值给void类型 */
    "strictFunctionTypes": true,           /* strictFunctionTypes的值为true或false，用于指定是否使用函数参数双向协变检查 */
    "strictBindCallApply": true,           /* 设为true后会对bind、call和apply绑定的方法的参数的检测是严格检测的 */
    "strictPropertyInitialization": true,  /* 设为true后会检查类的非undefined属性是否已经在构造函数里初始化，如果要开启这项，需要同时开启strictNullChecks，默认为false */
   "noImplicitThis": true,                /* 当this表达式的值为any类型的时候，生成一个错误 */
    "alwaysStrict": true,                  /* alwaysStrict的值为true或false，指定始终以严格模式检查每个模块，并且在编译之后的js文件中加入"use strict"字符串，用来告诉浏览器该js为严格模式 */

    /* Additional Checks */
    "noUnusedLocals": true,                /* 用于检查是否有定义了但是没有使用的变量，对于这一点的检测，使用eslint可以在你书写代码的时候做提示，你可以配合使用。它的默认值为false */
    "noUnusedParameters": true,            /* 用于检查是否有在函数体中没有使用的参数，这个也可以配合eslint来做检查，默认为false */
    "noImplicitReturns": true,             /* 用于检查函数是否有返回值，设为true后，如果函数没有返回值则会提示，默认为false */
    "noFallthroughCasesInSwitch": true,    /* 用于检查switch中是否有case没有使用break跳出switch，默认为false */

    /* Module Resolution Options */
    "moduleResolution": "node",            /* 用于选择模块解析策略，有'node'和'classic'两种类型' */
    "baseUrl": "./",                       /* baseUrl用于设置解析非相对模块名称的基本目录，相对模块不会受baseUrl的影响 */
    "paths": {},                           /* 用于设置模块名称到基于baseUrl的路径映射 */
    "rootDirs": [],                        /* rootDirs可以指定一个路径列表，在构建时编译器会将这个路径列表中的路径的内容都放到一个文件夹中 */
    "typeRoots": [],                       /* typeRoots用来指定声明文件或文件夹的路径列表，如果指定了此项，则只有在这里列出的声明文件才会被加载 */
    "types": [],                           /* types用来指定需要包含的模块，只有在这里列出的模块的声明文件才会被加载进来 */
    "allowSyntheticDefaultImports": true,  /* 用来指定允许从没有默认导出的模块中默认导入 */
    "esModuleInterop": true /* 通过为导入内容创建命名空间，实现CommonJS和ES模块之间的互操作性 */,
    "preserveSymlinks": true,              /* 不把符号链接解析为其真实路径，具体可以了解下webpack和nodejs的symlink相关知识 */

    /* Source Map Options */
    "sourceRoot": "",                      /* sourceRoot用于指定调试器应该找到TypeScript文件而不是源文件位置，这个值会被写进.map文件里 */
    "mapRoot": "",                         /* mapRoot用于指定调试器找到映射文件而非生成文件的位置，指定map文件的根路径，该选项会影响.map文件中的sources属性 */
    "inlineSourceMap": true,               /* 指定是否将map文件的内容和js文件编译在同一个js文件中，如果设为true，则map的内容会以//# sourceMappingURL=然后拼接base64字符串的形式插入在js文件底部 */
    "inlineSources": true,                 /* 用于指定是否进一步将.ts文件的内容也包含到输入文件中 */

    /* Experimental Options */
    "experimentalDecorators": true /* 用于指定是否启用实验性的装饰器特性 */
    "emitDecoratorMetadata": true,         /* 用于指定是否为装饰器提供元数据支持，关于元数据，也是ES6的新标准，可以通过Reflect提供的静态方法获取元数据，如果需要使用Reflect的一些方法，需要引入ES2015.Reflect这个库 */
  }
  "files": [], // files可以配置一个数组列表，里面包含指定文件的相对或绝对路径，编译器在编译的时候只会编译包含在files中列出的文件，如果不指定，则取决于有没有设置include选项，如果没有include选项，则默认会编译根目录以及所有子目录中的文件。这里列出的路径必须是指定文件，而不是某个文件夹，而且不能使用* ? **/ 等通配符
  "include": [],  // include也可以指定要编译的路径列表，但是和files的区别在于，这里的路径可以是文件夹，也可以是文件，可以使用相对和绝对路径，而且可以使用通配符，比如"./src"即表示要编译src文件夹下的所有文件以及子文件夹的文件
  "exclude": [],  // exclude表示要排除的、不编译的文件，它也可以指定一个列表，规则和include一样，可以是文件或文件夹，可以是相对路径或绝对路径，可以使用通配符
  "extends": "",   // extends可以通过指定一个其他的tsconfig.json文件路径，来继承这个配置文件里的配置，继承来的文件的配置会覆盖当前文件定义的配置。TS在3.2版本开始，支持继承一个来自Node.js包的tsconfig.json配置文件
  "compileOnSave": true,  // compileOnSave的值是true或false，如果设为true，在我们编辑了项目中的文件保存的时候，编辑器会根据tsconfig.json中的配置重新生成文件，不过这个要编辑器支持
  "references": [],  // 一个对象数组，指定要引用的项目
}
```

### jest测试

### 搭建环境

#### vs code 插件

- Typescript Importer 
- Typescript Toolbox 自动引入和生成

#### 单元测试

```js
// 安装jest
$ npm i jest @types/jest ts-jest -D
// 根目录创建文件jest.config.js:
module.exports = {
   // roots: 让我们指定测试的根目录,通常情况下我们建议设置为 src/
  "roots": [
    "<rootDir>/src"
  ],
      
  // transform: 这里我们使用 ts-jest 来测试 tsx/ts 文件
  "transform": {
    "^.+\\.tsx?$": "ts-jest"
  },
}
// package.json
{
    "scripts": {
    ...
    // jest: 是运行测试,jest 会寻找在 src/ 目录下所有符合要求的文件进行测试
    "test": "jest",
    "test:c": "jest --coverage",
	// jest --watchAll: 是以监控模式监控所有符合要求的文件
    "test:w": "jest --watchAll --coverage"
  },
}
 
// eslint
$ npm i eslint eslint-plugin-react @typescript-eslint/parser @typescript-eslint/eslint-plugin -D

//.eslintrc
module.exports = {
    parser: '@typescript-eslint/parser',
    settings: {
        react: {
            version: 'detect'
        }
    },
    parserOptions: {
        project: './tsconfig.json',
    },
    plugins: ['@typescript-eslint'],
    extends: [
        'plugin:react/recommended',
        'plugin:@typescript-eslint/recommended',
    ],
    rules: {
        "@typescript-eslint/explicit-function-return-type": "off",
    }
}

//debug
{
    "name": "node",
    "type": "node",
    "request": "launch",
    "program": "${workspaceRoot}/dist/foo.js", //注意,这个路径必须是需要调试的文件`src/foo.ts`的编译后的路径即`/dist/foo.js`
    "args": [],
    "cwd": "${workspaceRoot}",
    "protocol": "inspector"
}

  
```

