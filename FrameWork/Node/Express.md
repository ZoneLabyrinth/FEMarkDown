## 中间件

#### 概念

中间件是一些可以访问request对象，response对象以及应用程序请求-响应周期中的next函数的函数。next函数是是Express路由器中的一个功能，当被调用时，它会在当前中间件之后执行中间件。

中间件可以执行以下任务：

- 执行任何代码
- 对request和response对象进行更改
- 结束`request-response`循环
- 调用栈中下一个中间件

如果当前的中间件没有结束`request-response`循环，他必须调用`next()`将控制权传入下一个中间件。否则，请求将被挂起。

以下的图片显示了被中间件调用的元素：

![image-20190522223950116](http://ww4.sinaimg.cn/large/006tNc79ly1g3ah2i8l4fj31h40hyjzy.jpg)

### 例子

以下是一个简单的"Hello World"Express应用例子。文章剩下的将会定义并且添加两个中间件到应用里：一个叫做myLogger会打印一个简单的日志信息，另一个 **requestTime**会显示`HTTP request`的时间戳

```
var express =require('express')
var app = express()

app.get('/',function(req,res){
	res.send('Hello World!')
})

app.listen(3000)
```

