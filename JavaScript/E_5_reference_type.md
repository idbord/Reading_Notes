# 引用类型

## 定义
什么是引用类型？
js的变量分为基本类型和引用类型。基本类型变量的值为五种基本数据类型，而引用类型变量的值多为包含多个值的对象，即为该变量类型的一个实例。

## Object类型
创建Object类型的实例有两种方法：
```js
var apple = new Object();
var apple = {}; 
```
第一种是new操作符跟Object构造函数，
第二种是对象字面量表示法。
开发中更多的愿意使用后一种方法，因为代码量少，同时给人一种封装的感觉。

在向函数传递大量可选参数时，多选用传递对象的方法，参数及其值作为对象的键值对。

访问对象的属性常通过点方法，但也可以使用[]访问，后者的优点在于可以通过变量访问属性值，如：
```js
var propertyName = "name";
apple[propertyName];
```
## Array类型
Array也是一种常用的类型，数组嘛，哪里都有它。js中的数组和其他语言中的数组有一点不太一样，它允许一个数组中有不同类型的值，可以叫做**动态数组**。

创建Array类型的实例也有两种方法：
```js
var categories = new Array();
var categories = []; 
```
第一种是new操作符跟Array构造函数，
第二种是数组字面量表示法。
在第一种方法中，如果只传递一个参数，根据参数类型不同情况有所区分：
```js
var categories = new Array(3);//创建一个包含三项的数组，值均为undefined
var categories = new Array(“yang”);//创建一个包含一项的数组，值为“yang”
```
### length属性
length是数组对象的一个重要属性，它的值总是>=0，同时，length的值是可写的，就是我们可以通过将length的值减小n，从而删除掉数组尾部的n个元素，同时，也可以通过length向数组中插入元素。
```js
var categories = [1,2,3];
categories.length = 2;
console.log(categories[2]);//undefined
categories[5] = 6;
console.log(categories[5]);//6
console.log(categories.length);//6
```
### 检测数组
检测数组有两种方法：
```js
var arr = [];
arr instanceof Array; //true
Array.isArray(arr); //true,IE9+
```
### 转换方法
调用数组对象的toString方法会返回数组元素的字符串形式，每个元素由一个“,“隔开，而调用valueOf方法则会依然返回原数组，如下：
```js
var a = [1,2,3];
a.toString();// "1,2,3"，相当于a.join(',');
a.valueOf();// [1,2,3]
```
### 栈方法
js数组拥有的栈这种数据结构的方法，即为压栈push和弹出栈pop。对于，js数组而言，push方法在数组尾部增加传递的元素，同时返回压入之后的数组，而pop方法则在弹出数组尾部的元素，同时返回弹出的元素。

### 队列方法
js数组拥有的队列这种数据结构的方法，即为入队和出队。结合上面的栈方法，js数组可以划分出两组队列方法：

* push和shift
* unshift和pop

第一种是数组尾部作为队尾的队列，同样，push方法返回新队列，shift方法返回出队元素；
第二种是数组头部作为队尾的队列，unshift方法返回新队列，pop方法返回出队元素。
### 重排序方法
js数组拥有reverse和sort两个排序方法，其中，reverse是反向排序，sort默认是按升序排序，它比较的是字符串，即使数组元素是数值类型，依然如此，所以这样的排序并不能很好的满足我们的需求，但是，sort方法能够接收一个参数——比较函数，我们可以通过DIY一个比较函数，来决定如何排序。我们可以向比较函数传递两个参数，通过比较两个参数的大小，返回结果，若结果为负数，则交换数组中两个元素的位置，若结果为非负数，则保持这两个元素在数组中的位置。如下是一种简短的写法：
```js
var list = [12,32,13,4];
var compareUp = function(para1, para2){
    return para1-para2;
}// 这是一个升序排序的比较函数
list.sort(compareUp);
console.log(list[0]); // 4

var compareDown = function(para1, para2){
    return para2-para1;
}// 这是一个降序排序的比较函数
list.sort(compareDown);
console.log(list[0]); //32
```
### 操作方法

