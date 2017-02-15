---
type: doc
layout: reference
category: "Syntax"
title: "Интерфейсы"
---

<!--# Interfaces-->
# Интерфейсы

<!--Interfaces in Kotlin are very similar to Java 8. They can contain declarations of abstract methods, as well as method
implementations. What makes them different from abstract classes is that interfaces cannot store state. They can have
properties but these need to be abstract or to provide accessor implementations.-->
Интерфейсы в <b>Kotlin</b> очень похожи на интерфейсы в <b>Java 8</b>. Они могут содержать абстрактные методы, методы с реализацией. Главное отличие интерфейсов от абстрактных классов заключается в невозможности хранения переменных экземпляров. Они могут иметь свойства, но те должны быть либо абстрактными, либо предоставлять реализацию методов доступа. 

<!--An interface is defined using the keyword *interface*{: .keyword }-->
Интерфейс определяется ключевым словом **interface**

``` kotlin
interface MyInterface {
    fun bar()
    fun foo() {
      // необязательное тело
    }
}
```

<!--## Implementing Interfaces-->
## Реализация интерфейсов

<!--A class or object can implement one or more interfaces-->
Класс или объект могут реализовать от одного и более интерфейсов

``` kotlin
class Child : MyInterface {
    override fun bar() {
        // тело
    }
}
```

<!--## Properties in Interfaces-->
## Свойства в интерфейсах

<!--You can declare properties in interfaces. A property declared in an interface can either be abstract, or it can provide
implementations for accessors. Properties declared in interfaces can't have backing fields, and therefore accessors
declared in interfaces can't reference them.-->
Вы можете объявлять свойства в интерфейсах. Свойство, объявленное в интерфейсе, может быть либо абстрактным, либо иметь свою реализацию методов доступа. Свойства в интерфейсах не могут иметь _backing fields_, соответственно, методы доступа таких свойств не могут к ним обращаться.

``` kotlin
interface MyInterface {
    val prop: Int // абстрактное свойство

    val propertyWithImplementation: String
        get() = "foo"

    fun foo() {
        print(prop)
    }
}

class Child : MyInterface {
    override val prop: Int = 29
}
```

<!--## Resolving overriding conflicts-->
## Устранение противоречий при переопределении

<!--When we declare many types in our supertype list, it may appear that we inherit more than one implementation of the same method. For example-->
Когда мы объявлем большое количество типов в списке нашего супертипа, может так выйти, что мы допустим более одной реализации одного и того же метода. К примеру

``` kotlin
interface A {
    fun foo() { print("A") }
    fun bar()
}

interface B {
    fun foo() { print("B") }
    fun bar() { print("bar") }
}

class C : A {
    override fun bar() { print("bar") }
}

class D : A, B {
    override fun foo() {
        super<A>.foo()
        super<B>.foo()
    }
}
```

<!--Interfaces *A* and *B* both declare functions *foo()* and *bar()*. Both of them implement *foo()*, but only *B* implements *bar()* (*bar()* is not marked abstract in *A*,
because this is the default for interfaces, if the function has no body). Now, if we derive a concrete class *C* from *A*, we, obviously, have to override *bar()* and provide
an implementation. And if we derive *D* from *A* and *B*, we don’t have to override *bar()*, because we have inherited only one implementation of it.
But we have inherited two implementations of *foo()*, so the compiler does not know which one to choose, and forces us to override *foo()* and say what we want explicitly.-->
Оба интерфейса *A* и *B* объявляют функции *foo()* и *bar()*. Оба реализуют *foo()*, но только *B* содержит реализацию *bar()*
(*bar()* не отмечен, как абстрактный метод в интерфейсе *A* потому, что в интерфейсах это подразумевается по умолчанию, если у функции нет тела). Теперь, если мы унаследуем какой-нибудь класс *C* от *A*, нам, очевидно, придётся переопределять *bar()* обеспечивать его реализацию. А если мы унаследуем *D* от *A* и *B*, нам не надо будет переопределять *bar()*, потому что мы унаследовали только одну его имплементацию. Но мы получили в наследство две имплементации *foo()*, поэтому компилятору не известно, какую выбрать. Он заставит нас переопределить функцию *foo()* и явно указать что мы имели в виду.  
