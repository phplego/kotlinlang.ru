---
type: doc
layout: reference
category: Collections
title: "Выбор одного элемента"
url: https://kotlinlang.ru/docs/reference/collection-elements.html
---

<!-- # Retrieve single elements -->
# Выбор одного элемента

<!-- Kotlin collections provide a set of functions for retrieving single elements from collections.
Functions described on this page apply to both lists and sets. -->
В Kotlin существует набор функций для извлечения отдельных элементов из коллекции.
Функции, описанные в этом разделе, применимы как к спискам, так и к множествам (`Set`).

<!-- As the [definition of list](collections-overview.md) says, a list is an ordered collection.
Hence, every element of a list has its position that you can use for referring.
In addition to functions described on this page, lists offer a wider set of ways to retrieve and search for elements by indices.
For more details, see [List-specific operations](list-operations.md). -->
В [определении списка](collections-overview.html) сказано, что список - это упорядоченная коллекция. Следовательно, каждый элемент списка имеет свою позицию, которую вы можете использовать в качестве ссылки на этот элемент.
В дополнение к функциям, описанным в этом разделе, списки предлагают более широкий набор способов извлечения и поиска элементов по индексам. Для получения дополнительной информации см. [List: специфичные операции](list-operations.html).

<!-- In turn, set is not an ordered collection by [definition](collections-overview.md).
However, the Kotlin `Set` stores elements in certain orders.
These can be the order of insertion (in `LinkedHashSet`), natural sorting order (in `SortedSet`), or another order.
The order of a set of elements can also be unknown.
In such cases, the elements are still ordered somehow, so the functions that rely on the element positions still return their results.
However, such results are unpredictable to the caller unless they know the specific implementation of `Set` used. -->
В свою очередь множество не является упорядоченной коллекцией по [определению](collections-overview.html). Однако в Kotlin `Set` хранит элементы в определённом порядке. Это может быть порядок вставки (как в `LinkedHashSet`), естественный порядок сортировки (как в `SortedSet`) или какой-либо другой порядок.
Но также порядок может быть и неизвестен. В таком случае элементы всё равно каким-то образом упорядочены, поэтому функции, которые полагаются на позиции элементов, по-прежнему возвращают свои результаты. Однако такие результаты непредсказуемы для конечного пользователя, если он не знает, какую из реализаций использует `Set`.


<a name="retrieve-by-position"></a>
<!-- ## Retrieve by position -->
## Получение элемента по его позиции

<!-- For retrieving an element at a specific position, there is the function [`elementAt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/element-at.html).
Call it with the integer number as an argument, and you'll receive the collection element at the given position.
The first element has the position `0`, and the last one is `(size - 1)`. -->
Для получения элемента в определённой позиции существует функция
[`elementAt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/element-at.html).
При вызове передайте ей целое число в качестве аргумента и она вернёт вам элемент коллекции, который находится в данной позиции. У первого элемента позиция равна `0`, а у последнего - `(size - 1)`.

