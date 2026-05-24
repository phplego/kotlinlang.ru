---
type: doc
layout: reference
category: "Other"
title: "Равенство"
url: https://kotlinlang.ru/docs/equality.html
---

<!-- При переводе статьи оригинальная версия была от 05 May 2026 -->

<!-- # Equality -->
# Равенство

<!-- In Kotlin there are two types of equality: -->
В Kotlin есть два типа равенства:

<!-- * _Structural_ equality (`==`) - a check for the `equals()` function
* _Referential_ equality (`===`) - a check for two references pointing to the same object -->

* Равенство *структур* (`==`) - проверка функции `equals()`
* Равенство *ссылок* (`===`) - проверка того, что две ссылки указывают на один и тот же объект

<a name="structural-equality"></a>

<!-- ## Structural equality -->
## Равенство структур

<!-- Structural equality verifies if two objects have the same content or structure. Structural equality is checked by the `==`
operation and its negated counterpart `!=`.
By convention, an expression like `a == b` is translated to: -->
Равенство структур проверяет, имеют ли два объекта одинаковое содержимое или структуру. Оно проверяется оператором `==`
и его отрицанием `!=`. По соглашению выражение `a == b` транслируется в:

```kotlin
a?.equals(b) ?: (b === null)
```

<!-- If `a` is not `null`, it calls the `equals(Any?)` function. Otherwise (`a` is `null`), it checks that `b`
is referentially equal to `null`: -->
Если `a` не `null`, вызывается функция `equals(Any?)`. Иначе (`a` равно `null`) проверяется, что `b` ссылочно
равно `null`:

```kotlin
fun main() {
    var a = "hello"
    var b = "hello"
    var c = null
    var d = null
    var e = d

    println(a == b)
    // true
    println(a == c)
    // false
    println(c == e)
    // true
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- Note that there's no point in optimizing your code when comparing to `null` explicitly:
`a == null` will be automatically translated to `a === null`. -->
Заметьте, что в явном сравнении с `null` для оптимизации нет смысла: `a == null` будет автоматически транслироваться в
`a === null`.

<!-- In Kotlin, the `equals()` function is inherited by all classes from the `Any` class. By default, the `equals()` function
implements [referential equality](#referential-equality). However, classes in Kotlin can override the `equals()`
function to provide a custom equality logic and, in this way, implement structural equality. -->
В Kotlin функция `equals()` наследуется всеми классами от класса `Any`. По умолчанию функция `equals()` реализует
[равенство ссылок](#referential-equality). Однако классы в Kotlin могут переопределять функцию `equals()`, чтобы
предоставить собственную логику равенства и таким образом реализовать равенство структур.

<!-- Value classes and data classes are two specific Kotlin types that automatically override the `equals()` function.
That's why they implement structural equality by default. -->
Классы-значения (value classes) и data-классы - два специальных типа Kotlin, которые автоматически переопределяют
функцию `equals()`. Поэтому они реализуют равенство структур по умолчанию.

<!-- However, in the case of data classes, if the `equals()` function is marked as `final` in the parent class,
its behavior remains unchanged. -->
Однако для data-классов, если функция `equals()` помечена как `final` в родительском классе, ее поведение остается
неизменным.

<!-- Distinctly, non-data classes (those not declared with the `data` modifier) do not override the
`equals()` function by default. Instead, non-data classes implement referential equality behavior inherited from the `Any` class.
To implement structural equality, non-data classes require a custom equality logic to override the `equals()` function. -->
В отличие от них, обычные классы (не объявленные с модификатором `data`) по умолчанию не переопределяют
функцию `equals()`. Вместо этого такие классы реализуют поведение равенства ссылок, унаследованное от класса `Any`.
Чтобы реализовать равенство структур, таким классам нужна собственная логика равенства, переопределяющая функцию
`equals()`.

<!-- To provide a custom equals check implementation, override the
[`equals(other: Any?): Boolean`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/equals.html) function: -->
Чтобы предоставить собственную реализацию проверки равенства, переопределите функцию
[`equals(other: Any?): Boolean`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/equals.html):

```kotlin
class Point(val x: Int, val y: Int) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (other !is Point) return false

        // Сравнивает свойства для структурного равенства
        return this.x == other.x && this.y == other.y
    }
}
```

<!-- > When overriding the equals() function, you should also override the [hashCode() function](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/hash-code.html)
> to keep consistency between equality and hashing and ensure a proper behavior of these functions.
>
{style="note"} -->
> При переопределении функции `equals()` также следует переопределить функцию
> [`hashCode()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/hash-code.html), чтобы сохранить
> согласованность между равенством и хешированием и обеспечить правильное поведение этих функций.
{:.note}

