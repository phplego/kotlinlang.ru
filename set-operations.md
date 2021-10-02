---
type: doc
layout: reference
category: Collections
title: "Set: специфичные операции"
url: https://kotlinlang.ru/docs/reference/set-operations.html
---

<!-- # Set-specific operations -->
# Set: специфичные операции

<!-- The Kotlin collections package contains extension functions for popular operations on sets: finding intersections, merging,
or subtracting collections from each other. -->
Стандартная библиотека Kotlin включает в себя функции-расширения для популярных операций с множествами (`Set`): поиск пересечений, слияние или вычитание коллекций друг из друга.

<!-- To merge two collections into one, use the [`union()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/union.html)
function. It can be used in the infix form `a union b`.
Note that for ordered collections the order of the operands is important: in the resulting collection, the elements of the
first operand go before the elements of the second. -->
Чтобы объединить две коллекции в одну, используйте функцию
[`union()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/union.html). Её также можно использовать в инфиксной форме - `a union b`. Обратите внимание, что для отсортированных коллекций важен порядок операндов: в результирующей коллекции элементы первого операнда идут перед элементами второго.

<!-- To find an intersection between two collections (elements present in both of them), use [`intersect()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/intersect.html).
To find collection elements not present in another collection, use [`subtract()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/subtract.html).
Both these functions can be called in the infix form as well, for example, `a intersect b`. -->
Чтобы найти пересечения между двумя коллекциями (другими словами поиск элементов, присутствующих в обеих коллекциях), используйте функцию [`intersect()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/intersect.html). И наоборот, для поиска элементов, отсутствующих в другой коллекции, используйте
[`subtract()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/subtract.html). Обе функции могут быть вызваны в инфиксной форме, например, `a intersect b`.

```kotlin
fun main() {
    val numbers = setOf("one", "two", "three")

    println(numbers union setOf("four", "five")) // [one, two, three, four, five]
    println(setOf("four", "five") union numbers) // [four, five, one, two, three]

    println(numbers intersect setOf("two", "one")) // [one, two]
    println(numbers subtract setOf("three", "four")) // [one, two]
    println(numbers subtract setOf("four", "three")) // [one, two]
}
```

<!-- Note that set operations are supported by `List` as well.
However, the result of set operations on lists is still a `Set`, so all the duplicate elements are removed. -->
Обратите внимание, что операции с `Set` также применимы и к `List`. Однако результатом операций всё равно будет `Set`, поэтому все повторяющиеся элементы будут удалены.
