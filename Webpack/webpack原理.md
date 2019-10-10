### `wepack`启动过程

`npm run dev` 进入`node_modules/.bin`查找` webpack.cmd/webpack.sh`

入口 `node_modules/webpack/bin/webpack.js` 在`package.json`中查找`bin`字段

#### 入口文件分析 webpack.js

1. `process.exitCode = 0;`		// 正常执行返回
2. `const runCommand = (command, args) => {} `// 运行某个命令
3. `const isInstalled = packageName => { }`// 判断某个包是否被安装
4. `const CLIs = []`            // webpack可用的CLI:webpack-cli和webpack-commad
5. const installedClis = CLIs.filter(cli => cli.installed);   //判断是否两个CLI安装了
6. `if (installedClis.length === 0) {}	else if (installedClis.length === 1) {	`}// 根据安装数量进行处理

```js
#!/usr/bin/env node

// @ts-ignore
process.exitCode = 0;		// 1.正常执行返回
const runCommand = (command, args) => { // 2.运行某个命令
};
const isInstalled = packageName => { //3.判断某个包是否被安装
};
const CLIs = [                  //4. webpack可用的CLI:webpack-cli和webpack-commad
	{
		name: "webpack-cli",
		package: "webpack-cli",
		binName: "webpack-cli",
		alias: "cli",
		installed: isInstalled("webpack-cli"),
		recommended: true,
		url: "https://github.com/webpack/webpack-cli",
		description: "The original webpack full-featured CLI."
	},
	{
		name: "webpack-command",
		package: "webpack-command",
		binName: "webpack-command",
		alias: "command",
		installed: isInstalled("webpack-command"),
		recommended: false,
		url: "https://github.com/webpack-contrib/webpack-command",
		description: "A lightweight, opinionated webpack CLI."
	}
];

const installedClis = CLIs.filter(cli => cli.installed);   //5.判断是否两个CLI安装了

if (installedClis.length === 0) { 		// 6.根据安装数量进行处理
} else if (installedClis.length === 1) {
} else {
}

```

### 启动后结果

`webpack`最终找到`webpack-cli(webpack-command)`这个npm包，并且执行CLI



### Webpack-cli

主要做了3件事：

- 引入了`yargs`，对命令行进行定制
- 分析命令行参数，对哥哥餐宿进行转换，组成编译配置项
- 引入webpack，根据配置项进行编译和配置

```js
  // 处理不需要经过编译的命令 8个类型，在utils中
	const NON_COMPILATION_ARGS = [
    "init",     //创建一份webpack配置文件
    "migrate",  //进行webpack版本迁移
    "add",    //往webpack配置文件中增加属性
    "remove",  //往webpack配置文件中删除属性
    "serve",   //运行webpack-serve
    "generate-loader",  //生成webpack loader代码
    "generate-plugin",  //生成webpack plugin代码
    "info"  //返回与本地环境相关的一些信息
  ]; 
	const NON_COMPILATION_CMD = process.argv.find(arg => {
		if (arg === "serve") {
			global.process.argv = global.process.argv.filter(a => a !== "serve");
			process.argv = global.process.argv;
		}
		return NON_COMPILATION_ARGS.find(a => a === arg);
	});
	if (NON_COMPILATION_CMD) {
		return require("./utils/prompt-command")(NON_COMPILATION_CMD, ...process.argv);
	}

```

#### webpack-cli使用args分析：

/config/config-yargs/` 参数分组，将命令分为9类

- Config options: 配置相关参数（文件名称、运行环境等）
- Basic options：基础参数（entry设置，debug模式设置、watch监听设置、devtool设置）
- Module options： 模块参数，给loader设置扩展
- Output options：输出参数（输出路径、文件名称）
- Advanced options： 高级用法（记录设置、缓存设置、监听频率、bail等）
- Resolving options:解析参数（alias和解析的文件后缀设置）
- Optimizing options：优化参数
- Stats options：统计参数
- options：通用参数（帮助命令、版本信息等）



```js
			//webpack-cli从两处修改options
			//1.webpack.config.js配置文件
			//2.命令行参数
69		let options;
70		try {
71			options = require("./utils/convert-argv")(argv);
			} catch (err) {
				}
			}
