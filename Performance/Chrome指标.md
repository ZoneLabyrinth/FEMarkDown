## 关键性能指标



## 加载



## First Paint



这个比较容易理解，就是浏览器收到足够的内容进行第一次排版和绘制的时间，不过随着页面复杂度和动态化程度越来越高，这个指标变得越来越不能反映真实的用户体验。



## First Contentful Paint



FCP 的定义是第一次绘制的内容用户觉得足够感兴趣去看一下，浏览器判断 FCP 的标准比较简单，就是以绘制了任意文本和图片为标准。FCP 相对 FP 更有意义一些，但是它定义的标准从网页的角度来说仍然过于模糊，不同类型网页的 FCP 可能实际的差别非常大。



## First Meaningful Paint



FMP 的定义是浏览器已经绘制了页面的主要内容，从用户的角度来说，起码能看到第一屏比较完整的内容。FMP 相对 FCP 而言，对用户更有意义一些，特别是现在很多网页正文的内容是动态加载的。当然对浏览器来说主要的问题是如何判断已经达到 FMP，因为它的判断标准比 FCP 要模糊，只能通过一些猜测性质的推断，Chrome 认为自己能够达到 65 ～ 80% 的准确程度，并在后续 LayoutNG 项目中进一步改进判断的准确性。



## 响应流畅度



## Touch Scroll Start Latency (or Scroll Begin Latency)



SSL 的定义是从触发网页滚动的第一个 Touch Move 事件的时间戳到浏览器绘制这个滚动结果的这一帧的时间戳的 Delta 值。对用户的意义就是触屏滚动第一次响应是否及时。



## Touch Scroll Update Latency



SUL 的定义是 SSL 的后续每个 Touch Move 事件的时间戳到浏览器绘制这个滚动结果的这一帧的时间戳的 Delta 值。对用户的意义就是拖动网页是否跟手。



## Expected Queueing Time



