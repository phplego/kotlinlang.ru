---
type: doc
layout: reference
category: Collections
title: "Операции записи"
url: https://kotlinlang.ru/docs/collection-write.html
---

<!-- # Collection write operations -->
# Операции записи коллекций

<!-- [Mutable collections](collections-overview.md#collection-types) support operations for changing the collection contents, for example, adding or removing elements.
On this page, we'll describe write operations available for all implementations of `MutableCollection`.
For more specific operations available for `List` and `Map`, see [List-specific Operations](list-operations.md) and [Map Specific Operations](map-operations.md) respectively. -->
[Изменяемые коллекции](collections-overview.html#collection-types) поддерживают операции, изменяющие её содержимое, например, операции по добавлению и удалению элементов. В этом разделе описаны операции записи, доступные для всех реализаций `MutableCollection`. Более конкретные операции, доступные для `List` и `Map`, описаны в разделах [List: специфичные операции](list-operations.html) и [Map: специфичные операции](map-operations.html).


<a name="adding-elements"></a>
<!-- ## Adding elements -->
## Добавление элементов

<!-- To add a single element to a list or a set, use the [`add()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/add.html) function. The specified object is appended to the end of the collection. -->
Чтобы добавить один элемент в список или множество (`Set`), используйте функцию
[`add()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/add.html). Указанный объект будет добавлен в конец коллекции.

```kotlin
fun main() {
    val numbers = mutableListOf(1, 2, 3, 4)
    numbers.add(5)
    println(numbers) // [1, 2, 3, 4, 5]
}
```

<!-- [`addAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/add-all.html) adds every element of the argument object to a list or a set. The argument can be an `Iterable`, a `Sequence`, or an `Array`.
The types of the receiver and the argument may be different, for example, you can add all items from a `Set` to a `List`. -->
Функция [`addAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/add-all.html) добавляет все элементы из переданного в качестве аргумента объекта в список или множество. Аргументом может быть `Iterable`, `Sequence`, или `Array`. Типы объекта-получателя и аргумента могут быть разными, например, вы можете добавить все элементы из `Set` в `List`.

<!-- When called on lists, `addAll()` adds new elements in the same order as they go in the argument.
You can also call `addAll()` specifying an element position as the first argument.
The first element of the argument collection will be inserted at this position.
Other elements of the argument collection will follow it, shifting the receiver elements to the end.  -->
При вызове к списку `addAll()` добавляет новые элементы в том же порядке, в котором они находятся в коллекции-аргументе. Вы также можете передать `addAll()` позицию, в которую будет вставлен первый элемент из коллекции-аргумента. За ним последуют другие элементы из коллекции-аргумента, сдвигая элементы коллекции-получателя в конец.

```kotlin
fun main() {
    val numbers = mutableListOf(1, 2, 5, 6)
    numbers.addAll(arrayOf(7, 8))
    println(numbers) // [1, 2, 5, 6, 7, 8]
    numbers.addAll(2, setOf(3, 4))
    println(numbers) // [1, 2, 3, 4, 5, 6, 7, 8]
}
```

<!-- You can also add elements using the in-place version of the [`plus` operator](collection-plus-minus.md) - [`plusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html) (`+=`)
 When applied to a mutable collection, `+=` appends the second operand (an element or another collection) to the end of the collection. -->
Вы также можете добавлять элементы, используя _in-place_ версию оператора
[`plus`](collection-plus-minus.html) -
[`plusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html) (`+=`). При применении к изменяемой коллекции `+=` добавляет второй операнд (элемент или другую коллекцию) в конец коллекции.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two")
    numbers += "three"
    println(numbers) // [one, two, three]
    numbers += listOf("four", "five")    
    println(numbers) // [one, two, three, four, five]
}
```


<a name="removing-elements"></a>
<!-- ## Removing elements -->
## Удаление элементов

<!-- To remove an element from a mutable collection, use the [`remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove.html) function.
`remove()` accepts the element value and removes one occurrence of this value.  -->
Чтобы удалить элемент из изменяемой коллекции, используйте функцию
[`remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove.html). Она принимает значение элемента в качестве аргумента и удаляет из коллекции одно вхождение этого значения.

```kotlin
fun main() {
    val numbers = mutableListOf(1, 2, 3, 4, 3)
    numbers.remove(3) // удаляет только первое значение `3`
    println(numbers) // [1, 2, 4, 3]
    numbers.remove(5) // ничего не удаляет
    println(numbers) // [1, 2, 4, 3]
}
```

<!-- For removing multiple elements at once, there are the following functions:

* [`removeAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove-all.html) removes all elements that are present in the argument collection.
   Alternatively, you can call it with a predicate as an argument; in this case the function removes all elements for which the predicate yields `true`.
