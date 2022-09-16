---
type: doc
layout: reference
category: Collections
title: "Операции коллекций"
url: https://kotlinlang.ru/docs/collection-operations.html
---

<a name="collection-operations-overview"></a>
<!-- # Collection operations overview -->
# Операции коллекций: общий обзор

<!-- The Kotlin standard library offers a broad variety of functions for performing operations on collections. This includes
simple operations, such as getting or adding elements, as well as more complex ones including search, sorting, filtering,
transformations, and so on.  -->
Стандартная библиотека Kotlin предлагает большой набор функций для работы с коллекциями. Сюда входят простые операции, такие как получение или добавление элементов, а также более сложные, например, поиск, сортировка, фильтрация, преобразование и т. д.


<a name="extension-and-member-functions"></a>
<!-- ## Extension and member functions -->
## Функции-расширения (Extension) и функции-члены (member functions)

<!-- Collection operations are declared in the standard library in two ways: [member functions](classes.md#class-members) of
collection interfaces and [extension functions](extensions.md#extension-functions). -->
Операции коллекций в стандартной библиотеке объявляются двумя способами: как [функции-члены](classes.html#class-members) и как [функции-расширения](extensions.html#extension-functions).

<!-- Member functions define operations that are essential for a collection type. For example, [`Collection`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/index.html)
contains the function [`isEmpty()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/is-empty.html)
for checking its emptiness; [`List`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/index.html) contains
[`get()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/get.html) for index access to elements,
and so on. -->
С помощью функций-членов определяются операции, которые необходимы для определённых типов коллекций. Например, у
[`Collection`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/index.html) есть функция
[`isEmpty()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/is-empty.html) для проверки того, что коллекция пуста; у
[`List`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/index.html) есть функция
[`get()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/get.html) для обращения к элементу по его индексу и т. д.

<!-- When you create your own implementations of collection interfaces, you must implement their member functions.
To make the creation of new implementations easier, use the skeletal implementations of collection interfaces from the
standard library: [`AbstractCollection`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-collection/index.html),
[`AbstractList`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-list/index.html),
[`AbstractSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-set/index.html),
[`AbstractMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-map/index.html), and their
mutable counterparts. -->
Если вы решите создать свою собственную реализацию коллекции, например, на основе интерфейса `Collection`, то вы также должны будете реализовать все его функции-члены. Чтобы упростить эту задачу, используйте специально подготовленные реализации интерфейсов из стандартной библиотеки:
[`AbstractCollection`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-collection/index.html),
[`AbstractList`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-list/index.html),
[`AbstractSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-set/index.html),
[`AbstractMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-abstract-map/index.html).
Помимо вышеперечисленных есть аналоги для создания изменяемых (mutable) коллекций.

<!-- Other collection operations are declared as extension functions. These are filtering, transformation, ordering, and other
collection processing functions. -->
Другие операции коллекций объявлены как функции-расширения. К ним относятся операции для фильтрации, преобразования, сортировки и прочих видов обработки элементов коллекции.


<a name="common operations"></a>
<!-- ## Common operations -->
## Общие операции

<!-- Common operations are available for both [read-only and mutable collections](collections-overview.md#collection-types).
Common operations fall into these groups:

* [Transformations](collection-transformations.md)
* [Filtering](collection-filtering.md)
* [`plus` and `minus` operators](collection-plus-minus.md)
* [Grouping](collection-grouping.md)
* [Retrieving collection parts](collection-parts.md)
* [Retrieving single elements](collection-elements.md)
* [Ordering](collection-ordering.md)
* [Aggregate operations](collection-aggregate.md) -->
Общие операции доступны как для [изменяемых, так и для неизменяемых коллекций](collections-overview.html#collection-types).
Их можно разделить на следующие группы:

* [Преобразование](collection-transformations.html)
* [Фильтрация](collection-filtering.html)
* [Операторы `plus` и `minus`](collection-plus-minus.html)
* [Группировка](collection-grouping.html)
* [Выборка элементов](collection-parts.html)
* [Выбор одного элемента](collection-elements.html)
* [Сортировка](collection-ordering.html)
* [Объединение](collection-aggregate.html)

<!-- Operations described on these pages return their results without affecting the original collection. For example, a filtering
operation produces a _new collection_ that contains all the elements matching the filtering predicate.
Results of such operations should be either stored in variables, or used in some other way, for example, passed in other
functions. -->
Операции, описанные в разделах выше, возвращают результат, не изменяя исходную коллекцию. Например, операция `filter` создаёт _новую коллекцию_, которая содержит элементы, соответствующие условию фильтра.
Результаты этих операций можно использовать несколькими способами, например, сохранять в переменные или передавать в другие функции.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")  
    numbers.filter { it.length > 3 }  // список `numbers` не изменится, результат фильтра теряется
    println("numbers are still $numbers") // [one, two, three, four]
    val longerThan3 = numbers.filter { it.length > 3 } // результат сохраняется в `longerThan3`
    println("numbers longer than 3 chars are $longerThan3") // [three, four]
}
```

<!-- For certain collection operations, there is an option to specify the _destination_ object.
Destination is a mutable collection to which the function appends its resulting items instead of returning them in a new object.
For performing operations with destinations, there are separate functions with the `To` postfix in their names, for example,
[`filterTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-to.html) instead of [`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html)
or [`associateTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate-to.html) instead of [`associate()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate.html).
These functions take the destination collection as an additional parameter. -->
Некоторые функции в качестве параметра принимают целевой объект - _destination_. Он представляет собой изменяемую коллекцию, в которую функция добавляет результаты своих вычислений, вместо того, чтобы самостоятельно создавать новый объект.
Названия таких функций можно отличить по постфиксу `To`, например,
[`filterTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-to.html) вместо
[`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html) или
[`associateTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate-to.html) вместо
[`associate()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate.html).

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    val filterResults = mutableListOf<String>()  // целевой объект
    numbers.filterTo(filterResults) { it.length > 3 }
    numbers.filterIndexedTo(filterResults) { index, _ -> index == 0 }
    println(filterResults) // содержит результат обеих операций - [three, four, one]
}
```

<!-- For convenience, these functions return the destination collection back, so you can create it right in the corresponding
argument of the function call: -->
Эти функции возвращают целевой объект обратно, поэтому его можно создать прямо при вызове функции:

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    // отфильтрованные элементы будут помещены в `HashSet`,
    // который автоматом исключит дубликаты
    val result = numbers.mapTo(HashSet()) { it.length }
    println("distinct item lengths are $result") // [3, 4, 5]
}
```

<!-- Functions with destination are available for filtering, association, grouping, flattening, and other operations. For the
complete list of destination operations see the [Kotlin collections reference](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index.html). -->
Почти все стандартные функции имеют аналог в виде функции, работающей с целевым объектом. С полным списком можно ознакомиться в [документации](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index.html).


<a name="write operations"></a>
<!-- ## Write operations -->
## Операции записи

<!-- For mutable collections, there are also _write operations_ that change the collection state. Such operations include
adding, removing, and updating elements. Write operations are listed in the [Write operations](collection-write.md) and
corresponding sections of [List-specific operations](list-operations.md#list-write-operations) and [Map specific operations](map-operations.md#map-write-operations). -->
Для изменяемых коллекций доступны так называемые _операции записи_, которые изменяют состояние коллекции. Это операции по добавлению, удалению и обновлению элементов. Подробнее они рассматриваются в отдельном разделе - [Операции записи](collection-write.html), а также частично в разделах [List: специфичные операции](list-operations.html#list-write-operations) и [Map: специфичные операции](map-operations.html#map-write-operations).

<!-- For certain operations, there are pairs of functions for performing the same operation: one applies the operation in-place
and the other returns the result as a separate collection. For example, [`sort()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort.html)
sorts a mutable collection in-place, so its state changes; [`sorted()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted.html)
creates a new collection that contains the same elements in the sorted order. -->
Помимо прочего существуют пары функций, которые выполняют одинаковые операции, но с одной лишь разницей: одна функция изменяет исходную коллекцию и поэтому работает только с изменяемыми типами, тогда как вторая возвращает результат в виде новой коллекции и таким образом позволяет работать с изменяемыми и неизменяемыми типами коллекций.
Например, функция [`sort()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort.html) сортирует исходную изменяемую коллекцию, тем самым меняя её состояние;
функция [`sorted()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted.html) создаёт новую коллекцию, содержащую те же элементы, что и исходная, но в отсортированном порядке.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three", "four")
    val sortedNumbers = numbers.sorted()
    println(numbers == sortedNumbers)  // false
    numbers.sort()
    println(numbers == sortedNumbers)  // true
}
```