EQT 是一个预估值，用来预估输入事件的平均响应时间，它的定义是事件到达主线程事件队列的时间，和被主线程从事件队列中取出进行处理的时间差一个预估值。EQT 的计算有点复杂，有兴趣的读者可以看[这篇文章](https://link.zhihu.com/?target=https%3A//docs.google.com/document/d/1Vgu7-R84Ym3lbfTRi98vpdspRr1UwORB4UV-p9K1FF0/edit)，大致说来，它是对任一窗口期的主线程繁忙程度的计算，主线程越多任务运行，单个任务运行的时间越长，EQT 的值就越高，也意味着输入事件得到响应的预估时间就越长。Chrome 给的一个统计值是在低端 Android 设备上，99% 的概率在 1.5 秒以内。



## Frame Throughput



这个指标 [Chrome 的描述比较模糊](https://link.zhihu.com/?target=https%3A//docs.google.com/document/d/1Ww487ZskJ-xBmJGwPO-XPz_QcJvw-kSNffm0nPhVpj8/edit%23heading%3Dh.hoc9sslvha5n)，大概是绘制每一帧的时间，个人感觉意义不大，也不知道要怎么计算 GPU 耗时的部分。

个人觉得帧率需要跟具体的动画联系起来才有意义，毕竟浏览器绘制跟游戏的绘制不一样，浏览器跟其它应用一样，只有需要更新才会触发绘制，而不是一直都不断地进行绘制。偶尔，不连续的更新不能构成一个有意义的动画，它的绘制耗时对用户对流畅度的感知也没有意义，反而会增加大量噪音，模糊了我们想要得到的结果。我们更需要知道的是一个特定的动画，比如一个惯性滚动动画，一个 CSS Transform 动画的帧率，实际上内核可以计算出某个动画绘制的帧数，再加上知道这个动画的时长，这样就有足够的信息计算出这个动画的帧率。



> 另外，Time to Interactive 和 First Input Delay 是两个实验性的指标，用于反映页面处于无响应的加载过程的耗时，因为 Web 开发者可能会过度优化 FCP 而导致页面可交互的延迟变得更高，所以这两个指标也可以作为一种平衡。



## 能耗



从浏览器来说，的确很难从内部对能耗进行测试（一般能耗测试更多依赖于外部硬件），目前给出的能耗相关的一个统计指标是主线程的负载程度，简单说就是主线程负载程度越高，就认为能耗越高，不过个人觉得不太靠谱。



## 内存占用



## Browser 和 Renderer 进程的内存占用



这里是指一个网页自身的私有数据的内存占用，比如 DOM 树，排版树，图层树，JavaScript 对象等等，不包括公共的部分。



## OOMs



每百万次页面加载出现 OOMs 的次数。



## 面向 Web 开发者的性能数据收集



上述的性能指标，目前还是 Chrome 通过内置的 UMA 服务进行收集，然后内核开发者监控这些指标是否提升或者衰退。对 Web 开发者来说，Chrome 也提供了在线查询页面可以看到 Top 站点的性能数据。但是在提供相应的 Web API 供 Web 开发者收集自己网页的线上性能数据，还是进展的非常缓慢（线下的话，DevTools 和 Lighthouse 可以直接或者间接提供部分数据）。

如果 Web 开发者可以通过 Web API 收集自己网页的线上性能数据，用于新版本上线前的 A/B 测试性能对比，长期监控性能的历史变化趋势等等，可能会更有意义。但是目前来说只有 [Performance](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/API/Performance)和 [PerformanceObserver](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/API/PerformanceObserver) API 提供了有限的支持。



## 关键性能指标的 Web API 支持



加载的 FP，FCP，FMP，目前 PerformanceObserver 的 [Paint Timing API](https://link.zhihu.com/?target=https%3A//w3c.github.io/paint-timing/) 已经支持 FP 和 FCP，FMP 估计未来也会支持。



```js
var observer = new PerformanceObserver(list => {
      list.getEntries().forEach(entry => {
        if (entry.name == "first-paint" ||
            entry.name == "first-contentful-paint") {
          console.log("Name: "       + entry.name      +
                      ", Type: "     + entry.entryType +
                      ", Start: "    + entry.startTime +
                      ", Duration: " + entry.duration  + "\n");
        }
      })
    });
    observer.observe({entryTypes: ['paint']});
```





响应流畅度的 SSL，SUL，EQT 等，很可惜，还没有相应的 API 支持，唯一规划中的 [Long Tasks API](https://link.zhihu.com/?target=https%3A//w3c.github.io/longtasks/) 提供了间接估算主线程繁忙程度的支持（Long Task 的定义是超过 50ms 运行时间的任务）。动画流畅度的 API 支持目前并没有看见有规划。



```js
var observer = new PerformanceObserver(function(list) {
    var perfEntries = list.getEntries();
    for (var i = 0; i < perfEntries.length; i++) {
        // Process long task notifications:
        // report back for analytics and monitoring
        // ...
    }
});
// register observer for long task notifications
observer.observe({entryTypes: ["longtask"]});
// Long script execution after this will result in queueing
// and receiving “longtask” entries in the observer.
```





能耗相关的性能指标，主线程负载没有相关的 API 直接支持，Long Tasks API 倒是提供一个间接的方法，虽然并不太可靠。

内存相关的性能指标，performance.memory 目前只提供非常有限的内存占用信息（usedJSHeapSize），未来应该会增加更细分和丰富的信息。[Device Memory API](https://link.zhihu.com/?target=https%3A//docs.google.com/document/d/1O0fKj5q4U8EF2LZdScXB6c02laQmixPekG-fuCQUHys/edit%23heading%3Dh.olkbwjiu4dbo) 提供了 API 让网页获取设备的可用内存大小，这样可以针对不同内存大小的设备分发不同的资源（比如不同分辨率的图片）。