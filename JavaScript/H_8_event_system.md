# JavaScript事件冒泡和事件捕获

事件冒泡和事件捕获，它们分别由微软和网景公司提出，目的是解决页面中事件传播顺序的问题。

```html
<div>
    <p>click!</p>
</div>
```

在上面这一段代码中一个div有一个p子元素，如果它们两个元素都绑定有一个click事件，那么怎么才能知道谁绑定的事件会先被触发呢？

微软和网景就分别提出了事件冒泡和事件捕获两种几乎相反的模型。

## 事件冒泡

事件冒泡，即事件开始时由最具体的元素接收，然后逐级向父级元素传播。在以上代码中，点击click事件的传播顺序为：p>div>html>body>document>window

## 事件捕获

事件捕获，即最外层的元素最先接受事件，最具体的元素最后接收事件。在上面代码中，点击click事件的传播顺序为：window>document>body>html>div>p

## DOM事件流

在单击一个元素的时候，你也单击了它的父级元素，甚至整个页面。事件流就是用来描述从页面接收事件的顺序。

“DOM2级事件流”规定事件流包括三个阶段：事件捕获阶段，目标阶段，冒泡阶段。以上面的代码为例，单击p元素会按如下顺序触发事件：window>document>body>html>div>p>p>div>html>body>document>window

在DOM事件流中，实际的目标在捕获阶段不会接收到事件，意味着捕获阶段进行到div就结束了。下一阶段是目标阶段，p上的事件发生，并在事件中被看成冒泡阶段的一部分。然后冒泡阶段发生，事件逆向传回。

"DOM2级事件"规范要求捕获阶段不涉及事件目标，但IE9，Safari，Chrome，FF，Opera9.5及更高版本都会在捕获阶段触发目标对象上的事件，所以，就有两个机会在目标对象上操作事件。

## 事件处理程序
响应某个事件的函数就叫做事件处理程序(或事件侦听器)，事件处理程序名称以"on"开头。为事件指定处理程序的几种方法：

#### HTML事件处理程序
每个html标签支持的事件，都可以使用一个与相应事件处理程序名同名的html属性来指定。如下所示。
```html
<input type="button" value="click" onclick="alert('click success!')"/>
```
当然也可以在script标签中定义函数，然后在属性之中调用。

#### DOM0级事件处理程序
简单，跨浏览器。在事件流的冒泡阶段处理。
```js
var btn-alert = document.getElementById('btn-alert');
btn-alert.onclick = function(){
    alert(this.id);
}
```
#### DOM2级事件处理程序
"DOM2级事件"定义了两个方法，用于处理指定和删除事件处理程序的操作:addEventListener()和removeEventListener()。它门接收三个参数:要处理的事件名，事件处理程序的函数，和一个布尔值。布尔值为true，表示在捕获阶段调用事件处理程序;如果为false，表示在冒泡阶段调用事件处理程序。

使用DOM2级方法添加事件处理程序的主要好处是可以添加多个事件来处理程序。

```js
var btn-alert = document.getElementById('btn-alert');
btn-alert.addEventListener('click'， function(){
    alert(this.id);
}， false);

btn-alert.addEventListener('click'， function(){
    alert("hello!");
}， false);
```

以上代码为btn-alert元素添加了两个事件处理程序。它们会按照添加的顺序被触发。

通过addEventListener()添加的事件处理程序只能通过removeEventListener()函数移除，对于上面的代码，由于事件处理函数为匿名函数，所以无法移除。

所以，我们修改代码为:

```js
var btn-alert = document.getElementById('btn-alert');
btn-alert.addEventListener('click'， handler， false);
var handler = function(){
    alert(this.id);
}
btn-alert.removeEventListener('click'， handler， false);
```
#### IE事件处理程序
IE实现了与DOM中类似的两个方法:attachEvent()和detachEvent()。它们接收两个参数:事件处理程序名称，事件处理程序函数。通过attachEvent()添加的事件处理程序在冒泡阶段调用。注意，事件处理程序名不是"click"，而是"onclick"，其他同理。
```js
var btn-alert = document.getElementById('btn-alert');
btn-alert.attachEvent('onclick'， function(){
    alert("hello!"); 
}); 
```
不同于使用addEventListener()方法，使用attachEvent()方法的情况下，事件处理程序会在全局作用域内运行，this等于window。

