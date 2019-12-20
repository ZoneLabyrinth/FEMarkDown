

### createPortal

```typescript

    
 //统一放在外部，或者使用useRef保存值
//const el = document.createElement('div');
const appRoot = document.getElementById('root');

const ScreenFull: React.FC<IProps> = React.memo(({ children, visible = false, onCancel }) => {
    
    // 再此处创建div， 每次重绘都会改变。
    // const el = document.createElement('div');

    const el = React.useRef<HTMLDivElement | null>(null);
    // 不添加null el.current会报错，具体如下。 
    //https://github.com/DefinitelyTyped/DefinitelyTyped/issues/31065#issuecomment-446425911

    function getRootEl() {
        if (!el.current) {
            el.current! = document.createElement('div');
        }
    }

    getRootEl();

    useOnMounted(() => {
        appRoot!.appendChild(el.current!);
    });

    useOnUnmount(() => {
        appRoot!.removeChild(el.current!);
    });

    console.log(appRoot);
    const target = usePortal('root');


    return ReactDOM.createPortal(
        <div className={classnames({
            'screen-full-container': true,
            'screen-full-hidden': !visible,
        })}
        >
            {children}
            <div className="screen-full-mask" onClick={onCancel} />
        </div>,
        el.current!
    );
});
```

useRef<htmlelement| null>