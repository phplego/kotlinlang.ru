---
type: doc
layout: reference
category: Collections
title: "Преобразование"
url: https://kotlinlang.ru/docs/reference/collection-transformations.html
---

<a name="collection-transformation-operations"></a>
<!-- # Collection transformation operations -->
# Преобразование коллекций

<!-- The Kotlin standard library provides a set of extension functions for collection _transformations_.
These functions build new collections from existing ones based on the transformation rules provided.
In this page, we'll give an overview of the available collection transformation functions. -->
Стандартная библиотека Kotlin предоставляет целый набор функций-расширений для _преобразования_ коллекций.
Они создают новые коллекции из существующих на основе предоставляемых правил преобразования.
В этом разделе находится общий обзор подобных функций.


<a name="map"></a>
<!-- ## Map -->
## Map

<!-- The _mapping_ transformation creates a collection from the results of a function on the elements of another collection.
The basic mapping function is [`map()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map.html).
It applies the given lambda function to each subsequent element and returns the list of the lambda results.
The order of results is the same as the original order of elements.
To apply a transformation that additionally uses the element index as an argument, use [`mapIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-indexed.html).  -->
Группа функций, корнем которых является [`map()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map.html). Она позволяет преобразовать исходную коллекцию путём применения заданной лямбда-функции к каждому её элементу, объединяя результаты в новую коллекцию. При этом порядок элементов сохраняется. Этот процесс преобразования также называют _маппингом_ (ориг. _mapping_).
Если для преобразования коллекции вам требуется знать индекс элементов, то используйте функцию [`mapIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-indexed.html).

```kotlin
fun main() {
    val numbers = setOf(1, 2, 3)
    println(numbers.map { it * 3 }) // [3, 6, 9]
    println(numbers.mapIndexed { idx, value -> value * idx }) // [0, 2, 6]
}
```

<!-- If the transformation produces `null` on certain elements, you can filter out the `null`s from the result collection by
calling the [`mapNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-not-null.html) function
instead of `map()`, or [`mapIndexedNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-indexed-not-null.html)
instead of `mapIndexed()`. -->
Если какой-либо элемент или элементы могут быть преобразованы в значение равное `null`, то вместо `map()` можно использовать функцию [`mapNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-not-null.html), которая отфильтрует такие элементы и они не попадут в новую коллекцию. Соответственно, вместо `mapIndexed()` можно использовать [`mapIndexedNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-indexed-not-null.html).

```kotlin
fun main() {
    val numbers = setOf(1, 2, 3)
    println(numbers.mapNotNull {
      if ( it == 2) null else it * 3
    }) // [3, 9]
    println(numbers.mapIndexedNotNull { idx, value ->
      if (idx == 0) null else value * idx
    }) // [2, 6]
}
```

<!-- When transforming maps, you have two options: transform keys leaving values unchanged and vice versa.
To apply a given transformation to keys, use [`mapKeys()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-keys.html);
in turn, [`mapValues()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-values.html) transforms values.
Both functions use the transformations that take a map entry as an argument, so you can operate both its key and value. -->
Ассоциативные списки можно преобразовывать двумя способами: преобразовывать ключи, не изменяя значения, и наоборот.
Для преобразования ключей используйте функцию [`mapKeys()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-keys.html);
в свою очередь [`mapValues()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-values.html) преобразует значения.
Они обе используют функцию преобразования, которая в качестве аргумента принимает пару "ключ-значение", поэтому вы можете работать как с ключом, так и со значением.

```kotlin
fun main() {
    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
    println(numbersMap.mapKeys { it.key.uppercase() }) // {KEY1=1, KEY2=2, KEY3=3, KEY11=11}
    println(numbersMap.mapValues { it.value + it.key.length }) // {key1=5, key2=6, key3=7, key11=16}
}
```


<a name="zip"></a>
<!-- ## Zip -->
## Zip

