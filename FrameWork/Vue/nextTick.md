

微任务队列，优先级 promise -> MutationObserver->setImmidate->setTimeout









```js
//执行一次timerfunc
let pending = false
//回调队列
function flushCallback(){
    pending = false;
    let cb = callbacks.slice(0);
    //释放callbacks
    callbacks.length = 0
    for(let i = 0;i<cb.length;i++){
        cb[i]()
    }
}

//在2.5版本，我们使用 宏任务 与微任务组合

//然而，当状态在重绘前被改变时候会有副作用

//同时，在事件处理时使用（宏）任务将会导致一些无法绕开的奇怪行为

//所以我们现在又重新使用微任务

//这种权衡的一个主要缺点是，在某些情况下，微任务具有太高的优先级，并且在所谓的顺序事件之间触发甚至在同一个事件冒泡
let timerFunc
//nextTick利用可以通过Promise.then或者MutationObserve的微任务队列

//MutationObserver拥有更宽泛的支持。然而当他在IOS>9.3.3的UIwebview中触发一个点击事件时有个严重的Bug

//他会在触发后一段时间内完全阻止运行。所以，如果Promise可用，将优先使用：


if(typeof Promise !== 'undefined'){
    const p = Promise.resolve()
    timerFunc = ()=>{
        p.then(flushCallback)
    }
}else if(!isIE&& typeof MutationObserver !== 'undefined'){
    let counter = 1;
    const observer = new MutationObserver(flushCallback)
    const textNode = document.createTextNode(String(counter))
    observer.observe(textNode,{
        characterData:true
    })
    timerFunc = () =>{
        counter = (counter + 1 )%2
        textNode.data = String(counter)
    }
}else if(typeof setImmediate !== 'undefined'){

    timerFunc = () =>{

        setImmediate(flushCallback)

    }

}else{
    timerFunc = () =>{
        setTimeout(flushCallback, 0);
    }
}
function nextTick(cb,ctx){
    let _resolve
    callbacks.push(()=>{
        if(cb) {
            try {
                cb.call(ctx)
            }catch(e){
                throw new Error(e)
            }
        }
    })
    //等待
    if(!pending){
        pending=true
        timerFunc()
    }
   if(!cb && typeof Promise !== 'undefined'){
        return new Promise(resolve => {
            _resolve = resolve
        })
    }
	}
}


```

