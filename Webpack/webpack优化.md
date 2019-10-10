​		原有的vue-cli,create-react-app在项目较大时编译时间过长，扩展性不好。重写了一套，优化了构建速度和体积。主要从以下优化了webpack

## 构建速度

1. ### 多线程压缩

   开启多线程压缩有多种方式，`happy-pack/thread-loader/parallel-webpack`等，`happy-pack`在`webpack3`时很受欢迎，主要是在`webpack` `compile`后通过`happy-pack`创建的线程池分模块构建，处理后重新传到主线程，目前官方已经不再维护，推荐使用的`webpack4`的`thread-loader`。在`webpack`解析一个模块时， `thread-loader`会将它及它的依赖分配到 **worker** 线程中，添加`thread-loader`如下：

   ```js
   //1. 安装依赖
    npm install --save-dev thread-loader (之后方法省略该步骤)
   //2. module中引入
     module: {
             rules: [
                 {
                     test: /\.js$/,
                     include: path.resolve(__dirname, '../src'),
                     //多线程编译 项目不大不建议使用
                     use: [{
                         loader: 'thread-loader',
                         options: {
                             workers: 3
                         }
                     },
                    	'babel-loader?cacheDirectory=true']
   
                 }
               ]
     }
   
   
   ```

2. ### 多进程多实例并行压缩

   同样多种方式: **parallel-uglify-plugin/ugilfyjs-wepack-plugin/terser-webpack-plugin** ，而`terser-webpack-plugin`支持压缩ES6语法。使用同样简单：

   ```js
   //安装后添加到optimization中
   
   optimization: {
       minimizer: [
         new TerserPlugin({
           //开启并行压缩，true为默认值，可设数值， 同样项目小不建议使用，可能反而延长时间
           parallel: true,
           //开启压缩缓存
           cache: true
         })
       ],
     },
   ```

3. ### 分包

   使用**html-webpack-externals-plugin**指定引入包CDN

   ```js
   new HtmlWebpackExternalsPlugin({
     externals: [
       {
         module: 'jquery',
         entry: '//unpkg.com/jquery@3.2.1/dist/jquery.min.js',
         global: 'jQuery',
       },
       {
         module: 'react',
         entry: '//unpkg.com/react@16/umd/react.production.min.js',
         global: 'React',
       },
     ],
   })
   ```

   此方法需手动引入，同时会导致页面打入多个`<script>`标签进行引用，对于SplitChunk还是会对基础包进行分析。项目不大可以使用。推荐使用DLL

4. ### DLLPlugin预编译

   这是webpack自带的神器，此插件专门用于单独的webpack配置中，以创建dll的捆绑包。它创建一个manifest.json文件，DllReferencePlugin使用它来映射依赖项。[webpack详情](https://webpack.js.org/plugins/dll-plugin/#root)

   可以先创建`webpack.dll.js`文件，将公共包提出来（以下文件都存在config文件夹，__dirname目录可能补同）

   ```js
   const path = require('path')
   const webpack = require('webpack')
   module.exports = {
       entry: {
         	//将一下包打成library文件
           library:[
               'react',
               'react-dom',
               'redux',
               'react-redux',
               'axios'
           ],
       },
       output:{
           filename:'[name]_[hash].js',
         	//路径不要存在dist中，否则会被cleanwebpackplugin清理
           path: path.join(__dirname,'../build/library'),
           library:'[name]'
       },
       plugins:[
         	//使用dll
           new webpack.DllPlugin({
               name:'[name]_[hash]',
               path: path.join(__dirname,'../build/library/[name].json'),
           })
       ]
   }
   ```

   在`pack.json`中新加一条命令运行：

   ```js
       "dll": "webpack --config ./config/webpack.dll.js",
   ```

   run后会产生build文件夹

   在原`webpack production`模式下添加

   ```js
   	plugins:[
       new webpack.DllReferencePlugin({
         manifest:require(path.resolve(__dirname,'../build/library/library.json')),
       })
     ]
   ```

   会发现速度提升，同时打包体积变小。此时需在编译后的html文件中手动添加library文件夹。可使用`copy-webpack-plugin`和 `html-webpack-tags-plugin`实现自动添加：

   ```js
   	plugins:[
   				new HtmlWebpackPlugin(),
           new HtmlWebpackTagsPlugin({
             	//需要引入的文件
               tags: [`${require('../build/library/library.json').name}.js`],
              //加载末尾
               append: false
           }),
       ]
   ```

   注：`html-webpack-tags-plugin`必须配合`html-webpack-plugin`，并且在此之后引入。同时，该插件似乎和`speed-measure-webpack-plugin`冲突，若是引用了`speed-measure-webpack-plugin`建议在构建时暂时关闭。

   在`production`配置中添加：

   ```js
   plugins:[
       new CopyWebpackPlugin([
         //目录从项目根文件开始，从复制build/lirary文件到当前，需要更多配置可查询官方
       { from: 'build/library', to: '' },
       ]),
     ]
   ```

   此时运行可以自动添加文件。

5. ### 开启缓存

   缓存对于二次编译具有很大的提高。通常有几种方法：

   - 开启`babel-loaer`缓存（直接在`babel-loader`后添加`?cacheDirectory=true`方法1中)

   - 开启`terser-webpack-plugin`缓存（方法二中）

   - 使用`hard-source-webpack-plugin`提升模块转换阶段缓存

     ```js
     plugins:[
     		// ...
         new HardSourceWebpackPlugin(),
     ]
     ```

