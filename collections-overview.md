---
type: doc
layout: reference
category: Collections
title: "Коллекции. Общий обзор"
url: https://kotlinlang.ru/docs/collections-overview.html
---

<a name="collections-overview"></a>
<!--# Collections overview-->
# Коллекции. Общий обзор

<!-- The Kotlin Standard Library provides a comprehensive set of tools for managing _collections_ – groups of a variable number
of items (possibly zero) that share significance to the problem being solved and are operated upon commonly. -->
Стандартная библиотека Kotlin предоставляет большой набор инструментов для работы с _коллекциями_ — группами с переменным количеством элементов (или нулём элементов), которые используются для решения какой-либо задачи.

<!-- Collections are a common concept for most programming languages, so if you're familiar with, for example, Java or Python
collections, you can skip this introduction and proceed to the detailed sections.  -->
Коллекции — это общая концепция для большинства языков программирования, поэтому, если вы знакомы с коллекциями, например, в Java или Python, то можете пропустить данное введение и перейти к разделам с более подробным описанием.

<!-- A collection usually contains a number of objects (this number may also be zero) of the same type. Objects in a collection
are called _elements_ or _items_. For example, all the students in a department form a collection that can be used to
calculate their average age.  -->
Обычно в коллекции находится несколько объектов одного типа (но также коллекция может быть пустой). Эти объекты называются _элементами_ или _items_. Например, все студенты одного факультета образуют коллекцию, которую можно использовать для расчёта их среднего возраста.

<!-- The  following collection types are relevant for Kotlin:
* _List_ is an ordered collection with access to elements by indices – integer numbers that reflect their position.
Elements can occur more than once in a list. An example of a list is a sentence: it's a group of words, their order is
important, and they can repeat.
* _Set_ is a collection of unique elements. It reflects the mathematical abstraction of set: a group of objects without
repetitions. Generally, the order of set elements has no significance. For example, an alphabet is a set of letters.
* _Map_ (or _dictionary_) is a set of key-value pairs. Keys are unique, and each of them maps to exactly one value. The
 values can be duplicates. Maps are useful for storing logical connections between objects, for example, an employee's ID
 and their position. -->
Типы коллекций в Kotlin:
* _List_ (список) - упорядоченная коллекция, в которой к элементам можно обращаться по индексам — целым числам, отражающим положение элементов в коллекции. Идентичные элементы (дубликаты) могут встречаться в списке более одного раза. Примером списка является предложение: это группа слов, их порядок важен, и они могут повторяться.
* _Set_ (множество) - коллекция уникальных элементов. Отражает математическую абстракцию множества: группа объектов без повторов. Как правило, порядок расположения элементов здесь не имеет значения. Примером множества является алфавит.
* _Map_ (словарь, ассоциативный список) - набор из пар "ключ-значение". Ключи уникальны и каждый из них соответствует ровно одному значению. Значения могут иметь дубликаты. Ассоциативные списки полезны для хранения логических связей между объектами, например, ID сотрудников и их должностей.

<!-- Kotlin lets you manipulate collections independently of the exact type of objects stored in them. In other words, you add
a `String` to a list of `String`s the same way as you would do with `Int`s or a user-defined class.
So, the Kotlin Standard Library offers generic interfaces, classes, and functions for creating, populating, and managing
collections of any type. -->
Kotlin позволяет управлять коллекциями независимо от того, какой именно тип объектов в них хранится: будь то `String`, `Int` или какой-то собственный класс, общие принципы работы с коллекцией всегда неизменны. Стандартная библиотека Kotlin предоставляет общие интерфейсы, классы и функции для создания, заполнения и управления коллекциями любого типа.

<!-- The collection interfaces and related functions are located in the `kotlin.collections` package. Let's get an overview
of its contents. -->
Интерфейсы коллекций и связанные с ними функции находятся в пакете `kotlin.collections`. Давайте рассмотрим его содержимое.


<a name="collection-types"></a>
<!-- ## Collection types -->
## Типы коллекций

<!-- The Kotlin Standard Library provides implementations for basic collection types: sets, lists, and maps.
A pair of interfaces represent each collection type:

