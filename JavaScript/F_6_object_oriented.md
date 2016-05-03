# js中的面向对象

ES5中没有类的概念（ES6中已有），这是JS不同于其他面向对象语言的地方。js中对象是一组没有特定顺序的值，对象的属性或方法都有一个名字，每个名字映射一个值。

## 理解对象
### 属性类型
**数据属性**，它包含一个数据值的位置，在这个位置可以读取和写入值。数据属性有四个描述其行为的特性：

* [[Configurable]]
* [[Enumerable]]
* [[Writable]]
* [[Value]]

当我们直接在对象上定义属性时，属性的[[Configurable]]、[[Enumerable]]、[[Writable]]都被置为了true，而[[Value]]被设置为指定的值。

我们可以通过调用Object.defineProperty()方法修改默认的特性值，如下：
```js
var person = {};
Object.defineProperty(person, "name", {
    writable:false,
    value:"Yang"
});
person.name = "Jack";
console.log(person.name);// Yang
```
注意，就是在调用Object.defineProperty()方法时，如果不指定特性的值，则默认为false。

**访问器属性**，它包含一对get和set方法，get在访问时调用，set在写入时调用。访问其属性具有四个特性：
* [[Configurable]] //默认true
* [[Enumerable]] //默认true
* [[Get]]
* [[Set]]

访问器属性不能直接定义，必须通过调用Object.defineProperty()来定义，如下：
```js
var person = {
    name: "Yang",
    _born: 1994,
    age: 22
};
Object.defineProperty(person, "born", {
    get: function(){
        return this._born;
    },
    set: function(newValue){
        if(newValue>1994){
            this.born = newValue;
            this.age -= newValue-1994;
        }
    }
});
person.born = 1998;
console.log(person.age); // 18
```
当定义多个属性时，可以调用方法Object.defineProperties();
```js
Object.defineProperty(person, {
    _born:{
        writable: true,
        value: 1994
    },
    name: {
        writable: true,
        value: "Yang"
    },
    age:{
        
    },
    born: {
        get: function(){}，
        set: function(){}
    }
});
```
### 属性特性的读取
先取到对象属性的描述符对象，如下：
```js
var descriptor = Object.getOwnPropertyDescriptor(person, "_born");
console.log(descriptor.value);// 1994
```
## 创建对象
每个对象基于一个引用类型创建。
### 工厂模式
工厂模式抽象创建具体对象的过程。ES5没有类的概念，所以通过函数来封装创建对象的细节，如下：
```js
function createPerson(name, age){
    var obj = new Object();
    obj.name = name;
    obj.age = age;
    obj.sayAge = function(){
        console.log(this.name);
    };
    return obj;
}

var person1 = createPerson("Yang", 18);
var person2 = createPerson("Jack", 22);
```
工厂模式不能解决对象识别的问题。
### 构造函数模式
构造函数可以用来创建特定类型的对象，我们可以自定义构造函数来创建自定义对象，如下：
```js
function Person(name, age){
    this.name = name;
    this.age = age;
    this.sayAge = function(){
        console.log(this.age);    
    };
}

var person1 = new Person("Yang", 18);

console.log(person1.constructor == Person);// true
console.log(person1 instanceOf Person);// true
```
通过new操作符加构造函数创建对象会经历四个步骤：
* 创建一个新对象
* 将构造函数的作用域赋给新对象，即this指向新对象
* 执行构造函数中的代码，为新对象添加属性
* 返回新对象

构造函数的缺点在于，每个方法在每一个实例创建时都会重新创建一次，所以每个实例中的方法都是对不同函数的引用。

可以将方法定义到全局中，如下：
```js
function Person(name, age){
    this.name = name;
    this.age = age;
    this.sayAge = sayAge;
}

function sayAge(){
    console.log(this.age);    
};
```
但这么做，封装性很差。
### 原型模式
首先，我们需要**理解原型对象**。

