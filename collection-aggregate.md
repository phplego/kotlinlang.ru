---
type: doc
layout: reference
category: Collections
title: "Объединение"
url: https://kotlinlang.ru/docs/reference/collection-aggregate.html
---

<!-- # Aggregate operations -->
# Операции объединения

<!-- Kotlin collections contain functions for commonly used _aggregate operations_ – operations that return a single value based
on the collection content. Most of them are well known and work the same way as they do in other languages:

* [`minOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/min-or-null.html) and [`maxOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/max-or-null.html) return the smallest and the largest element respectively. On empty collections, they return `null`.
* [`average()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/average.html) returns the average value of elements in the collection of numbers.
* [`sum()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sum.html) returns the sum of elements in the collection of numbers.
* [`count()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) returns the number of elements in a collection. -->
Коллекции Kotlin содержат функции для часто используемых _операций объединения_ (ориг. _aggregate operations_), которые возвращают одно значение на основе содержимого коллекции. Большинство из них хорошо известны и работают так же, как и на других языках:

* [`minOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/min-or-null.html) и
  [`maxOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/max-or-null.html) возвращают наименьший и наибольший элемент коллекции соответственно. Если коллекция пуста, то вернётся `null`.
* [`average()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/average.html) возвращает среднее значение элементов в коллекции чисел.
* [`sum()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sum.html) возвращает сумму элементов в коллекции чисел.
* [`count()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) возвращает количество элементов в коллекции.

```kotlin
fun main() {
    val numbers = listOf(6, 42, 10, 4)

    println("Count: ${numbers.count()}") // Count: 4
    println("Max: ${numbers.maxOrNull()}") // Max: 42
    println("Min: ${numbers.minOrNull()}") // Min: 4
    println("Average: ${numbers.average()}") // Average: 15.5
    println("Sum: ${numbers.sum()}") // Sum: 62
}
```

<!-- There are also functions for retrieving the smallest and the largest elements by certain selector function or custom [`Comparator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparator/index.html):

* [`maxByOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/max-by-or-null.html) and [`minByOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/min-by-or-null.html) take a selector function and return the element for which it returns the largest or the smallest value.
* [`maxWithOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/max-with-or-null.html) and [`minWithOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/min-with-or-null.html) take a `Comparator` object and return the largest or smallest element according to that `Comparator`.  -->
Также существуют функции для получения самых маленьких и самых больших элементов в коллекции с помощью определённой функции-селектора или пользовательского [`Comparator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparator/index.html):

* [`maxByOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/max-by-or-null.html) и
  [`minByOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/min-by-or-null.html) принимают функцию-селектор и возвращают элемент, для которого эта функция возвращает наибольшее или наименьшее значение.
* [`maxWithOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/max-with-or-null.html) и
  [`minWithOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/min-with-or-null.html) принимают объект `Comparator` и возвращают наибольший или наименьший элемент, соответствующий этому `Comparator`.

<!-- These functions return `null` on empty collections. There are also alternatives for `maxByOrNull()` and `minByOrNull()`:
[`maxOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/max-of.html) and [`minOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/min-of.html), which do the same but throw a `NoSuchElementException` on empty collections. -->
Если коллекция пуста, то эти функции вернут `null`. У функций `maxByOrNull()` и `minByOrNull()` есть аналоги:
[`maxOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/max-of.html) и
[`minOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/min-of.html), которые работают аналогично, но бросают исключение `NoSuchElementException`, если коллекция пуста.

```kotlin
fun main() {
    val numbers = listOf(5, 42, 10, 4)
    val min3Remainder = numbers.minByOrNull { it % 3 }
    println(min3Remainder) // 42

    val strings = listOf("one", "two", "three", "four")
    val longestString = strings.maxWithOrNull(compareBy { it.length })
    println(longestString) // three
}
```

<!-- Besides regular `sum()`, there is an advanced summation function [`sumOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sum-of.html)
that takes a selector function and returns the sum of its application to all collection elements. Selector can return
different numeric types: `Int`, `Long`, `Double`, `UInt`, and `ULong` (also `BigInteger` and `BigDecimal` on the JVM). -->
Помимо обычного `sum()`, существует расширенная функция
[`sumOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sum-of.html). Она принимает функцию-селектор, которая применяет заданную операцию к каждому элементу коллекции, и возвращает сумму всех элементов с учётом этих изменений. Функция-селектор может возвращать различные числовые типы: `Int`, `Long`, `Double`, `UInt`, и `ULong` (а также `BigInteger` и `BigDecimal` из JVM).

```kotlin
fun main() {
    val numbers = listOf(5, 42, 10, 4)
    println(numbers.sumOf { it * 2 }) // 122
    println(numbers.sumOf { it.toDouble() / 2 }) // 30.5
}
```

<a name="fold-and-reduce"></a>
<!-- ## Fold and reduce -->
## Fold и reduce

<!-- For more specific cases, there are the functions [`reduce()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce.html) and [`fold()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold.html) that apply the provided operation to the collection elements sequentially and return the accumulated result.
The operation takes two arguments:  the previously accumulated value and the collection element. -->
Для более специфичных случаев существуют функции
[`reduce()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce.html) и
[`fold()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold.html), которые последовательно применяют предоставленную операцию к элементам коллекции и возвращают накопленный результат. Операция принимает два аргумента: ранее накопленное значение и элемент коллекции.

<!-- The difference between the two functions is that `fold()` takes an initial value and uses it as the accumulated value on
the first step, whereas the first step of `reduce()` uses the first and the second elements as operation arguments on the first step. -->
Разница между этими двумя функциями состоит в том, что `fold()` принимает начальное значение и использует его как накопленное значение на первом шаге, тогда как `reduce()` на первом шаге в качестве аргументов операции использует первый и второй элементы.

