---
type: doc
layout: reference
category: Collections
title: "Операторы plus и minus"
url: https://kotlinlang.ru/docs/collection-plus-minus.html
---

<!-- # Plus and minus operators -->
# Операторы `plus` и `minus`

<!-- In Kotlin, [`plus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus.html) (`+`) and [`minus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus.html)
(`-`) operators are defined for collections.
They take a collection as the first operand; the second operand can be either an element or another collection.
The return value is a new read-only collection:

* The result of `plus` contains the elements from the original collection _and_ from the second operand.
* The result of `minus` contains the elements of the original collection _except_ the elements from the second operand.
   If it's an element, `minus` removes its _first_ occurrence; if it's a collection, _all_ occurrences of its elements are removed. -->

В Kotlin для коллекций определены операторы
[`plus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus.html) (`+`) и
[`minus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus.html) (`-`).
В качестве первого операнда они принимают коллекцию; вторым операндом может быть элемент или другая коллекция.
Возвращаемым результатом является новая неизменяемая коллекция:

* При использовании оператора `plus` результат будет содержать элементы исходной коллекции _и_ элементы второго операнда.
* При использовании оператора `minus` результат будет содержать элементы исходной коллекции _за исключением_ элементов второго операнда. Если второй операнд — это элемент, то оператора `minus` удалит его _первое_ вхождение из исходной коллекции; если же это коллекция, то будут удалены _все_ вхождения её элементов.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")

    val plusList = numbers + "five"
    val minusList = numbers - listOf("three", "four")
    println(plusList) // [one, two, three, four, five]
    println(minusList) // [one, two]
}
```

<!-- For the details on `plus` and `minus` operators for maps, see [Map specific operations](map-operations.md).
The [augmented assignment operators](operator-overloading.md#augmented-assignments) [`plusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html)
(`+=`) and [`minusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html) (`-=`) are
also defined for collections. However, for read-only collections, they actually use the `plus` or `minus` operators and
try to assign the result to the same variable. Thus, they are available only on `var` read-only collections.
For mutable collections, they modify the collection if it's a `val`. For more details see [Collection write operations](collection-write.md). -->

Подробнее об использовании операторов `plus` и `minus` с ассоциативным списком можно ознакомиться в разделе [Map: специфичные операции](map-operations.html).

Также для коллекций определены [расширенные операторы присваивания](operator-overloading.html#augmented-assignments) такие как,
[`plusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html) (`+=`) и
[`minusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html) (`-=`). Однако с неизменяемыми коллекциями они фактически используют операторы `plus` или `minus`, пытаясь присвоить результат той же переменной. Поэтому неизменяемая коллекция должна быть объявлена при помощи ключевого слова `var`.
Для изменяемых коллекций этого не требуется.
Подробнее читайте в разделе [Операции записи](collection-write.html).
