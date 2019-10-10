## react-router

使用`BrowserRouter`刷新后找不到页面

解决：

在webpack-dev-server配置中添加historyApiFallback

```js
module.exports = 
  devServer: {
  	historyApiFallback: true
  }
}
```



### 解决Webpack中提示syntax 'classProperties' isn't currently enabled的错误

##### 安装如下插件

`npm i -D @babel/plugin-proposal-class-properties`

##### 在babelrc中配置插件：

```
{
        plugins: ['@babel/plugin-proposal-class-properties']
      }
 },
```



#### regeneratorRuntime is not defined 错误

原因 使用了`ES7 async/await`方法,转换

解决：

```js
npm install --save-dev @babel/plugin-transform-runtime //babel>7版本
```

##### .babelrc文件

```js
 {
        plugins: [
        	// ....
        	"@babel/plugin-transform-runtime"
        ]
      }
 },
 
```



### axios

使用`axios.get(url)`可以使用

​	instance = axios.create(config)





### DLL分包错误

`html-webpack-tags-plugin`配合`html-webpack-plugin`使用，但与`speed-measure-plugin`不兼容（未解决）