<!-- Functions with the same name and other signatures (like `equals(other: Foo)`) don't affect equality checks with
the operators `==` and `!=`. -->
Функции с тем же именем и другими сигнатурами, например `equals(other: Foo)`, не влияют на проверку равенства с помощью
операторов `==` и `!=`.

<!-- Structural equality has nothing to do with comparison defined by the `Comparable<...>` interface, so only a custom
`equals(Any?)` implementation may affect the behavior of the operator. -->
Структурное равенство не имеет ничего общего со сравнением, определяемым интерфейсом `Comparable<...>`, поэтому только
пользовательская реализация `equals(Any?)` может повлиять на поведение оператора.

<a name="referential-equality"></a>

<!-- ## Referential equality -->
## Равенство ссылок

<!-- Referential equality verifies the memory addresses of two objects to determine if they are the same instance. -->
Равенство ссылок проверяет адреса памяти двух объектов, чтобы определить, являются ли они одним и тем же экземпляром.

<!-- Referential equality is checked by the `===` operation and its negated counterpart `!==`. `a === b` evaluates to
true if and only if `a` and `b` point to the same object: -->
Равенство ссылок проверяется с помощью оператора `===` и его отрицания `!==`. Выражение `a === b` является истиной тогда
и только тогда, когда `a` и `b` указывают на один и тот же объект:

```kotlin
fun main() {
    var a = "Hello"
    var b = a
    var c = "world"
    var d = "world"

    println(a === b)
    // true
    println(a === c)
    // false
    println(c === d)
    // true

}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- For values represented by primitive types at runtime
(for example, `Int`), the `===` equality check is equivalent to the `==` check. -->
Для значений, представленных примитивными типами во время выполнения (например, `Int`), проверка равенства `===`
эквивалентна проверке `==`.

<!-- > The referential equality is implemented differently in Kotlin/JS. For more information about equality, see the [Kotlin/JS](js-interop.md#equality) documentation.
>
{style="tip"} -->
> В Kotlin/JS равенство ссылок реализовано иначе. Подробнее о равенстве см. в документации
> [Kotlin/JS](https://kotlinlang.org/docs/js-interop.html#equality).
{:.note}

<a name="floating-point-numbers-equality"></a>

<!-- ## Floating-point numbers equality -->
## Равенство чисел с плавающей точкой

<!-- When the operands of an equality check are statically known to be `Float` or `Double` (nullable or not), the check follows the
[IEEE 754 Standard for Floating-Point Arithmetic](https://en.wikipedia.org/wiki/IEEE_754). -->
Когда статически известно, что операнды проверки равенства являются `Float` или `Double` (nullable или нет), проверка
выполняется в соответствии со
[стандартом IEEE 754 для арифметики с плавающей точкой](https://en.wikipedia.org/wiki/IEEE_754).

<!-- The behavior is different for operands that are not statically typed as floating-point numbers. In these cases,
structural equality is implemented. As a result, checks with operands not statically typed as floating-point numbers differ from the
IEEE standard. In this scenario:

* `NaN` is equal to itself
* `NaN` is greater than any other element (including `POSITIVE_INFINITY`)
* `-0.0` is not equal to `0.0` -->
Для операндов, которые статически не типизированы как числа с плавающей точкой, поведение отличается. В таких случаях
реализуется равенство структур. В результате проверки с операндами, которые статически не типизированы как числа с
плавающей точкой, отличаются от стандарта IEEE. В этом случае:

* `NaN` равно самому себе
* `NaN` больше любого другого элемента (включая `POSITIVE_INFINITY`)
* `-0.0` не равно `0.0`

<!-- For more information, see [Floating-point numbers comparison](numbers.md#floating-point-number-comparison). -->
Подробнее см. в разделе [Сравнение чисел с плавающей точкой](basic-types.html#floating-point-numbers-comparison).

<a name="array-equality"></a>

<!-- ## Array equality -->
## Равенство массивов

<!-- To compare whether two arrays have the same elements in the same order, use [`contentEquals()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/content-equals.html). -->
Чтобы проверить, содержат ли два массива одинаковые элементы в одном и том же порядке, используйте
[`contentEquals()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/content-equals.html).

<!-- For more information, see [Compare arrays](arrays.md#compare-arrays). -->
Подробнее см. в разделе [Сравнение массивов](https://kotlinlang.org/docs/arrays.html#compare-arrays).
