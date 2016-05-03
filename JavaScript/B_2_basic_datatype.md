# JavaScrpt中的六种数据类型

JavaScript中有五种基本数据类型，即Undefined,Null,Boolean,Number，String,外加一种复杂数据类型Object.JavaScript中所有的数据最终都为这六种中的其中之一。

> #### 注意：
* typeof函数即可用来检测数据类型，它会返回对应类型的字符串值，比如'undefined', 'boolean', 'string', 'number', 'object'；
* 函数属于对象类型，但由于函数的一些特殊属性，当使用typeof检测时，会返回‘function’用以区别于其他对象；
* 特殊值null被认为是一个空的对象引用，当使用typeof检测时会返回'object'。

## Undefined
1. 这种类型的只有undefined一个值。
2. 对于使用var声明而未初始化的变量，它会被默认初始化为undefined。
3. 对于未声明的和使用var声明但未初始化的变量，使用typeof检测时，都会返回undefined。
4. 在定义变量的时候，尽量显示的初始化变量值为非undefined，然后可以通过typeof检测一个变量是否声明。

## Null
1. 这种类型也只有一个值，即null；
2. 逻辑上，null是一个空对象指针，typeof检测时返回'object'；
3. 如果要定义一个将用于保存对象的变量，尽量初始化为null，然后通过该变量是否等于null，即可判断该变量是否已经保存了一个非空对象。
4. undefined值派生自null，所以undefined==null返回true

## Boolean
布尔类型字面值有true和false。
**其他类型与布尔类型的转换**

| type      | true          | false     |
| --------- |:-------------:| :-----:   |
| Boolean   | true          | false     |
| String    | 非空字符串    | ""        |
| Number    | 非零          | 0和NaN    |
| Object    | 非空对象      | null      |
| undefined | /             | undefined |

## Number
Number类型包括整型和浮点型以及特殊的数值NaN。

### 整型
整型其实没啥说的，但需注意几点小的方面：

* JavaScript中字面值默认十进制，同时支持八进制（以0开头）、十六进制（以0x开头）的字面值表示。
* 支持+0和-0的表示，+0==-0返回true。

### 浮点型
浮点型为包含一个小数点，且小数点之后至少有一个数字的字面值。如果小数点后没有数字，则解析为小数点前的整数。浮点型的最高精度为17位小数，可用2e-5表示0.00002。

### NaN
NaN即非数值（Not a Number），一个特殊数值，在本应返回数值而未返回的情况下，为避免抛出错误影响代码执行，使用NaN进行表示。关于NaN注意两点：

* 任何有NaN参与的操作都会返回NaN；
* NaN不等于任何值，包括自身，即NaN==NaN返回false。
* isNaN()方法接受一个任何类型变量作为参数，判断它是否为"不是数值"类型。比如，isNaN(NaN)返回true，isNaN("1")返回false

### 数值范围
```
##### js中能表示的数值
最小数（Number.MIN_VALUE）：5e-324
最大数（Number.MAX_VALUE）：1.7976931348623157e+308
最小安全整数（Number.MIN_SAFE_INTEGER）：-9007199254740991
最大安全整数（Number.MAX_SAFE_INTEGER）：9007199254740991
```
如果在计算过程中，数值超过了js能表示的最大数，则会对其进行转化，正数转化为Infinity，负数转化为-Infinity。这两个数值可通过Number.POSITIVE_INFINITY和Number.NEGATIVE_INFINITY获取。同时，isFinite()可以接受一个数值变量作为参数对其进行判断。

参考：[http://segmentfault.com/a/1190000002608050](http://segmentfault.com/a/1190000002608050)

### 数值转换
数值转换即将非数值类型转换为数值，主要涉及到三个函数：Number(), parseInt(), parseFloat()。

#### 转型函数Number()
用于处理任何类型数据
对于Undefined类型，undefined->NaN;
对于Null类型，null->0;
对于Boolean类型，true->1,false->0;
对于String类型，给出以下几个例子：
```
""->0,
"02.1"->2.1,
"ox21"->33,
"12hi"->NaN
```
对于对象类型，则会先调用对象的valueOf方法，然后依照上面的规则进行转换；如果转换结果为NaN，则调用toString()方法，然后继续依照上面的方法进行。

除此之外，都返回NaN。

#### parseInt()
针对字符串，将字符串转换为整型
```
parseInt("")->NaN
parseInt("123hi")->123
parseInt("123.1")->123
parseInt("ox21")->33
parseInt("070")->70
该函数支持指定转换的基数，即传入第二个参数就好：
parseInt("70", 8)->56
parseInt("21", 16)->33
```

#### parseFloat()
针对字符串，将字符串转换为浮点型，参考parseInt()。
同时，parseFloat会忽略第一个小数点以后的小数点以及开头的零，它只解析十进制，含十六进制的字符串只会被转化为零。
```
parseFloat("")->NaN
parseFloat("123hi")->123
parseFloat("123.1")->123.1
parseFloat("ox21")->0
parseFloat("070")->56
```
## String
用于表示由零个或多个16位Unicode字符组成的字符序列，即字符串
```
特殊转义字符 \n, \t, \r, \f
```
字符串的值是不可改变的，若需要改变某个变量保存的字符串，需要创建一个新的字符串，然后消除之前的字符串，因此，字符串拼接时效率较低。以前我在浏览器端渲染数据时，喜欢自己拼接字符串，现在想来，这么做不仅写代码的效率低，也会带来性能问题，所以尽量选用模板渲染引擎啦，比如angular,至于angular的效率嘛，下次研究一下=-=

在js中，字符转换用的最多的方法就是toString()了，可以给toString指定转换的基数，比如
```
var a=10;
console.log(a.toString(2));
输出"1010"
```
当然还可以通过 10+""将10转换为"10"。

## Object
对象就是一组数据和功能的集合
```
创建对象
var ob = new Object();
构造函数属性，在这里就是function Object(){};
ob.constructor;
检查是否有某个属性,这里返回true
ob.a = 1;
ob.hasOwnProperty(a);
检查传入对象是否是该对象的原型
isPrototypeOf(object)
检查属性是否可遍历
propertyIsEnumerable(propertyName)
返回对象的字符串表示，与执行环境相对应
toLocaleString()
返回对象的字符串表示
toString()
返回对象的字符串、数值、布尔值表示
valueOf()
```
Object对象无疑是最复杂的数据类型，后面将结合特性和内容继续补充。