* A _read-only_ interface that provides operations for accessing collection elements.
* A _mutable_ interface that extends the corresponding read-only interface with write operations: adding, removing, and
updating its elements. -->
Стандартная библиотека Kotlin предоставляет реализации для основных типов коллекций: `Set`, `List`, `Map`. Есть два вида интерфейсов, предоставляющих каждый из этих типов:
* _неизменяемый_ ( _read-only_) - предоставляет операции, которые дают доступ к элементам коллекции.
* _изменяемый_ (_mutable_) - расширяет предыдущий интерфейс и дополнительно даёт доступ к операциям добавления, удаления и обновления элементов коллекции.

<!-- Note that altering a mutable collection doesn't require it to be a [`var`](basic-syntax.md#variables): write operations
modify the same mutable collection object, so the reference doesn't change.
Although, if you try to reassign a `val` collection, you'll get a compilation error. -->
Обратите внимание, что изменяемую коллекцию не требуется объявлять с помощью ключевого слова [`var`](properties.html). Связано это с тем, что изменения вносятся в изначальные объекты коллекции без изменения ссылки на саму коллекцию. Но если вы объявите коллекцию с помощью `val` и попытаетесь ее перезаписать, то получите ошибку компиляции.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three", "four")
    numbers.add("five") // this is OK    
    //numbers = mutableListOf("six", "seven") // compilation error
}
```

<!-- The read-only collection types are [covariant](generics.md#variance).
This means that, if a `Rectangle` class inherits from `Shape`, you can use a `List<Rectangle>` anywhere the `List<Shape>`
is required.
In other words, the collection types have the same subtyping relationship as the element types. Maps are covariant on
the value type, but not on the key type. -->
Неизменяемые типы коллекций [ковариантны](generics.html). Это означает, что если класс `Rectangle` наследуется от `Shape`, вы можете использовать `List<Rectangle>` там, где требуется `List<Shape>`.
Другими словами, типы коллекций имеют такое же отношение подтипов, что и типы элементов. `Map`-ы ковариантны по типу значения, но не по типу ключа.

<!-- In turn, mutable collections aren't covariant; otherwise, this would lead to runtime failures. If `MutableList<Rectangle>`
was a subtype of `MutableList<Shape>`, you could insert other `Shape` inheritors (for example, `Circle`) into it, thus
violating its `Rectangle` type argument. -->
В свою очередь, изменяемые коллекции не являются ковариантными; в противном случае это привело бы к сбоям во время выполнения. Если `MutableList<Rectangle>` был подтипом `MutableList<Shape>`, вы могли добавить в него других наследников `Shape` (например, `Circle`), таким образом нарушая изначальный тип коллекции - `Rectangle`.

<!-- Below is a diagram of the Kotlin collection interfaces: -->
Ниже представлена ​​схема интерфейсов коллекций Kotlin:

<img src="https://kotlinlang.org/docs/images/collections-diagram.png" width="500"/>

Пройдемся по интерфейсам и их реализациям.


<a name="collection"></a>
<!-- ### Collection -->
### Collection

<!-- [`Collection<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/index.html) is the root of
the collection hierarchy. This interface represents the common behavior of a read-only collection: retrieving size,
checking item membership, and so on.
`Collection` inherits from the `Iterable<T>` interface that defines the operations for iterating elements. You can use
`Collection` as a parameter of a function that applies to different collection types. For more specific cases, use
the `Collection`'s inheritors: [`List`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/index.html)
 and [`Set`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-set/index.html). -->