<!-- `elementAt()` is useful for collections that do not provide indexed access, or are not statically known to provide one.
In case of `List`, it's more idiomatic to use [indexed access operator](list-operations.md#retrieve-elements-by-index) (`get()` or `[]`). -->
Функция `elementAt()` полезна для использования с коллекциями, которые не предоставляют индексированный доступ. При работе с `List` более идиоматично использовать [оператор доступа по индексу](list-operations.html#retrieve-elements-by-index) (`get()` или `[]`).

```kotlin
fun main() {
    val numbers = linkedSetOf("one", "two", "three", "four", "five")
    println(numbers.elementAt(3)) // four

    // элементы хранятся в порядке возрастания
    val numbersSortedSet = sortedSetOf("one", "two", "three", "four")
    println(numbersSortedSet.elementAt(0)) // four
}
```

<!-- There are also useful aliases for retrieving the first and the last element of the collection: [`first()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html)
and [`last()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html). -->
Также существуют полезные функции для получения первого и последнего элемента коллекции:
[`first()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html) и
[`last()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html).

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five")
    println(numbers.first()) // one
    println(numbers.last()) // five
}
```

<!-- To avoid exceptions when retrieving element with non-existing positions, use safe variations of `elementAt()`:

* [`elementAtOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/element-at-or-null.html) returns null when the specified position is out of the collection bounds.
* [`elementAtOrElse()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/element-at-or-else.html) additionally takes a lambda function that maps an `Int` argument to an instance of the collection element type.
   When called with an out-of-bounds position, the `elementAtOrElse()` returns the result of the lambda on the given value. -->
Чтобы избежать исключений при обращении к несуществующим в коллекции элементам, используйте безопасные варианты функции `elementAt()`:
* [`elementAtOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/element-at-or-null.html) - возвращает `null`, если указанная позиция выходит за границы коллекции.
* [`elementAtOrElse()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/element-at-or-else.html) - дополнительно принимает лямбда-функцию. Когда происходит вызов с позицией, которая выходит за границы коллекции, `elementAtOrElse()` возвращает результат выполнения лямбды.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five")
    println(numbers.elementAtOrNull(5)) // null
    println(numbers.elementAtOrElse(5) { index -> "The value for index $index is undefined"}) // The value for index 5 is undefined
}
```


<a name="retrieve-by-condition"></a>
<!-- ## Retrieve by condition -->
## Получение элемента по условию

<!-- Functions [`first()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html) and [`last()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html)
also let you search a collection for elements matching a given predicate. When you call `first()` with a predicate that
tests a collection element, you'll receive the first element on which the predicate yields `true`.
In turn, `last()` with a predicate returns the last element matching it. -->
Функции [`first()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html) и
[`last()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html)
позволяют искать в коллекции элементы, соответствующие заданному предикату. При вызове функции `first()` с предикатом, вы получите первый элемент, для которого предикат возвращает `true`. В свою очередь функция `last()` с предикатом вернёт последний элемент, соответствующий предикату.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five", "six")
    println(numbers.first { it.length > 3 }) // three
    println(numbers.last { it.startsWith("f") }) // five
}
```

<!-- If no elements match the predicate, both functions throw exceptions.
To avoid them, use [`firstOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-or-null.html)
and [`lastOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last-or-null.html) instead:
they return `null` if no matching elements are found. -->
Если ни один элемент не соответствует предикату, обе функции выбросят исключение. Чтобы этого избежать, используйте [`firstOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-or-null.html) и
[`lastOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last-or-null.html): эти функции вернут `null`, если элементы не найдены.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five", "six")
    println(numbers.firstOrNull { it.length > 6 }) // null
}
```

<!-- Use the aliases if their names suit your situation better:

* [`find()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/find.html) instead of `firstOrNull()`
* [`findLast()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/find-last.html) instead of `lastOrNull()` -->
Также вы можете использовать нижеперечисленные функции, если их имена лучше подходят для вашей ситуации:
* [`find()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/find.html) вместо `firstOrNull()`
* [`findLast()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/find-last.html) вместо `lastOrNull()`

```kotlin
fun main() {
    val numbers = listOf(1, 2, 3, 4)
    println(numbers.find { it % 2 == 0 }) // 2
    println(numbers.findLast { it % 2 == 0 }) // 4
}
```


<a name="retrieve-with-selector"></a>
<!-- ## Retrieve with selector -->
## Получение элемента с помощью селектора

<!-- If you need to map the collection before retrieving the element, there is a function [`firstNotNullOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-not-null-of.html).
It combines 2 actions:
- Maps the collection with the selector function
- Returns the first non-null value in the result -->
Если перед извлечением элемента вы хотите его преобразовать, то используйте функцию
[`firstNotNullOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-not-null-of.html).
Она сочетает в себе два действия:
- Преобразует коллекцию с помощью функции селектора.
- Возвращает первое значение, не равное `null`.

<!-- `firstNotNullOf()` throws the `NoSuchElementException` if the resulting collection doesn't have a non-null element.
Use the counterpart [`firstNotNullOfOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-not-null-of-or-null.html)
to return null in this case. -->
Функция `firstNotNullOf()` бросает исключение `NoSuchElementException`, если не было найдено значения, отличного от `null`. Чтобы этого избежать, используйте функцию
[`firstNotNullOfOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-not-null-of-or-null.html), которая в этом случае вернёт `null`.

```kotlin
fun main() {
    val list = listOf<Any>(0, "true", false)
    // Преобразует каждый элемент в строку и возвращает первый элемент с нужной длиной.
    val longEnough = list.firstNotNullOf { item -> item.toString().takeIf { it.length >= 4 } }
    println(longEnough) // true
}
```


<a name="random-element"></a>
<!-- ## Random element -->
## Получение случайного элемента

<!-- If you need to retrieve an arbitrary element of a collection, call the [`random()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/random.html) function.
You can call it without arguments or with a [`Random`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.random/-random/index.html)
object as a source of the randomness. -->
Функция [`random()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/random.html) позволяет получить произвольный элемент коллекции. Её можно вызывать без аргументов, либо с объектом
[`Random`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.random/-random/index.html).

```kotlin
fun main() {
    val numbers = listOf(1, 2, 3, 4)
    println(numbers.random()) // 1
}
```

<!-- On empty collections, `random()` throws an exception. To receive `null` instead, use [`randomOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/random-or-null.html). -->
Если коллекция пуста, то функция `random()` выбросит исключение. Чтобы вместо исключения получать `null`, используйте [`randomOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/random-or-null.html).


<a name="check-element-existence"></a>
<!-- ## Check element existence -->
## Проверка на наличие элемента

<!-- To check the presence of an element in a collection, use the [`contains()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/contains.html) function.
It returns `true` if there is a collection element that `equals()` the function argument.
You can call `contains()` in the operator form with the `in` keyword. -->
Чтобы проверить есть ли в коллекции определённый элемент, используйте функцию
[`contains()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/contains.html). Функция вернёт `true`, если элемент, переданный ей в качестве аргумента, есть в коллекции.
Также эту функцию можно использовать в форме оператора при помощи ключевого слова `in`.

<!-- To check the presence of multiple instances together at once, call [`containsAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/contains-all.html)
with a collection of these instances as an argument. -->
Для проверки на наличие в коллекции сразу нескольких элементов используйте функцию
[`containsAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/contains-all.html). В качестве аргумента передайте ей коллекцию искомых элементов.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five", "six")
    println(numbers.contains("four")) // true
    println("zero" in numbers) // false

    println(numbers.containsAll(listOf("four", "two"))) // true
    println(numbers.containsAll(listOf("one", "zero"))) // false
}
```

<!-- Additionally, you can check if the collection contains any elements by calling [`isEmpty()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/is-empty.html)
or [`isNotEmpty()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/is-not-empty.html). -->
Помимо прочего, вы можете проверить, содержит ли коллекция какие-либо элементы, вызвав
[`isEmpty()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/is-empty.html) или
[`isNotEmpty()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/is-not-empty.html).

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four", "five", "six")
    println(numbers.isEmpty()) // false
    println(numbers.isNotEmpty()) // true

    val empty = emptyList<String>()
    println(empty.isEmpty()) // true
    println(empty.isNotEmpty()) // false
}
```
