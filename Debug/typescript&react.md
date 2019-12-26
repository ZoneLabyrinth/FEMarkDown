### input onchange事件

inchange 事件显示没有value属性

```typescript
//受控组件，初始值不能为undefined null 
const [value, setValue ] = React.useState('');

//确定为input元素则不报错
  const onChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setValue(e.target.value);
  }

  return (
    <div className="searchbar-container">
      <input
        className="input-container"
        onChange={onChange}
        value={value}
        placeholder={placeholder}
        onFocus={handlerFocus}
      />
    </div>
  );
```

