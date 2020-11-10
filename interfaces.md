---
type: doc
layout: reference
category: "Syntax"
title: "Интерфейсы"
url: https://kotlinlang.ru/docs/reference/interfaces.html
---

<!--# Interfaces-->
# Интерфейсы

<!--Interfaces in Kotlin are very similar to Java 8. They can contain declarations of abstract methods, as well as method
implementations. What makes them different from abstract classes is that interfaces cannot store state. They can have
properties but these need to be abstract or to provide accessor implementations.-->
Интерфейсы в <b>Kotlin</b> очень похожи на интерфейсы в <b>Java 8</b>. Они могут содержать абстрактные методы, методы с реализацией. Главное отличие интерфейсов от абстрактных классов заключается в невозможности хранения переменных экземпляров. Они могут иметь свойства, но те должны быть либо абстрактными, либо предоставлять реализацию методов доступа. 

<!--An interface is defined using the keyword *interface*{: .keyword }-->
Интерфейс определяется ключевым словом **interface**:

```kotlin
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
Класс или объект могут реализовать любое количество интерфейсов:

```kotlin
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
Вы можете объявлять свойства в интерфейсах. Свойство, объявленное в интерфейсе, может быть либо абстрактным, либо иметь свою реализацию методов доступа. Свойства в интерфейсах не могут иметь теневых полей, соответственно, методы доступа к таким свойствам не могут обращаться к теневым полям.

```kotlin
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
Когда мы объявлем большое количество типов в списке нашего супертипа, может так выйти, что мы допустим более одной реализации одного и того же метода. Например:

```kotlin
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
    
    override fun bar() {
        super<B>.bar()
    }
}
```

<!--Interfaces A and B both declare functions foo() and bar(). Both of them implement foo(), but only B implements bar() (bar() is not marked abstract in A, because this is the default for interfaces, if the function has no body). Now, if we derive a concrete class C from A, we, obviously, have to override bar() and provide an implementation.
However, if we derive D from A and B, we need to implement all the methods which we have inherited from multiple interfaces, and to specify how exactly D should implement them. This rule applies both to methods for which we've inherited a single implementation (bar()) and multiple implementations (foo()).-->
Оба интерфейса *A* и *B* объявляют функции *foo()* и *bar()*. Оба реализуют *foo()*, но только *B* содержит реализацию *bar()*
(*bar()* не отмечен как абстрактный метод в интерфейсе *A*, потому что в интерфейсах это подразумевается по умолчанию, если у функции нет тела). Теперь, если мы унаследуемся каким-нибудь классом *C* от интерфейса *A*, нам, очевидно, придётся переопределять метод *bar()*, обеспечивая его реализацию. 

Однако, если мы унаследуемся классом *D* от интерфейсов *A* и *B*, нам надо будет переопределять все методы, которые мы унаследовали от этих интерфейсов. Это правило касается как тех методов, у которых имеется только одна реализация (*bar()*), так и тех, у которых есть несколько реализаций (*foo()*).
