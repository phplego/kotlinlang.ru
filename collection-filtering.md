---
type: doc
layout: reference
category: Collections
title: "Фильтрация"
url: https://kotlinlang.ru/docs/collection-filtering.html
---

<a name="filtering-collections"></a>
<!-- # Filtering collections -->
# Фильтрация коллекций

<!-- Filtering is one of the most popular tasks in collection processing.
In Kotlin, filtering conditions are defined by _predicates_ – lambda functions that take a collection element and return
a boolean value: `true` means that the given element matches the predicate, `false` means the opposite. -->
Фильтрация — одна из самых популярных задач при работе с коллекцией. В Kotlin условия фильтра задаются с помощью _предикатов_ — лямбда-функций, которые принимают элемент коллекции, а возвращают логическое значение (`boolean`): `true` означает, что элемент соответствует предикату, `false` - не соответствует.

<!-- The standard library contains a group of extension functions that let you filter collections in a single call.
These functions leave the original collection unchanged, so they are available for both [mutable and read-only](collections-overview.md#collection-types)
collections. To operate the filtering result, you should assign it to a variable or chain the functions after filtering. -->
В стандартной библиотеке есть группа функций-расширений, с помощью которых можно фильтровать коллекции за один вызов такой функции. Они не изменяют исходную коллекцию, поэтому их можно использовать и для [изменяемых, и для неизменяемых](collections-overview.html#collection-types) коллекций. Чтобы использовать результат фильтрации, вы должны либо присвоить его переменной, либо использовать его в цепочке вызовов.


<a name="filter-by-predicate"></a>
<!-- ## Filter by predicate -->
## Фильтр с предикатом

<!-- The basic filtering function is [`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html).
When called with a predicate, `filter()` returns the collection elements that match it.
For both `List` and `Set`, the resulting collection is a `List`, for `Map` it's a `Map` as well. -->
Основная функция для фильтра коллекций - [`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html).
Если использовать `filter()` с предикатом, то будут возвращены те элементы, которые ему соответствуют.
Для `List` и `Set` функция возвращает результат типа `List`, для `Map` возвращается `Map`.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")  
    val longerThan3 = numbers.filter { it.length > 3 }
    println(longerThan3) // [three, four]

    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
    val filteredMap = numbersMap.filter { (key, value) -> key.endsWith("1") && value > 10}
    println(filteredMap) // {key11=11}
}
```

<!-- The predicates in `filter()` can only check the values of the elements.
If you want to use element positions in the filter, use [`filterIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-indexed.html).
It takes a predicate with two arguments: the index and the value of an element. -->
В функции `filter()` предикаты могут проверять только значения элементов. Если при фильтрации вы хотите использовать индексы элементов, то вам нужна функция [`filterIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-indexed.html). Она принимает предикат с двумя аргументами: индекс и значение элемента.

<!-- To filter collections by negative conditions, use [`filterNot()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not.html).
It returns a list of elements for which the predicate yields `false`. -->
Если вы хотите отфильтровать элементы, которые не соответствуют заданному условию, то используйте функцию [`filterNot()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not.html). Она возвращает те элементы, для которых предикат вернул значение `false`.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")

    val filteredIdx = numbers.filterIndexed { index, s -> (index != 0) && (s.length < 5)  }
    val filteredNot = numbers.filterNot { it.length <= 3 }

    println(filteredIdx) // [two, four]
    println(filteredNot) // [three, four]
}
```

<!-- There are also functions that narrow the element type by filtering elements of a given type: -->
Также существуют функции, которые фильтруют только элементы заданного типа:

<!-- * [`filterIsInstance()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-is-instance.html) returns
    collection elements of a given type. Being called on a `List<Any>`, `filterIsInstance<T>()` returns a `List<T>`, thus
    allowing you to call functions of the  `T` type on its items. -->
* [`filterIsInstance()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-is-instance.html) возвращает элементы заданного типа. При вызове функции `filterIsInstance<T>()` для списка типа `List<Any>` вернётся список типа `List<T>`, что позволяет вызывать специфичные для элементов `T` функции.

  ```kotlin
  fun main() {
    val numbers = listOf(null, 1, "two", 3.0, "four")
    println("All String elements in upper case:")
    numbers.filterIsInstance<String>().forEach {
        println(it.uppercase())
    }
  }

  // В логе будет:
  // All String elements in upper case:
  // TWO
  // FOUR
  ```

<!-- * [`filterNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not-null.html) returns all
      non-null elements. Being called on a `List<T?>`, `filterNotNull()` returns a `List<T: Any>`, thus allowing you to treat
      the elements as non-null objects. -->
* [`filterNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not-null.html) возвращает значения неравные `null`. При вызове функции для списка `List<T?>` вернётся список типа `List<T: Any>`, что позволяет обращаться к элементам как к ненулевым объектам.

  ```kotlin
  fun main() {
      val numbers = listOf(null, "one", "two", null)
      numbers.filterNotNull().forEach {
          println(it.length)   // length is unavailable for nullable Strings
      }
  }

  // В логе будет:
  // 3
  // 3
  ```

<a name="partition"></a>
<!-- ## Partition -->
## Фильтр с разделением

<!-- Another filtering function – [`partition()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/partition.html)
– filters a collection by a predicate and keeps the elements that don't match it in a separate list.
So, you have a `Pair` of `List`s as a return value: the first list containing elements that match the predicate and the
second one containing everything else from the original collection. -->
Другая функция для фильтрации - [`partition()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/partition.html) - фильтрует коллекцию по предикату и сохраняет элементы, которые ему не соответствуют, в отдельном списке. Возвращает объект типа `Pair<List, List>`, где первый список содержит элементы, соответствующие предикату, а второй — все остальные элементы из исходной коллекции.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    val (match, rest) = numbers.partition { it.length > 3 }

    println(match) // [three, four]
    println(rest) // [one, two]
}
```


<a name="test-predicates"></a>
<!-- ## Test predicates -->
## Проверочные предикаты

<!-- Finally, there are functions that simply test a predicate against collection elements:

* [`any()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/any.html) returns `true` if at least one element matches the given predicate.
* [`none()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/none.html) returns `true` if none of the elements match the given predicate.
* [`all()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/all.html) returns `true` if all elements match the given predicate.
    Note that `all()` returns `true` when called with any valid predicate on an empty collection. Such behavior is known in logic as [vacuous truth](https://en.wikipedia.org/wiki/Vacuous_truth). -->
Ну и наконец, есть функции, которые проверяют каждый элемент на соответствие предикату:
* [`any()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/any.html) - возвращает `true`, если хотя бы один элемент соответствует предикату.
* [`none()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/none.html) - возвращает `true`, если ни один элемент не соответствует предикату.
* [`all()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/all.html) - возвращает `true`, если все элементы соответствуют предикату. Обратите внимание, что если вызвать `all()` для пустой коллекции с любым валидным предикатом, то функция вернёт `true`. Такое поведение известно как [бессмысленная истина](https://en.wikipedia.org/wiki/Vacuous_truth) (ориг. vacuous truth).

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")

    println(numbers.any { it.endsWith("e") }) // true
    println(numbers.none { it.endsWith("a") }) // true
    println(numbers.all { it.endsWith("e") }) // false

    println(emptyList<Int>().all { it > 5 })   // true - бессмысленная истина
}
```

<!-- `any()` and `none()` can also be used without a predicate: in this case they just check the collection emptiness.
`any()` returns `true` if there are elements and `false` if there aren't; `none()` does the opposite. -->
`any()` и `none()` могут использоваться без предиката. В этом случае они просто проверяют пуста ли коллекция.
`any()` возвращает `true`, если в коллекции есть элементы, а `none()` наоборот, возвращает `true`, если коллекция пуста.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    val empty = emptyList<String>()

    println(numbers.any()) // true
    println(empty.any()) // false

    println(numbers.none()) // false
    println(empty.none()) // true
}
```
