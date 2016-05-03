# 函数表达式

## 匿名函数
通过函数声明定义函数时，可以通过一个name属性获取到函数的名字。而通过函数表达式定义函数时，会先创建一个函数，然后将它复制给一个变量，当我们尝试去获取其name属性时，返回为空字符串，所以，这样所创建的函数就叫做匿名函数，即anoymous function。如下：
```js
function test(){};
console.log(test.name); // "test"

var anoymous = function(){};
console.log(anoymous.name); // ""
```
## 闭包closure
闭包是指能访问另一个函数作用域中的变量的函数，创建闭包的常见方式是在一个函数内部定义一个函数。
#### 函数调用的过程
```js
function compare(value1, value2){
    return value1-value2;
};
compare(1,2);
```
如上，定义了一个compare函数，同时，我们在全局环境中调用了compare函数。调用时，会创建一个包含arguments、value1、value2的活动对象，并将其作为compare函数的变量对象，同时创建了该变量对象的作用域链，在该作用域链中，全局执行环境的变量对象位于第二位，compare函数的变量对象位于第一位，函数在执行过程中，会顺着作用域链，在变量对象中查找变量。如下：

![](http://7xlzgd.com1.z0.glb.clouddn.com/closure_1)

**变量对象**
每个执行环境都有一个表示变量的对象，即变量对象。上面提到全局环境的变量对象会始终存在，而像compare函数的变量对象都属于局部变量对象，只在该函数执行过程中存在。

**作用域链**
在创建函数时，会创建一个预先包含全局变量对象的作用域链，该作用域链保存在内部的[[scope]]属性中。而当调用函数时，会为函数创建一个执行环境，然后通过复制函数的[[scope]]属性中的对象构建起执行环境的作用域链。此后，函数的活动对象，作为变量对象压入执行环境的作用域链。

**闭包的情况**
```js
var createComparisionFunction = function(propertyName){
    return function(object1, object2){
        var value1 = object1[propertyName],
            value2 = object2[propertyName]
        if(value1<value2){
            return -1;
        }else if(value1>value2){
            return 1;
        }else{
            return 0;
        }
    }
};
var compare = createComparisionFunction("name");
var result = compare({"name": "Nicholas"}, {"name": "Greg"});
```
![](http://7xlzgd.com1.z0.glb.clouddn.com/closure_2)

对于闭包，它会将它的包含函数的变量对象加到作用域链中，所以在createComparisionFunction中创建的匿名函数包含了外部函数的作用域，且当createComparisionFunction函数执行完后，它的变量对象不会被销毁，因为，在匿名函数的作用域链中依然有对其的引用，所以直到匿名函数被销毁，createComparisionFunction函数的变量对象(亦即活动对象)才会被销毁。

**注意**
闭包会携带包含它的函数的作用域，因此会比其他函数占用更多内存，不能过度使用。

**闭包与变量**
作用域链机制带来的一个副作用，即闭包函数只能访问包含函数中一个变量的最后一个值。比如面试中常常问到的一个问题，实现jQuery中的index方法，或者具体来说，有一个ul元素，如何做到在我点击其中的li时，输出该li的索引，先来看一种实现：
```js
var list = ul_element.getElementsByTagName('li');
for(var i=0, len=list.length; i<len; i++){
    list[i].onclick = function(){
            console.log(i+1);
    };
}
```
在上面的实现中，如果共有4个li元素，则每次点击，都会输出5。因为每一个li元素的onclick事件绑定的匿名函数中，都有对其包含环境中i的引用，而当for循环结束，该环境中i的值为4，所以每一个onclick事件响应时，输出的i+1均为5。

下面是一种合理的实现：
```js
var list = ul_element.getElementsByTagName('li');
for(var i=0, len=list.length; i<len; i++){
    list[i].onclick = (function(num){
        return function(){
            console.log(num+1);
        }
    })(i);
}
```
**闭包中调用this对象**
```js
var name = "global";
var person = {
    name: "local",
    getName: function(){
        return function(){
            return this.name;
        }
    }
};
console.log(person.getName()()); //非严格模式 global
```
该对象的方法在全局环境中被调用，返回一个匿名函数，所以匿名函数的执行环境为全局环境，返回全局中的name值。

在闭包内顺利访问外部函数的this对象，如下：
```js
var name = "global";
var person = {
    name: "local",
    getName: function(){
        var that = this;
        return function(){
            return that.name;
        }
    }
};
console.log(person.getName()()); // local
```
**内存泄漏**
闭包的作用域链中保存着HTML元素，意味着该元素将无法被销毁。(IE9之前，IE懒得考虑了，所以了解就好吧==)

## 模仿块级作用域
js中没有块级作用域，同Python。如下:
```js
for(var i=0; i<5; i++){
    console.log(i);
}
console.log(i); // 5
```
在这里，i是定义在全局变量对象中的，不会随着for循环的结束而从内存中销毁，从i被定义开始，就可以在全局环境中访问它。

我们可以使用匿名函数来模拟块级作用域(私有作用域)，如下：
```js
(function(){
    //块级作用域，其实就是一个函数的作用域
})();
```
定义了一个匿名函数，然后通过()将其转换为函数表达式，最后立即调用它。
## 私有变量
任何在函数中出现的变量都可以认为是私有变量，因为不能在函数外部访问。



