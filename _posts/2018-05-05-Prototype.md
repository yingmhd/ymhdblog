---
layout: mypost
title: JS原型
tags: JS
---

## 正文

* 每一个函数都有一个prototype属性，它是一个指针指向对象，这个对象就是通过调用构造函数而创建的的对象实例的原型对象。
>Person.prototype == man
* 所有的原型对象(Person.prototype)里面有一个属性(constructor)指向prototype属性所在的函数指针，即构造函数(Person)。
> Person.prototype.constructor == Person
* 构造函数创建的实例对象（man）,默认会包含一个内部属性(\__proto__)，指向构造函数的原型对象(Person.prototype)
> man.\__proto__ == Person.prototype

### 原型的语法：
```
function Person(){}
Person.prototype.name = 'NIKE';
Person.prototype.size = 29;
var man = new Person();
```
也可以用对象字面量来重写：
```
function Person(){}
Person.prototype = {
  constructor : Person,
  name : 'NIKE',
  size : 29
}
```
> 注意：重写原型对象的时候，该原型对象的constructor指向的不再是它对象的构造函数，而是object的构造函数，所以必须在重写中显示定义constructor为Person

通过原型可以给原生的引用类型增加方法：
```
String.prototype.test = function(){}
var str = 'sssssss';
str.test();
```
>原型模式的缺点：原型中所有的属性都是共享的，所有实例对象都会共享
>>解决办法：构造函数+原型对象
```
function Person(name,size){
  this.name = name;
  this.size = size;
}
Person.prototype.say = function(){
  return this.name;
}
```

另外两种构造模式：
1. 寄生构造模式：（类似于工厂模式）
```
function Person(name,size){
  var o = new object();
  o.name = name;
  o.size = size;
  o.say = function(){
    return this.name;
  }
  return o;
}
```
2. 稳妥构造模式：(没有公共属性)
```
function Person(name,size){
  var o = new object();
  o.say = function(){
    return name;
  }
}
```
>这两种模式的问题是：创建的对象跟构造函数没有一毛钱关系。

### 继承
##### 1.原型链继承
```
function Super(){}
Super.prototype.say = function(){
  return 1;
}
function Sub(){}
Sub.prototype = new Super();
//覆盖
Sub.prototype.say = function(){
  return 2;
}
```
>原理：创建Super的实例，然后把该实例赋给Sub.prototype，那么Sub.prototype则拥有Sup的所有属性和方法，当通过Sub构造函数实例一个对象后，该对象不仅包含Sub的属性和对象，同时还包含Sup的，即实现了继承

> 注意：通过原型链实现继承的时候，不能使用对象字面量，会重写原型链。

> 缺点：原型属性会被所有实例共享，并且不能想超类行的构造函数（Super）传递参数。

##### 2. 借用构造函数
```
function Super(name){
  this.name = name;
}
function Sub(name){
  Super.call(this,name)
}
```
> 优点：可以传递参数<br/>缺点：方法都在构造函数里面定义，没有函数复用的意义

##### 3.组合继承
```
function Super(name){
  this.name = name;
}
Super.prototype.say = function(){
  return this.name;
}
function Sub(name,age){
  //继承属性
  Super.call(this,name);
  this.age = age;
}
//继承方法
Sub.prototype = new Super();
Sub.prototype.constructor = Sub;
```
>缺点：需要调用两次构造函数(call和实例化)

##### 4.原型式继承
```
var person = {
  name : 'NIKE',
  age : 29
}
var anotherPerson = Object.create(person);
```
>优点：不需要创建构造函数<br/>缺点：跟原型模式一样，会共享属性
>>题外：Object.create的实现：

```
function object(o){
  function F(){}
  F.prototype = o;
  return new F();
}
```
##### 5.寄生组合式继承（最理想的继承方式）
```
function inheritPrototype(sub,sup){
  var prototype = Object.create(sup.prototype);
  prototype.constructor = sub;
  sub.prototype = prototype;
}
function Super(name){
  this.name = name;
}
Super.prototype.say = function(){
  return this.name;
}
function Sub(name,age){
  Super.call(this,name);
  this.age = age;
}
inheritPrototype(Sub,Super);
```
>优点：不必为了子类型的原型而调用超类型的构造函数（我们所需要的无非是超类型原型的一个副本）

#### 面向对象中牵扯到的方法：
1. Object.defineProperty() 定义对象数据的属性
```
var person = {}
Object.defineProperty(person,"name",{
  writable : false, //只读
  configurable : false, //不可配置，一旦设置为false,不可再进行配置
  enumerable : false, //不可枚举，不能通过for..in循环
  value : 'NIKE'
})
```
定义多个属性 Object.defineProperties
```
Object.defineProperties(person,{
  name : 'NIke',
  _year:{  //_命名规则，表示该属性属于私有属性
    writable : true,
    value : 2004
  },
  year:{
    get:function(){
      return this._year;
    },
    set:function(value){
      this._year = value;
    }
  }
});
```
2. A instanceof B 判断A是否是B的实例
```
man instanceof Person == true
man instanceof Object == true
```
3. Object.getPrototypeOf() 返回实例对象的内部属性（\__proto__）
```
Object.getPrototypeOf(man) == Person.prototype
```
4. A.prototype.isPrototypeOf(B) 判断A.prototype是否是B的原型对象
```
Person.prototype.isPrototypeOf(man)
```
5. A.hasOwnProperty(B) 检测一个属性B是存在于实例A中(true)，还是存在于原型中(false)
```
man.hasOwnProperty('name')
```
6. hasPrototypeProperty(A,B) 与方法5正好相反，只不过使用方法略有不同
7. Object.keys(A) 返回对象A中所有可枚举属性，以字符串数组返回
