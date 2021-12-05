---
type: doc
layout: reference
category: Collections
title: "Создание коллекций"
url: https://kotlinlang.ru/docs/constructing-collections.html
---

<a name="constructing-collections"></a>
<!-- # Constructing collections -->
# Создание коллекций

<a name="construct-from-elements"></a>
<!-- ## Construct from elements -->
## Коллекция с элементами

<!-- The most common way to create a collection is with the standard library functions [`listOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/list-of.html),
[`setOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/set-of.html),
[`mutableListOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-list-of.html),
[`mutableSetOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-set-of.html).
If you provide a comma-separated list of collection elements as arguments, the compiler detects the element type
automatically. When creating empty collections, specify the type explicitly. -->
Самый распространённый способ создать коллекцию - использовать функции
[`listOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/list-of.html),
[`setOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/set-of.html),
[`mutableListOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-list-of.html),
[`mutableSetOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-set-of.html).
Этим функциям в качестве аргументов можно передать элементы, разделяя их запятой. В этом случае тип коллекции указывать не обязательно - компилятор сам его определит. Если же вы хотите создать пустую коллекцию, то её тип необходимо указывать явно.

```kotlin
val numbersSet = setOf("one", "two", "three", "four")
val emptySet = mutableSetOf<String>()
```

<!-- The same is available for maps with the functions [`mapOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-of.html)
and [`mutableMapOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-map-of.html). The map's
keys and values are passed as `Pair` objects (usually created with `to` infix function).  -->
Таким же образом создаются и ассоциативные списки - при помощи функций
[`mapOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-of.html)
и
[`mutableMapOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-map-of.html).
Ключи и значения ассоциативного списка передаются как объекты `Pair` (обычно создаются с помощью инфиксной функции `to`).

```kotlin
val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 1)
```

