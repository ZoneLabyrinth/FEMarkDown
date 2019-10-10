### 基本架构

通常遵循以下基本步骤来使用service workers：

1. service worker URL通过 `serviceWorkerContainer.register()`来获取和注册
2. 如果注册成功，service worker就在 `ServiceWorkerGlobalScope` 环境中运行；这是一个特殊类型的worker上下文运行环境，与主运行现成（执行脚本）相互独立，同时也没有访问DOM能力
3. service worker现在可以处理事件了。
4. 受 service worker 控制的页面打开后会尝试去安装 service worker。最先发送给service worker的事件是安装事件（在这个事件里可以开始进行填充IndexDB和缓存站点资源）。这个流程同原生APP或者Firefox OS APP是一样的——让所有资源可以离线访问
5. 当 `oninstall`事件的处理程序执行完毕后，可以认为service worker安装完成了。
6. 下一步是激活。当service worker 安装完成后，会接收到一个激活事件（activate event）。 `onactivate`主要用途是清理先前版本的service worker脚本中使用的资源。
7. service worker现在可以控制页面了。但仅是在`register()`成功后的打开页面。也就是说，页面起始于有没有service worker，且在页面的接下来声明周期内位置这个状态。所以，页面不得不重新加载以让service worker获得完全的控制



### 生命周期

1. 下载
2. 安装
3. 激活

用户首次访问service worker 控制的网站或页面时，service worker会立刻被下载

之后至少每24小时它会被下载一次。它 可能 被频繁的下载，不过每24小时一定会被下载一次，以避免不良脚本长时间生效。

无论它与现有的service worker不同，还是第一次在页面或网站遇到service worker，如果下载的文件是新的，安装就会尝试进行。

如果首次启用service worker，页面会首先尝试安装，安装成功后它会被激活。

如果现有service worker已启用，新版本会在后台安装，但不会被激活，这个时序成为 worker in waiting。知道所有已加载的页面不再使用旧的service worker才会激活新的service worker。只要页面不再依赖旧的service worker,新的service worker会被激活（成为active worker)

可以金婷`InstallEvent`，事件触发时的标准行为是准备service worker用于使用，例如使用内奸的storage Api来创建缓存，并且防止应用离线时所需的资源。

还有一个activate事件，触发时可以清理旧缓存和旧的service worker关联的东西。



service worker 可以通过`FetchEvent`事件去响应请求。通过使用`FetchEvent.respondWith`方法，你可以任意修改对于这些请求的响应。







### 开发者工具

`chrome://inspect/#service-workers` 展示当前设备上激活和存储的service worker

`chrome://serviceworker-internals/`调试woker进程



