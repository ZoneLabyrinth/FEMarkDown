# Webpack Production Bundling fails because of UglifyJS Error with Dom7

原文：https://github.com/nolimits4web/swiper/issues/2263

 打包时swiper冲突

引入时

```js
import Swiper from 'swiper/dist/js/swiper.js'
```



```js

module: {

​        rules: [

​            {

​                test: /\.js$/,

​                //uglifyjs和swiper冲突

​                exclude: /node_modules\/(?!(dom7|swiper)\/).*/,

​                //多线程编译

​                use: [

​                    {

​                        loader: 'thread-loader',

​                        options: {

​                            workers: 3

​                        }

​                    },

​                    'babel-loader?cacheDirectory=true'

​                ]

​            },   module: {

​        rules: [

​            {

​                test: /\.js$/,

​                //uglifyjs和swiper冲突

​                exclude: /node_modules\/(?!(dom7|swiper)\/).*/,

​                //多线程编译

​                use: [

​                    {

​                        loader: 'thread-loader',

​                        options: {

​                            workers: 3

​                        }

​                    },

​                    'babel-loader?cacheDirectory=true'

​                ]

​            },
```