```js
var btn-alert = document.getElementById('btn-alert');
btn-alert.attachEvent('onclick'， function(){
    alert(this == window); //true 
});
```

同样的，通过attachEvent()方法添加的事件处理程序只能通过detachEvent()方法移除，但对于上面的匿名处理程序函数是无法移除的，所以，必须做一下处理:

```js
var btn-alert = document.getElementById('btn-alert');
btn-alert.attachEvent('onclick'， handler);
var handler = function(){
    alert("hello!");
}
//移除成功!
btn-alert.detachEvent('onclick'， handler);
```
#### 跨浏览器事件处理程序
```js
var EventUtil = {
    addHandler: function(element， type， handler){
        if(element.addEventListener){
            element.addEventListener(type， handler， false);
        }else if(element.attachEvent){
            element.attachEvent("on"+type， handler);
        }else{
            element["on"+type] = handler;
        }
    }，

    removeHandler: function(element， type， handler){
        if(element.removeEventListener){
            element.removeEventListener(type， handler， false);
        }else if(element.detachEvent){
            element.detachEvent("on"+type， handler);
        }else{
            element["on"+type] = null;
        }
    }
};
//调用
var btn-alert = document.getElementById("btn-alert");
var handler = function(){
    alert("hello!");
};
EventUtil.addHandler(btn-alert， "click"， handler);
EventUtil.removeHandler(btn-alert， "click"， handler);
```

我们添加了两个兼容DOM和IE的方法。

## 事件对象

#### DOM中的事件对象

在触发DOM上的某个事件时，就会产生一个事件对象event，该对象包含所有与事件有关的信息。

主要有以下几个常用属性和方法:
target 事件的目标元素， currentTarget 事件处理程序当前正在处理的元素， type 被触发的事件类型

preventDefault() 取消事件的默认行为。使用前提是cancelable属性值为true

stopPropagation() 取消事件的进一步捕获或冒泡，同时阻止处理事件程序被调用。使用前提是bubbles属性值为true。

#### IE中的事件对象

大多数情况下，通过window.event访问。

cancelBubble， 默认false， 把属性值设置为true时，相当于DOM中的stopPropagation()方法，取消冒泡

returnValue， 默认为true， 把属性值设置为false，相当于DOM中的preventDefault()方法，取消事件的默认行为

srcElemet，事件的目标元素，相当于DOM中的target属性

type，被触发的事件类型

#### 跨浏览器的事件对象

```js
var EventUtil = {
    getEvent: function(event){
        retrun event ? event: window.event; 
    }，

    getTarget: function(event){
        return event.target || event.srcElement;
    }，

    preventDefault: function(event){
        if(event.preventDefault){
            event.preventDefault();
        }else{
            event.returnValue = false;
        }
    }，

    stopPropagation: function(event){
        if(event.stopPropagation){
            event.stopPropagation();
        }else{
            event.cancelBubble = true;
        }
    }
}

//调用
var btn-alert = document.getElementById("btn-alert");
btn-alert.onclick = function(event){
    event = EventUtil.getEvent(event);
    EventUtil.preventDefault(event);
};

```

#### 事件委托
```html
<ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
</ul>
```
现有如上一段html，需要实现点击每个li元素，显示其对应的文本内容。

普通的写法
```js
var lis = document.getElementsByTagName('li');
for(var i=0，len=lis.length; i<len; i++){
    lis[i].onclick = function(){
        alert(this.innerHTML);
    };
}
```
以上写法的缺点在于:1。每绑定一个事件都需要占用一定内存;2。如果动态添加了一个li，则它并没有绑定click事件。

用事件委托可以解决以上问题，代码如下:
```js
var parentElement = document.getElementsByTagName('ul')[0];
parentElement.onclick = function(e){
    console.log(EventUtil.getTarget(e).innerHTML);
};
```