<!-- _Zipping_ transformation is building pairs from elements with the same positions in both collections.
In the Kotlin standard library, this is done by the [`zip()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/zip.html)
extension function. -->
Данная группа функций преобразования - _zipping_ - берёт два списка и создаёт из их элементов пары. При этом пары создаются из элементов с одинаковыми индексами.
В стандартной библиотеке Kotlin для такого преобразования есть функция-расширение [`zip()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/zip.html).

<!-- When called on a collection or an array with another collection (array) as an argument, `zip()` returns the `List` of
`Pair` objects. The elements of the receiver collection are the first elements in these pairs. -->
Если функцию `zip()` вызвать для одной коллекции и при этом передать ей в качестве аргумента другую коллекцию, то в результате вы получите список типа `List<Pair<K, V>>`.
Элементы коллекции-получателя будут первыми элементами в этих объектах `Pair`.

<!-- If the collections have different sizes, the result of the `zip()` is the smaller size; the last elements of the larger
collection are not included in the result. -->
Если коллекции имеют разные размеры, то `zip()` вернёт новую коллекцию, длина которой равняется минимальной из исходных коллекций; последние элементы бОльшей коллекции будут отсечены.

<!-- `zip()` can also be called in the infix form `a zip b`. -->
Также `zip()` можно вызывать в инфиксной форме `a zip b`.

```kotlin
fun main() {
    val colors = listOf("red", "brown", "grey")
    val animals = listOf("fox", "bear", "wolf")
    println(colors zip animals) // [(red, fox), (brown, bear), (grey, wolf)]

    val twoAnimals = listOf("fox", "bear")
    println(colors.zip(twoAnimals)) // [(red, fox), (brown, bear)]
}
```

<!-- You can also call `zip()` with a transformation function that takes two parameters: the receiver element and the argument
element. In this case, the result `List` contains the return values of the transformation function called on pairs of the
receiver and the argument elements with the same positions. -->
Вместе с коллекцией `zip()` можно передать функцию преобразования, которая принимает два параметра: элемент коллекции-получателя и элемент коллекции-аргумента, при этом оба элемента располагаются в своих коллекциях по одинаковому индексу. В этом случае вернётся `List` с содержимым из результатов вычисления функции преобразования.

```kotlin
fun main() {
    val colors = listOf("red", "brown", "grey")
    val animals = listOf("fox", "bear", "wolf")

    println(colors.zip(animals) { color, animal ->
      "The ${animal.replaceFirstChar { it.uppercase() }} is $color"
    }) // [The Fox is red, The Bear is brown, The Wolf is grey]
}
```

<!-- When you have a `List` of `Pair`s, you can do the reverse transformation – _unzipping_ – that builds two lists from these
pairs:

* The first list contains the first elements of each `Pair` in the original list.
* The second list contains the second elements. -->

Для списка типа `List<Pair<K, V>>` можно выполнить обратное преобразование - _unzipping_, которое разбирает пары на два отдельных списка:

* В первый список помещаются первые элементы каждого объекта `Pair`.
* Во второй список помещаются вторые элементы каждого объекта `Pair`.

<!-- To unzip a list of pairs, call [`unzip()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/unzip.html). -->
Чтобы "распаковать" такой список вызовите для него функцию [`unzip()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/unzip.html).

```kotlin
fun main() {
    val numberPairs = listOf("one" to 1, "two" to 2, "three" to 3, "four" to 4)
    println(numberPairs.unzip()) // ([one, two, three, four], [1, 2, 3, 4])
}
```


<a name="associate"></a>
<!-- ## Associate -->
## Associate

<!-- _Association_ transformations allow building maps from the collection elements and certain values associated with them.
In different association types, the elements can be either keys or values in the association map. -->
Функции преобразования данного типа позволяют создавать ассоциативные списки из элементов коллекции и связанных с ними значений. При этом элементы могут быть как ключами ассоциативного списка, так и значениями.

