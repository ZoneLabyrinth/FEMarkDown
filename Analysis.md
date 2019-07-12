### `navigator.sendBeacon(url,data);` 

在退出页面时上报数据，使用`new Image()`或异步的`ajax`方法等容易遭aborted。使用同步的xhr又可能导致阻塞

#### 用法

```js
window.addEventListener('unload', logData, false); 

function logData() {
	navigator.sendBeacon("/log", analyticsData); 
} 
```

`sendBeacon` 如果成功进入浏览器的发送队列后，会返回`true`；如果受到队列总数、数据大小的限制后，会返回`false`。返回`ture`后，只是表示进入了发送队列，浏览器会尽力保证发送成功，但是否成功了，不会再有任何返回值。目前暂无具体的数据长度限制标准。  

`navigator.sendBeacon()`发送数据时，`content-type`只能是以下几种格式之一（暂不支持`application/json`)：

- `application/x-www-form-urlencoded`
- `multipart/form-data`
- `text/plain`

三种类型情况：

- #### DOMString

  如果数据类型是 `string`，此时该请求会自动设置请求头的 `Content-Type` 为 `text/plain`。服务端一般需要处理`content-type`不为`json`情况

  ```js
  function logData(url, data) = {
    navigator.sendBeacon(url, data);
  };
  ```

- #### Blob

如果用 `Blob` 发送数据，需要手动设置 `Blob` 的 MIME type，一般设置为 `application/x-www-form-urlencoded`。

```js
function logData(url, data) = {
  const blob = new Blob([JSON.stringify(data), {
    type: 'application/x-www-form-urlencoded',
  }]);
  navigator.sendBeacon(url, blob)
};
```

- #### `FormData`

表单元素，可以直接创建一个新的`FormData`，请求头中`Content-Type`自动会设为`multipart/form-data`

```js
function logData(url, data) = {
  const formData = new FormData();
  Object.keys(data).forEach((key) => {
    let value = data[key];
    if (typeof value !== 'string') {
      // formData只能append string 或 Blob
      value = JSON.stringify(value);
    }
    formData.append(key, value);
  });
  navigator.sendBeacon(url, formData);
};
```



兼容降级处理

```js
navigator.sendBeacon ||function sendBeacon(url){
	const xmlhttp = new XMLHttpRequest();
          xmlhttp.onreadystatechange = function(){
            if(xmlhttp.readyState === 4){
              if(xmlhttp.status === 200){
                resolve(xmlhttp.response)
              }else{
                reject('error'+xmlhttp.text)
              }
            }
          }
          xmlhttp.open('post',url)
          xmlhttp.setRequestHeader('Content-type','text/plain;charset=UTF-8')
          xmlhttp.send(JSON.stringify(params))
}; 
```











### 取消页面剪切板限制

```js
 document.onmouseup = function(){
 	 let data = getSelect()
     if(data){
     	console.log(data);   
     }
 }

function getSelect() {
    if(document.Selection){       
			//ie浏览器
			return document.selection.createRange().text;     	 
		}else{    
			//标准浏览器
			return document.getSelection().toString();	 
		}	
}

//document.execCommond('Copy')
```

