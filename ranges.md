---
type: doc
layout: reference
category: Collections
title: "Диапазоны и прогрессии"
url: https://kotlinlang.ru/docs/ranges.html
---

<a name="ranges-and-progressions"></a>
<!-- # Ranges and progressions -->
# Диапазоны и прогрессии

<!-- Kotlin lets you easily create ranges of values using the [`rangeTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/range-to.html)
function from the `kotlin.ranges` package and its operator form `..`. Usually, `rangeTo()` is complemented by `in` or
`!in` functions. -->
Kotlin позволяет легко создавать диапазоны значений с помощью функции [`rangeTo()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/range-to.html), которая находится в пакете `kotlin.ranges`. У функции есть операторная форма - `..`. Обычно `rangeTo()` используется совместно с функциями `in` или `!in`.

```kotlin
if (i in 1..4) {  // эквивалентная запись 1 <= i && i <= 4
    print(i)
}
```

<!-- Integral type ranges ([`IntRange`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-int-range/index.html),
[`LongRange`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-long-range/index.html),
[`CharRange`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-char-range/index.html)) have an extra feature:
they can be iterated over. These ranges are also [progressions](https://en.wikipedia.org/wiki/Arithmetic_progression) of
the corresponding integral types. -->
У диапазонов целочисленных типов
([`IntRange`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-int-range/index.html),
[`LongRange`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-long-range/index.html),
[`CharRange`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-char-range/index.html))
есть дополнительная функция: они поддерживают итерацию. Эти диапазоны также являются [прогрессиями](https://en.wikipedia.org/wiki/Arithmetic_progression).

<!-- Such ranges are generally used for iteration in `for` loops. -->
Подобные диапазоны, как правило, используются в цикле `for`.

```kotlin
fun main() {
    for (i in 1..4) print(i) // 1234
}
```

<!-- To iterate numbers in reverse order, use the [`downTo`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/down-to.html)
function instead of `..`. -->
Для перебора чисел в обратном порядке используйте функцию [`downTo`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/down-to.html) вместо `..`.

```kotlin
fun main() {
    for (i in 4 downTo 1) print(i) // 4321
}
```

<!-- It is also possible to iterate over numbers with an arbitrary step (not necessarily 1). This is done via the
[`step`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/step.html) function. -->
Можно перебирать числа с произвольным шагом. Осуществляется это с помощью функции [`step`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/step.html).

```kotlin
fun main() {
    for (i in 1..8 step 2) print(i) // 1357
    println()
    for (i in 8 downTo 1 step 2) print(i) // 8642
}
```

<!-- To iterate a number range which does not include its end element, use the
[`until`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/until.html) function: -->
Если требуется перебрать диапазон чисел, исключая его последний элемент, то используйте функцию [`until`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/until.html).

```kotlin
fun main() {
    for (i in 1 until 10) { // i in [1, 10), 10 будет исключён
        print(i) // 123456789
    }
}
```


<a name="range"></a>
<!-- ## Range -->
## Диапазоны

<!-- A range defines a closed interval in the mathematical sense: it is defined by its two endpoint values which are both
included in the range. Ranges are defined for comparable types: having an order, you can define whether an arbitrary
instance is in the range between two given instances. -->
В математическом смысле диапазон - это закрытый интервал: он определяется двумя значениями и они оба являются частью диапазона. Диапазоны применимы к сопоставимым (_comparable_) типам: имея порядок, вы можете определить, находится ли произвольный экземпляр в диапазоне между двумя заданными экземплярами.

<!-- The main operation on ranges is `contains`, which is usually used in the form of `in` and `!in` operators. -->
Основная операция с диапазонами - это `contains`, которая обычно используется в форме операторов `in` и `!in`.

<!-- To create a range for your class, call the `rangeTo()` function on the range start value and provide the end value as an
argument. `rangeTo()` is often called in its operator form `..`. -->
Чтобы создать диапазон на основе ваших классов, вызовите функцию `rangeTo()` для начального значения диапазона и укажите конечное значение в качестве аргумента. Чаще всего используется операторная форма функции `rangeTo()` - `..`.

```kotlin
class Version(val major: Int, val minor: Int): Comparable<Version> {
    override fun compareTo(other: Version): Int {
        if (this.major != other.major) {
            return this.major - other.major
        }
        return this.minor - other.minor
    }
}

fun main() {
    val versionRange = Version(1, 11)..Version(1, 30)
    println(Version(0, 9) in versionRange) // false
    println(Version(1, 20) in versionRange) // true
}
```


<a name="progression"></a>
<!-- ## Progression -->
## Прогрессии

<!-- As shown in the examples above, the ranges of integral types, such as `Int`, `Long`, and `Char`, can be treated as
[arithmetic progressions](https://en.wikipedia.org/wiki/Arithmetic_progression) of them.
In Kotlin, these progressions are defined by special types: [`IntProgression`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-int-progression/index.html),
[`LongProgression`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-long-progression/index.html),
and [`CharProgression`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-char-progression/index.html). -->
Как показано в приведённых выше примерах, диапазоны целочисленных типов, таких как `Int`, `Long` и `Char`, можно рассматривать как [арифметические прогрессии](https://en.wikipedia.org/wiki/Arithmetic_progression).
В Kotlin есть специальные типы для определения таких прогрессий:
[`IntProgression`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-int-progression/index.html),
[`LongProgression`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-long-progression/index.html) и
[`CharProgression`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/-char-progression/index.html).

<!-- Progressions have three essential properties: the `first` element, the `last` element, and a non-zero `step`.
The first element is `first`, subsequent elements are the previous element plus a `step`.
Iteration over a progression with a positive step is equivalent to an indexed `for` loop in Java/JavaScript. -->
У прогрессий есть три основных свойства: `first`, `last` и `step`, при этом `step` не может быть нулём.
`first` - это первый элемент. Последующие элементы - это предыдущий элемент плюс `step`.
Итерация по прогрессии с положительным шагом (`step`) эквивалентна индексируемому циклу `for` в Java / JavaScript.

```java
for (int i = first; i <= last; i += step) {
  // ...
}
```

<!-- When you create a progression implicitly by iterating a range, this progression's `first` and `last` elements are the
range's endpoints, and the `step` is 1. -->
При неявном создании прогрессии путём итерации диапазона, элементы `first` и `last` этой прогрессии являются конечными точками диапазона, а `step` равен 1.

```kotlin
fun main() {
    for (i in 1..10) print(i) // 12345678910
}
```

<!-- To define a custom progression step, use the `step` function on a range. -->
Чтобы прогрессии задать собственный шаг, используйте функцию `step`.

```kotlin
fun main() {
    for (i in 1..8 step 2) print(i) // 1357
}
```

<!-- The `last` element of the progression is calculated this way:
* For a positive step: the maximum value not greater than the end value such that `(last - first) % step == 0`.
* For a negative step: the minimum value not less than the end value such that `(last - first) % step == 0`. -->
Последний элемент прогрессии (`last`) рассчитывается следующим образом:
* Для положительного шага: максимальное значение, но не больше конечного значения - `(last - first) % step == 0`.
* Для отрицательного шага: минимальное значение, но не меньше конечного значения - `(last - first) % step == 0`.

<!-- Thus, the `last` element is not always the same as the specified end value. -->
Таким образом, элемент `last` не всегда совпадает с конечным значением диапазона.

```kotlin
fun main() {
    for (i in 1..9 step 3) print(i) // 147, last = 7
}
```

<!-- To create a progression for iterating in reverse order, use `downTo` instead of `..` when defining the range for it. -->
Чтобы создать прогрессию для итерации в обратном направлении, при определении диапазона используйте `downTo` вместо `..`.

```kotlin
fun main() {
    for (i in 4 downTo 1) print(i) // 4321
}
```

<!-- Progressions implement `Iterable<N>`, where `N` is `Int`, `Long`, or `Char` respectively, so you can use them in various
[collection functions](collection-operations.md) like `map`, `filter`, and other. -->
Прогрессии реализуют интерфейс `Iterable<N>`, где `N` - это `Int`, `Long` или `Char`, поэтому вы можете использовать их в различных [функциях коллекций](collection-operations.html), таких как `map`, `filter` и т. д.

```kotlin
fun main() {
    println((1..10).filter { it % 2 == 0 }) // [2, 4, 6, 8, 10]
}
```