```



#### Webpack-cli执行的结果

webpack-cli对配置文件和命令行参数进行转换最终生成配置选项参数options最终会根据配置参数实例化webpack对象，然后执行构建流程



## Tapable

**webpack本质：可以将其理解为一种基于事件流的编程范例，一系列的插件运行。**

Table 是一个类似于Node.js的`EventEmitter`的库，主要是控制钩子函数的发布与订阅，控制着`webpack`的插件系统

Tapable库暴露了很多Hook类，为插件提供挂载的钩子

```js
const {
	SyncHook,        //同步钩子
	SyncBailHook, 	//同步熔断钩子 遇到return返回
  SyncWaterfallHook, //同步流水钩子
  SyncLoopHook,    //同步循环钩子
  AsyncParallelHook, //异步并发钩子
  AsyncParallelBailHook,  //异步并发熔断钩子
  AsyncSeriesHook,   //异步串行钩子
  AsyncSeriesBailHook, //异步串行熔断钩子
  AsyncSeriesWaterfallHook, //异步穿行流水钩子
}
```

| type          | function                                                |
| ------------- | ------------------------------------------------------- |
| Hook          | 所有钩子后缀                                            |
| Waterfall     | 同步方法，但是它会传值给下一个函数                      |
| Bail          | 熔断：当函数有任何返回值，就会在当前执行函数停止        |
| Loop          | 监听函数返回true表示继续循环，返回undefined表示结束循环 |
| Sync          | 同步方法                                                |
| AsyncSeries   | 异步穿行钩子                                            |
| AsyncParallel | 异步并行执行钩子                                        |



### Tapable的使用

#### new Hook 新建钩子

`Tapable` 暴露出来的都是类方法，`new`一个类方法获得我们需要的钩子

`class` 接受数据参数`options`，非必传。类方法会根据传参，接受同样数量的参数

```js
const hook1 = new SyncHook(["arg1","arg2","arg3"])
```

#### 钩子的绑定与执行

`Tabpack`提供了同步&异步绑定钩子的方法，并且他们都有绑定事件和执行事件对应 方法。

| **Async***                    | **Sync***  |
| ----------------------------- | ---------- |
| 绑定：tapAsync/tapPromise/tap | 绑定：tap  |
| 执行：callAsync/promise       | 执行：call |

#### 基本用法

```js
const hook1 = new SyncHook(["arg1","arg2","arg3"])
//绑定事件到webpack事件流
hook1.tap('hook1',(arg1,arg2,arg3)=>console.log(arg1,arg2,arg3)) /1,2,3

//执行绑定事件
hook1.call(1,2,3)
```





### webpack流程

webpack编译按一下钩子调用顺序执行:

```js
entry-options -> run -> make -> before-resolve -> build-module -> normal-module-loader -> program -> seal -> emit

初始化options -> 开始编译 -> 从entry开始递归的分析依赖，对每个依赖模块进行build -> 对模块位置进行解析 -> 开始构建某个模块  -> 将loader加载完成的module进行编译，生成AST树 -> 遍历AST，当遇到require等一些调用表达式时，手机依赖 -> 所有依赖build完成，开始优化 -> 输出到dist目录

```

#### Entry-optipns

##### webpackOptionsApply

将所有的配置`options`参数转换成`webpack`内部插件 

使用默认插件列表 （mode: dev|prod)

将插件作为`compiler`实例

##### EntryOptions



##### Chunk生成算法

1. `webpack`现将`entry`中对应的`module`都生成一个新的`chunk`
2. 遍历`module`的依赖列表，将依赖的`module`也加入到`chunk`中
3. 如果一个依赖`module`是动态引入的模块，那么久会根据这个`module`创建一个新的`chunk`，继续遍历依赖
4. 重复上面的过程，知道得到所有的`chunks`

##### 文件生成





### AST

抽象语法树（abstract syntax tree)，是源代码的抽象语法结构的树状表现形式，这里特指编程语言的源代码。书上的每个节点都表示源代码中的一种结构。

场景：

1. 模板引擎
2. 转换 ES6 -> ES5  , TS -> JS



### webpack模块机制

- 打包出来是一个 **IIFE**（匿名闭包）
- modules是一个数组，每一项是一个模块初始化函数
- `__webpack_require__`用来加载模块，返回`module.exports`
- 通过 **WEBPACK_REQUIRE_METHOD(0)** 启动程序





