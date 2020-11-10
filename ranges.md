---
type: doc
layout: reference
category: "Syntax"
title: "Интервалы"
url: https://kotlinlang.ru/docs/reference/ranges.html
---

<!-- # Ranges -->
# Интервалы

<!-- Range expressions are formed with `rangeTo` functions that have the operator form `..` which is complemented by *in*{: .keyword } and *!in*{: .keyword }. -->
<!-- Range is defined for any comparable type, but for integral primitive types it has an optimized implementation. Here are some examples of using ranges -->
Интервалы оформлены с помощью функций `rangeTo` и имеют оператор в виде `..`, который дополняется  *in* и *!in*.
Они применимы ко всем сравниваемым (_comparable_) типам, но для целочисленных примитивов есть оптимизированная реализация. Вот несколько примеров применения интервалов.

```kotlin
if (i in 1..10) { // equivalent of 1 <= i && i <= 10
    println(i)
}
```

<!-- Integral type ranges (`IntRange`, `LongRange`, `CharRange`) have an extra feature: they can be iterated over. -->
<!-- The compiler takes care of converting this analogously to Java's indexed *for*{: .keyword }-loop, without extra overhead. -->
Интервалы целочисленного типа (`IntRange`, `LongRange`, `CharRange`) имеют определённое преимущество: они могут иметь дополнительную итерацию.
Компилятор конвертирует такие интервалы в аналогичные циклы *for* из языка <b>Java</b>.

```kotlin
for (i in 1..4) print(i) // prints "1234"

for (i in 4..1) print(i) // prints nothing
```

<!-- What if you want to iterate over numbers in reverse order? It's simple. You can use the `downTo()` function defined in the standard library -->
А что, если вы хотите произвести итерацию в обратном порядке? Это просто. Можете использовать функцию `downTo()`, определённую в стандартной библиотеке:

```kotlin
for (i in 4 downTo 1) print(i) // prints "4321"
```

<!-- Is it possible to iterate over numbers with arbitrary step, not equal to 1? Sure, the `step()` function will help you -->
А есть ли возможность производить итерацию с шагом, отличным от единицы? Разумеется. В этом вам поможет функция `step()`:

```kotlin
for (i in 1..4 step 2) print(i) // prints "13"

for (i in 4 downTo 1 step 2) print(i) // prints "42"
```

<!-- To create a range which does not include its end element, you can use the `until` function: -->
Для создания интервала, который не включает последний элемент перебора, используйте `until`:

```kotlin
for (i in 1 until 10) { // i in [1, 10), 10 is excluded
     println(i)
}
```

<!-- ## How it works -->
## Как это работает

<!-- Ranges implement a common interface in the library: `ClosedRange<T>`. -->
Интервалы реализуют интерфейс `ClosedRange<T>`.

<!-- `ClosedRange<T>` denotes a closed interval in the mathematical sense, defined for comparable types. -->
<!-- It has two endpoints: `start` and `endInclusive`, which are included in the range. -->
<!-- The main operation is `contains`, usually used in the form of *in*{: .keyword }/*!in*{: .keyword } operators. -->
Говоря математическим языком, интерфейс `ClosedRange<T>` обозначет ограниченный отрезок и предназначен для типов, подлежащих сравнению.
У него есть две контрольные точки: `start` и `endInclusive`. Главной операцией является `contain`. Чаще всего она используется вместе с операторами **in**/**!in**.

<!-- Integral type progressions (`IntProgression`, `LongProgression`, `CharProgression`) denote an arithmetic progression. -->
<!-- Progressions are defined by the `first` element, the `last` element and a non-zero `increment`. -->
<!-- The first element is `first`, subsequent elements are the previous element plus `increment`. The `last` element is always hit by iteration unless the progression is empty. -->
Целочисленные последовательности (`IntProgression`, `LongProgression`, `CharProgression`) являются арифметическими.
Последовательности определены элементами `first`, `last` и ненулевым значением `increment`.
Элемент `first` является первым, последующими являются элементы, полученные при инкрементации предыдущего элемента с помощью `increment`. Если последовательность не
 является пустой, то элемент `last` всегда достигается в результате инкрементации.

<!-- A progression is a subtype of `Iterable<N>`, where `N` is `Int`, `Long` or `Char` respectively, so it can be used in *for*{: .keyword }-loops and functions like `map`, `filter`, etc. -->
<!-- Iteration over `Progression` is equivalent to an indexed *for*{: .keyword }-loop in Java/JavaScript: -->
Последовательность является подтипом `Iterable<N>`, где `N` - это `Int`, `Long` или `Char`. Таким образом, её можно использовать в циклах **for** и функциях типа `map`, `filter` и т.п.
Итерация `Progression` идентична индексируемому циклу **for** в <b>Java</b>/<b>JavaScript</b>

``` java
for (int i = first; i != last; i += increment) {
  // ...
}
```

