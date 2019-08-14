### 纯CSS实现滚动条头部显示

#### 代码

​	

```css
 body{
            position: relative;
        }


        .indicator{
            position:absolute;
            top: 0;
            left: 0;
            bottom: 0;
            right: 0;
            background: linear-gradient(to right top,teal 50%, transparent 50%) no-repeat;
            background-size: 100% calc(100% - 100vh);
            z-index:1;
            pointer-events: none;
            mix-blend-mode: darken;
        }
        
        .indicator::after{
            content: "";
            position: fixed;
            top: 5px;
            bottom: 0;
            right: 0;
            left: 0;
            background: #fff;
            z-index: 1;
        }
```

​	传统CSS滚动指示器为了防止对角渐变（也就是滚动进度条）的覆盖页面上的元素内容，因此写在了最底层的body元素上，这就导致如果body元素内的普通元素内容有背景色，或者背景图之类的，就会覆盖进度条，产生致命缺陷。

​	把对角渐变（也就是滚动进度条）连同里面的白色覆盖层写在了普通元素的上面，这样避开被覆盖的致命缺陷。但是这样实现带来另外一个问题，页面的内容都被白色图层覆盖了，那页面内容岂不是都看不见了？不要担心，有CSS声明可以让白色的图层变成透明，那就是`mix-blend-mode:darken`，也就是darken混合模式。darken混合模式的混合方式很好理解，两个颜色进行混合，哪个颜色深就使用哪个颜色？

​	要知道所有的颜色里面最浅的就是白色，于是我们只要把我们的白色覆盖层的混合模式设置为darken，那必然最终呈现出来的颜色一定是覆盖层下面元素内容的颜色，换句话说我们的白色透明覆盖层变透明了。