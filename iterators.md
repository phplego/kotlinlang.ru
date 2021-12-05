---
type: doc
layout: reference
category: Collections
title: "Итераторы"
url: https://kotlinlang.ru/docs/iterators.html
---

<a name="iterators"></a>
<!-- # Iterators -->
# Итераторы

<!-- For traversing collection elements, the Kotlin standard library supports the commonly used mechanism of _iterators_ –
objects that provide access to the elements sequentially without exposing the underlying structure of the collection.
Iterators are useful when you need to process all the elements of a collection one-by-one, for example, print values or
make similar updates to them. -->
Для обхода элементов коллекции стандартная библиотека Kotlin поддерживает механизм _итераторов_ - объектов, которые предоставляют доступ к элементам последовательно, не раскрывая базовую структуру коллекции. Итераторы полезны, когда вам нужно обработать все элементы коллекции один за другим, например, вывести в лог их значения или обновить.

<!-- Iterators can be obtained for inheritors of the [`Iterable<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterable/index.html)
interface, including `Set` and `List`, by calling the [`iterator()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterable/iterator.html)
function. -->
Итераторы доступны всем наследникам интерфейса [`Iterable<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterable/index.html),  включая `Set` и `List`, путём вызова функции [`iterator()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterable/iterator.html).

<!-- Once you obtain an iterator, it points to the first element of a collection; calling the [`next()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterator/next.html)
function returns this element and moves the iterator position to the following element if it exists. -->
При получении итератора он сначала указывает на первый элемент коллекции; вызов функции [`next()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterator/next.html) возвращает этот элемент и перемещает позицию итератора на следующий элемент, если такой существует.

<!-- Once the iterator passes through the last element, it can no longer be used for retrieving elements; neither can it be
reset to any previous position. To iterate through the collection again, create a new iterator. -->
Как только итератор проходит через последний элемент, его больше нельзя использовать для извлечения элементов; его также нельзя вернуть в предыдущее положение. Чтобы снова перебрать коллекцию, нужно создать новый итератор.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    val numbersIterator = numbers.iterator()
    while (numbersIterator.hasNext()) {
        println(numbersIterator.next())
    }
}

// В логе будет:
// one
// two
// three
// four
```

<!-- Another way to go through an `Iterable` collection is the well-known `for` loop. When using `for` on a collection, you
obtain the iterator implicitly. So, the following code is equivalent to the example above: -->
Другой способ перебрать `Iterable` коллекцию - это всем известный цикл `for`. При использовании `for` вы неявно получаете итератор. Поэтому, приведённый ниже код эквивалентен предыдущему примеру:

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    for (item in numbers) {
        println(item)
    }
}

// В логе будет:
// one
// two
// three
// four
```

<!-- Finally, there is a useful `forEach()` function that lets you automatically iterate a collection and execute the given
code for each element. So, the same example would look like this: -->
Также, есть полезная функция `forEach()`, которая позволяет автоматически перебирать коллекцию и выполнять заданный код для каждого элемента. Перепишем пример выше:

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    numbers.forEach {
        println(it)
    }
}

// В логе будет:
// one
// two
// three
// four
```


<a name="list-iterators"></a>
<!-- ## List iterators -->
## Итераторы списков

<!-- For lists, there is a special iterator implementation: [`ListIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/index.html).
It supports iterating lists in both directions: forwards and backwards. -->
Для списков существует специальная реализация итератора: [`ListIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/index.html).
Он поддерживает итерацию списков в обоих направлениях: вперёд и назад.

<!-- Backward iteration is implemented by the functions [`hasPrevious()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/has-previous.html)
and [`previous()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/previous.html).
Additionally, the `ListIterator` provides information about the element indices with the functions [`nextIndex()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/next-index.html)
and [`previousIndex()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/previous-index.html). -->
Обратная итерация реализуется функциями
[`hasPrevious()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/has-previous.html) и
[`previous()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/previous.html).
Кроме того, `ListIterator` предоставляет информацию об индексах элементов с помощью функций
[`nextIndex()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/next-index.html) и
[`previousIndex()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/previous-index.html).

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    val listIterator = numbers.listIterator()
    while (listIterator.hasNext()) listIterator.next()
    println("Iterating backwards:")
    while (listIterator.hasPrevious()) {
        print("Index: ${listIterator.previousIndex()}")
        println(", value: ${listIterator.previous()}")
    }
}

// В логе будет:
// Iterating backwards:
// Index: 3, value: four
// Index: 2, value: three
// Index: 1, value: two
// Index: 0, value: one
```

<!-- Having the ability to iterate in both directions, means the `ListIterator` can still be used after it reaches the last element. -->
Наличие возможности итерации в обоих направлениях означает, что `ListIterator` может быть использован даже после того, как он достигнет последнего элемента.


<a name="mutable-iterators"></a>
<!-- ## Mutable iterators -->
## Итераторы изменяемых коллекций

<!-- For iterating mutable collections, there is [`MutableIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-iterator/index.html)
that extends `Iterator` with the element removal function [`remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-iterator/remove.html).
So, you can remove elements from a collection while iterating it.  -->
Для итерации изменяемых коллекций существует [`MutableIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-iterator/index.html), который расширяет `Iterator` с помощью функции удаления элементов [`remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-iterator/remove.html). Поэтому, вы можете удалять элементы из коллекции во время итерации.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three", "four")
    val mutableIterator = numbers.iterator()

    mutableIterator.next()
    mutableIterator.remove()    
    println("After removal: $numbers") // [two, three, four]
}
```

<!-- In addition to removing elements, the [`MutableListIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list-iterator/index.html)
can also insert and replace elements while iterating the list. -->
Помимо удаления элементов, [`MutableListIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list-iterator/index.html) позволяет добавлять и заменять элементы во время итерации.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "four", "four")
    val mutableListIterator = numbers.listIterator()

    mutableListIterator.next()
    mutableListIterator.add("two")
    mutableListIterator.next()
    mutableListIterator.set("three")   
    println(numbers) // [one, two, three, four]
}
```
