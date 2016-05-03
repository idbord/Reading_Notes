# 简单理解Ajax技术

## 写在前面
Ajax的名字源于2005年Jesse的一篇文章，在这篇文章里，他介绍了被他命名为Ajax的技术，即Asynchronous JavaScript + XML，Ajax技术可以向服务请求额外的数据而不需要刷新当前页面，能够带来更好的用户体验。

## XMLHttpRequest——Ajax技术的核心
XMLHttpRequest对象，简称XHR，由微软首先引入的一个特性，后来其他浏览器提供商提供了相同的实现。

创建兼容IE5+的XHR对象，如下：
```js
var xhr = window.XMLHttpRequest ? new XMLHttpRequest():new ActiveXObject('Microsoft.XMLHTTP');
//现在IE8都懒得兼容了，其实早已不用管IE5、IE6。
var xhr = new XMLHttpRequest(); //IE7+
```
## XHR的属性和方法
**readystate**
该属性表示请求/响应过程的当前活动阶段，共有五个值对应五个阶段：
* 0，未初始化。尚未启动请求，即未调用open方法
* 1，启动。已启动请求，但尚未发送请求，即未调用send方法
* 2，发送。已发送请求，但尚未接收到服务器响应数据
* 3，接收。已经接收到部分响应数据
* 4，完成。已经接收到全部响应数据，并可在客户端使用

**onreadystatechange**
readystate每改变一次，都会触发一次onreadystatechange事件，通过该事件可以检测readystate变化后的值。不过，需要在启动请求之前给该事件指定事件处理程序。

**status**
响应HTTP状态，如200、304、500等

**statusText**
HTTP状态说明信息

**responseText**
响应的主体，即返回的数据

**responseXML**
如果响应内容类型为"text/xml"或"application/xml"，则该属性保存包含响应数据的XML DOM文档

**open()**
该方法是xhr实例调用的第一个方法，它接收三个参数，分别为http方法、请求url、是否异步，如xhr.open("post","/index/data", true)启动了一个针对"/index/data"的异步POST请求。该请求不会立即发送。

**setRequestHeader()**
设置请求头。
xhr.sendRequestHeader("testHeader", "testValue")

**send()**
针对上面启动的请求，必须调用xhr.send(null)发送，send方法接受一个参数，即需要发送给服务器的数据，如果没有数据，则设为null。

**getResponseHeader()**
获取响应头某个字段的数据。另外，可以通过getAllResponseHeader()获取全部响应头信息。

## 通过XHR构造Ajax的示例
```js
var ajax  = function(url, type, data, callback, async){
    var xhr = window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject('Microsoft.XMLHTTP');
    xhr.onreadystatechange = function(){
        if(xhr.readyState == 4){
            if(xhr.status > 200 && xhr.status < 300 || xhr.status == 304){
                console.log(xhr.statusText);
                callback(xhr.responseText);
            }else{
                console.log("request failed:"+xhr.status);
            }
        }
    };
    switch(type){
        case "GET":
            url = solveParameter(url, data);
            xhr.open("get", url, async);
            xhr.setRequestHeader("test_header", "test_value");
            xhr.send(null);
            break;
        case "POST":
            xhr.open("post", url, async);
            xhr.setRequestHeader("test_header", "test_value");
            xhr.send(data);
    }
};
//get请求时，需要对每个参数的键值使用encodeURIComponent进行编码。
function solveParameter(url, data){
    if(data != "" && data != null){
        for(var key in data){
            url += (url.indexOf("?") == -1 ? "?" : "&");
            url += encodeURIComponent(key) + "=" + encodeURIComponent(data[key]);
        }
    }
    return url;
}
```

## FormData类型
FormData是XMLHttpRequest 2级提供的，它为序列化表单以及创建与表单格式相同的数据提供便利。
```js
var data = new FormData();
data.append("key","value");
```

```js
var form = document.getElementById("register-form");
xhr.send(new FormData(form));
```
使用FormData不必明确地在XHR对象上设置请求头部，XHR对象能够识别FormData，并配置相应的请求头部信息。