```kotlin
fun main() {
    val numbers = listOf(5, 2, 10, 4)

    val simpleSum = numbers.reduce { sum, element -> sum + element }
    println(simpleSum) // 21
    val sumDoubled = numbers.fold(0) { sum, element -> sum + element * 2 }
    println(sumDoubled) // 42

    // некорректно: первый элемент не будет удвоен
    // val sumDoubledReduce = numbers.reduce { sum, element -> sum + element * 2 }
    // println(sumDoubledReduce)
}
```

<!-- The example above shows the difference: `fold()` is used for calculating the sum of doubled elements.
If you pass the same function to `reduce()`, it will return another result because it uses the list's first and second
elements as arguments on the first step, so the first element won't be doubled. -->
В примере выше показана разница: `fold()` используется для вычисления суммы удвоенных элементов. Если вы передадите ту же функцию в `reduce()`, она вернёт другой результат, поскольку он использует первый и второй элементы списка в качестве аргументов на первом шаге, в связи с чем первый элемент не будет удвоен.

<!-- To apply a function to elements in the reverse order, use functions [`reduceRight()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right.html)
and [`foldRight()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold-right.html).
They work in a way similar to `fold()` and `reduce()` but start from the last element and then continue to previous.
Note that when folding or reducing right, the operation arguments change their order: first goes the element, and then the accumulated value. -->
Существуют функции [`reduceRight()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right.html) и
[`foldRight()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold-right.html), которые работают аналогично `fold()` и `reduce()`, но в обратном порядке: начинают с последнего элемента, а затем переходят к предыдущему. Обратите внимание, что при использовании `foldRight()` или `reduceRight()` аргументы операции меняются местами: сначала идёт элемент, а затем накопленное значение.

```kotlin
fun main() {
    val numbers = listOf(5, 2, 10, 4)
    val sumDoubledRight = numbers.foldRight(0) { element, sum -> sum + element * 2 }
    println(sumDoubledRight) // 42
}
```

<!-- You can also apply operations that take element indices as parameters.
For this purpose, use functions [`reduceIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-indexed.html)
and [`foldIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold-indexed.html) passing element
index as the first argument of the operation. -->

<!-- Finally, there are functions that apply such operations to collection elements from right to left - [`reduceRightIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right-indexed.html)
and [`foldRightIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold-right-indexed.html).  -->
Также вы можете использовать функции
[`reduceIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-indexed.html) и
[`foldIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold-indexed.html), которые дополнительно дают доступ к индексам элементов в качестве первого аргумента операции. Либо функции
[`reduceRightIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right-indexed.html)
и [`foldRightIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold-right-indexed.html), которые работают аналогично, но в обратном порядке.

```kotlin

fun main() {
    val numbers = listOf(5, 2, 10, 4)
    val sumEven = numbers.foldIndexed(0) { idx, sum, element -> if (idx % 2 == 0) sum + element else sum }
    println(sumEven) // 15

    val sumEvenRight = numbers.foldRightIndexed(0) { idx, element, sum -> if (idx % 2 == 0) sum + element else sum }
    println(sumEvenRight) // 15
}
```

<!-- All reduce operations throw an exception on empty collections. To receive `null` instead, use their `*OrNull()` counterparts:
* [`reduceOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-or-null.html)
* [`reduceRightOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right-or-null.html)
* [`reduceIndexedOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-indexed-or-null.html)
* [`reduceRightIndexedOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right-indexed-or-null.html) -->
Все `reduce`-операции бросают исключение, если коллекция пуста. Чтобы вместо исключения возвращался `null`, используйте их аналоги с постфиксом `*OrNull()`:
* [`reduceOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-or-null.html)
* [`reduceRightOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right-or-null.html)
* [`reduceIndexedOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-indexed-or-null.html)
* [`reduceRightIndexedOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right-indexed-or-null.html)

<!-- For cases where you want to save intermediate accumulator values, there are functions
[`runningFold()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-fold.html) (or its synonym [`scan()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/scan.html))
and [`runningReduce()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-reduce.html). -->
Для случаев, когда вы хотите сохранить промежуточное накопленное значение, существуют функции
[`runningFold()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-fold.html) (или её синоним
[`scan()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/scan.html)) и
[`runningReduce()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-reduce.html).

```kotlin
fun main() {
    val numbers = listOf(0, 1, 2, 3, 4, 5)
    val runningReduceSum = numbers.runningReduce { sum, item -> sum + item }
    val runningFoldSum = numbers.runningFold(10) { sum, item -> sum + item }

    val transform = { index: Int, element: Int -> "N = ${index + 1}: $element" }
    println(runningReduceSum.mapIndexed(transform).joinToString("\n", "Sum of first N elements with runningReduce:\n"))
    println(runningFoldSum.mapIndexed(transform).joinToString("\n", "Sum of first N elements with runningFold:\n"))

    // В логе будет:
    // Sum of first N elements with runningReduce:
    // N = 1: 0
    // N = 2: 1
    // N = 3: 3
    // N = 4: 6
    // N = 5: 10
    // N = 6: 15
    // Sum of first N elements with runningFold:
    // N = 1: 10
    // N = 2: 10
    // N = 3: 11
    // N = 4: 13
    // N = 5: 16
    // N = 6: 20
    // N = 7: 25
}
```

<!-- If you need an index in the operation parameter, use [`runningFoldIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-fold-indexed.html)
or [`runningReduceIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-reduce-indexed.html). -->
Если вам нужен индекс в качестве параметра операции, используйте
[`runningFoldIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-fold-indexed.html)
или [`runningReduceIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-reduce-indexed.html).