<!-- The basic association function [`associateWith()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate-with.html)
creates a `Map` in which the elements of the original collection are keys, and values are produced from them by the given
transformation function. If two elements are equal, only the last one remains in the map. -->
Основная функция здесь - [`associateWith()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate-with.html). Она создаёт `Map`, в которой элементы исходной коллекции являются ключами, а значения вычисляются с помощью функции преобразования. Если встречаются два равных элемента, то в ассоциативный список попадёт только последний из них.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    println(numbers.associateWith { it.length }) // {one=3, two=3, three=5, four=4}
}
```

<!-- For building maps with collection elements as values, there is the function [`associateBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate-by.html).
It takes a function that returns a key based on an element's value. If two elements are equal, only the last one remains
in the map. -->
Для создания ассоциативного списка, в котором элементы коллекции будут выступать в роли значений, существует функция [`associateBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate-by.html).
Она принимает функцию, которая в свою очередь возвращает ключ, основанный на значении элемента. Если встречаются два равных элемента, то в ассоциативный список попадёт только последний из них.

<!-- `associateBy()` can also be called with a value transformation function. -->
Также `associateBy()` можно вызвать с функцией, преобразующей значения.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")

    println(numbers.associateBy { it.first().uppercaseChar() }) // {O=one, T=three, F=four}
    println(numbers.associateBy(
      keySelector = { it.first().uppercaseChar() },
      valueTransform = { it.length }
    )) // {O=3, T=5, F=4}
}
```

<!-- Another way to build maps in which both keys and values are somehow produced from collection elements is the function [`associate()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate.html).
It takes a lambda function that returns a `Pair`: the key and the value of the corresponding map entry. -->
Есть еще один способ создания ассоциативного списка, при котором ключи и значения тем или иным образом создаются на основе элементов коллекции - при помощи функции [`associate()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate.html).
Она принимает лямбда-функцию, которая возвращает объект `Pair`. Этот объект и представляет собой пару "ключ-значение".

<!-- Note that `associate()` produces short-living `Pair` objects which may affect the performance.
Thus, `associate()` should be used when the performance isn't critical or it's more preferable than other options. -->
Обратите внимание, что `associate()` создаёт недолговечные объекты `Pair`, которые могут повлиять на производительность. Поэтому используйте `associate()` только тогда, когда производительность для вас не критична, либо если эта функция предпочтительнее остальных.

<!-- An example of the latter is when a key and the corresponding value are produced from an element together.  -->
Примером последнего является ситуация, когда ключ и значение для него создаются одновременно на основе элемента.

```kotlin
fun main() {
    data class FullName (val firstName: String, val lastName: String)

    fun parseFullName(fullName: String): FullName {
        val nameParts = fullName.split(" ")
        if (nameParts.size == 2) {
            return FullName(nameParts[0], nameParts[1])
        } else throw Exception("Wrong name format")
    }

    val names = listOf("Alice Adams", "Brian Brown", "Clara Campbell")
    println(names.associate { name ->
      parseFullName(name).let { it.lastName to it.firstName }
    }) // {Adams=Alice, Brown=Brian, Campbell=Clara}
}
```

<!-- Here we call a transform function on an element first, and then build a pair from the properties of that function's result. -->
В примере выше сначала вызывается функция преобразования элемента, а затем происходит создание пары на основе полученного от этой функции результата.


<a name="flatten"></a>
<!-- ## Flatten -->
## Flatten

<!-- If you operate nested collections, you may find the standard library functions that provide flat access to nested collection
elements useful. -->
При работе с вложенными коллекциями вам могут пригодиться функции, обеспечивающие "плоский" доступ к их элементам.

<!-- The first function is [`flatten()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flatten.html).
You can call it on a collection of collections, for example, a `List` of `Set`s.
The function returns a single `List` of all the elements of the nested collections. -->
Первая функция - [`flatten()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flatten.html).
Её можно использовать для коллекции, содержащей другие коллекции, например, для `List`, в котором находятся `Set`-ы.
Функция возвращает объединённый `List`, состоящий из всех элементов всех вложенных коллекций.

```kotlin
fun main() {
    val numberSets = listOf(setOf(1, 2, 3), setOf(4, 5, 6), setOf(1, 2))
    println(numberSets.flatten()) // [1, 2, 3, 4, 5, 6, 1, 2]
}
```

<!-- Another function – [`flatMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flat-map.html) provides
a flexible way to process nested collections. It takes a function that maps a collection element to another collection.
As a result, `flatMap()` returns a single list of its return values on all the elements.
So, `flatMap()` behaves as a subsequent call of `map()` (with a collection as a mapping result) and `flatten()`. -->
Другая функция - [`flatMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flat-map.html), которая обеспечивает гибкий способ работы с вложенными коллекциями. Она принимает функцию, которая маппит элемент исходной коллекции в другую коллекцию.
В качестве результата `flatMap()` возвращает объединённый список из всех обработанных элементов.
По сути `flatMap()` ведёт себя как вызов `map()` (с возвращением коллекции в качестве результата маппинга) и `flatten()`.

```kotlin
data class StringContainer(val values: List<String>)

fun main() {
    val containers = listOf(
        StringContainer(listOf("one", "two", "three")),
        StringContainer(listOf("four", "five", "six")),
        StringContainer(listOf("seven", "eight"))
    )
    println(containers.flatMap { it.values }) // [one, two, three, four, five, six, seven, eight]
}

```


<a name="string-representation"></a>
<!-- ## String representation -->
## Преобразование коллекции в String

<!-- If you need to retrieve the collection content in a readable format, use functions that transform the collections to
strings: [`joinToString()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to-string.html) and
[`joinTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to.html). -->
С помощью функций [`joinToString()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to-string.html) и
[`joinTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to.html) содержимое коллекции можно преобразовать в читаемый вид - `String`.

<!-- `joinToString()` builds a single `String` from the collection elements based on the provided arguments.
`joinTo()` does the same but appends the result to the given [`Appendable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/-appendable/index.html) object. -->
`joinToString()` на основе предоставленных аргументов объединяет элементы коллекции в строку.
`joinTo()` делает то же самое, но добавляет результат к переданному объекту [`Appendable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/-appendable/index.html).

<!-- When called with the default arguments, the functions return the result similar to calling `toString()` on the collection:
a `String` of elements' string representations separated by commas with spaces.  -->
При вызове этих функций с аргументами по умолчанию, они вернут результат, аналогичный вызову `toString()` для коллекции: элементы будут представлены в виде `String`, разделённые между собой запятой с пробелом.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")

    println(numbers) // [one, two, three, four]
    println(numbers.joinToString()) // one, two, three, four

    val listString = StringBuffer("The list of numbers: ")
    numbers.joinTo(listString)
    println(listString) // The list of numbers: one, two, three, four
}
```

<!-- To build a custom string representation, you can specify its parameters in function arguments `separator`, `prefix`, and
`postfix`. The resulting string will start with the `prefix` and end with the `postfix`. The `separator` will come after
each element except the last. -->
Но также вы можете кастомизировать строковое представление коллекции, указав необходимые вам параметры при помощи специальных аргументов - `separator`, `prefix`, и `postfix`. Преобразованная строка будет начинаться с префикса (`prefix`) и заканчиваться постфиксом (`postfix`). А `separator` будет добавлен после каждого элемента, кроме последнего.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")    
    println(numbers.joinToString(
      separator = " | ",
      prefix = "start: ",
      postfix = ": end"
    )) // start: one | two | three | four: end
}
```

<!-- For bigger collections, you may want to specify the `limit` – a number of elements that will be included into result.
If the collection size exceeds the `limit`, all the other elements will be replaced with a single value of the `truncated`
argument. -->
Если коллекция большая, то вы можете указать `limit` - количество элементов, которые будут включены в результат.
При этом все элементы, превышающие `limit`, будут заменены одним значением аргумента `truncated`.

```kotlin
fun main() {
    val numbers = (1..100).toList()
    println(numbers.joinToString(
      limit = 10,
      truncated = "<...>")
    ) // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, <...>
}
```

<!-- Finally, to customize the representation of elements themselves, provide the `transform` function.  -->
Ну и наконец, чтобы кастомизировать представление самих элементов, передайте в качестве аргумента функцию `transform`.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    println(numbers.joinToString {
      "Element: ${it.uppercase()}"
    }) // Element: ONE, Element: TWO, Element: THREE, Element: FOUR
}
```
