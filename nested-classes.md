---
type: doc
layout: reference
category: "Syntax"
title: "Вложенные классы"
url: https://kotlinlang.ru/docs/reference/nested-classes.html
---

<!--Nested Classes-->
# Вложенные классы 

<!--Classes can be nested in other classes-->
Классы могут быть вложены в другие классы

```kotlin
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() // == 2
```

<!--## Inner classes-->
## Внутренние классы
<!--A class may be marked as inner to be able to access members of outer class. Inner classes carry a reference to an object of an outer class:-->
Класс может быть отмечен как внутренний с помощью слова `inner`, тем самым он будет иметь доступ к членам внешнего класса. 
Внутренние классы содержат ссылку на объект внешнего класса:

```kotlin
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}

val demo = Outer().Inner().foo() // == 1
```

<!--See [Qualified *this*{: .keyword } expressions](this-expressions.html) to learn about disambiguation of *this*{: .keyword } in inner classes.-->
Подробнее об использовании `this` во внутренних классах: [Qualified `this` expressions](this-expressions.html)

<!--## Anonymous inner classes-->
## Анонимные внутренние классы
<!--Anonymous inner class instances are created using an [object expression](object-declarations.html#object-expressions):-->
Анонимные внутренние экземпляры классов создаются с помощью [object expression](object-declarations.html#object-expressions):

```kotlin
window.addMouseListener(object: MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }
                                                                                                            
    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
})
```

<!--If the object is an instance of a functional Java interface (i.e. a Java interface with a single abstract method),
you can create it using a lambda expression prefixed with the type of the interface:-->
Если объект является экземпляром функционального Java-интерфейса (т.е. Java-интерфейса с единственным абстрактным методом), 
вы можете создать его с помощью лямбда-выражения с префиксом — типом интерфейса:

```kotlin
val listener = ActionListener { println("clicked") }
```