6. ### 缩小构建目标

   缩小构建目标，减少每次构建文件查找范围也能提高速度，有以下情况：

   ```js
   // 1. 排除或缩小目录
       module: {
           rules: [
               {
                   test: /\.js$/,
                 	//只在解析src下文件
                 	include: path.resolve(__dirname, '../src'),
                 	//排除 node_module文件夹，通常不需要同时使用。
   								exclude: 'node_module',
                   use: [{
                         loader: 'thread-loader',
                         options: {
                             workers: 3
                         }
                     },
                         'babel-loader?cacheDirectory=true']
                 }
             ]
       	}
   //2.resolve模块使用
     resolve: {
       //合理使用别名,减少搜索层级
       alias: {
         //import react可以直接找到
         "react": path.resolve(__dirname, "../node_module/react/dist/react.min.js"),
       },
        modules:[path.resolve(__dirname,"../node_module")]
         //后缀名，如import 'index'时会自动按extensions顺序查找，可选多个，但查找速度慢，指定为一下数组中类型时，其他类必须写明后缀，否则无法找到
        extensions:[.js],
        //packjson指定的入口文件
        mainFeilds:['main'] 
     }      
   ```



## 构建体积优化

### 图片优化

基于Node库的`Imagemin`(定制选项，可以引入第三方优化插件，可以处理多种图片格式)

使用：配置`image-webpack-loader` [快速入口](https://www.npmjs.com/package/image-webpack-loader)

```js
 module:{
   rules:[
     {
       test: /\.(gif|png|jpe?g|svg|blob)$/,
       use: [
         'file-loader',
         {
           loader: 'image-webpack-loader',
           options: {
             mozjpeg: {
               progressive: true,
               quality: 65
             },
             // optipng.enabled: false will disable optipng
             optipng: {
               enabled: false,
             },
             pngquant: {
               quality: '65-90',
               speed: 4
             },
             gifsicle: {
               interlaced: false,
             },
             // the webp option will enable WEBP
             webp: {
               quality: 75
             }
           }
         },
       ],
     },
   ]
 }
```

Mac OS 可能由于libpng错误，官方建议安装或更新`libpng`

```shell
brew install libpng
```

若还有错误，可能由于node引起

```js
node rebuild
```

重新编译可能时间长

### **CSSTreeShaking**

`webpack3`中使用`purifyCSS`,已经停止维护，4中可使用`purgecss-webpack-plugin` 进行`treeshaking`，删除未使用的css；

```js
const PATHS = {
    src: path.join(__dirname, '../src')
}

plugins:[
		new MiniCssExtractPlugin(),
		//必须和mini-css-extract-plugin配合使用
    new PurgecssPlugin({
    	paths: glob.sync(`${PATHS.src}/**/*`, { nodir: true }),
    }),
  ]
```

### 动态引入polyfill

Polyfill一般占用很大，很多时候不是必须的。一般常用的方法是使用`babel-polyfill`，体积会很大。可以使用`polyfill-service`按需加载。`polyfill-service`主要通过UA来判断该引用哪些polyfill。所以缺点也明显，各大浏览器会伪造UA来抢占，这会造成 service判断困难。

**CDN**:

**<script src="https://cdn.polyfill.io/v2/polyfill.min.js"></script>**

## 其他优化

除了对项目和速度进行优化，也可以对`webpack`展示优化，提高友好性。

- 提示框
- 进度条
- 仪表板
- 错误提示
- 体积分析

```js
plugins:[
  // 提示框
  new WebpackBuildNotifyerPlugin({
  	//提示项目名
    title: 'project',
    suppressSuccess: true
  }),
  //进度条
  new ProgressBarPlugin(),
  //仪表盘，看需要
  //new DashboardPlugin()，
  //错误提示，需要同stats使用
  new FriendlyErrorsWebpackPlugin(),
	// 体积分析，需要的时候加上，有多种分析插件
  // new BundleAnalyzerPlugin(),
]
```





