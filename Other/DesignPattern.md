# Design Pattern

## Overview

设计模式永远都在做几件事：

- 把实现和接口**分离**
- 把实现**封装**
- 把接口**抽象**

所以越学你会觉得他们越来越像
**区分的关键是了解并且熟悉他们的使用场景**

## Categories Of Design Pattern

- **Creational Design Patterns**: Creational design patterns focus on handling object creation mechanisms where objects are created in a manner suitable for the situation we're working in. 
- **Structural Design Patterns**: Structural patterns are concerned with object composition and typically identify simple ways to realize relationships between different objects. 
- **Behavioral Design Patterns**: Behavioral patterns focus on improving or streamlining the communication between disparate objects in a system.

## 面向对象的五个原则([SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design)))

- **单一职责原则(Single responsibility principle)**： 一个类，最好只做一件事，只有一个引起它的变化
- **开放封闭原则(Open/closed principle)**： 软件实体应该是可扩展的，而不可修改的。也就是，对扩展开放，对修改封闭的。能保持软件内部的封装体系稳定，不被需求的变化影响
- **依赖倒置原则(Dependency inversion principle)**： 具体而言就是高层模块不依赖于底层模块，二者都同依赖于抽象（接口）；抽象不依赖于具体，具体依赖于抽象。
- **接口隔离原则(Interface segregation principle)**： 接口应该是内聚的，应该避免“胖”接口。一个类对另外一个类的依赖应该建立在最小的接口上，不要强迫依赖不用的方法，这是一种接口污染。
- Liskov替换原则(Liskov substitution principle)： 子类必须能够替换其基类。这一思想体现为对继承机制的约束规范，只有子类能够替换基类时，才能保证系统在运行期内识别子类，这是保证继承复用的基础。

**每一次完成一种设计后，应该再想想，如果复用的话是否会违反以上规则**

## Contents
- [Bridge Pattern](#bridge-pattern)
- [Facade Pattern](#facade-pattern)
- [Composite Pattern](#composite-pattern)
- [Adapter Patterns](#adapter-patterns)
- [Decorator Pattern](#decorator-pattern)
- [Factory Pattern](#factory-pattern)

## Bridge Pattern

>- **Decoupling abstractions from their implementations allows pieces to be independently managed.**

>- **decouple an abstraction from its implementation so that the two can vary independently.**

桥接模式将抽象（当然包括接口）与实现分离:

```
function getBeerById(id, callback) {
    // Make request for beer by ID, then return the beer data.
    asyncRequest('GET', 'beer.uri?id=' + id, function(resp) {
        // callback response
        callback(resp.responseText);
    });
}

addEvent(element, 'click', getBeerByIdBridge);

function getBeerByIdBridge (e) {
    getBeerById(this.id, function(beer) {
        console.log('Requested Beer: '+beer);
    });
}
```

## Facade Pattern

>**Facades serve to simplify complex interfaces**. They can do error checking for you behind the scenes, clean up large objects that are no longer in use, and present an object’s features in an easier to use fashion. 

外观模式用于封装接口的实现

```
function addEvent(el, type, fn) {
    if (window.addEventListener) {
        el.addEventListener(type, fn, false);
    } else if (window.attachEvent) {
        el.attachEvent('on' + type, fn);
    } else {
        el['on' + type] = fn;
    }
}
```

## Composite Pattern

>It allows you to treat a collection of objects the same as you would treat any of the
particular sub-objects.

将对象组合成树形结构以表示“部分-整体”的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性

```
function Sentence () {
    this.words = [];
}

// sentence 与 word 是部分与组合的关系，当把sentence删除时
// 实际上是把删除操作传递下去，逐个删除word
Sentence.prototype.remove = function () {
    this.words.forEach(function (word) {
        word.remove();
    })
}
```

## Adapter Patterns

>The adapter pattern allows you to adapt existing interfaces to classes that would otherwise be incompatible. Objects that use this pattern can also be called wrappers,

将一个类的接口转换成客户希望的另外一个接口。该模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。

>简单的说，需要的东西就在面前，但却不能使用，而短时间又无法改造它，于是我们就想办法适配它

>系统的数据和行为都正常，都接口不符时，我们应该考虑用适配器

```
var Adapter = {
    log: function () {
        console.log(arguments);
    }
}
```

## Decorator Pattern

动态的给一个对象添加一些额外的职责，就增加功能来说，装饰模式比生产子类更为灵活

>They can be used to modify existing systems where we wish to add additional features to objects without the need to heavily modify the underlying code using them.

非常简单的实现：

```
// The constructor to decorate
function MacBook() {
    this.cost = function () { return 997; };
    this.screenSize = function () { return 11.6; };
}
 
// Decorator 1
function Memory( macbook ) {
    var v = macbook.cost();
    macbook.cost = function() {
        return v + 75;
    };
}
 
// Decorator 2
function Engraving( macbook ){
    var v = macbook.cost();
    macbook.cost = function(){
        return  v + 200;
    };
}
 
// Decorator 3
function Insurance( macbook ){
    var v = macbook.cost();
    macbook.cost = function(){
        return  v + 250;
    };
}
 
var mb = new MacBook();
Memory(mb);
Engraving(mb);
Insurance(mb);
```

**注意**，这样的实现是有问题的，它**破坏了开放封闭原则**，直接覆盖了实例的方法。具体实现可以参考`Pro Javscript Desgin Pattern`或者[这里](http://addyosmani.com/resources/essentialjsdesignpatterns/book/#decoratorpatternjavascript);

## Factory Pattern

重点要区分：

- 简单工厂
- 工厂
- 抽象工厂

>This is particularly useful if the object creation process is relatively complex, e.g. if it strongly depends on dynamic factors or application configuration.

>工厂方法模式，定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类

Javascript中没有接口（但是可以用类来代替）！很多的模式在Javascript中都违反了“开放-封闭”的原则，如果要复用就要修改类（或者用类来代替接口）！

>工厂方法模式的实现时，客户端需要决定实例化哪一个工厂来实现运算类，选择判断的问题还是存在的，也就是说，工厂方法把简单工厂的内部逻辑判断移到了客户端代码来进行（这是与**与简单工厂最大的区别**），你想要加的功能，本来是修改工厂类的（**简单工厂方法**），而现在是修改客户端








