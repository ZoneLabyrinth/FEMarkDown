#### TypeScript原始类型

- `boolean`
- `number`
- `string`
- `void`
- `null/undefined`
- `symbol`
- `bigint` 操作大整数

### 其他类型

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

### 枚举

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