<!-- For integral types, the `..` operator creates an object which implements both `ClosedRange<T>` and `*Progression`. -->
<!-- For example, `IntRange` implements `ClosedRange<Int>` and extends `IntProgression`, thus all operations defined for `IntProgression` are available for `IntRange` as well. -->
<!-- The result of the `downTo()` and `step()` functions is always a `*Progression`. -->
Для целочисленных типов оператор `..` создаёт объект, который реализует в себе  `ClosedRange<T>` и `*Progression*`.
К примеру, `IntRange` наследуется от класса `IntProgression` и реализует интерфейс `ClosedRange<Int>`. Поэтому все операторы, обозначенные для `IntProgression`, также доступны и для
`IntRange`. Результатом функций `downTo()` и `step()` всегда будет `*Progression*`(перев.: _последовательность_).


<!-- Progressions are constructed with the `fromClosedRange` function defined in their companion objects: -->
Последовательности спроектированы с использованием функции `fromClosedRange` в их вспомогательном объекте (_companion object_):


```kotlin
    IntProgression.fromClosedRange(start, end, increment)
```

<!-- The `last` element of the progression is calculated to find maximum value not greater than the `end` value for positive `increment` or minimum value not less than the `end` value for negative `increment` such that `(last - first) % increment == 0`. -->
Для нахождения максимального значения в прогрессии вычисляется элемент `last`. Для последовательности с положительным инкрементом этот элемент вычисляется так, чтобы он был не больше
элемента `end`. Для тех последовательностей, где инкремент отрицательный - не меньше.


<!-- ## Utility functions -->
## Вспомогательные функции (ориг.: _Utility functions_)

### `rangeTo()`

<!-- The `rangeTo()` operators on integral types simply call the constructors of `*Range` classes, e.g.: -->
Операторы `rangeTo()` для целочисленных типов просто вызывают конструктор класса `*Range*`:

```kotlin
class Int {
    //...
    operator fun rangeTo(other: Long): LongRange = LongRange(this, other)
    //...
    operator fun rangeTo(other: Int): IntRange = IntRange(this, other)
    //...
}
```

<!-- Floating point numbers (`Double`, `Float`) do not define their `rangeTo` operator, and the one provided by the standard library for generic `Comparable` types is used instead: -->
Числа с плавающей точкой (`Double`, `Float`) не имеют своего оператора `rangeTo`. Такой оператор обозначен для них в дженериках типа `Comparable` стандартной библиотеки:

```kotlin
    public operator fun <T: Comparable<T>> T.rangeTo(that: T): ClosedRange<T>
```

<!-- The range returned by this function cannot be used for iteration. -->
Интервал, полученный с помощью такой функции, не может быть использован для итерации.

### `downTo()`

<!-- The `downTo()` extension function is defined for any pair of integral types, here are two examples: -->
Экстеншн-функция `downTo()` задана для любой пары целочисленных типов, вот два примера:

```kotlin
fun Long.downTo(other: Int): LongProgression {
    return LongProgression.fromClosedRange(this, other, -1L)
}

fun Byte.downTo(other: Int): IntProgression {
    return IntProgression.fromClosedRange(this, other, -1)
}
```

### `reversed()`

<!-- The `reversed()` extension functions are defined for each `*Progression` classes, and all of them return reversed progressions. -->
Функция `reversed()` расширяет класс `*Progression*` таким образом, что все экземпляры этого класса возвращают обратные последовательности при её вызове.

```kotlin
fun IntProgression.reversed(): IntProgression {
    return IntProgression.fromClosedRange(last, first, -increment)
}
```

### `step()`

<!-- `step()` extension functions are defined for `*Progression` classes, -->
<!-- all of them return progressions with modified `step` values (function parameter). -->
<!-- The step value is required to be always positive, therefore this function never changes the direction of iteration. -->
Функция-расширение `step()` также определена для классов `*Progression*`.
Она возвращает последовательность с изменённым значением шага `step` (параметр функции).
Значение шага всегда должно быть положительным числом для того, чтобы функция никогда не меняла направления своей итерации.

```kotlin
fun IntProgression.step(step: Int): IntProgression {
    if (step <= 0) throw IllegalArgumentException("Step must be positive, was: $step") //шаг должен быть положительным
    return IntProgression.fromClosedRange(first, last, if (increment > 0) step else -step)
}

fun CharProgression.step(step: Int): CharProgression {
    if (step <= 0) throw IllegalArgumentException("Step must be positive, was: $step")
    return CharProgression.fromClosedRange(first, last, step)
}
```

<!-- Note that the `last` value of the returned progression may become different from the `last` value of the original progression in order to preserve the invariant `(last - first) % increment == 0`. Here is an example: -->
Обратите внимание, что значение элемента `last` в возвращённой последовательности может отличаться от значения `last` первоначальной последовательности с тем,
чтобы предотвратить инвариант `(last - first) % increment == 0`. Вот пример:

```kotlin
    (1..12 step 2).last == 11  // последовательность чисел со значениями [1, 3, 5, 7, 9, 11]
    (1..12 step 3).last == 10  // последовательность чисел со значениями [1, 4, 7, 10]
    (1..12 step 4).last == 9   // последовательность чисел со значениями [1, 5, 9]
```
