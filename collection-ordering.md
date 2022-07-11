---
type: doc
layout: reference
category: Collections
title: "Сортировка"
url: https://kotlinlang.ru/docs/collection-ordering.html
---

<!-- # Ordering -->
# Сортировка коллекций

<!-- The order of elements is an important aspect of certain collection types.
For example, two lists of the same elements are not equal if their elements are ordered differently.  -->
Для некоторых типов коллекций является важным аспектом то, как упорядочены их элементы. Например, два списка с одинаковыми элементами не равны, если их элементы упорядочены по-разному.

<!-- In Kotlin, the orders of objects can be defined in several ways. -->
В Kotlin порядок объектов можно задать несколькими способами.

<!-- First, there is _natural_ order. It is defined for inheritors of the [`Comparable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/index.html)
interface. Natural order is used for sorting them when no other order is specified. -->
Во-первых, существует _естественный_ порядок (ориг. _natural_ order). Он характерен для наследников интерфейса
[`Comparable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/index.html). Если не был указан другой порядок сортировки, то они будут отсортированы в естественном порядке.

<!-- Most built-in types are comparable:

* Numeric types use the traditional numerical order: `1` is greater than `0`; `-3.4f` is greater than `-5f`, and so on.
* `Char` and `String` use the [lexicographical order](https://en.wikipedia.org/wiki/Lexicographical_order): `b` is greater
   than `a`; `world` is greater than `hello`. -->
Большинство встроенных типов являются `Comparable`:
* Числовые типы используют традиционный числовой порядок: `1` больше, чем `0`; `-3.4f` больше, чем `-5f` и т.д.
* `Char` и `String` используют [лексикографический порядок](https://en.wikipedia.org/wiki/Lexicographical_order): `b` больше, чем `a`; `world` больше, чем `hello`.

<!-- To define a natural order for a user-defined type, make the type an inheritor of `Comparable`.
This requires implementing the `compareTo()` function. `compareTo()` must take another object of the same type as an argument
and return an integer value showing which object is greater:

* Positive values show that the receiver object is greater.
* Negative values show that it's less than the argument.
* Zero shows that the objects are equal. -->
Таким образом, чтобы ваш собственный класс при сортировке использовал естественный порядок, сделайте его наследником `Comparable`. Дополнительно потребуется реализовать функцию `compareTo()`. В качестве аргумента она должна принимать другой объект такого же типа, а возвращать целое число, указывающее, какой из объектов больше:

* Положительное значение показывает, что объект-приёмник больше аргумента.
* Отрицательное значение показывает, что объект-приёмник меньше аргумента.
* Ноль показывает, что объекты равны.

<!-- Below is a class for ordering versions that consist of the major and the minor part. -->
В примере ниже показан класс для сортировки версий, состоящий из переменных `major` и `minor`.

```kotlin
class Version(val major: Int, val minor: Int): Comparable<Version> {
    override fun compareTo(other: Version): Int {
        if (this.major != other.major) {
            return this.major - other.major
        } else if (this.minor != other.minor) {
            return this.minor - other.minor
        } else return 0
    }
}

fun main() {    
    println(Version(1, 2) > Version(1, 3)) // false
    println(Version(2, 0) > Version(1, 5)) // true
}
```

<!-- _Custom_ orders let you sort instances of any type in a way you like.
Particularly, you can define an order for non-comparable objects or define an order other than natural for a comparable type.
To define a custom order for a type, create a [`Comparator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparator/index.html) for it.
`Comparator` contains the `compare()` function: it takes two instances of a class and returns the integer result of the comparison between them.
The result is interpreted in the same way as the result of a `compareTo()` as is described above.  -->
_Пользовательский_ порядок (ориг. _custom_ order) позволяет вам сортировать экземпляры любого типа так, как вам нравится. В частности, вы можете определить порядок для сортировки объектов, не являющихся наследниками `Comparable`, а также задать `Comparable` типам порядок, отличный от естественного. Осуществляется это при помощи
[`Comparator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparator/index.html). У `Comparator` есть функция `compare()`, которая принимает два объекта, а в качестве результата сравнения возвращает целое число. Результат интерпретируется так же, как описано выше для функции `compareTo()`.

```kotlin
fun main() {
    val lengthComparator = Comparator { str1: String, str2: String -> str1.length - str2.length }
    println(listOf("aaa", "bb", "c").sortedWith(lengthComparator)) // [c, bb, aaa]
}
```

<!-- Having the `lengthComparator`, you are able to arrange strings by their length instead of the default lexicographical order. -->
С помощью `lengthComparator` вы можете изменить порядок сортировки строк: вместо сортировки в лексикографическом порядке они будут отсортированы по длине.

<!-- A shorter way to define a `Comparator` is the [`compareBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.comparisons/compare-by.html)
function from the standard library. `compareBy()` takes a lambda function that produces a `Comparable` value from an instance
and defines the custom order as the natural order of the produced values. -->
Существует более упрощённый вариант использования `Comparator` - функция
[`compareBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.comparisons/compare-by.html) из стандартной библиотеки. Она принимает лямбда-функцию, которая из объекта создаёт `Comparable` значение и задаёт пользовательский порядок, который по факту представляет собой естественный порядок созданных `Comparable` значений.


<!-- With `compareBy()`, the length comparator from the example above looks like this: -->
Используя `compareBy()` можно модифицировать `lengthComparator` из примера выше следующим образом:

```kotlin
fun main() {  
    println(listOf("aaa", "bb", "c").sortedWith(compareBy { it.length })) // [c, bb, aaa]
}
```

<!-- The Kotlin collections package provides functions for sorting collections in natural, custom, and even random orders.
On this page, we'll describe sorting functions that apply to [read-only](collections-overview.md#collection-types) collections.
These functions return their result as a new collection containing the elements of the original collection in the requested order.
To learn about functions for sorting [mutable](collections-overview.md#collection-types) collections in place, see the [List-specific operations](list-operations.md#sort). -->
Kotlin предоставляет функции, которые позволяют сортировать коллекции в естественном, пользовательском и даже в случайном порядке. В текущем разделе рассмотрены функции сортировки, которые работают только с [неизменяемыми](collections-overview.html#collection-types) коллекциями. Такие функции возвращают результат сортировки в виде новой коллекции, содержащей элементы исходной коллекции, но упорядоченные в соответствии с заданным условием. Информация о функциях для сортировки [изменяемых](collections-overview.html#collection-types) коллекций находится в разделе [List: специфичные операции](list-operations.html#sort).


<a name="natural-order"></a>
<!-- ## Natural order -->
## Естественный порядок

<!-- The basic functions [`sorted()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted.html) and [`sortedDescending()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-descending.html)
return elements of a collection sorted into ascending and descending sequence according to their natural order.
These functions apply to collections of `Comparable` elements. -->
Основные функции [`sorted()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted.html) и
[`sortedDescending()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-descending.html) возвращают элементы коллекции, отсортированные по возрастанию или убыванию в соответствии с их естественным порядком.
Эти функции могут быть применены к коллекциям, содержащим элементы `Comparable` типа.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")

    println("Sorted ascending: ${numbers.sorted()}") // Sorted ascending: [four, one, three, two]
    println("Sorted descending: ${numbers.sortedDescending()}") // Sorted descending: [two, three, one, four]
}
```


<a name="custom-orders"></a>
<!-- ## Custom orders -->
## Пользовательский порядок

<!-- For sorting in custom orders or sorting non-comparable objects, there are the functions [`sortedBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-by.html) and [`sortedByDescending()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-by-descending.html).
They take a selector function that maps collection elements to `Comparable` values and sort the collection in natural order of that values. -->
Для сортировки объектов в пользовательском порядке или для сортировки объектов, не являющихся наследниками `Comparable`, существуют функции [`sortedBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-by.html) и
[`sortedByDescending()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-by-descending.html). Они используют функцию-селектор для преобразования элементов коллекции в `Comparable` значения и сортируют коллекцию в естественном порядке этих значений.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")

    val sortedNumbers = numbers.sortedBy { it.length }
    println("Sorted by length ascending: $sortedNumbers") // Sorted by length ascending: [one, two, four, three]
    val sortedByLast = numbers.sortedByDescending { it.last() }
    println("Sorted by the last letter descending: $sortedByLast") // Sorted by the last letter descending: [four, two, one, three]
}
```

<!-- To define a custom order for the collection sorting, you can provide your own `Comparator`.
To do this, call the [`sortedWith()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-with.html) function passing in your `Comparator`.
With this function, sorting strings by their length looks like this: -->
Чтобы задать пользовательский порядок для сортировки коллекции, вы можете предоставить свой собственный `Comparator`. Для этого вызовите функцию [`sortedWith()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-with.html) и передайте ей свой `Comparator`. С помощью этой функции сортировка строк по их длине будет выглядеть следующим образом:

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    println("Sorted by length ascending: ${numbers.sortedWith(compareBy { it.length })}") // Sorted by length ascending: [one, two, four, three]
}
```


<a name="reverse-order"></a>
<!-- ## Reverse order -->
## Обратный порядок

<!-- You can retrieve the collection in the reversed order using the [`reversed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reversed.html) function. -->
С помощью функции [`reversed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reversed.html) вы можете получить коллекцию, отсортированную в обратном порядке.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    println(numbers.reversed()) // [four, three, two, one]
}
```

<!-- `reversed()` returns a new collection with the copies of the elements.
So, if you change the original collection later, this won't affect the previously obtained results of `reversed()`. -->
`reversed()` возвращает новую коллекцию с копиями элементов из исходной коллекции. Поэтому, если вы измените исходную коллекцию, это не повлияет на ранее полученный от `reversed()` результат.

<!-- Another reversing function - [`asReversed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/as-reversed.html)
- returns a reversed view of the same collection instance, so it may be more lightweight and preferable than `reversed()`
if the original list is not going to change.  -->
Другая функция - [`asReversed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/as-reversed.html) - возвращает перевёрнутое представление исходной коллекции. Эта функция может быть более легковесной и предпочтительной, чем `reversed()`, при условии, что исходная коллекция не будет изменяться.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    val reversedNumbers = numbers.asReversed()
    println(reversedNumbers) // [four, three, two, one]
}
```

<!-- If the original list is mutable, all its changes reflect in its reversed views and vice versa. -->
Если же в исходную коллекцию будут внесены изменения, все эти изменения отразятся в её перевёрнутых представлениях и наоборот.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three", "four")
    val reversedNumbers = numbers.asReversed()
    println(reversedNumbers) // [four, three, two, one]
    numbers.add("five")
    println(reversedNumbers) // [five, four, three, two, one]
}
```

<!-- However, if the mutability of the list is unknown or the source is not a list at all, `reversed()` is more preferable
since its result is a copy that won't change in the future. -->
Однако, если источник не является списком, либо неизвестно является ли список изменяемым, тогда функция `reversed()` более предпочтительна для использования, поскольку она возвращает копию, которая в будущем не изменится.


<a name="random-order"></a>
<!-- ## Random order -->
## Случайный порядок

<!-- Finally, there is a function that returns a new `List` containing the collection elements in a random order - [`shuffled()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/shuffled.html).
You can call it without arguments or with a [`Random`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.random/-random/index.html) object. -->
Наконец, существует функция [`shuffled()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/shuffled.html), которая возвращает новый `List`, содержащий элементы исходной коллекции в случайном порядке. Её можно вызывать либо без аргументов, либо с объектом [`Random`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.random/-random/index.html).

```kotlin
fun main() {
     val numbers = listOf("one", "two", "three", "four")
     println(numbers.shuffled()) // [three, four, one, two]
}
```
