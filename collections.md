---
type: doc
layout: reference
category: Other
title: "Коллекции"
url: https://kotlinlang.ru/docs/reference/collections.html
---

<!--# Collections-->
# Коллекции

<!--Unlike many languages, Kotlin distinguishes between mutable and immutable collections (lists, sets, maps, etc). 
Precise control over exactly when collections can be edited is useful for eliminating bugs, and for designing good APIs.

It is important to understand up front the difference between a read only _view_ of a mutable collection, 
and an actually immutable collection. Both are easy to create, but the type system doesn't express the difference, 
so keeping track of that (if it's relevant) is up to you.

The Kotlin `List<out T>` type is an interface that provides read only operations like `size`, `get` and so on. Like in Java, 
it inherits from `Collection<T>` and that in turn inherits from `Iterable<T>`. Methods that change the list are added by the 
`MutableList<T>` interface. This pattern holds also for `Set<out T>/MutableSet<T>` and `Map<K, out V>/MutableMap<K, V>`.

We can see basic usage of the list and set types below: -->
В отличие от многих языков, Kotlin различает изменяемые и неизменяемые коллекции (списки, множества, ассоциативные списки и т.д.).
Точный контроль над тем, когда именно коллекции могут быть изменены, полезен для устранения багов и разработки хорошего API.

Важно понимать различие между read-only _представлением_ изменяемой коллекции, и фактически неизменяемой коллекцией. 
Их легко создать, но вот система типов не выражает различие между ними, поэтому следить за этим должны вы (если это необходимо).

Тип `List<out T>` в Kotlin — интерфейс, который предоставляет read-only операции, такие как `size`, `get`, и другие. Так же, как и в Java, 
он наследуется от `Collection<T>`, а значит и от `Iterable<T>`. Методы, которые изменяют список, добавлены в интерфейс `MutableList<T>`.
То же самое относится и к `Set<out T>/MutableSet<T>`, `Map<K, out V>/MutableMap<K, V>`.

Пример базового использования списка (list) и множества (set):

```kotlin
val numbers: MutableList<Int> = mutableListOf(1, 2, 3)
val readOnlyView: List<Int> = numbers
println(numbers)        // выведет "[1, 2, 3]"
numbers.add(4)
println(readOnlyView)   // выведет "[1, 2, 3, 4]"
readOnlyView.clear()    // -> не скомпилируется

val strings = hashSetOf("a", "b", "c", "c")
assert(strings.size == 3)
```

<!--Kotlin does not have dedicated syntax constructs for creating lists or sets. Use methods from the standard library, such as
`listOf()`, `mutableListOf()`, `setOf()`, `mutableSetOf()`.
Map creation in NOT performance-critical code can be accomplished with a simple [idiom](idioms.html#read-only-map): 
`mapOf(a to b, c to d)` -->
Kotlin не имеет специальных синтаксических конструкций для создания списков или множеств. Используйте методы из стандартной библиотеки,
такие как `listOf()`, `mutableListOf()`, `setOf()`, `mutableSetOf()`.
Создание ассоциативного списка некритичным для производительности способом может быть осуществленно с помощью простой [идиомы](idioms.html#Read-only-ассоциативный-список):
`mapOf(a to b, c to d)`

<!--Note that the `readOnlyView` variable points to the same list and changes as the underlying list changes. 
If the only references that exist to a list are of the read only variety, we can consider the collection fully immutable. 
A simple way to create such a collection is like this:-->
Заметьте, что переменная `readOnlyView` изменяется вместе со списком, на который она указывает. Если единственная ссылка, указывающая
на список является read-only, то мы можем с уверенностью сказать, что коллекция полностью неизменяемая.
Простой способ создания такой коллекции выглядит так:
```kotlin
val items = listOf(1, 2, 3)
```

<!--Currently, the `listOf` method is implemented using an array list, but in future more memory-efficient fully immutable 
collection types could be returned that exploit the fact that they know they can't change.-->
В данный момент, метод `listOf` реализован с помощью ArrayList, но не исключено, что в будущем могут быть использованы другие типы коллекций, более эффективные по памяти за счёт своей неизменности.

<!--Note that the read only types are [covariant](generics.html#variance). That means, you can take a `List<Rectangle>` and assign 
it to `List<Shape>` assuming Rectangle inherits from Shape. This wouldn't be allowed with the mutable collection types because 
it would allow for failures at runtime.-->
Заметьте, что read-only типы [ковариантны](generics.html#variance). Это значит, что вы можете взять `List<Rectangle>` (список прямоугольников) и присвоить его `List<Shape>` (списку фигур) предполагая, что Rectangle наследуется от Shape. Такое присвоение было бы запрещено с изменяемыми коллекциями, потому что в таком случае появляется риск возникновения ошибок времени исполнения.

<!--Sometimes you want to return to the caller a snapshot of a collection at a particular point in time, one that's guaranteed to not change:-->
Иногда вам необходимо вернуть состояние коллекции в определённый момент времени:

```kotlin
class Controller {
    private val _items = mutableListOf<String>()
    val items: List<String> get() = _items.toList()
}
```

<!--The `toList` extension method just duplicates the lists items, thus, the returned list is guaranteed to never change.-->
[Расширение](extensions.html) `toList` просто копирует элементы списка. Таким образом, возвращаемый список гарантированно не изменится.

<!--There are various useful extension methods on lists and sets that are worth being familiar with:-->
Существует несколько полезных расширений для списков и множеств, с которыми стоит познакомиться:

```kotlin
val items = listOf(1, 2, 3, 4)
items.first() == 1
items.last() == 4
items.filter { it % 2 == 0 }   // возвратит [2, 4]

val rwList = mutableListOf(1, 2, 3)
rwList.requireNoNulls()        // возвратит [1, 2, 3]
if (rwList.none { it > 6 }) println("Нет элементов больше 6")  // выведет "Нет элементов больше 6"
val item = rwList.firstOrNull()
```

<!--... as well as all the utilities you would expect such as sort, zip, fold, reduce and so on.-->
Также, обратите внимание на такие утилиты как `sort`, `zip`, `fold`, `reduce`.

<!--Maps follow the same pattern. They can be easily instantiated and accessed like this:-->
То же самое происходит и с ассоциативными списками. Они могут быть с лёгкостью инициализированы и использованы следующим образом:

```kotlin
val readWriteMap = hashMapOf("foo" to 1, "bar" to 2)
println(readWriteMap["foo"])  // выведет "1"
val snapshot: Map<String, Int> = HashMap(readWriteMap)
```
