---
type: doc
layout: reference
category: "Syntax"
title: "Enum классы"
url: https://kotlinlang.ru/docs/enum-classes.html
---

<!-- При переводе статьи оригинальная версия была от 23 June 2025 -->

<!-- # Enum classes -->
# Enum-классы

<!-- The most basic use case for enum classes is the implementation of type-safe enums: -->
Наиболее базовый пример использования enum (перечисления) — это реализация типобезопасных перечислений.

```kotlin
enum class Direction {
    NORTH, SOUTH, WEST, EAST
}
```

<!-- Each enum constant is an object. Enum constants are separated by commas. -->
Каждая enum-константа является объектом. При объявлении константы разделяются запятыми.

<!-- Since each enum is an instance of the enum class, it can be initialized as: -->
Так как каждая enum-константа является экземпляром enum-класса, она может быть инициализирована:

```kotlin
enum class Color(val rgb: Int) {
    RED(0xFF0000),
    GREEN(0x00FF00),
    BLUE(0x0000FF)
}
```

<a name="anonymous-classes"></a>
<!-- ## Anonymous classes -->
## Анонимные классы

<!-- Enum constants can declare their own anonymous classes with their corresponding methods, as well as with overriding base
methods. -->
Enum-константы могут объявлять собственные анонимные классы с соответствующими методами, а также переопределять методы
базового класса.

```kotlin
enum class ProtocolState {
    WAITING {
        override fun signal() = TALKING
    },

    TALKING {
        override fun signal() = WAITING
    };

    abstract fun signal(): ProtocolState
}
```

<!-- If the enum class defines any members, separate the constant definitions from the member definitions with a semicolon. -->
Если enum-класс определяет какие-либо члены, отделяйте объявления констант от объявлений членов точкой с запятой.

<a name="implementing-interfaces-in-enum-classes"></a>
<!-- ## Implementing interfaces in enum classes -->
## Реализация интерфейсов в классах enum

<!-- An enum class can implement an interface (but it cannot derive from a class), providing either a common implementation of
interface members for all the entries, or separate implementations for each entry within its anonymous class.
This is done by adding the interfaces you want to implement to the enum class declaration as follows: -->
Enum-класс может реализовывать интерфейс (но не наследоваться от класса), предоставляя либо общую реализацию членов
интерфейса для всех записей enum, либо отдельные реализации для каждой записи в её анонимном классе. Для этого добавьте
интерфейсы, которые нужно реализовать, в объявление enum-класса:

```kotlin
import java.util.function.BinaryOperator
import java.util.function.IntBinaryOperator

enum class IntArithmetics : BinaryOperator<Int>, IntBinaryOperator {
    PLUS {
        override fun apply(t: Int, u: Int): Int = t + u
    },
    TIMES {
        override fun apply(t: Int, u: Int): Int = t * u
    };
    
    override fun applyAsInt(t: Int, u: Int) = apply(t, u)
}

fun main() {
    val a = 13
    val b = 31
    for (f in IntArithmetics.entries) {
        println("$f($a, $b) = ${f.apply(a, b)}")
    }
}
```

<!-- All enum classes implement the [Comparable](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/index.html)
interface by default. Constants in the enum class are defined in the natural order. For more information, see [Ordering](collection-ordering.md). -->
Все enum-классы по умолчанию реализуют интерфейс
[Comparable](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/index.html). Константы enum-класса
объявляются в естественном порядке. Подробнее см. в разделе [Сортировка](collection-ordering.html).

<a name="working-with-enum-constants"></a>
<!-- ## Working with enum constants -->
## Работа с enum-константами

<!-- Enum classes in Kotlin have synthetic properties and methods for listing the defined enum constants and getting an enum constant by
its name. The signatures of these methods are as follows (assuming the name of the enum class is `EnumClass`): -->
Enum-классы в Kotlin имеют синтетические свойства и методы для получения списка объявленных enum-констант и получения
enum-константы по её имени. Ниже приведены сигнатуры этих методов (при условии, что имя enum-класса — `EnumClass`):

```kotlin
EnumClass.valueOf(value: String): EnumClass
EnumClass.entries: EnumEntries<EnumClass> // специализированный List<EnumClass>
```

<!-- Below is an example of them in action: -->
Ниже приведён пример их использования:

