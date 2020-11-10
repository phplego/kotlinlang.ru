---
type: doc
layout: reference
category: "Syntax"
title: "Делегирование"
url: https://kotlinlang.ru/docs/reference/delegation.html
---

<!--# Delegation-->
# Делегирование

<!--## Class Delegation-->
## Делегирование класса

<!--The [Delegation pattern](https://en.wikipedia.org/wiki/Delegation_pattern) has proven to be a good alternative to implementation inheritance,
and Kotlin supports it natively requiring zero boilerplate code.
A class `Derived` can inherit from an interface `Base` and delegate all of its public methods to a specified object:-->
[Шаблон делегирования](https://ru.wikipedia.org/wiki/%D0%A8%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%B4%D0%B5%D0%BB%D0%B5%D0%B3%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)
является хорошей альтернативой наследованию, и Kotlin поддерживает его нативно, освобождая вас от необходимости написания шаблонного кода.

```kotlin
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

class Derived(b: Base) : Base by b

fun main(args: Array<String>) {
    val b = BaseImpl(10)
    Derived(b).print() // prints 10
}
```

<!--The *by*{: .keyword }-clause in the supertype list for `Derived` indicates that `b` will be stored internally in objects of `Derived`
and the compiler will generate all the methods of `Base` that forward to `b`.-->
Ключевое слово `by` в оглавлении `Derived`, находящееся после типа делегируемого класса, говорит о том, что объект `b` типа `Base` будет храниться внутри экземпляра `Derived`, и компилятор сгенерирует у `Derived` соответствующие методы из `Base`, которые при вызове будут переданы объекту `b`
