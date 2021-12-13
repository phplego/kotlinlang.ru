---
type: doc
layout: reference
category: "Syntax"
title: "Делегирование"
url: https://kotlinlang.ru/docs/delegation.html
---

<!-- При переводе статьи оригинальная версия была от 31 March 2021 -->

<!--# Delegation-->
# Делегирование

<!--The [Delegation pattern](https://en.wikipedia.org/wiki/Delegation_pattern) has proven to be a good alternative to 
implementation inheritance, and Kotlin supports it natively requiring zero boilerplate code.-->
[Шаблон делегирования](https://ru.wikipedia.org/wiki/%D0%A8%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%B4%D0%B5%D0%BB%D0%B5%D0%B3%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)
является хорошей альтернативой наследованию, и Kotlin поддерживает его нативно, освобождая вас от необходимости
написания шаблонного кода.

<!-- A class `Derived` can implement an interface `Base` by delegating all of its public members to a specified object: -->
Класс `Derived` может реализовать интерфейс `Base`, делегировав все свои public члены указанному объекту.

```kotlin
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

class Derived(b: Base) : Base by b

fun main() {
    val b = BaseImpl(10)
    Derived(b).print()
}
```

<!--The `by`-clause in the supertype list for `Derived` indicates that `b` will be stored internally in objects 
of `Derived` and the compiler will generate all the methods of `Base` that forward to `b`.-->
Ключевое слово `by` в оглавлении `Derived`, указывает, что `b` будет храниться внутри экземпляра `Derived`, и компилятор
сгенерирует все методы из `Base`, которые при вызове будут переданы объекту `b`.

<a name="overriding-a-member-of-an-interface-implemented-by-delegation"></a>

<!-- ## Overriding a member of an interface implemented by delegation -->
## Переопределение члена интерфейса, реализованного делегированием

<!-- [Overrides](inheritance.md#overriding-methods) work as you expect: the compiler will use your `override` 
implementations instead of those in the delegate object. If you want to add `override fun printMessage() { print("abc") }` to 
`Derived`, the program would print *abc* instead of *10* when `printMessage` is called: -->
[Переопределения](inheritance.html#overriding-methods) работают так, как вы ожидаете: компилятор будет использовать
ваши `override` реализации вместо реализаций в объекте делегата. Если вы хотите добавить
`override fun printMessage() { print("abc") }` в `Derived`, программа будет печатать *abc* вместо *10* при вызове `printMessage`.

```kotlin
interface Base {
    fun printMessage()
    fun printMessageLine()
}

class BaseImpl(val x: Int) : Base {
    override fun printMessage() { print(x) }
    override fun printMessageLine() { println(x) }
}

class Derived(b: Base) : Base by b {
    override fun printMessage() { print("abc") }
}

fun main() {
    val b = BaseImpl(10)
    Derived(b).printMessage()
    Derived(b).printMessageLine()
}
```

<!-- Note, however, that members overridden in this way do not get called from the members of the 
delegate object, which can only access its own implementations of the interface members: -->
Однако обратите внимание, что члены, переопределенные таким образом, не вызываются из членов делегата,
который может получить доступ только к своим собственным реализациям членов интерфейса.

```kotlin
interface Base {
    val message: String
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override val message = "BaseImpl: x = $x"
    override fun print() { println(message) }
}

class Derived(b: Base) : Base by b {
    // Это свойство недоступно из `b` реализации `print`
    override val message = "Message of Derived"
}

fun main() {
    val b = BaseImpl(10)
    val derived = Derived(b)
    derived.print()
    println(derived.message)
}
```

<!-- Learn more about [delegated properties](delegated-properties.md). -->
Узнайте больше о [делегированных свойствах](delegated-properties.html).
