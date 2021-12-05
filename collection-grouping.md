---
type: doc
layout: reference
category: Collections
title: "Группировка"
url: https://kotlinlang.ru/docs/collection-grouping.html
---

<!-- # Grouping -->
# Группировка элементов коллекции

<!-- The Kotlin standard library provides extension functions for grouping collection elements.
The basic function [`groupBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/group-by.html) takes a
lambda function and returns a `Map`. In this map, each key is the lambda result and the corresponding value is the `List`
of elements on which this result is returned. This function can be used, for example, to group a list of `String`s by
their first letter.  -->
В стандартной библиотеке Kotlin есть функции-расширения для группировки элементов коллекции. Основная функция для группировки -
[`groupBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/group-by.html), которая принимает лямбда-функцию и возвращает `Map`. В этой `Map` каждый ключ — это результат вычисления лямбда-функции, а соответствующее ему значение — это `List` из элементов, значения которых сходятся с полученным результатом от лямбда-функции. Функцию `groupBy()` можно использовать, например, для группировки списка строк по их первой букве.

<!-- You can also call `groupBy()` with a second lambda argument – a value transformation function.
In the result map of `groupBy()` with two lambdas, the keys produced by `keySelector` function are mapped to the results
of the value transformation function instead of the original elements. -->
Также `groupBy()` может быть передана вторая лямбда-функция — функция преобразования значений. В этом случае ключи, созданные первой лямбдой `keySelector`, будут отражать результаты второй лямбды `valueTransform`, вместо исходных элементов.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five")

    println(numbers.groupBy { it.first().uppercase() }) // {O=[one], T=[two, three], F=[four, five]}

    println(numbers.groupBy(
      keySelector = { it.first() },
      valueTransform = { it.uppercase() }
    )) // {o=[ONE], t=[TWO, THREE], f=[FOUR, FIVE]}
}
```

<!-- If you want to group elements and then apply an operation to all groups at one time, use the function [`groupingBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/grouping-by.html).
It returns an instance of the [`Grouping`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-grouping/index.html)
type. The `Grouping` instance lets you apply operations to all groups in a lazy manner: the groups are actually built
right before the operation execution. -->
Если вам требуется сгруппировать элементы, а затем применить какую-либо операцию ко всем группам одновременно, то используйте функцию
[`groupingBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/grouping-by.html). Она возвращает экземпляр типа
[`Grouping`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-grouping/index.html). В свою очередь `Grouping` позволяет "лениво" применять операции ко всем группам: фактически группы будут создаваться прямо перед выполнением операции.

<!-- Namely, `Grouping` supports the following operations:

* [`eachCount()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/each-count.html) counts the elements in each group.
* [`fold()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold.html) and [`reduce()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce.html)
   perform [fold and reduce](collection-aggregate.md#fold-and-reduce) operations on each group as a separate collection
   and return the results.
* [`aggregate()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/aggregate.html) applies a given operation
   subsequently to all the elements in each group and returns the result.
   This is the generic way to perform any operations on a `Grouping`. Use it to implement custom operations when fold or reduce are not enough. -->
`Grouping` поддерживает следующие операции:
* [`eachCount()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/each-count.html) - подсчитывает количество элементов в каждой группе.
* [`fold()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold.html) и [`reduce()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce.html) - выполняют операции [fold и reduce](collection-aggregate.html#fold-and-reduce) для каждой группы как для отдельной коллекции, после чего возвращают результат.
* [`aggregate()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/aggregate.html) - последовательно применяет данную операцию ко всем элементам в каждой группе, после чего возвращает результат. Это общий способ выполнения любых операций с объектом `Grouping`. Используйте его для реализации собственных операций, когда операций `fold` и `reduce` недостаточно.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five", "six")
    println(numbers.groupingBy { it.first() }.eachCount()) // {o=1, t=2, f=2, s=1}
}
```