[`Collection<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/index.html) является корнем в иерархии коллекций. Этот интерфейс представляет собой обычное поведение неизменяемой коллекции: операции типа `size`, `get` и т. д.
`Collection` наследуется от интерфейса `Iterable<T>`, который определяет операции для итерации элементов. Вы можете использовать `Collection` как параметр функции, которая может работать с разными типами коллекций. Для более конкретных случаев следует использовать наследников `Collection`: [`List`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/index.html) и [`Set`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-set/index.html).

```kotlin
fun printAll(strings: Collection<String>) {
        for(s in strings) print("$s ")
        println()
    }

fun main() {
    val stringList = listOf("one", "two", "one")
    printAll(stringList) // one two one

    val stringSet = setOf("one", "two", "three")
    printAll(stringSet) // one two three
}
```

<!-- [`MutableCollection<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-collection/index.html) is
a `Collection` with write operations, such as `add` and `remove`. -->
[`MutableCollection<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-collection/index.html) - это `Collection` с операциями записи, такими как `add` и `remove`.

```kotlin
fun List<String>.getShortWordsTo(shortWords: MutableList<String>, maxLength: Int) {
    this.filterTo(shortWords) { it.length <= maxLength }
    // throwing away the articles
    val articles = setOf("a", "A", "an", "An", "the", "The")
    shortWords -= articles
}

fun main() {
    val words = "A long time ago in a galaxy far far away".split(" ")
    val shortWords = mutableListOf<String>()
    words.getShortWordsTo(shortWords, 3)
    println(shortWords) // [ago, in, far, far]
}
```

<a name="list"></a>
<!-- ### List -->
### List

<!-- [`List<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/index.html) stores elements in a
specified order and provides indexed access to them. Indices start from zero – the index of the first element – and go
to `lastIndex` which is the `(list.size - 1)`.  -->
[`List<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/index.html) хранит элементы в определённом порядке и обеспечивает к ним доступ по индексу. Индексы начинаются с нуля (0 - индекс первого элемента) и идут до `lastIndex`, который равен `(list.size - 1)`.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    println("Number of elements: ${numbers.size}") // 4
    println("Third element: ${numbers.get(2)}") // three
    println("Fourth element: ${numbers[3]}") // four
    println("Index of element \"two\" ${numbers.indexOf("two")}") // 1
}
```

<!-- List elements (including nulls) can duplicate: a list can contain any number of equal objects or occurrences of a
single object.
Two lists are considered equal if they have the same sizes and [structurally equal](equality.md#structural-equality)
elements at the same positions. -->
Элементы списка (в том числе `null`) могут дублироваться: список может содержать любое количество одинаковых объектов.
Два списка считаются равными, если они имеют одинаковый размер и их элементы в одних и тех позициях [структурно равны](equality.html).

```kotlin
data class Person(var name: String, var age: Int)

fun main() {
    val bob = Person("Bob", 31)
    val people = listOf(Person("Adam", 20), bob, bob)
    val people2 = listOf(Person("Adam", 20), Person("Bob", 31), bob)
    println(people == people2) // true
    bob.age = 32
    println(people == people2) // false
}
```

<!-- [`MutableList<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/index.html) is a `List`
with list-specific write operations, for example, to add or remove an element at a specific position. -->
[`MutableList<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/index.html) - это `List` с операциями записи, специфичными для списка, например, для добавления или удаления элемента в определённой позиции.

```kotlin
fun main() {
    val numbers = mutableListOf(1, 2, 3, 4)
    numbers.add(5)
    numbers.removeAt(1)
    numbers[0] = 0
    numbers.shuffle()
    println(numbers) // [4, 0, 3, 5]
}
```

<!-- As you see, in some aspects lists are very similar to arrays.
However, there is one important difference:  an array's size is defined upon initialization and is never changed;
in turn, a list doesn't have a predefined size; a list's size can be changed as a result of write operations: adding,
updating, or removing elements. -->
Как видите, в некоторых аспектах списки очень похожи на массивы. Однако есть одно важное отличие: размер массива определяется при инициализации и никогда не изменяется; в свою очередь список не имеет предопределённого размера; размер списка может быть изменён в результате операций записи: добавления, обновления или удаления элементов.

<!-- In Kotlin, the default implementation of `List` is [`ArrayList`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-array-list/index.html)
which you can think of as a resizable array. -->
По умолчанию в Kotlin реализацией `List` является [`ArrayList`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-array-list/index.html), который можно рассматривать как массив с изменяемым размером.


<a name="set"></a>
<!-- ### Set -->
### Set

<!-- [`Set<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-set/index.html) stores unique elements;
their order is generally undefined. `null` elements are unique as well: a `Set` can contain only one `null`.
Two sets are equal if they have the same size, and for each element of a set there is an equal element in the other set. -->
[`Set<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-set/index.html) хранит уникальные элементы; их порядок обычно не определён. `null` также является уникальным элементом: `Set` может содержать только один `null`.
Два множества равны, если они имеют одинаковый размер и для каждого элемента множества есть равный элемент в другом множестве.

```kotlin
fun main() {
    val numbers = setOf(1, 2, 3, 4)
    println("Number of elements: ${numbers.size}") // Number of elements: 4
    if (numbers.contains(1)) println("1 is in the set")

    val numbersBackwards = setOf(4, 3, 2, 1)
    println("The sets are equal: ${numbers == numbersBackwards}") // true
}
```

<!-- [`MutableSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-set/index.html) is a `Set` with
write operations from `MutableCollection`. -->
[`MutableSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-set/index.html) - это `Set` с операциями записи из `MutableCollection`.

<!-- The default implementation of `Set` – [`LinkedHashSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-linked-hash-set/index.html) –
preserves the order of elements insertion.
Hence, the functions that rely on the order, such as `first()` or `last()`, return predictable results on such sets. -->
По умолчанию реализацией `Set` является [`LinkedHashSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-linked-hash-set/index.html), который сохраняет порядок вставки элементов. Следовательно, функции, которые зависят от порядка элементов, такие как `first()` или `last()`, возвращают предсказуемые результаты для таких множеств.

```kotlin
fun main() {
    val numbers = setOf(1, 2, 3, 4)  // по умолчанию LinkedHashSet
    val numbersBackwards = setOf(4, 3, 2, 1)

    println(numbers.first() == numbersBackwards.first()) // false
    println(numbers.first() == numbersBackwards.last()) // true
}
```

<!-- An alternative implementation – [`HashSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-hash-set/index.html) –
says nothing about the elements order, so calling such functions on it returns unpredictable results. However, `HashSet`
requires less memory to store the same number of elements. -->
Альтернативная реализация - [`HashSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-hash-set/index.html) - не сохраняет порядок элементов, поэтому при вызове функций `first()` или `last()` вернётся непредсказуемый результат. Однако `HashSet` требует меньше памяти для хранения того же количества элементов.


<a name="map"></a>
<!-- ### Map -->
### Map

<!-- [`Map<K, V>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/index.html) is not an inheritor of
the `Collection` interface; however, it's a Kotlin collection type as well.
A `Map` stores _key-value_ pairs (or _entries_); keys are unique, but different keys can be paired with equal values.
The `Map` interface provides specific functions, such as access to value by key, searching keys and values, and so on.  -->
[`Map<K, V>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/index.html) не является наследником интерфейса `Collection`; однако это один из типов коллекций в Kotlin.
`Map` хранит пары "ключ-значение" (или _entries_); ключи уникальны, но разные ключи могут иметь одинаковые значения.
Интерфейс `Map` предоставляет такие функции, как доступ к значению по ключу, поиск ключей и значений и т. д.

```kotlin
fun main() {
    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 1)

    println("All keys: ${numbersMap.keys}") // [key1, key2, key3, key4]
    println("All values: ${numbersMap.values}") // [1, 2, 3, 1]
    if ("key2" in numbersMap) println("Value by key \"key2\": ${numbersMap["key2"]}")    
    if (1 in numbersMap.values) println("The value 1 is in the map")
    if (numbersMap.containsValue(1)) println("The value 1 is in the map") // аналогичен предыдущему условию
}
```

<!-- Two maps containing the equal pairs are equal regardless of the pair order. -->
Две `Map`-ы, содержащие равные пары, равны независимо от порядка пар.

```kotlin
fun main() {
    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 1)    
    val anotherMap = mapOf("key2" to 2, "key1" to 1, "key4" to 1, "key3" to 3)

    println("The maps are equal: ${numbersMap == anotherMap}") // The maps are equal: true
}
```

<!-- [`MutableMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/index.html) is a `Map` with
map write operations, for example, you can add a new key-value pair or update the value associated with the given key. -->
[`MutableMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/index.html) - это `Map` с операциями записи, например, можно добавить новую пару "ключ-значение" или обновить значение, связанное с указанным ключом.

```kotlin
fun main() {
    val numbersMap = mutableMapOf("one" to 1, "two" to 2)
    numbersMap.put("three", 3)
    numbersMap["one"] = 11

    println(numbersMap) // {one=11, two=2, three=3}
}
```

<!-- The default implementation of `Map` – [`LinkedHashMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-linked-hash-map/index.html) –
preserves the order of elements insertion when iterating the map.
In turn, an alternative implementation – [`HashMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-hash-map/index.html) –
says nothing about the elements order. -->
По умолчанию реализацией `Map` является [`LinkedHashMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-linked-hash-map/index.html) - сохраняет порядок элементов.
Альтернативная реализация - [`HashMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-hash-map/index.html) - не сохраняет порядок элементов.
