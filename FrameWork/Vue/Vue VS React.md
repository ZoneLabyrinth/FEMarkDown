翻译自 https://medium.com/js-dojo/react-or-vue-which-javascript-ui-library-should-you-be-using-543a383608d

2016年，React巩固了其作为Javascript Web框架之王的地位。今年，其网络和本地移动图书馆迅速增长，并且主要竞争对手Angular的领先优势。

但2016年对Vue来说同样令人印象深刻。它的第2版的发布给Javascript社区留下了深刻的印象，今年它获得了25,000名额外的Github明星证明。

React和Vue的范围无可否认相似：两者都是基于轻量级组件的库，用于构建仅关注视图层的用户界面。两者都可以在一个简单的项目中使用，或者使用尖端工具扩展到复杂的应用程序。

结果，许多Web开发人员想知道他们应该使用哪一个。一个明显优于另一个吗？他们是否有特定的利弊需要注意？或者它们基本相同？

> 注意：本文最初发布于2016年12 [月26日的Vue.js Developers博客](https://medium.com/js-dojo/react-or-vue-which-javascript-ui-library-should-you-be-using-543a383608d/?utm_source=medium-vjd&utm_medium=article&utm_campaign=rov)上

#### **两个框架，两个倡导者。**

在本文中，我想通过彻底和公平的比较来回答这些问题。唯一的问题是：我是一个无耻的Vue粉丝男孩，完全有偏见。今年我在我的项目中大量使用了Vue，在Medium上赞扬了它，甚至还发布了[Udemy课程](https://www.udemy.com/vuejs-2-essentials/?couponCode=JSDOJO-MEDIUM)。

为了平衡我的偏见，我已经在我的朋友Alexis Mangin中购买了他，他既是一个优秀的Javascript开发人员又是一个很大的React粉丝。他同样沉浸在React中，经常在网络和移动项目中使用它。

有一天，亚历克西斯问我：“你为什么这么进入Vue，而不是React？”因为我不太了解React，所以我无法给出一个好的答案。所以我把这个想法告诉了他，我们有一天坐在笔记本电脑上，互相展示我们选择的图书馆所提供的东西。



经过双方的大量讨论和学习，以下六点是我们的主要发现。

#### 如果您喜欢使用模板构建应用程序（或想要选项），请使用Vue。

将标记放在HTML文件中是Vue应用程序的默认选项。与Angular类似，胡须括号用于数据绑定表达式，而指令（特殊HTML属性）用于向模板添加功能。

以下演示了一个简单的Vue应用程序。它打印一条消息，并有一个动态反转消息的按钮：

```
// HTML
<div id =“app”> 
  <p> {{message}} </ p> 
  <button v-on：click =“reverseMessage”> Reverse Message </ button> 
</ div>
// JS
new Vue（{ 
  el：'＃app'，
  data：{ 
    message：'Hello Vue.js！
  }，
  方法：{ 
    reverseMessage：function（）{ 
      this.message = this.message.split（''）。reverse（） .join（''）; 
    } 
  } 
}）;
```

相比之下，React应用程序避开模板并要求开发人员在Javascript中创建他们的DOM，通常是使用JSX。下面是使用React实现的同一个简单应用程序：

```
// HTML
<div id =“app”> </ div>
// JS（预翻译）
class App扩展了React.Component { 
  constructor（props）{ 
    super（props）; 
    this.state = { 
      message：'Hello React.js！' 
    }; 
  } 
  reverseMessage（）{ 
    this.setState（{ 
      message：this.state.message.split（''）。reverse（）。join（''）
    }）; 
  } 
  render（）{ 
    return（
      <div> 
        <p> {this.state.message} </ p> 
        <button onClick = {（）=> this.reverseMessage（）}> 
          Reverse Message 
        </ button> 
      </ div> 
    ）
  } 
}
ReactDOM.render（App，document.getElementById（'app'））;
```

对于那些来自标准Web开发范例的新开发人员来说，模板更容易理解。但即使是一些有经验的开发人员也喜欢它们，因为模板可以更好地分离布局和功能，并提供使用像Pug这样的预处理器的选项。

但模板的代价是必须学习所有扩展的HTML语法，而渲染函数只需要标准HTML和Javascript的知识。渲染功能也可以从更容易的调试和测试中受益。

但是，在这一点上，你不能错过Vue，因为它引入了在版本2 中使用模板*或*渲染函数的选项。

#### 如果你喜欢简单和“正常工作”的东西，请选择Vue。

一个简单的Vue项目可以直接从浏览器运行而无需转换。这允许Vue以jQuery的方式轻松地放入项目中。

虽然这在技术上也可以使用React，但典型的React代码更多地依赖于JSX和ES6功能，如类和非变异数组方法。

但Vue的简洁性在其设计中更为深入。让我们比较两个库如何处理应用程序数据（即“状态”）。

React中的状态是不可变的，因此您无法直接更改它。您需要使用*setState* API方法：

```
this.setState（{ 
    message：this.state.message.split（''）。reverse（）。join（''）
}）;
```

区分当前和之前的状态是React如何知道在DOM中重新呈现的时间和内容，因此需要不可变状态。

相反，数据只是在Vue中发生变异。在Vue中可以更改冗长的相同数据属性：

```
//请注意，数据属性可用作
// Vue实例的属性
this.message = this.message.split（''）。reverse（）。join（''）;
```

在你断定Vue的渲染系统必须缺乏React的效率之前，让我们先看看Vue中的状态是如何管理的：当你向状态中添加一个新对象时，Vue将遍历它的所有属性并将它们转换为getter和setter方法。Vue的反应系统现在可以跟踪状态，并在变异时自动重新渲染DOM。

令人印象深刻的是，改变Vue中的状态不仅更简洁，而且它的重新渲染系统实际上比React更快更有效。

不过，Vue的反应系统确实有一些警告。例如，它无法检测属性添加或删除以及某些阵列更改。这些情况可以使用Vue API中类似React的*set*方法进行处理。

#### 如果您需要尽可能小而快的应用程序，请使用Vue。

当应用程序的状态发生变化时，React和Vue都将构建一个虚拟DOM并同步真实的DOM。两者都有自己的优化这一过程的方法。

Vue核心开发人员提供了一个基准测试，显示Vue的渲染系统比React更快。在此测试中，10,000个项目的列表呈现100次。比较结果如下。



![img](http://ww4.sinaimg.cn/large/006tNc79ly1g3h4xmgch4j318g0grgmq.jpg)

在vuejs.org上发布的基准

从务实的角度来看，这种基准只与边缘情况有关。大多数应用程序不需要例行执行此类操作，因此通常不应将其视为重要的比较点。

但是，页面大小与所有项目相关，Vue也占上风。缩小，Vue库的当前版本仅为25.6KB。

要在React中获得类似的功能集，您需要React DOM（37.4KB）和React with Addons库（11.4KB），总计48.8KB，几乎是Vue的两倍。公平地说，你将使用React获得更大的API，但是你没有获得双倍的功能。

#### 如果您打算构建一个大型应用程序，请使用React。

比较在Vue和React中实现的简单应用程序，就像本文开头的那个，最初可能会偏向于开发人员偏爱Vue。这是因为基于模板的应用程序初看起来更容易理解，并且更快启动和运行。

但这些最初的好处引入了技术债务，可能会减缓应用程序的开发速度。模板容易被忽视运行时错误，难以测试，并且不易重构或分解。

相比之下，Javascript制作的模板可以组织成具有良好分解和干燥代码的组件，这些代码更具可重用性和可测试性。

Vue还具有组件系统和渲染功能。但是React的渲染系统更具可配置性，并且具有浅渲染等功能，与React的测试实用程序相结合，可以提供更多可测试和可维护的代码。

同时，React的不可变应用程序数据可能不够简洁，但当透明度和可测试性变得至关重要时，它会在更大的应用程序中闪耀。

#### 如果您想要一个适用于Web和本机应用程序的库，请使用React。

React Native是一个使用Javascript构建本机移动应用程序的库。它与React.js相同，只使用本机组件而不是使用Web组件。如果您已经学习了React.js，那么您将很容易获得React Native，反之亦然。

```
// JS
从'react'导入React，{Component}; 
从'react-native'导入{AppRegistry，Text，View};  
class HelloWorld扩展Component {    
  render（）{      
    return（        
      <View>          
        <Text> Hello，React Native！</ Text> 
      </ View> 
    ）;   
  } 
}
AppRegistry.registerComponent（'HelloWorld'，（）=> HelloWorld）;
```

重要的是，开发人员可以在Web或本机移动设备上构建应用程序，而无需使用不同的知识和工具。如果您打算为网络和移动设备开发，学习React会让您大获成功。

阿里巴巴的Weex是另一个跨平台的UI项目。目前，它认为Vue是一个“灵感”并使用了大量相同的语法，并计划完全集成Vue。但是，这种整合的时间表和具体细节仍不清楚。

由于Vue将HTML模板作为其设计的核心部分，并且没有自定义渲染作为当前功能，因此很难看到Vue.js的当前形式的本机对应物将与React.js和React一样紧密。本地人。

#### 如果你想要最大的生态系统，请选择React。

毫无疑问，React目前是一个比较受欢迎的图书馆，每月下载量为~2.5M NPM，而Vue每月下载量为225K左右。



![img](http://ww3.sinaimg.cn/large/006tNc79ly1g3h4xor723j318g0i50um.jpg)

人气不仅仅是一个浅薄的利益。这意味着有更多的文章，教程和Stack Overflow答案可以提供帮助。这意味着有更多工具和附加组件可以在项目中使用，并使开发人员无法自行构建所有内容。

这两个图书馆都是开源的，但React诞生于Facebook并受益于该赞助。致力于React的开发商和公司可以确保持续维护。

相比之下，Vue是由一位开发人员Evan You创建的，而您目前是Vue唯一的全职维护人员。Vue有一些企业赞助，但没有Facebook或Google的规模。

值得称赞的是Vue团队的小尺寸和独立性并未成为劣势。Vue有一个常规的发布周期，更令人印象深刻的是，Vue在Github上只有54个未解决的问题，相比之下，有3456个已关闭的问题，而React有更大的530个未决问题，而3447已经关闭。

#### 如果您已经对其中一个感到满意，则无需切换。

总结一下，我们的发现，Vue的优势在于：

- 灵活的模板或渲染功能选项
- 语法和项目设置简单
- 渲染速度更快，尺寸更小

React的优势：

- 规模更大，更好，更可测试
- Web和本机应用程序
- 更大的生态系统，提供更多支持和工具

但是，React和Vue都是特殊的UI库，并且具有比差异更多的相似之处。他们的大多数最佳功能是共享的：

- 使用虚拟DOM快速渲染
- 轻量级
- 反应成分
- 服务器端呈现
- 易于与路由器，捆绑器和状态管理集成
- 伟大的支持和社区

如果您认为我们错过了一些我们希望在评论中听到的内容。快乐发展！



## 生命周期

### Vue

1. **`beforeCreate`**

   组件和data都为undefined

2. **`created`**

   el还是undefined，而数据已经和data中的属性进行绑定（放在data中属性当值发生改变的同时，视图也会发生变化），在这里可以在渲染前倒数第二次更改数据的机会，不会触发其他的钩子函数，一般可以在这里做初始数据的获取

   > 从created到beforeMount的过程中，
   >
   > - **首先会判断vue实例中有没有el选项，如果有的话则进行下面的编译，但是如果没有el选项，则停止生命周期，直到vue实例上调用vm.$mount(el)**。
   > - 如果有el，再判断是否有template参数，如果有，则把其当作模板编译成render函数，如果没有，则把外部的html作为模板编译。template中的模板优先级高于outer HTML模板。
   > - 在vue对象中还有一个render函数，它是以createElement作为参数，然后做渲染操作，而且我们可以直接嵌入JSX.
   > - 综合排名优先级：render函数选项 > template选项 > outer HTML.

   **如果要在created阶段中进行dom操作，就要将操作都放在 Vue.nextTick() 的回调函数中，因为created() 钩子函数执行的时候 DOM 其实并未进行任何渲染，而此时进行 DOM 操作无异于徒劳，所以此处一定要将 DOM 操作的 js 代码放进 Vue.nextTick() 的回调函数中。**

3. **`beforeMount`**

   完成data和el数据初始化，但是页面中还只是vue占位符，可以在渲染前最后一次更改数据，可以在此获取初始数据

4. **`mounted`**

   载入，dom挂载，可以在此使用`ajax`

5. **`beforeUpdate`**:

   更新当前状态，重新渲染前，使用虚拟dom和diff算法对比

6. **`updated`**

   dom重新渲染完成

7. **`beforeDestroy`**

   组件销毁前（清楚事件绑定，计数器等）

8. **`destroyed`**:

   销毁后（Dom存在，但不受Vue控制），卸载vue事件（watcher，事件监听，子组件等）

#### 一般使用情况

- beforeCreate: 使用Loading
- created:结束Loading，初始数据获取
- mounted: ajax请求
- beforeDestroy：退出确认
- destroyed: 内存清理

### React

![image-20190730205154970](http://ww4.sinaimg.cn/large/006tNc79ly1g5i5plr9aij31ii0u0dk2.jpg) 