* slice(a,b),基于现有数组的位置a到位置b创建一个新的数组，不包括a位置的元素，类似Python的切片
* splice，非常强大的一个方法，可对数组元素进行删除，插入，替换。
```js
var a = [1,2,3];
a.splice(0,2); // 删除的第一项，删除的项数目
console.log(a); // [3]

a.splice(1,0,11,12); // 从当前数组的位置1开始插入11和12
console.log(a); // [3,11,12]

a.splice(1, 1, 11, 12); // 删除当前数组位置0的元素，然后在位置0插入11和12
console.log(a); // [3, 11, 12, 12]
```
* concat，将传递的参数中的元素插入数组尾部
```js
var a = [1,2,3];
a.concat(4,5);
console.log(a); // [1,2,3,4,5]
```
### 位置方法
数组实例有两个位置方法：
indexOf和lastIndexOf，都接收要查找的项和开始查找的位置这两个参数，默认indexOf从数组开始往后查找，另一个则从数组尾部往前开始查找，返回找到想得index，找不到返回-1。
### 迭代方法
数组实例共有五个迭代方法，即every、filter、forEach、map、some，它们都接收三个参数，即当前项的值、项的索引、数组本身。
五个迭代方法中，every和some方法返回true或false，filter和map方法返回处理后的数组，forEach和for循环迭代数组类似。
### 缩小方法
缩小方法，即迭代所有项，构建一个最终返回的值。共有reduce和reduceRight两个方法，区别在于迭代方向相反。
```js
var list = [1,2,3,4];
var mul = list.reduce(function(prev, cur, index, array){
    return prev*cur;
});
console.log(mul); // 24
```
## Function类型
js中，函数其实就是对象，函数名就是一个变量，它包含指向函数对象的指针，函数可以作为变量进行传递。同时，js中的函数没有重载。
### 函数声明和函数表达式
如下：
```js
// 函数声明
console.log(output(2));// 4
function output(a){
    return a*a;
}

// 函数表达式
console.log(output(2));// 报错
var output = function(a){
    return a*a;
}
```
在第一部分，代码正常运行，在未定义之前即可调用output函数，因为在预处理过程中，js解析器通过函数声明提升的过程，声明了函数并将其放到了代码树的顶端。所以，即使调用在定义之前，也没有问题。
而第二部分，函数位于一个初始化语句中，在调用output时，output仅仅是一个值为undefined的变量，并没有保存对函数的引用，所以导致“unexpected identifier”错误。
### 函数的内部对象arguments和this
arguments对象类似于数组，包含着所有传入函数的参数，该对象有一个callee属性，该属性是一个指针，指向拥有该对象的函数，通过该属性，我们可以对阶乘函数进行改写，如下：
```js
var factorial = function(num){
    if(num<=1){
        return 1;
    }else{
        return num*arguments.callee(num-1);// 改写前 return num*factorial(num-1);
    }
}
```
如此，可以不限于函数名，对该函数进行调用，不过，在严格模式下，无法访问callee，所以可以做如下修改：
```
var factorial = (function f(num){
    if(num<=1){
        return 1;
    }else{
        return num*f(num-1);
    }
});
```

另外一个是this对象，它是对函数执行环境对象的引用，如下：
```js
var name="jack";
var person = {"name": "Bob"};
function sayName(){
    console.log(this.name);
}
sayName();// jack
person.sayName = sayName;
person.sayName();// Bob
```
### 属性和方法
函数包含两个属性：length和prototype。
length即为函数接收参数的个数。
prototype即原型属性，prototype是保存所有实例方法的所在，诸如toString、valueOf等方法都实际保存在该属性名下。
函数包含两个非继承而来的方法，apply和call，它们作用相同，就是在特定的作用域中调用函数，我们知道函数体内的this对象是对函数执行环境的引用，这里apply和call的作用就相当于设置函数体内的this对象的值，它们的区别在于apply接收只两个参数，在函数执行的作用域和参数数组，而call除第一个参数外，其他参数都直接传递而非参数数组。call和apply的强大之处在于能够**扩充函数赖以运行的作用域**
```js
var name="jack";
var person = {"name": "Bob"};
function sayName(){
    console.log(this.name);
}
sayName();// jack
sayName.call(this);// jack
sayName.call(person);// Bob,本次调用，sayName的执行环境发生了改变，因为sayName内部的this指向发生了变化
```
使用call和apply来扩充函数运行的作用域，好处就是对象与方法耦合度低。
## Date类型
```js
创建日期对象：
//月份基于0
var some_date = new Date(2012, 0, 3, 12, 23, 34);//不传参数时默认当前时间
//Tue Jan 03 2012 12:23:34 GMT+0800 (CST)
```
Date.parse接收表示日期的字符串，转化为相应日期的毫秒数。
Date.now()返回调用时日期和时间的毫秒数
### 日期格式化的几个函数
```js
toDateString//星期，月日年
toTimeString//时分秒，时区
toLocaleDateString//星期，月日年
toLocaleTimeString//时分秒，时区
其他的使用时再具体参考吧
```
## RegExp类型
通过正则表达式字面量和RegExp构造函数创建的正则表达式是不一样的，第一种方法创建的表达式会共享一个RegExp实例，而使用构造函数创建的每一个正则表达式都是一个新实例。
```js
var expression = / pattern / flags;
```
flags共包含三种，即g（全局）、i（不区分大小写）、m（多行）
### RegExp实例的属性与方法
属性略

exec()，每次调用返回一个匹配项，设置全局标志时，每次调用都返回新匹配的项。

test()，返回Boolean值。
## 基本包类型
为了便于操作基本类型变量，js提供了三个特殊的引用类型：Boolean、Number、String
在js中，每次读取基本类型变量时，后台都会创建一个对应基本包引用类型的对象，可以像其他引用类型一样调用一些方法来操作他们，如subStr等，而每当读取结束，后台就会立即销毁这个对象，而不像其他引用类型那样等到当前作用域结束才从内存中销毁。
## 单体内置对象
### Global对象
URI编码方法，略

eval()方法，相当于一个js解析器，它接收一个参数，把参数内容当js代码进行解析并执行，通过eval执行的代码具有与该执行环境相同的作用域链。
### Math对象
很多方法，其实用的不多，没必要可以去记吧=.=

Math.ceil 向上舍

Math.floor 向下舍

Math.round 标准四舍五入

Math.random 返回介于0-1的随机数，不包括0和1
