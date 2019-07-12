### 单线程

弱点：

- 无法利用多核CPU；
- 错误会引起整个应用退出，应用健壮性值得考验
- 大量计算占用CPU导致无法继续调用异步I/O

#### Web Works

解决大计算量问题降低阻塞几率。webworkers能创建工作线程来进行计算，以解决JavaScript大计算阻塞UI渲染的问题。工作现成为了不阻塞主线程，通过消息传递的方式来传递运行结果，这也是的工作不能线程访问到主线程的UI。

Node采用了与Web Workers相同的思路来解决单线程中大计算量的问题：child_process

## 应用场景

#### I/O密集型

I/O密集的有事主要在于Node利用事件循环的处理能力，而不是启动每一个线程为每一个请求服务，资源占用极少。

## 非I/O的异步api

1. ### 定时器

2. ### process.nextTick()

   `setTimeout(fn,0)`不够精确，定时器需要动用红黑树，浪费性能。而`process.nextTick()`相对较为轻量。

   ```js
   process.nextTick = function (callback){
       
       if(process._exiting) return
       
       if(tickDepth>= process.maxTickDepth)
       	maxTickWarn();
       
       var tock = {callback: callback};
       if(process.domain) tock.domain = process.domain;
       nextTickQueue.push(tock);
       if(nextTickQueue.length){
           process._needTickCallback();
       }
      
   }
   ```

   每次调用，只会将回调函数放入队列。再下一轮Tick时取出执行。定时器复杂度为O(lg(n))，而`nextTick()`为O(1)

3. #### setImmediate()

   与`process.nextTick()`类似，但`process`属于idle观察者，`setImmediate()`属于check观察者。再每个轮询中，idle观察者先于I/O观察者，I/O观察者先于check观察者。

   具体实现上，`process.nextTick()`的回调函数保存在一个数组中。`setImmediate()`的结果则是保存在链表中。在行为上，`process.nextTick()`在每轮循环中会将数组中的回调函数全部执行完，而`setImmediate()`在每轮循环中执行链表中的一个回调函数。

   之所以这样设计，是为了保证每轮循环能够较快的执行结束，防止CPU占用过多而阻塞后续I/O调用的情况。

   ### 事件驱动与高性能服务器

   ### 高阶函数

   函数作为参数或将函数作为返回值的函数

   ### 偏函数

   创建一个调用另外一个部分——参数或者变量已经预置的函数——的函数的用法。

   ```js
   var isType = function(type){
       return function(obj){
           return toString.call(obj) == `[object ${type}]`;
       }
   }
   ```

   通过`isTpye()`函数预先指定`type`值，然后返回一个新函数

   指定部分参数来产生一个新的定制函数的形式就是偏函数

   

   

   

   

   









