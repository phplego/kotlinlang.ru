---
type: doc
layout: reference
category: "Syntax"
title: "Вложенные и внутренние классы"
url: https://kotlinlang.ru/docs/nested-classes.html
---

<!-- При переводе статьи оригинальная версия была от 11 February 2021 -->

<!-- Nested and inner classes -->
# Вложенные и внутренние классы

<!-- Classes can be nested in other classes: -->
Классы могут быть вложены в другие классы.

```kotlin
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() // == 2
```

<!-- You can also use interfaces with nesting. All combinations of classes and interfaces are possible: You can nest interfaces
in classes, classes in interfaces, and interfaces in interfaces. -->
Вы также можете использовать интерфейсы с вложенностью. Возможны любые комбинации классов и интерфейсов: вы можете
вкладывать интерфейсы в классы, классы в интерфейсы и интерфейсы в интерфейсы.

```kotlin
interface OuterInterface {
    class InnerClass
    interface InnerInterface
}

class OuterClass {
    class InnerClass
    interface InnerInterface
}
```

<!-- ## Inner classes -->
## Внутренние классы

<!-- A nested class marked as `inner` can access the members of its outer class. Inner classes carry a reference to an object of an outer class: -->
Класс, отмеченный как внутренний с помощью слова `inner`, может иметь доступ к членам внешнего класса. Внутренние классы
содержат ссылку на объект внешнего класса.

```kotlin
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}

val demo = Outer().Inner().foo() // == 1
```

<!-- See [Qualified `this` expressions](this-expressions.md) to learn about disambiguation of `this` in inner classes. -->
Подробнее об использовании `this` во внутренних классах: [Qualified `this` expressions](this-expressions.html).

<!-- ## Anonymous inner classes -->
## Анонимные внутренние классы

<!-- Anonymous inner class instances are created using an [object expression](object-declarations.md#object-expressions): -->
Экземпляры анонимного внутреннего класса создаются с помощью [объектов-выражений](object-declarations.html#object-expressions).

```kotlin
window.addMouseListener(object : MouseAdapter() {

    override fun mouseClicked(e: MouseEvent) { ... }

    override fun mouseEntered(e: MouseEvent) { ... }
})
```

<!-- > On the JVM, if the object is an instance of a functional Java interface (that means a Java interface with a single 
> abstract method), you can create it using a lambda expression prefixed with the type of the interface:
>
>```kotlin
> val listener = ActionListener { println("clicked") }
> ``` -->
> В JVM если объект является экземпляром функционального Java-интерфейса (т.е. Java-интерфейса с единственным
> абстрактным методом), вы можете создать его с помощью лямбда-выражения с префиксом — типом интерфейса.
>
> ```kotlin
> val listener = ActionListener { println("clicked") }
> ```
