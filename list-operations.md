---
type: doc
layout: reference
category: Collections
title: "List: специфичные операции"
url: https://kotlinlang.ru/docs/list-operations.html
---

<!-- # List-specific operations -->
# List: специфичные операции

<!-- [`List`](collections-overview.md#list) is the most popular type of built-in collection in Kotlin. Index access to the elements
of lists provides a powerful set of operations for lists.  -->
[`List`](collections-overview.html#list) - самый популярный из всех типов коллекций в Kotlin. Он предоставляет мощный набор операций благодаря наличию доступа к элементам по индексу.


<a name="retrieve-elements-by-index"></a>
<!-- ## Retrieve elements by index -->
## Получение элементов по индексу

<!-- Lists support all common operations for element retrieval: `elementAt()`, `first()`, `last()`, and others listed in [Retrieve single elements](collection-elements.md).
What is specific for lists is index access to the elements, so the simplest way to read an element is retrieving it by index.
That is done with the [`get()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/get.html) function
with the index passed in the argument or the shorthand `[index]` syntax. -->
Списки поддерживают все стандартные операции для получения элементов: `elementAt()`, `first()`, `last()` и другие, перечисленные в разделе
[Выбор одного элемента](collection-elements.html).
Списки характерны тем, что предоставляют доступ к элементам по индексу, поэтому именно по индексу проще всего получить необходимый элемент. Это осуществляется с помощью функции
[`get()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/get.html), которая принимает индекс в качестве аргумента. Также можно использовать сокращённый синтаксис - `[index]`.

<!-- If the list size is less than the specified index, an exception is thrown.
There are two other functions that help you avoid such exceptions:
* [`getOrElse()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-else.html) lets you provide the function for calculating the default value to return if the index isn't present in the collection.
* [`getOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-null.html) returns `null` as the default value.  -->
Если размер списка меньше указанного индекса, то будет выброшено исключение. Есть ещё две функции, которые помогут этого избежать:
* [`getOrElse()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-else.html) - позволяет вам предоставить функцию для вычисления значения по умолчанию. Если по указанному индексу элемент не был найден, то будет возвращён результат вычисления этой функции.
* [`getOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-null.html) - возвращает `null` в качестве значения по умолчанию.

```kotlin
fun main() {
    val numbers = listOf(1, 2, 3, 4)
    println(numbers.get(0)) // 1
    println(numbers[0]) // 1
    //numbers.get(5) // exception!
    println(numbers.getOrNull(5)) // null
    println(numbers.getOrElse(5, {it})) // 5
}
```


<a name="retrieve-list-parts"></a>
<!-- ## Retrieve list parts -->
## Получение части списка

<!-- In addition to common operations for [Retrieving Collection Parts](collection-parts.md), lists provide the [`subList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/sub-list.html)
function that returns a view of the specified elements range as a list.
Thus, if an element of the original collection changes, it also changes in the previously created sublists and vice versa. -->
Помимо обычных операций для [Выборки элементов](collection-parts.html), списки предоставляют функцию
[`subList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/sub-list.html), которая возвращает представление указанного диапазона элементов в виде списка. Таким образом, если элемент исходной коллекции будет изменён, то он также изменится и в списке, созданном с помощью функции `subList()`, и наоборот.

```kotlin
fun main() {
    val numbers = (0..13).toList()
    println(numbers.subList(3, 6)) // [3, 4, 5]
}
```


<a name="find-element-positions"></a>
<!-- ## Find element positions -->
## Поиск позиции элемента

<a name="linear-search"></a>
<!-- ### Linear search -->
### Линейный поиск

<!-- In any lists, you can find the position of an element using the functions [`indexOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of.html) and [`lastIndexOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last-index-of.html).
They return the first and the last position of an element equal to the given argument in the list.
If there are no such elements, both functions return `-1`. -->
В любом списке вы можете найти позицию элемента с помощью функций
[`indexOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of.html) и
[`lastIndexOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last-index-of.html).
Они возвращают первую и последнюю позицию элемента, равного заданному аргументу. Если таких элементов нет, то обе функции возвращают `-1`.

```kotlin
fun main() {
    val numbers = listOf(1, 2, 3, 4, 2, 5)
    println(numbers.indexOf(2)) // 1
    println(numbers.lastIndexOf(2)) // 4
}
```

<!-- There is also a pair of functions that take a predicate and search for elements matching it:
* [`indexOfFirst()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of-first.html) returns the *index of the first* element matching the predicate or `-1` if there are no such elements.
* [`indexOfLast()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of-last.html) returns the *index of the last* element matching the predicate or `-1` if there are no such elements. -->
Также существуют две функции, которые принимают предикат и ищут соответствующие ему элементы:
* [`indexOfFirst()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of-first.html) - возвращает _индекс первого_ элемента, соответствующего заданному предикату или `-1`, если таких элементов нет.
* [`indexOfLast()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of-last.html) - возвращает _индекс последнего_ элемента, соответствующего заданному предикату или `-1`, если таких элементов нет.

```kotlin
fun main() {
    val numbers = mutableListOf(1, 2, 3, 4)
    println(numbers.indexOfFirst { it > 2}) // 2
    println(numbers.indexOfLast { it % 2 == 1}) // 2
}
```

<a name="binary-search-in-sorted-lists"></a>
<!-- ### Binary search in sorted lists -->
### Бинарный поиск в отсортированном списке

<!-- There is one more way to search elements in lists – [binary search](https://en.wikipedia.org/wiki/Binary_search_algorithm).
It works significantly faster than other built-in search functions but *requires the list to be [sorted](collection-ordering.md)*
in ascending order according to a certain ordering: natural or another one provided in the function parameter.
Otherwise, the result is undefined.  -->
Ещё один способ поиска элементов в списке – [бинарный поиск](https://en.wikipedia.org/wiki/Binary_search_algorithm). Он работает значительно быстрее остальных встроенных функций поиска, но для его использования требуется, чтобы
_список был [отсортирован](collection-ordering.html)_ по возрастанию в соответствии с определённым порядком: естественным или любым другим, указанным в параметре функции. В противном случае результат не будет определён.

<!-- To search an element in a sorted list, call the [`binarySearch()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/binary-search.html)
function passing the value as an argument.
If such an element exists, the function returns its index; otherwise, it returns `(-insertionPoint - 1)` where `insertionPoint`
is the index where this element should be inserted so that the list remains sorted.
If there is more than one element with the given value, the search can return any of their indices. -->
Чтобы найти элемент в отсортированном списке, вызовите функцию
[`binarySearch()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/binary-search.html), передав ей искомое значение в качестве аргумента. Если такой элемент существует, функция вернёт его индекс; в противном случае она вернёт `(-insertionPoint - 1)`, где `insertionPoint` - это индекс, в который этот элемент должен быть вставлен, чтобы список оставался отсортированным. Если существует более одного элемента с указанным значением, функция может вернуть любой из их индексов.

<!-- You can also specify an index range to search in: in this case, the function searches only between two provided indices. -->
Вы также можете указать диапазон индексов для поиска: в этом случае функция выполняет поиск только между двумя предоставленными индексами.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three", "four")
    numbers.sort()
    println(numbers) // [four, one, three, two]
    println(numbers.binarySearch("two")) // 3
    println(numbers.binarySearch("z")) // -5
    println(numbers.binarySearch("two", 0, 2)) // -3
}
```

<!-- #### Comparator binary search -->
#### Бинарный поиск с `Comparator`

<!-- When list elements aren't `Comparable`, you should provide a [`Comparator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparator.html) to use in the binary search.
The list must be sorted in ascending order according to this `Comparator`. Let's have a look at an example: -->
Если элементы списка не являются `Comparable`, вы должны предоставить
[`Comparator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparator.html), который будет использован в бинарном поиске. Список должен быть отсортирован по возрастанию в соответствии с этим `Comparator`. Давайте посмотрим на пример:

```kotlin
data class Product(val name: String, val price: Double)

fun main() {
    val productList = listOf(
        Product("WebStorm", 49.0),
        Product("AppCode", 99.0),
        Product("DotTrace", 129.0),
        Product("ReSharper", 149.0))

    println(
      productList
        .binarySearch(Product("AppCode", 99.0), compareBy<Product> { it.price }
        .thenBy { it.name }
      )
    ) // 1
}
```

<!-- Here's a list of `Product` instances that aren't `Comparable` and a `Comparator` that defines the order: product `p1`
precedes product `p2` if `p1`'s  price is less than `p2`'s price.
So, having a list sorted ascending according to this order, we use `binarySearch()` to find the index of the specified `Product`. -->
В данном примере есть:
- список из экземпляров класса `Product`, которые не являются `Comparable`.
- `Comparator`, который определяет порядок: продукт `p1` предшествует продукту `p2`, если цена `p1` меньше, чем цена `p2`.
Итак, имея список, отсортированный по возрастанию в соответствии с этим порядком, мы используем `binarySearch()`, чтобы найти индекс указанного `Product`.

<!-- Custom comparators are also handy when a list uses an order different from natural one, for example, a case-insensitive order for `String` elements. -->
Пользовательские компараторы также удобны, когда для сортировки списка используется порядок, отличный от естественного, например, без учета регистра для элементов `String`.

```kotlin
fun main() {
    val colors = listOf("Blue", "green", "ORANGE", "Red", "yellow")
    println(colors.binarySearch("RED", String.CASE_INSENSITIVE_ORDER)) // 3
}
```

<!-- #### Comparison binary search -->
#### Бинарный поиск с функцией сравнения

<!-- Binary search with _comparison_ function lets you find elements without providing explicit search values.
Instead, it takes a comparison function mapping elements to `Int` values and searches for the element where the function returns zero.
The list must be sorted in the ascending order according to the provided function; in other words, the return values of
comparison must grow from one list element to the next one. -->
Бинарный поиск с функцией _сравнения_ (_comparison_) позволяет находить элементы без предоставления явных значений для поиска. Вместо этого он принимает элементы функции сравнения, преобразованные в `Int`, и ищет элемент, для которого функция возвращает ноль. Список должен быть отсортирован по возрастанию в соответствии с порядком, предоставленным функцией; другими словами, возвращаемые функцией значения должны расти от одного элемента списка к следующему.

```kotlin
import kotlin.math.sign

data class Product(val name: String, val price: Double)

fun priceComparison(product: Product, price: Double) = sign(product.price - price).toInt()

fun main() {
    val productList = listOf(
        Product("WebStorm", 49.0),
        Product("AppCode", 99.0),
        Product("DotTrace", 129.0),
        Product("ReSharper", 149.0))

    println(productList.binarySearch { priceComparison(it, 99.0) }) // 1
}
```

<!-- Both comparator and comparison binary search can be performed for list ranges as well. -->
Бинарный поиск как с компаратором, так и с функцией сравнения может выполняться для диапазонов списков.


<a name="list-write-operations"></a>
<!-- ## List write operations -->
## Операции записи

<!-- In addition to the collection modification operations described in [Collection write operations](collection-write.md),
[mutable](collections-overview.md#collection-types) lists support specific write operations.
Such operations use the index to access elements to broaden the list modification capabilities. -->
В дополнение к изменяющим коллекцию операциям, которые описаны в разделе [Операции записи коллекций](collection-write.html), [изменяемые](collections-overview.html#collection-types) списки поддерживают операции записи, характерные только для списков. Эти операции используют индекс для доступа к элементам, что расширяет возможности для изменения списка.

<a name="add"></a>
<!-- ### Add -->
### Добавление элементов

<!-- To add elements to a specific position in a list, use [`add()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/add.html)
and [`addAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/add-all.html) providing the position for
element insertion as an additional argument.
All elements that come after the position shift to the right. -->
Чтобы добавить элементы в определённую позицию в списке, используйте
[`add()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/add.html)
и
[`addAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/add-all.html), которым в качестве аргумента передайте позицию для вставки элемента. Все элементы, которые идут после указанной позиции, будут смещены вправо.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "five", "six")
    numbers.add(1, "two")
    numbers.addAll(2, listOf("three", "four"))
    println(numbers) // [one, two, three, four, five, six]
}
```

<a name="update"></a>
<!-- ### Update -->
### Обновление элементов

<!-- Lists also offer a function to replace an element at a given position - [`set()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/set.html)
and its operator form `[]`. `set()` doesn't change the indexes of other elements. -->
Списки также предоставляют функцию для замены элемента в заданной позиции -
[`set()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/set.html), у которой есть операторная форма `[]`. `set()` не меняет индексы других элементов.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "five", "three")
    numbers[1] =  "two"
    println(numbers) // [one, two, three]
}
```

<!-- [`fill()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fill.html) simply replaces all the collection
elements with the specified value. -->
Функция [`fill()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fill.html) просто заменяет все элементы коллекции на указанное значение.

```kotlin
fun main() {
    val numbers = mutableListOf(1, 2, 3, 4)
    numbers.fill(3)
    println(numbers) // [3, 3, 3, 3]
}
```

<a name="remove"></a>
<!-- ### Remove -->
### Удаление элементов

<!-- To remove an element at a specific position from a list, use the [`removeAt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/remove-at.html)
function providing the position as an argument.
All indices of elements that come after the element being removed will decrease by one. -->
Чтобы удалить элемент из определённой позиции списка, используйте функцию
[`removeAt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/remove-at.html), передав ей в качестве аргумента эту позицию. Все индексы элементов, которые идут после удаляемого, будут уменьшены на единицу.

```kotlin
fun main() {
    val numbers = mutableListOf(1, 2, 3, 4, 3)    
    numbers.removeAt(1)
    println(numbers) // [1, 3, 4, 3]
}
```

<a name="sort"></a>
<!-- ### Sort -->
### Сортировка

<!-- In [Collection Ordering](collection-ordering.md), we describe operations that retrieve collection elements in specific orders.
For mutable lists, the standard library offers similar extension functions that perform the same ordering operations in place.
When you apply such an operation to a list instance, it changes the order of elements in that exact instance. -->
В разделе [Сортировка коллекций](collection-ordering.html) описаны операции, которые возвращают элементы коллекции в определённом порядке. Для изменяемых списков в стандартной библиотеке есть аналогичные функции-расширения, выполняющие сортировку, но вместо возврата нового отсортированного списка они изменяют порядок элементов в исходном списке.

<!-- The in-place sorting functions have similar names to the functions that apply to read-only lists, but without the `ed/d` suffix:
*  `sort*` instead of `sorted*` in the names of all sorting functions: [`sort()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort.html), [`sortDescending()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort-descending.html), [`sortBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort-by.html), and so on.
* [`shuffle()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/shuffle.html) instead of `shuffled()`.
* [`reverse()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reverse.html) instead of `reversed()`. -->
Подобные функции сортировки названы похожими именами, но без суффикса `ed/d`:
*  `sort*` вместо `sorted*` в именах всех функций сортировки:
  [`sort()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort.html),
  [`sortDescending()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort-descending.html),
  [`sortBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort-by.html) и так далее.
* [`shuffle()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/shuffle.html) вместо `shuffled()`.
* [`reverse()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reverse.html) вместо `reversed()`.

<!-- [`asReversed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/as-reversed.html) called on a mutable list
returns another mutable list which is a reversed view of the original list. Changes in that view are reflected in the original list.
The following example shows sorting functions for mutable lists: -->
Функция [`asReversed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/as-reversed.html), вызываемая к изменяемому списку, возвращает другой изменяемый список, который является перевёрнутым представлением исходного списка. Вместе с изменением этого представления вы изменяете и исходный список.

В следующем примере показаны функции сортировки для изменяемых списков:

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three", "four")

    numbers.sort()
    println("Sort into ascending: $numbers") // Sort into ascending: [four, one, three, two]
    numbers.sortDescending()
    println("Sort into descending: $numbers") // Sort into descending: [two, three, one, four]

    numbers.sortBy { it.length }
    println("Sort into ascending by length: $numbers") // Sort into ascending by length: [two, one, four, three]
    numbers.sortByDescending { it.last() }
    println("Sort into descending by the last letter: $numbers") // Sort into descending by the last letter: [four, two, one, three]

    numbers.sortWith(compareBy<String> { it.length }.thenBy { it })
    println("Sort by Comparator: $numbers") // Sort by Comparator: [one, two, four, three]

    numbers.shuffle()
    println("Shuffle: $numbers") // Shuffle: [one, two, three, four]

    numbers.reverse()
    println("Reverse: $numbers") // Reverse: [four, three, two, one]
}
```
