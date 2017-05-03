# 理一理Javascript的原型链
> 特别感谢[JS核心系列：浅谈原型对象和原型链](http://www.cnblogs.com/onepixel/p/5024903.html)给了我很大的启发！本文也是基于此文自己动手做了实验，并得到了相似的结果，遂与大家分享。

在Javascript中，大致存在两种对象：普通对象和函数对象。
一般而言，通过`new Function()`创建的对象是函数对象，其他的是函数对象。
```
// a
function func1() {}
// b
var func2 = function() {}
// c
var func3 = new Function()
```
a和b在创建的时候，JS会自动通过`new Function()`来创建对象，所以a，b，c都是通过`new Function()`创造的函数对象。
```
// o1
var o1 = {}
// o2
var o2 = new Object();
// o3
var o3 = new func1();
```
o1, o2是使用对象字面量的形式创建的普通对象。
o3不是通过`new Function()`的形式创建的对象，所以也是普通对象。

每当创建函数对象时，该对象中都会内置一些属性，其中包括`prototype`和`__proto__`。prototype即为原型对象
其内记录着函数对象的一些属性和方法。
```
function f1() {}
f1.prototype.foo = "bar";
console.log(f1.prototype); // Object {foo: "bar", constructor: function}
console.log(f1.foo); // undefined
```
`prototype`对`f1`不可见。也就是说，`f1`在调用自身属性或方法时不会查找`prototype`内的属性和方法。
![调用](https://image.hduzplus.xyz/image/1493823798318.png)

`prototype`的主要作用是继承。`prototype`内定义的属性和方法都是留给自己的后代使用的。
说到后代，就必须说说js中的原型链。此时，另一个属性`__proto__`就登场了。
`__proto__`的作用是保留父类的`prototype`对象。
`js`在使用`new`表达式创建对象时，会将父类的`prototype`赋值给新对象的`__proto__`。这样就形成了代代继承。
```
var obj = new f1();
console.log(obj.foo); // "bar"
console.log(obj.__proto__); // Object {foo: "bar", constructor: function}
console.log(obj.__proto__ == f1.prototype); // true
```
![结果](https://image.hduzplus.xyz/image/1493824079406.png)
现在我们知道，`obj`中的`__proto__`保存的是`f1`中的`prototype`。即创建出的普通对象的`__proto__`等于创建其的函数对象的原型。

那么`f1`的`prototype`的`__proto__`又是什么呢？
```
console.log(f1.prototype.__proto__ == Object.prototype); // true
console.log(Object.prototype.__proto__); // null
```
![结果2](https://image.hduzplus.xyz/image/1493824162810.png)
可以看出，`f1`的`prototype`的`__proto__`指向了`Object`的`prototype`，而`Object`的`prototype`的`__proto__`为`null`。
可以通过以上示例画出一条非常清晰的链式调用，此链即为 “原型链”。
![原型链](https://image.hduzplus.xyz/image/1493822167096.png)
在拥有这样一条原型链之后，当`obj.foo`执行时，会先查找自身是否有该属性(__不会查找`prototype`__)。如果没找到，则会沿着原型链依此去查找。
在上例中，我们将`foo`定义在`f1`的`prototype`上，`obj.foo`在调用时就在原型链中找到了`foo`这个属性并执行。

总结：
1. 原型链真正的形成靠的是`__proto__`而非`prototype`。`js`在执行对象方法时会查找对象自身是否有该方法，如果不存在，会在原型链上找而不会在自身的`prototype`上找。
2. 如果`__proto__`改变则整条原型链都会发生改变，等于改变了对象的数据类型。
3. 函数的`prototype`不属于自身的原型链。
4. 在原型对象(`prototype`)上定义方法和属性是为了被子类继承和调用。

> 测试用代码可以在我的github上下载，我一直相信，自己动手去测试才是掌握知识点的最好方法。
