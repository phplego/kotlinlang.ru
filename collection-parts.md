---
type: doc
layout: reference
category: Collections
title: "Выборка элементов"
url: https://kotlinlang.ru/docs/collection-parts.html
---

<!-- # Retrieve collection parts -->
# Выборка элементов коллекции

<!-- The Kotlin standard library contains extension functions for retrieving parts of a collection.
These functions provide a variety of ways to select elements for the result collection: listing their positions explicitly,
specifying the result size, and others.  -->
В стандартной библиотеке Kotlin есть функции-расширения, которые предоставляют множество способов выбора элементов: явное перечисление их позиций, указание размера результата и пр.


<a name="slice"></a>
<!-- ## Slice -->
## Slice

<!-- [`slice()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/slice.html) returns a list of the collection
elements with given indices. The indices may be passed either as a [range](ranges.md) or as a collection of integer values.  -->
Функция [`slice()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/slice.html) возвращает список элементов коллекции по указанным индексам. Индексы могут передаваться как [диапазон](ranges.html), либо как набор целочисленных значений.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five", "six")    
    println(numbers.slice(1..3)) // [two, three, four]
    println(numbers.slice(0..4 step 2)) // [one, three, five]
    println(numbers.slice(setOf(3, 5, 0))) // [four, six, one]
}
```


<a name="take-and-drop"></a>
<!-- ## Take and drop -->
## Take и drop

<!-- To get the specified number of elements starting from the first, use the [`take()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take.html) function.
For getting the last elements, use [`takeLast()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take-last.html).
When called with a number larger than the collection size, both functions return the whole collection. -->
Для получения определённого количества элементов, находящихся в начале коллекции (начиная с первого и далее), используйте функцию [`take()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take.html). В свою очередь функция
[`takeLast()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take-last.html) вернёт список, содержащий указанное количество последних элементов.
При вызове этих функций с числом, превышающим размер коллекции, они обе вернут всю коллекцию целиком.

<!-- To take all the elements except a given number of first or last elements, call the [`drop()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop.html)
and [`dropLast()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop-last.html) functions respectively.  -->
Для получения всех элементов, кроме указанного количества первых или последних элементов, используйте функции
[`drop()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop.html) и
[`dropLast()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop-last.html) соответственно.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five", "six")
    println(numbers.take(3)) // [one, two, three]
    println(numbers.takeLast(3)) // [four, five, six]
    println(numbers.drop(1)) // [two, three, four, five, six]
    println(numbers.dropLast(5)) // [one]
}
```

<!-- You can also use predicates to define the number of elements for taking or dropping.
There are four functions similar to the ones described above:

* [`takeWhile()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take-while.html) is `take()` with a predicate: it takes the elements up to but excluding the first one not matching the predicate. If the first collection element doesn't match the predicate, the result is empty.
* [`takeLastWhile()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take-last-while.html) is similar to `takeLast()`: it takes the range of elements matching the predicate from the end of the collection. The first element of the range is the element next to the last element not matching the predicate. If the last collection element doesn't match the predicate, the result is empty;
* [`dropWhile()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop-while.html) is the opposite to `takeWhile()` with the same predicate: it returns the elements from the first one not matching the predicate to the end.
* [`dropLastWhile()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop-last-while.html) is the opposite to `takeLastWhile()` with the same predicate: it returns the elements from the beginning to the last one not matching the predicate. -->
У вышеперечисленных функций есть аналоги, которые определяют количество элементов с помощью предиката:
* [`takeWhile()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take-while.html) - это аналог функции `take()`: возвращает первые элементы, соответствующие заданному предикату. Элементы отбираются с начала коллекции и до тех пор, пока не встретится элемент, несоответствующий предикату. Если первый элемент коллекции не соответствует предикату, то результат будет пустым.
* [`takeLastWhile()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take-last-while.html) - это аналог функции `takeLast()`: возвращает последние элементы, соответствующие заданному предикату. Элементы отбираются с конца коллекции и до тех пор, пока не встретится элемент, несоответствующий предикату. Если последний элемент коллекции не соответствует предикату, то результат будет пустым.
* [`dropWhile()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop-while.html) - противоположность `takeWhile()`: возвращает все элементы, кроме первых элементов, соответствующих заданному предикату.
* [`dropLastWhile()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop-last-while.html) - противоположность `takeLastWhile()`: возвращает все элементы, кроме последних элементов, соответствующих заданному предикату.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five", "six")
    println(numbers.takeWhile { !it.startsWith('f') }) // [one, two, three]
    println(numbers.takeLastWhile { it != "three" }) // [four, five, six]
    println(numbers.dropWhile { it.length == 3 }) // [three, four, five, six]
    println(numbers.dropLastWhile { it.contains('i') }) // [one, two, three, four]
}
```


<a name="chunked"></a>
<!-- ## Chunked -->
## Chunked

<!-- To break a collection onto parts of a given size, use the [`chunked()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/chunked.html) function.
`chunked()` takes a single argument – the size of the chunk – and returns a `List` of `List`s of the given size.
The first chunk starts from the first element and contains the `size` elements, the second chunk holds the next `size` elements,
and so on. The last chunk may have a smaller size. -->
Функция [`chunked()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/chunked.html) позволяет разбить коллекцию на части (или чанки) заданного размера. Она принимает единственный аргумент `size` - размер каждой части - и возвращает `List`, состоящий из вложенных списков. Первая часть будет содержать в себе элементы, начиная с первого и далее, но количеством не более заданного размера; вторая часть - последующие элементы, но не более заданного размера и т.д. В последней части может быть меньше элементов, чем указанный размер (остаток).

```kotlin
fun main() {
    val numbers = (0..13).toList()
    println(numbers.chunked(3)) // [[0, 1, 2], [3, 4, 5], [6, 7, 8], [9, 10, 11], [12, 13]]
}
```

<!-- You can also apply a transformation for the returned chunks right away.
To do this, provide the transformation as a lambda function when calling `chunked()`.
The lambda argument is a chunk of the collection. When `chunked()` is called with a transformation,
the chunks are short-living `List`s that should be consumed right in that lambda. -->
Также функция `chunked()` позволяет сразу же преобразовывать полученные части, для чего ей необходимо передать функцию преобразования в виде лямбды. При этом каждая из частей является аргументом этой лямбды. Сами части в этом случае будут недолговечными объектами типа `List` и должны использоваться непосредственно в лямбде.

```kotlin
fun main() {
    val numbers = (0..13).toList()

    // `it` - это часть исходной коллекции
    println(numbers.chunked(3) { it.sum() }) // [3, 12, 21, 30, 25]
}
```


<a name="windowed"></a>
<!-- ## Windowed -->
## Windowed

<!-- You can retrieve all possible ranges of the collection elements of a given size.
The function for getting them is called [`windowed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/windowed.html):
it returns a list of element ranges that you would see if you were looking at the collection through a sliding window of the given size.
Unlike `chunked()`,  `windowed()` returns element ranges (_windows_) starting from *each* collection element.
All the windows are returned as elements of a single `List`. -->
С помощью функции [`windowed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/windowed.html) вы можете получить все возможные диапазоны элементов коллекции. Представьте, что вы смотрите на коллекцию через "окно" определённого размера. Это окно может скользить от начала до конца коллекции, тем самым последовательно выделяя каждый возможный её диапазон.
В отличие от `chunked()`, `windowed()` возвращает диапазоны (или _окна_), в которых первым элементом является каждый последующий элемент коллекции, а размер диапазона равен размеру "окна".
Все диапазоны возвращаются в качестве элементов одного списка.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five")    
    println(numbers.windowed(3)) // [[one, two, three], [two, three, four], [three, four, five]]
}
```

<!-- `windowed()` provides more flexibility with optional parameters:

* `step` defines a distance between first elements of two adjacent windows. By default the value is 1, so the result contains windows starting from all elements. If you increase the step to 2, you will receive only windows starting from odd elements: first, third, and so on.
* `partialWindows` includes windows of smaller sizes that start from the elements at the end of the collection. For example, if you request windows of three elements, you can't build them for the last two elements. Enabling `partialWindows` in this case includes two more lists of sizes 2 and 1. -->
У функции `windowed()` есть дополнительные параметры:

* `step` - определяет расстояние между первыми элементами соседних диапазонов. По умолчанию равен 1, поэтому в этом случае результат будет содержать диапазоны, начинающиеся со всех элементов коллекции. Если вы зададите шаг равный 2, то получите диапазоны, начинающиеся только с нечётных элементов: 1, 3 и т.д.
* `partialWindows` - позволяет включать в итоговый список диапазоны меньшего размера, которые начинаются с последних элементов коллекции. Например, вы хотите получить диапазоны из трёх элементов, а осталось всего два элемента. В этом случае `partialWindows` добавит ещё два списка размером 2 и 1.

<!-- Finally, you can apply a transformation to the returned ranges right away.
To do this, provide the transformation as a lambda function when calling `windowed()`. -->
Наконец, вы можете сразу же преобразовывать полученные диапазоны. Для этого передайте `windowed()` функцию преобразования в виде лямбды.

```kotlin
fun main() {
    val numbers = (1..10).toList()
    println(numbers.windowed(3, step = 2, partialWindows = true)) // [[1, 2, 3], [3, 4, 5], [5, 6, 7], [7, 8, 9], [9, 10]]
    println(numbers.windowed(3) { it.sum() }) // [6, 9, 12, 15, 18, 21, 24, 27]
}
```

<!-- To build two-element windows, there is a separate function - [`zipWithNext()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/zip-with-next.html).
It creates pairs of adjacent elements of the receiver collection.
Note that `zipWithNext()` doesn't break the collection into pairs; it creates a `Pair` for _each_ element except the last
one, so its result on `[1, 2, 3, 4]` is `[[1, 2], [2, 3], [3, 4]]`, not `[[1, 2`], `[3, 4]]`.
`zipWithNext()` can be called with a transformation function as well; it should take two elements of the receiver collection
as arguments. -->
Для создания диапазонов из двух элементов существует отдельная функция -
[`zipWithNext()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/zip-with-next.html). Она создаёт пары из смежных элементов коллекции.
Обратите внимание, что `zipWithNext()` не разбивает коллекцию на пары; она создаёт объекты `Pair` для _каждого_ элемента, кроме последнего, поэтому результатом для `[1, 2, 3, 4]` будет `[[1, 2], [2, 3], [3, 4]]`, а не `[[1, 2`], `[3, 4]]`.
Также `zipWithNext()` можно вызывать с функцией преобразования, которая в качестве аргументов принимает два элемента коллекции.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five")    
    println(numbers.zipWithNext()) // [(one, two), (two, three), (three, four), (four, five)]
    println(numbers.zipWithNext() { s1, s2 -> s1.length > s2.length}) // [false, false, true, false]
}
```