* [`retainAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/retain-all.html) is the opposite of `removeAll()`: it removes all elements except the ones from the argument collection.
   When used with a predicate, it leaves only elements that match it.
* [`clear()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/clear.html) removes all elements from a list and leaves it empty. -->
Для одновременного удаления нескольких элементов существуют следующие функции:
* [`removeAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove-all.html) - удаляет все элементы, присутствующие в коллекции-аргументе. В качестве альтернативы вы можете вызвать её с предикатом; в этом случае функция удаляет все элементы, для которых предикат возвращает `true`.
* [`retainAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/retain-all.html) - противоположность `removeAll()`: удаляет все элементы кроме тех, что находятся в коллекции-аргументе. При использовании с предикатом она оставляет только те элементы, которые ему соответствуют.
* [`clear()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/clear.html) - удаляет все элементы из списка, оставляя его пустым.

```kotlin
fun main() {
    val numbers = mutableListOf(1, 2, 3, 4)
    println(numbers) // [1, 2, 3, 4]
    numbers.retainAll { it >= 3 }
    println(numbers) // [3, 4]
    numbers.clear()
    println(numbers) // []

    val numbersSet = mutableSetOf("one", "two", "three", "four")
    numbersSet.removeAll(setOf("one", "two"))
    println(numbersSet) // [three, four]
}
```

<!-- Another way to remove elements from a collection is with the [`minusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html) (`-=`) operator – the in-place version of [`minus`](collection-plus-minus.md).
The second argument can be a single instance of the element type or another collection.
With a single element on the right-hand side, `-=` removes the _first_ occurrence of it.
In turn, if it's a collection, _all_ occurrences of its elements are removed.
For example, if a list contains duplicate elements, they are removed at once.
The second operand can contain elements that are not present in the collection. Such elements don't affect the operation execution. -->
Еще один способ для удаления элементов из коллекции - оператор
[`minusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html) (`-=`) - это _in-place_ версия оператора [`minus`](collection-plus-minus.html). Второй аргумент может быть либо одним элементом, либо другой коллекцией. Если второй аргумент - это элемент, то оператор `-=` удалит _первое_ вхождение этого элемента. Если же второй аргумент - это коллекция, то будут удалены _все_ вхождения её элементов. Например, если список содержит повторяющиеся элементы, то они все будут удалены. Второй операнд может содержать элементы, которых нет в коллекции. Такие элементы не влияют на выполнение операции.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three", "three", "four")
    numbers -= "three"
    println(numbers) // [one, two, three, four]
    numbers -= listOf("four", "five")    
    //numbers -= listOf("four") // делает то же самое, что и выше
    println(numbers) // [one, two, three]
}
```

<a name="updating-elements"></a>
<!-- ## Updating elements -->
## Обновление элементов

<!-- Lists and maps also provide operations for updating elements.
They are described in [List-specific Operations](list-operations.md) and [Map Specific Operations](map-operations.md).
For sets, updating doesn't make sense since it's actually removing an element and adding another one. -->
Списки и ассоциативные списки также предоставляют операции для обновления элементов. Они описаны в разделах
[List: специфичные операции](list-operations.html) и [Map: специфичные операции](map-operations.html).
Для `Set` обновление элементов не имеет смысла, поскольку фактически он удаляет элемент и добавляет другой.
