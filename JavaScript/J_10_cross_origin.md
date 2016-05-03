# 跨域问题及解决方案详解

跨域问题是前端工程师必须面对的,之前了解过相关解决方案,但由于自己的开发实践中没有遇到,所以一直没有很深入的理解跨域的相关问题.上次在完善珞珈青年网时,需要提供一个给未来网首页调用的接口,由于young.future.org.cn和www.future.org.cn属于两个不同的域,所以必须解决跨域问题.

不管最终是怎么解决的,先来了解一下如何判定跨域问题.

##### 只要协议,域名,端口有任何一个不同,即可判定为不同域.

![](http://7xlzgd.com1.z0.glb.clouddn.com/cross-domain-1.png)

针对跨域问题,有如下几种解决方案:

### 通过JSONP跨域
JSONP(JSON with Padding),也叫填充式JSON.JSONP由两部分组成:回调函数和数据.回调函数就是当服务器的响应返回时在页面中调用的函数,而数据,即此时传入回调函数的JSON数据.

服务器端:
```py
callback = request.args.get('callback')
data = json.dumps({"id":1, "name": "tom"})
return callback(data)
```

使用原生js如下所示:
```js
<script type="text/javascript">
    function calling(data){
        //solve the data
    }
</script>
<script src="http://example.com/getData?callback=calling"></script>
```

使用jQuery:
```js
<script type="text/javascript">
    $.getJSON("http://example.com/getData?callback=?",function(data){
        //solve the data
    });
</script>
```
JSONP兼容性好,但仅支持GET而不支持POST等类型HTTP请求;同时只支持跨域HTTP请求,不能解决不同域的两个页面之间进行JavaScript调用的问题.

### 跨域资源共享CORS
CORS(Cross-Origin Resource Sharing)跨域资源共享,定义了一种跨域访问的机制,可以让AJAX实现跨域访问.其基本原理就是使用自定义的HTTP头部让浏览器与服务器进行沟通.如果说JSONP是耍了点小聪明,那么CORS则是服务器端给浏览器开了通路.

实现方法就是在服务器端设置Access-Control-Allow-Origin,如果指定为需要访问资源的请求所属的域,则就允许了来自该域名的请求;如果指定为*,那么就允许了来自任何域的请求.

```py
from functools import wraps
from flask import make_response

def allow_cross_domain(fun):
    @wraps(fun)
    def wrapper_fun(*args, **kwargs):
        rst = make_response(fun(*args, **kwargs))
        rst.headers['Access-Control-Allow-Origin'] = '*'
        rst.headers['Access-Control-Allow-Methods'] = 'PUT,GET,POST,DELETE'
        allow_headers ="Referer,Accept,Origin,User-Agent"
        rst.headers['Access-Control-Allow-Headers'] = allow_headers
        return rst
    return wrapper_fun

@app.route('/hosts/')
@allow_cross_domain
def domains():
    pass
```

### 使用window.name进行跨域
window对象有一个name属性,在一个窗口的生命周期内,窗口载入的所有页面都共享一个window.name,每个页面对window.name都有读写的权限.

详见[window.name实现的跨域数据传输](http://www.cnblogs.com/rainman/archive/2011/02/21/1960044.html)

### 用HTML5的window.postMessage方法跨域
详见http://blog.csdn.net/bao19901210/article/details/21457755
window.postMessage(message,targetOrigin) 方法是html5新引进的特性,跨文档消息传输，可以使用它来向其它的window对象发送消息，无论这个window对象是属于同源或不同源，目前IE8+、FireFox、Chrome、Opera等浏览器都已经支持window.postMessage方法。

otherWindow.postMessage(message, targetOrigin);

otherWindow: 对接收信息页面的window的引用。可以是页面中iframe的contentWindow属性；window.open的返回值；通过name或下标从window.frames取到的值。
message: 所要发送的数据，string类型。
targetOrigin: 用于限制otherWindow，“*”表示不作限制

a.com/index.html中的代码：
```js
<iframe id="ifr" src="b.com/index.html"></iframe>
<script type="text/javascript">
    window.onload = function() {
        var ifr = document.getElementById('ifr');
        var targetOrigin = 'http://b.com';
        // 若写成'http://b.com/c/proxy.html'效果一样
        // 若写成'http://c.com'就不会执行postMessage了
        ifr.contentWindow.postMessage('I was there!', targetOrigin);
    };
</script>
```

b.com/index.html中的代码：
```js
    <script type="text/javascript">
        window.addEventListener('message', function(event){
            // 通过origin属性判断消息来源地址
            if (event.origin == 'http://a.com') {
                alert(event.data);    // 弹出"I was there!"
                alert(event.source);  // 对a.com、index.html中window对象的引用
                                      // 但由于同源策略，这里event.source不可以访问window对象
            }
        }, false);
    </script>
```

### 通过修改document.domain跨子域
浏览器都有一个同源策略，其限制之一就是第一种方法中我们说的不能通过ajax的方法去请求不同源中的文档。 它的第二个限制是浏览器中不同域的框架之间是不能进行js的交互操作的。
不同的框架之间是可以获取window对象的，但却无法获取相应的属性和方法。比如，有一个页面，它的地址是http://www.example.com/a.html ， 在这个页面里面有一个iframe，它的src是http://example.com/b.html, 很显然，这个页面与它里面的iframe框架是不同域的，所以我们是无法通过在页面中书写js代码来获取iframe中的东西的：

```js
<script type="text/javascript">
    function test(){
        var iframe = document.getElementById('￼ifame');
        var win = document.contentWindow;
        //可以获取到iframe里的window对象，但该window对象的属性和方法几乎是不可用的
        var doc = win.document;
        //这里获取不到iframe里的document对象
        var name = win.name;
        //这里同样获取不到window对象的name属性
    }
</script>
<iframe id = "iframe" src="http://example.com/b.html" onload = "test()"></iframe>
```

这个时候，document.domain就可以派上用场了，我们只要把http://www.example.com/a.html 和 http://example.com/b.html这两个页面的document.domain都设成相同的域名就可以了。但要注意的是，document.domain的设置是有限制的，我们只能把document.domain设置成自身或更高一级的父域，且主域必须相同。

1.在页面 http://www.example.com/a.html 中设置document.domain:
```js
<iframe id = "iframe" src="http://example.com/b.html" onload = "test()"></iframe>
<script type="text/javascript">
    document.domain = 'example.com';//设置成主域
    function test(){
        alert(document.getElementById('￼iframe').contentWindow);
        //contentWindow 可取得子窗口的 window 对象
    }
</script>
```

2.在页面 http://example.com/b.html 中也设置document.domain:
```js
<script type="text/javascript">
    document.domain = 'example.com';
    //在iframe载入这个页面也设置document.domain，使之与主页面的document.domain相同
</script>
```
修改document.domain的方法只适用于不同子域的框架间的交互。

##### 写在后面
当时未来网的那个问题是通过jsonp的方式解决的,在script标签的src属性中加载后端的传过来的js语句,然后在script标签中处理js语句中定义的数据.
