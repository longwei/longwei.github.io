---
layout: post
title: 大话 design pattern
---




What's design pattern?

> a design pattern is a general reusable solution to a commonly occurring problem within a given context in software design

I mean what's it really?

>the essential of DP is **decouple**, or separation of concerns. Of course not two problems are the same, but if you break it down to smaller pieces, there will be intersection between problems, and design pattern is a list of known common problems.

Should I used Design pattern in my project?

> solve things in the simplest way possible. Your goal should be simplicity, not I bend the problem so I can use a design pattern. Don't overdesign your solution, and you may not need them.

why design pattern always linked to OOP then if it is targeted at general solution?

>no programming language are created equally. some are more stubborn than other. for example
* imperative programming (c) => machine view, programer is the god and take full responsiblity for
* OOP (Java) => real world view, no function?->strategy, no global object?->singleton
* functional => math view. no variable?-> make a new copy, no loop?->recursion
> because OOP insist on everything is Object, by putting this constraint on the programming model, we need to come up with something that can by-pass the constraint.
> which is a trade-off around complexity management (Java 8 now have lambda)

why C don't need design pattern?

> C is what computation actually is, it represent what's machine works, not how programmer think.
> singleton? global variable. strtegy? function pointer.


***Let's go through the list***

Creational Patterns

>With the Factory pattern, you produce implementations (Apple, Banana, Cherry, etc.) of a particular interface -- say, IFruit.
>With the Abstract Factory pattern, you produce implementations of a particular Factory interface -- e.g., IFruitFactory. Each of those knows how to create different kinds of fruit.

* Factory Method
  留个接口, subclass来实现
  Factory Method pattern defines an interface for creating objects, uses inheritance and relies on a ***subclass*** to handle the desired object instantiation.

```
//factory method

class A {
    public void doSomething() = 0 {
        Foo f = makeFoo();
        f.whatever();
    }

    protected Foo makeFoo() {
        return new RegularFoo();
    }
}

class B extends A {
    protected Foo makeFoo() {
        //subclass is overriding the factory method
        //to return something different
        return new SuperFoo();
    }
}
```


* Abstract Factory
  已有interface定义, 实现满足这个interface的不同factory实现
  
  a Object delegates the responsibility of object instantiation to another object via composition.
  Instead of making Foo object itself(with a factory method), it going get a dfferent object(abstract factory) to create the foo object.

```
//abstract factory

interface Factory {
    Foo makeFoo();
    Bar makeBar();
}


class A {
    private Factory factory;

    public A(Factory factory) {
        this.factory = factory;
    }

    public void doSomething() {
        //The concrete class of "f" depends on the concrete class
        //of the factory passed into the constructor. If you provide a
        //different factory, you get a different Foo object.
        Foo f = factory.makeFoo();
        f.whatever();
    }
}


//need to make concrete factories that implement the "Factory" interface here

```
a good refer http://butunclebob.com/ArticleS.UncleBob.AbstractFactoryDanielT

* Builder
  构建的过程只是参数的不同而已，比如快餐店
  
  The builder pattern is a good choice when designing classes whose constructors or static factories would have more than a handful of parameters.

  >Consider a restaurant. The creation of "today's meal" is a factory pattern, because you tell the kitchen "get me today's meal" and the kitchen (factory) decides what object to generate, based on hidden criteria.

  >The builder appears if you order a custom pizza. In this case, the waiter tells the chef (builder) "I need a pizza; add cheese, onions and bacon to it!" Thus, the builder exposes the attributes the generated object should have, but hides how to set them.

  ```
  //JavaBean Pattern
  Pizza pizza = new Pizza(12);
  pizza.setCheese(true);
  pizza.setPepperoni(true);
  pizza.setBacon(true);

  //build pattern
  Pizza pizza = new Pizza.Builder(12)
                       .cheese(true)
                       .pepperoni(true)
                       .bacon(true)
                       .build();

  ```

* prototype
  细胞分裂
  The Prototype pattern specifies the kind of objects to create using a prototypical instance.

* singleton
  有且仅有一个
  * _instance and access method should be static
  * construct need to be private
  * using enum for initlization
  * the implementation of singleton should be enum or multu threading will cause issues
  * sample code //TODO

***Structural Patterns***


  * Adapter:
  like a real world adapter :)
  为了保持compatibility，也可以用wrapper, 或者refactoring。

  * Bridge
  The Bridge pattern decouples an abstraction from its implementation, so that the two can vary independently
  比如电路开关，有拨片的，拉的，还有转的。但都可以一个interface来定义, oop经常用，其实这定义有点牵强
  interface Bridge {
    ON();
    OFF()
  }

  * Composite
  The Composite composes objects into tree structures, and lets clients treat individual objects and compositions uniformly
  比如arithmetic expressions 或者 lisp的表达式, 看看vector of vector的面试题

  * Decorator
  The Decorator attaches additional responsibilities to an object dynamically
  就是加wrapper, 组合高于继承

  * Facade
  The Facade defines a unified, higher level interface to a subsystem, that makes it easier to use.
  比如客服电话，跟一个人联系就好了。

  * Flyweight:
  The Flyweight uses sharing to support large numbers of objects efficiently.
  比如线程池/电话网路是实际例子，用户不知道到底有多少资源可用。

  * Proxy:
  The Proxy provides a surrogate or place holder to provide access to an object
  比如check就是银行存款的proxy。


***Behavioral Patterns***


* Chain of Responsibility
  The Chain of Responsibility pattern avoids coupling the sender of a request to the receiver, by giving more than one object a chance to handle the request.
  比如DOM的event处理，或者exception处理

* Command:
  The Command pattern allows requests to be encapsulated as objects, thereby allowing clients to be paramaterized with different requests。
  就是指令实体化，这样就是tracking, undo.

* Interpreter:
  The Interpreter pattern defines a grammatical representation for a language and an interpreter to interpret the grammar.
  比如XML parsing参数...

* Iterator:
  The Iterator provides ways to access elements of an aggregate object sequentially without exposing the underlying structure of the object
  现在已经是所有语言内置了吧

* Mediator:
  The Mediator defines an object that controls how a set of objects interact. Loose coupling between colleague objects is achieved by having colleagues communicate with the Mediator, rather than with each other.
  比如飞机场的控制台，又比如MVC里面的control

* Memento:
  The Memento captures and externalizes an object's internal state, so the object can be restored to that state later.
  比如playlist，知道哪首歌，时间戳，UA就可以完成restore session。

* Observer
  The Observer defines a one to many relationship, so that when one object changes state, the others are notified and updated automatically
  比如手机的notification, signal to multiple slot, 或者make a object observer to another

* State:
  The State pattern allows an object to change its behavior when its internal state changes
  比如state machine.

* Strategy:
  A Strategy defines a set of algorithms that can be used interchangeably
  OO不承认函数造成的

* Template:
  The Template Method defines a skeleton of an algorithm in an operation, and defers some steps to subclasses.
  OOP里面多态的具体应用

* Visitor:
  The Visitor pattern represents an operation to be performed on the elements of an object structure, without changing the classes on which it operates.
  由于OO不承认函数造成的。处理DOM的Event会用到？