<!-- Note that the `to` notation creates a short-living `Pair` object, so it's recommended that you use it only if performance
isn't critical. To avoid excessive memory usage, use alternative ways. For example, you can create a mutable map and
populate it using the write operations. The [`apply()`](scope-functions.md#apply) function can help to keep the
initialization fluent here. -->
Обратите внимание, что нотация `to` создаёт недолговечный объект `Pair`, поэтому рекомендуется использовать его только в том случае, если производительность не критична. Чтобы избежать чрезмерного использования памяти, используйте альтернативные способы. Например, вы можете создать `MutableMap` и заполнить его с помощью операций записи. Функция [`apply ()`](scope-functions.html#apply) поможет создать плавную инициализацию.

```kotlin
val numbersMap = mutableMapOf<String, String>()
        .apply {
            this["one"] = "1";
            this["two"] = "2"
        }
```


<a name="empty-collections"></a>
<!-- ## Empty collections -->
## Пустая коллекция

<!-- There are also functions for creating collections without any elements: [`emptyList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/empty-list.html),
[`emptySet()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/empty-set.html), and
[`emptyMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/empty-map.html).
When creating empty collections, you should specify the type of elements that the collection will hold. -->
Существуют функции для создания пустых коллекций:
[`emptyList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/empty-list.html),
[`emptySet()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/empty-set.html) и
[`emptyMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/empty-map.html).
При создании пустой коллекции вы должны явно указывать тип элементов, которые будет содержать коллекция.

```kotlin
val empty = emptyList<String>()
```


<a name="initializer-functions-for-lists"></a>
<!-- ## Initializer functions for lists -->
## Функция-инициализатор для списков

<!-- For lists, there is a constructor that takes the list size and the initializer function that defines the element value
based on its index. -->
У списков есть конструктор, принимающий размер списка и функцию-инициализатор, которая определяет значение элементов на основе их индексов.


```kotlin
fun main() {
    val doubled = List(3, { it * 2 })  // или MutableList, если вы хотите изменять содержимое
    println(doubled) // [0, 2, 4]
}
```


<a name="concrete-type-constructors"></a>
<!-- ## Concrete type constructors -->
## Конструкторы конкретных типов

<!-- To create a concrete type collection, such as an `ArrayList` or `LinkedList`, you can use the available constructors for
these types. Similar constructors are available for implementations of `Set` and `Map`. -->
Чтобы создать коллекцию конкретного типа, например, `ArrayList` или `LinkedList`, вы можете использовать их конструкторы. Аналогичные конструкторы доступны и для реализаций `Set` и `Map`.

```kotlin
val linkedList = LinkedList<String>(listOf("one", "two", "three"))
val presizedSet = HashSet<Int>(32)
```


<a name="copy"></a>
<!-- ## Copy -->
## Копирование коллекции

<!-- To create a collection with the same elements as an existing collection, you can use copying operations. Collection
copying operations from the standard library create _shallow_ copy collections with references to the same elements.
Thus, a change made to a collection element reflects in all its copies.  -->
Если вам требуется создать новую коллекцию, но с элементами из существующей коллекции, то вы можете её скопировать. Операции копирования из стандартной библиотеки создают _неполные_ копии коллекций - со ссылками на исходные элементы.
Поэтому изменение, внесённое в элемент коллекции, будет применено ко всем его копиям.

<!-- Collection copying functions, such as [`toList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-list.html),
[`toMutableList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-mutable-list.html),
[`toSet()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-set.html) and others, create a snapshot
of a collection at a specific moment. Their result is a new collection of the same elements.
If you add or remove elements from the original collection, this won't affect the copies. Copies may be changed
independently of the source as well. -->
Функции копирования коллекций, такие как
[`toList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-list.html),
[`toMutableList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-mutable-list.html),
[`toSet()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-set.html) и другие, фиксируют состояние коллекции в определённый момент времени. Результат их выполнения - новая коллекция, но с элементами из исходной коллекции.
Если вы добавите или удалите элементы из исходной коллекции, это не повлияет на копии. Копии также могут быть изменены независимо от источника.

```kotlin
fun main() {
    val sourceList = mutableListOf(1, 2, 3)
    val copyList = sourceList.toMutableList()
    val readOnlyCopyList = sourceList.toList()
    sourceList.add(4)
    println("Copy size: ${copyList.size}") // 3

    //readOnlyCopyList.add(4) // ошибка компиляции
    println("Read-only copy size: ${readOnlyCopyList.size}") // 3
}
```

<!-- These functions can also be used for converting collections to other types, for example, build a set from a list or vice versa. -->
Эти функции также можно использовать для преобразования типа коллекции, например, для создания множества из списка или наоборот.

```kotlin
fun main() {
    val sourceList = mutableListOf(1, 2, 3)    
    val copySet = sourceList.toMutableSet()
    copySet.add(3)
    copySet.add(4)    
    println(copySet) // [1, 2, 3, 4]
}
```

<!-- Alternatively, you can create new references to the same collection instance. New references are created when you initialize a collection variable with an existing collection.
So, when the collection instance is altered through a reference, the changes are reflected in all its references. -->
В качестве альтернативы вы можете создать новую ссылку на тот же экземпляр коллекции. Новую ссылку можно создать, инициализировав новую переменную для коллекции и записав в нее существующую коллекцию.
Однако, изменив новую коллекцию, изменения отразятся во всех ссылках на эту коллекцию.

```kotlin

fun main() {
    val sourceList = mutableListOf(1, 2, 3)
    val referenceList = sourceList
    referenceList.add(4)
    println("Source size: ${sourceList.size}") // 4
}
```

<!-- Collection initialization can be used for restricting mutability. For example, if you create a `List` reference to a `MutableList`, the compiler will produce errors if you try to modify the collection through this reference. -->
Подобная инициализация может использоваться для того, чтобы ограничить доступ на изменение коллекции. Например, вы создаёте ссылку `List` на основе `MutableList`, если вы попытаетесь изменить коллекцию с помощью этой ссылки, то компилятор выдаст ошибку.

```kotlin
fun main() {
    val sourceList = mutableListOf(1, 2, 3)
    val referenceList: List<Int> = sourceList
    //referenceList.add(4) // ошибка компиляции
    sourceList.add(4)
    println(referenceList) // показывает текущее состояние sourceList - [1, 2, 3, 4]
}
```


<a name="invoke-functions-on-other-collections"></a>
<!-- ## Invoke functions on other collections -->
## Вызов вспомогательных функций

<!-- Collections can be created in result of various operations on other collections. For example, [filtering](collection-filtering.md)
a list creates a new list of elements that match the filter: -->
Коллекции могут создаваться в результате выполнения операций над другими коллекциями. Например, функция [filter](collection-filtering.html) создаёт новый список элементов, соответствующих заданному фильтру:

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")  
    val longerThan3 = numbers.filter { it.length > 3 }
    println(longerThan3) // [three, four]
}
```

<!-- [Mapping](collection-transformations.md#map) produces a list of a transformation results: -->
Существуют функции, которые [преобразуют](collection-transformations.html) исходные элементы и возвращают новый список с результатом. Например, функция `map`:

```kotlin
fun main() {
    val numbers = setOf(1, 2, 3)
    println(numbers.map { it * 3 }) // [3, 6, 9]
    println(numbers.mapIndexed { idx, value -> value * idx }) // [0, 2, 6]
}
```

<!-- [Association](collection-transformations.md#associate) produces maps: -->
Или функция `associate`, которая создаёт ассоциативный список:

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    println(numbers.associateWith { it.length }) // {one=3, two=3, three=5, four=4}
}
```

<!-- For more information about operations on collections in Kotlin, see [Collection operations overview](collection-operations.md). -->
Более подробная информация о таких функциях находится в разделе [Операции коллекций](collection-operations.html).