我们创建的每一个函数，都有一个prototype属性，它指向一个对象，而这个对象包含可以由特定类型的实例所共享的属性和方法。简单理解，原型对象是通过构造函数创建的对象实例的原型对象，使用原型对象可以让实例共享它所包含的属性和方法。每一个实例，都有一个内部属性指向该原型对象。另外，创建了自定义构造函数后，其对应原型对象默认只取得constructor属性，该属性指向构造函数。
```js
function Person(){}
Person.prototype = {
    constructor: Person,//会导致它的[[Enumerable]]值为true
    name: "Yang",
    age: 18,
    sayAge: function(){
        console.log(this.age);
    }
};
```
重写原型对象会导致现有原型对象与重写之前创建的实例无法连接，重写之前创建的实例指向的是之前的原型对象。

原型模式的最大问题在于原型对象中的属性是所有实例共享的，对于引用类型属性，如果修改其中一个实例的属性值，则所有的实例都会受到影响。如下：
```js
function Person(){}
Person.prototype = {
    constructor: Person,//会导致它的[[Enumerable]]值为true
    name: "Yang",
    age: 18,
    cource: ["Chinese", "English", "Math"],
    sayAge: function(){
        console.log(this.age);
    }
};
var person1 = new Person();
var person2 = new Person();

person1.course.unshift("Drawing");
console.log(person2.course);// ["Drawing", "Chinese", "English", "Math"]
```
### 组合构造函数和原型模式
我们综合构造函数和原型模式的优点，在创建对象时常将他们组合起来使用，如下：
```js
function Person(name, age){
    this.name = name;
    this.age = age;
}

Person.prototype = {
    constructor: Person,
    sayAge: function(){
        console.log(this.age);
    }
};
```
组合模式中，我们通过构造函数定义实例属性，通过重写原型对象来定义共享属性和方法。
### 其他模式
动态原型模式、寄生构造函数模式、稳妥构造函数模式，暂时不做具体了解。
## 继承
### 原型链继承
利用原型让一个类型继承另外一个类型的属性和方法，我们要做的就是重写新类型的原型对象，将其替换为要继承类型的一个实例，如下：
```js
function superType(){
    this.superValue = true;
}
superType.prototype.getSuperValue = function(){
    console.log(this.superValue);
};

function subType(){
    this.subValue = false;
}
// 继承superType
subType.prototype = new superType();
// 给新类型增加方法，注意不可通过对象字面量方法增加
subType.prototype.getSubValue = function(){
    console.log(this.subValue);
};

var instance = new subType();
// 新类型的实例调用原实例的方法
instance.getSuperValue(); // true
```
instance调用getSuperValue()时，首先在实例中查找该方法，没有，然后查找其原型对象，即superType的实例，然后查找superType.prototype，顺利找到，然后调用。通过原型链实现继承，在整个原型搜索过程会沿着原型链向上进行。

**原型链方法的问题**

首先，由于新创建类型的原型对象被替换为继承类型的实例，所以继承类型的实例若包含引用类型值，在新类型中都成为了原型属性，这些属性将会被新类型的所有实例共享。

同时，在创建新类型实例时，我们不能向继承类型的构造函数传递参数，因为这会影响到所有的实例。
### 借用构造函数继承
该方法主要是在新类型内部通过call或apply方法去调用继承类型的构造函数。如下：
```js
function superType(){
    this.superValue = true;
}
superType.prototype.getSuperValue = function(){
    console.log(this.superValue);
};

function subType(superValue, gender){
    superType.call(this, superValue);
    this.gender = gender;
}

var instance = new subType(false, "man");
instance.getSuperValue(); // false

**借用构造函数**的问题与构造函数模式创建对象一样，主要是函数不可复用。
```
### 组合继承
组合继承结合原型链和借用构造函数两种继承模式的优点，同时回避掉各自的缺点，是最常用的继承方法。如下：
```js
function superType(name, age){
    this.name = name;
    this.age = age;
}

superType.prototype = {
    constructor: superType,
    sayName: function () {
        console.log(this.name);
    },
    sayAge: function () {
        console.log(this.age);
    }
};

function subType(name, age, gender){
    superType.call(this, name, age);
    this.gender = gender;
}
subType.prototype = new superType();
subType.prototype.constructor = subType;
subType.prototype.sayGender = function(){
    console.log(this.gender);
};
var person2 = new subType("allen", 30, "man");

person2.sayName(); // "allen"
person2.sayGender(); // "man"
```
### 其他
原型式继承、寄生式继承、寄生组合式继承，暂时不做具体了解。