```kotlin
enum class RGB { RED, GREEN, BLUE }

fun main() {
    for (color in RGB.entries) println(color.toString()) // выведет RED, GREEN, BLUE
    println("The first color is: ${RGB.valueOf("RED")}") // выведет "The first color is: RED"
}
```

<!-- The `valueOf()` method throws an `IllegalArgumentException` if the specified name does
not match any of the enum constants defined in the class. -->
Метод `valueOf()` выбрасывает исключение `IllegalArgumentException`, если указанное имя не соответствует ни одной
enum-константе, объявленной в классе.

<!-- Prior to the introduction of `entries` in Kotlin 1.9.0, the `values()` function was used to retrieve an array of enum constants. -->
До появления `entries` в Kotlin 1.9.0 для получения массива enum-констант использовалась функция `values()`.

<!-- Every enum constant also has properties: [`name`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-enum/name.html)
and [`ordinal`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-enum/ordinal.html), for obtaining its name and
position (starting from 0) in the enum class declaration: -->
У каждой enum-константы также есть свойства [`name`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-enum/name.html)
и [`ordinal`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-enum/ordinal.html), которые позволяют получить её имя
и позицию (начиная с 0) в объявлении enum-класса:

```kotlin
enum class RGB { RED, GREEN, BLUE }

fun main() {
    println(RGB.RED.name)    // выведет RED
    println(RGB.RED.ordinal) // выведет 0
}
```

<!-- To reduce repetition when working with enum entries, try out context-sensitive resolution (currently in preview).
This feature allows you to omit the enum class name when the expected type is known, such as in `when` expressions or when assigning to a typed variable.

For more information, see [Preview of context-sensitive resolution](whatsnew22.md#preview-of-context-sensitive-resolution) or the related [KEEP proposal](https://github.com/Kotlin/KEEP/blob/improved-resolution-expected-type/proposals/context-sensitive-resolution.md). -->
> **Совет.** Чтобы сократить повторения при работе с записями enum, попробуйте контекстно-зависимое разрешение
> (сейчас доступно в предварительном режиме). Эта возможность позволяет опускать имя enum-класса, когда ожидаемый тип
> известен, например в выражениях `when` или при присваивании переменной с явным типом.
>
> Подробнее см. в разделе [Preview of context-sensitive resolution](https://kotlinlang.org/docs/whatsnew22.html#preview-of-context-sensitive-resolution)
> и в соответствующем [KEEP-предложении](https://github.com/Kotlin/KEEP/blob/improved-resolution-expected-type/proposals/context-sensitive-resolution.md).

<!-- You can access the constants in an enum class in a generic way using
the [`enumValues<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-values.html) and [`enumValueOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-value-of.html) functions.
In Kotlin 2.0.0, the [`enumEntries<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.enums/enum-entries.html) function is introduced as a replacement for the [`enumValues<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-values.html)
function. The `enumEntries<T>()` function returns a list of all enum entries for the given enum type `T`. -->
К константам в enum-классе можно получить доступ универсальным способом, используя функции
[`enumValues<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-values.html) и
[`enumValueOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-value-of.html). В Kotlin 2.0.0 функция
[`enumEntries<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.enums/enum-entries.html) появилась как замена
функции [`enumValues<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-values.html). Функция
`enumEntries<T>()` возвращает список всех записей enum для заданного enum-типа `T`.

<!-- The `enumValues<T>()` function is still supported, but we recommend that you use the `enumEntries<T>()` function instead
because it has less performance impact. Every time you call `enumValues<T>()` a new array is created, whereas whenever
you call `enumEntries<T>()` the same list is returned each time, which is far more efficient. -->
Функция `enumValues<T>()` по-прежнему поддерживается, но рекомендуется использовать вместо неё `enumEntries<T>()`,
поскольку она оказывает меньшее влияние на производительность. При каждом вызове `enumValues<T>()` создаётся новый
массив, тогда как `enumEntries<T>()` каждый раз возвращает один и тот же список, что намного эффективнее.

<!-- For example: -->
Например:

```kotlin
enum class RGB { RED, GREEN, BLUE }

inline fun <reified T : Enum<T>> printAllValues() {
    println(enumEntries<T>().joinToString { it.name })
}

printAllValues<RGB>()
// RED, GREEN, BLUE
```

<!-- For more information about inline functions and reified type parameters, see [Inline functions](inline-functions.md). -->
> Подробнее о встроенных функциях и reified-параметрах типа см. в разделе
> [Встроенные (inline) функции](inline-functions.html).
