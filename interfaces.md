---
type: doc
layout: reference
category: "Syntax"
title: "Интерфейсы"
url: https://kotlinlang.ru/docs/interfaces.html
---

<!-- # Interfaces -->
# Интерфейсы

<!-- Interfaces in Kotlin can contain declarations of abstract methods, as well as method
implementations. What makes them different from abstract classes is that interfaces cannot store a state. They can have
properties, but these need to be abstract or provide accessor implementations. -->
Интерфейсы в Kotlin могут содержать объявления абстрактных методов, а также методы с реализацией.
Главное отличие интерфейсов от абстрактных классов заключается в невозможности хранения переменных экземпляров.
Они могут иметь свойства, но те должны быть либо абстрактными, либо предоставлять реализацию методов доступа. 

<!-- An interface is defined using the keyword `interface`: -->
Интерфейс определяется ключевым словом `interface`.

```kotlin
interface MyInterface {
    fun bar()
    fun foo() {
      // необязательное тело
    }
}
```

<a name="implementing-interfaces"></a>
<!-- ## Implementing interfaces -->
## Реализация интерфейсов

<!-- A class or object can implement one or more interfaces: -->
Класс или объект могут реализовать любое количество интерфейсов.

```kotlin
class Child : MyInterface {
    override fun bar() {
        // тело
    }
}
```

<a name="properties-in-interfaces"></a>
<!-- ## Properties in interfaces -->
## Свойства в интерфейсах

<!-- You can declare properties in interfaces. A property declared in an interface can either be abstract or provide
implementations for accessors. Properties declared in interfaces can't have backing fields, and therefore accessors
declared in interfaces can't reference them: -->
Вы можете объявлять свойства в интерфейсах. Свойство, объявленное в интерфейсе, может быть либо абстрактным,
либо иметь свою реализацию методов доступа. Свойства в интерфейсах не могут иметь теневых полей, соответственно,
методы доступа к таким свойствам не могут обращаться к теневым полям.

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

<a name="interfaces-inheritance"></a>
<!-- ## Interfaces Inheritance -->
## Наследование интерфейсов

<!-- An interface can derive from other interfaces, meaning it can both provide implementations for their members and declare new
functions and properties. Quite naturally, classes implementing such an interface are only required to define
the missing implementations: -->
Интерфейс может быть производным от других интерфейсов, что означает, что он может как предоставлять реализации для их членов,
так и объявлять новые функции и свойства. Вполне естественно, что классы, реализующие такой интерфейс,
требуются только для определения отсутствующих реализаций.

```kotlin
interface Named {
    val name: String
}

interface Person : Named {
    val firstName: String
    val lastName: String
    
    override val name: String get() = "$firstName $lastName"
}

data class Employee(
    // реализация 'name' не требуется
    override val firstName: String,
    override val lastName: String,
    val position: Position
) : Person
```

<a name="resolving-overriding-conflicts"></a>
<!-- ## Resolving overriding conflicts -->
## Устранение противоречий при переопределении

<!-- When you declare many types in your supertype list, you may inherit more than one implementation of the same method: -->
Когда мы объявлем большое количество типов в списке нашего супертипа, может так выйти, что мы допустим более одной реализации одного и того же метода.

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

<!-- Interfaces *A* and *B* both declare functions *foo()* and *bar()*. Both of them implement *foo()*, but only *B* implements
*bar()* (*bar()* is not marked as abstract in *A*, because this is the default for interfaces if the function has no body).
Now, if you derive a concrete class *C* from *A*, you have to override *bar()* and provide an implementation. -->
Оба интерфейса *A* и *B* объявляют функции `foo()` и `bar()`. Оба реализуют `foo()`, но только *B* содержит реализацию `bar()`
(`bar()` не отмечен как абстрактный метод в интерфейсе *A*, потому что в интерфейсах это подразумевается по умолчанию, если у функции нет тела).
Теперь, если вы унаследуете какой-нибудь класс *C* от интерфейса *A*, вам, очевидно, придётся переопределять метод `bar()`, обеспечивая его реализацию. 

<!-- However, if you derive *D* from *A* and *B*, you need to implement all the methods that you have
inherited from multiple interfaces, and you need to specify how exactly *D* should implement them. This rule applies
both to methods for which you've inherited a single implementation (*bar()*) and to those for which you've inherited multiple implementations (*foo()*). -->
Однако если вы унаследуете класс *D* от интерфейсов *A* и *B*, вам надо будет переопределять все методы, которые вы унаследовали от этих интерфейсов,
и вам нужно указать, как именно *D* должен их реализовать.
Это правило касается как тех методов, у которых имеется только одна реализация (`bar()`), так и тех, у которых есть несколько реализаций (`foo()`).
