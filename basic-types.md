---
type: doc
layout: reference
category: "Syntax"
title: "Основные типы"
url: https://kotlinlang.ru/docs/basic-types.html
---

<!--
Актуализировано по официальной документации Kotlin:
Types overview — 01 April 2025
Numbers — 18 May 2026
Unsigned integer types — 07 February 2025
Booleans — 05 May 2026
Characters — 04 May 2026
Strings — 04 February 2026
Arrays — 01 April 2025
-->

<!-- Basic types -->
# Основные типы

<!-- In Kotlin, everything is an object in the sense that you can call member functions and properties on any variable.
While certain types have an optimized internal representation as primitive values at runtime (such as numbers, characters, and booleans),
they appear and behave like regular classes to you. -->
В Kotlin всё является объектом в том смысле, что вы можете вызывать функции-члены и свойства для любой переменной.
Хотя у некоторых типов есть оптимизированное внутреннее представление в виде примитивных значений во время выполнения
(например, у чисел, символов и логических значений), для вас они выглядят и ведут себя как обычные классы.

<!-- This section describes the basic types used in Kotlin: -->
В этом разделе описаны основные типы, используемые в Kotlin:

<!-- * Numbers and their unsigned counterparts
* Booleans
* Characters
* Strings
* Arrays -->
* [числа](#numbers) и их [беззнаковые аналоги](#unsigned-integers)
* [логические значения](#booleans)
* [символы](#characters)
* [строки](#strings)
* [массивы](#arrays)

<!-- To learn about other Kotlin types, such as `Nothing`, `Any`, and `Unit`, look through the Kotlin API reference: -->
Чтобы узнать о других типах Kotlin, таких как `Nothing`, `Any` и `Unit`, обратитесь к справочнику Kotlin API:

* [`Any`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/)
* [`Nothing`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-nothing.html)
* [`Unit`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-unit/)

<!-- Learn how to perform type checks and casts in Kotlin. -->
> Подробнее о проверках и приведениях типов см. в разделе [Проверки и приведения типов](typecasts.html).

<a name="numbers"></a>
<!-- ## Numbers -->
## Числа

<!-- The Kotlin number types represent:
* Integer values (`Byte`, `Short`, `Int`, and `Long`)
* Floating-point values (`Float` and `Double`) -->
Числовые типы Kotlin представляют:

* целые значения (`Byte`, `Short`, `Int` и `Long`)
* значения с плавающей точкой (`Float` и `Double`)

<!-- Use number types to store and process numeric data, for example, in arithmetic, counters, measurements, and other calculations. -->
Используйте числовые типы для хранения и обработки числовых данных: например, в арифметике,
счётчиках, измерениях и других вычислениях.

<a name="choose-a-number-type"></a>
<!-- ### Choose a number type -->
### Выбор числового типа

<!-- In most cases, you can refer to the following rules to determine the correct number type for your task: -->
В большинстве случаев для выбора подходящего числового типа можно ориентироваться на следующие правила:

<!-- * Use `Int` for whole numbers.
* Use `Long` for whole numbers outside the `Int` range.
* Use `Double` for decimal numbers.
* Use `Float` when lower precision is acceptable or required.
* Use `Byte` and `Short` when an API or data format requires them. -->
* Используйте `Int` для целых чисел.
* Используйте `Long` для целых чисел за пределами диапазона `Int`.
* Используйте `Double` для десятичных чисел.
* Используйте `Float`, когда допустима или требуется меньшая точность.
* Используйте `Byte` и `Short`, когда этого требует API или формат данных.

<!-- Kotlin also provides unsigned integer types as a Beta feature. -->
> Kotlin также предоставляет [беззнаковые целочисленные типы](#unsigned-integers) как бета-функцию.

<a name="integer-types"></a>
<!-- ### Integer types -->
### Целочисленные типы

<!-- Kotlin provides four integer types with different sizes and value ranges: -->
Kotlin предоставляет четыре целочисленных типа с разными размерами и диапазонами значений:

<!-- Type, Size (bits), Min value, Max value -->
| Тип     | Размер (биты) | Минимальное значение             | Максимальное значение                          |
|---------|---------------|----------------------------------|------------------------------------------------|
| `Byte`  | 8             | -128                             | 127                                            |
| `Short` | 16            | -32768                           | 32767                                          |
| `Int`   | 32            | -2,147,483,648 (-2<sup>31</sup>) | 2,147,483,647 (2<sup>31</sup> - 1)             |
| `Long`  | 64            | -9,223,372,036,854,775,808 (-2<sup>63</sup>) | 9,223,372,036,854,775,807 (2<sup>63</sup> - 1) |

<a name="literal-constants"></a>
<a name="declare-integer-values"></a>
<!-- #### Declare integer values -->
#### Объявление целочисленных значений

<!-- Kotlin supports the following literal forms for integer values: -->
Kotlin поддерживает следующие формы литералов для целочисленных значений:

<!-- * Decimals: `123`
* Hexadecimals: `0x0F`
* Binaries: `0b00001011` -->
* десятичные: `123`
* шестнадцатеричные: `0x0F`
* двоичные: `0b00001011`

<!-- Kotlin does not support octal literals. -->
> Kotlin не поддерживает восьмеричные литералы.

<!-- To declare a numeric value, specify the type explicitly: -->
Чтобы объявить числовое значение, укажите тип явно:

```kotlin
val one: Int = 1

// Используйте нижние подчёркивания, чтобы повысить читаемость
val oneBillion: Long = 1_000_000_000
val hexBytes: Int = 0x7F_EC_DE_5E
val bytes: Int = 0b01010010_01101001_10010100_10010010

val oneByte: Byte = 1
val oneShort: Short = 1
```

<!-- You can also append the `L` suffix, to declare a `Long` value: -->
Чтобы объявить значение `Long`, можно также добавить суффикс `L`:

```kotlin
val oneLong = 1L
```

<!-- When you declare a numeric type explicitly, the compiler checks that the value fits in the range of that type: -->
Когда вы объявляете числовой тип явно, компилятор проверяет, что значение входит в диапазон этого типа:

```kotlin
// Значение входит в диапазон Byte
val oneByte: Byte = 1

// Ошибка: значение не входит в диапазон Byte
val tooBig: Byte = 128
```

<!-- When you do not specify a numeric type, Kotlin infers `Int` if the value fits in the `Int` range.
Otherwise, Kotlin infers `Long`: -->
Если вы не указываете числовой тип, Kotlin выводит `Int`, когда значение входит в диапазон `Int`.
В остальных случаях Kotlin выводит `Long`:

```kotlin
val million = 1_000_000 // Int
val threeBillion = 3_000_000_000 // Long
```

<!-- If a value can be absent, use nullable types: -->
Если значение может отсутствовать, используйте nullable-типы:

```kotlin
val maybeAbsent: Int? = null
```

<a name="floating-point-types"></a>
<!-- ### Floating-point types -->
### Типы с плавающей точкой

<!-- For numbers with a fractional part, Kotlin provides `Float` and `Double`. -->
Для чисел с дробной частью Kotlin предоставляет типы `Float` и `Double`.

<!-- Floating-point types follow the IEEE 754 standard.
`Float` reflects the single precision. `Double` reflects the double precision. -->
Типы с плавающей точкой соответствуют [стандарту IEEE 754](https://en.wikipedia.org/wiki/IEEE_754).
`Float` отражает одинарную точность, а `Double` — двойную.

<!-- Floating-point types differ in size and precision: -->
Типы с плавающей точкой различаются размером и точностью:

<!-- Type, Size (bits), Significant bits, Exponent bits, Decimal digits -->
| Тип      | Размер (биты) | Значимые биты | Биты экспоненты | Десятичные цифры |
|----------|---------------|---------------|-----------------|------------------|
| `Float`  | 32            | 24            | 8               | 6-7              |
| `Double` | 64            | 53            | 11              | 15-16            |

<a name="declare-floating-point-values"></a>
<!-- #### Declare floating-point values -->
#### Объявление значений с плавающей точкой

<!-- To declare a floating-point literal, include a decimal point (`.`) or use exponent notation: -->
Чтобы объявить литерал с плавающей точкой, добавьте десятичную точку (`.`) или используйте экспоненциальную запись:

```kotlin
val pi = 3.14
val avogadro = 6.02214076e23
```

<!-- By default, Kotlin infers floating-point literals as `Double`.
To declare a `Float`, add the `f` or `F` suffix: -->
По умолчанию Kotlin выводит литералы с плавающей точкой как `Double`.
Чтобы объявить `Float`, добавьте суффикс `f` или `F`:

```kotlin
val pi = 3.14 // Double
val eFloat = 2.7182817f // Float
```

<!-- Kotlin rounds a `Float` literal that contains more precision than `Float` can store. -->
> Kotlin округляет литерал `Float`, если он содержит больше точности, чем может хранить `Float`.

<!-- If a value can be absent, use nullable types: -->
Если значение может отсутствовать, используйте nullable-типы:

```kotlin
val maybeAbsent: Double? = null
```

<a name="operations"></a>
<!-- ### Arithmetic operations -->
### Арифметические операции

<!-- Kotlin supports the standard arithmetic operations on numbers: `+`, `-`, `*`, `/`, and `%`. -->
Kotlin поддерживает стандартные арифметические операции над числами: `+`, `-`, `*`, `/` и `%`.

<!-- Use these operators to perform common calculations: -->
Используйте эти операторы для обычных вычислений:

```kotlin
fun main() {
    println(1 + 2) // 3
    println(2_500_000_000L - 1L) // 2499999999
    println(3.14 * 2.71) // 8.5094
    println(10.0 / 3) // 3.3333333333333335
}
```

<!-- The result type depends on the types of the operands. Learn more in Mixed numeric expressions. -->
Тип результата зависит от типов операндов. Подробнее см. в разделе [Смешанные числовые выражения](#mixed-numeric-expressions).

<!-- You can override these operators in custom number classes.
For more information, see Operator overloading. -->
> Эти операторы можно переопределять в пользовательских числовых классах.
> Подробнее см. в разделе [Перегрузка операторов](operator-overloading.html).

<a name="division-of-integers"></a>
<!-- #### Integer division -->
#### Целочисленное деление

<!-- Division between integer values always returns an integer result. The compiler discards the fractional part: -->
Деление целочисленных значений всегда возвращает целочисленный результат. Компилятор отбрасывает дробную часть:

```kotlin
fun main() {
    val intValue = 5 / 2
    println(intValue) // 2

    val longValue = 5L / 2
    println(longValue) // 2
}
```

<!-- To return a floating-point result, make at least one operand a `Float` or `Double`: -->
Чтобы получить результат с плавающей точкой, сделайте как минимум один операнд типом `Float` или `Double`:

```kotlin
fun main() {
    val a = 5 / 2.0
    println(a) // 2.5

    val b = 5 / 2.toDouble()
    println(b) // 2.5
}
```

<a name="explicit-conversions"></a>
<a name="type-conversion"></a>
<!-- ### Type conversion -->
### Преобразование типов

<!-- Numeric types are not subtypes of one another. Kotlin requires explicit conversions to avoid silent data loss and unexpected behavior. -->
Числовые типы не являются подтипами друг друга. Kotlin требует явных преобразований,
чтобы избежать незаметной потери данных и неожиданного поведения.

<!-- For example, a function that expects `Double` cannot accept an `Int` or a `Float` value without conversion: -->
Например, функция, ожидающая `Double`, не может принять значение `Int` или `Float` без преобразования:

```kotlin
fun main() {
    fun printDouble(x: Double) {
        print(x)
    }

    val x = 1.0
    val xInt = 1
    val xFloat = 1.0f
    val one: Double = 1 // Ошибка: несоответствие типа инициализатора

    printDouble(x) // OK
    printDouble(xInt) // Ошибка: несоответствие типа аргумента
    printDouble(xFloat) // Ошибка: несоответствие типа аргумента
}
```

<!-- All number types support conversions to other number types.
To convert a number to another type, use an explicit conversion function: -->
Все числовые типы поддерживают преобразования в другие числовые типы.
Чтобы преобразовать число в другой тип, используйте явную функцию преобразования:

* `toByte()`
* `toShort()`
* `toInt()`
* `toLong()`
* `toFloat()`
* `toDouble()`

<!-- For example, the following code converts an `Int` value to `Double`: -->
Например, следующий код преобразует значение `Int` в `Double`:

```kotlin
fun main() {
    val intValue: Int = 1
    val doubleValue = intValue.toDouble()

    println(doubleValue) // 1.0
}
```

<!-- When you convert a floating-point value to an integer type, the compiler discards the fractional part: -->
Когда вы преобразуете значение с плавающей точкой в целочисленный тип, компилятор отбрасывает дробную часть:

```kotlin
fun main() {
    val d: Double = 1.5
    val l: Long = d.toLong()

    println(l) // 1
}
```

<a name="mixed-numeric-expressions"></a>
<!-- #### Mixed numeric expressions -->
#### Смешанные числовые выражения

<!-- Kotlin does not support implicit conversion for assignments or function arguments.
However, you can combine different numeric types in arithmetic expressions. In such cases,
Kotlin determines a result type based on the operand types,
and arithmetic operators handle the conversion automatically: -->
Kotlin не поддерживает неявные преобразования при присваивании или передаче аргументов в функцию.
Однако вы можете объединять разные числовые типы в арифметических выражениях. В таких случаях
Kotlin определяет тип результата на основе типов операндов, а арифметические операторы выполняют
преобразование автоматически:

```kotlin
val intNumber: Int = 1
val longNumber: Long = 1000
val result = intNumber + longNumber // 1001, Long
```

<!-- If you try to assign the result to a smaller type, the compiler reports an error: -->
Если попытаться присвоить результат меньшему типу, компилятор сообщит об ошибке:

```kotlin
val intNumber: Int = 1
val longNumber: Long = 1000
val result: Int = intNumber + longNumber
// Ошибка: несоответствие типа инициализатора
```

<a name="data-overflow"></a>
<!-- ### Data overflow -->
### Переполнение данных

<!-- Numeric types can represent only values within their defined ranges. -->
Числовые типы могут представлять только значения в пределах определённых для них диапазонов.

<!-- If the result of an operation falls outside that range, overflow occurs.
If you convert a value to a smaller numeric type, the converted value may not preserve the original numeric value. -->
Если результат операции выходит за пределы такого диапазона, происходит переполнение.
Если преобразовать значение в меньший числовой тип, преобразованное значение может не сохранить исходное число.

<!-- This behavior can affect the result of your code even when the compiler accepts it. -->
Такое поведение может повлиять на результат вашего кода, даже если компилятор его принимает.

<a name="overflow-in-operations"></a>
<!-- #### Overflow in operations -->
#### Переполнение в операциях

<!-- Each integer type can store only values within its defined range.
When the result of an arithmetic operation exceeds that range, data overflow occurs: -->
Каждый целочисленный тип может хранить только значения в пределах определённого диапазона.
Когда результат арифметической операции превышает этот диапазон, происходит переполнение данных:

```kotlin
fun main() {
    val intNumber: Int = 2147483647
    // Максимальное значение Int равно 2147483647
    println(intNumber + 1) // -2147483648
}
```

<!-- Here, the result wraps around because the value no longer fits in `Int`. -->
Здесь результат переходит через границу диапазона, потому что значение больше не помещается в `Int`.

<!-- The compiler does not automatically produce an error when integer overflow occurs. -->
> Компилятор не выдаёт ошибку автоматически при целочисленном переполнении.

<a name="overflow-in-negation"></a>
<!-- #### Overflow in negation -->
#### Переполнение при смене знака

<!-- Overflow can also occur during negation.
For example, you cannot represent the positive counterpart of `Int.MIN_VALUE` as an `Int`. -->
Переполнение также может возникнуть при смене знака.
Например, положительный аналог `Int.MIN_VALUE` нельзя представить как `Int`.

```kotlin
fun main() {
    val min = Int.MIN_VALUE
    println(-min) // -2147483648
}
```

<a name="narrowing-conversions"></a>
<!-- #### Narrowing conversions -->
#### Сужающие преобразования

<!-- When you convert a value to a smaller integer type,
the result may not preserve the original numeric value: -->
Когда вы преобразуете значение в меньший целочисленный тип, результат может не сохранить исходное число:

```kotlin
fun main() {
    val large: Int = 130
    val narrowed: Byte = large.toByte()

    println(narrowed) // -126
}
```

<!-- However, since floating-point types follow the IEEE 754 Standard, very large results can become `Infinity`: -->
Однако, поскольку типы с плавающей точкой соответствуют [стандарту IEEE 754](https://en.wikipedia.org/wiki/IEEE_754),
очень большие результаты могут становиться `Infinity`:

```kotlin
fun main() {
    println(Double.MAX_VALUE * 2) // Infinity
}
```

<a name="bitwise-operations"></a>
<!-- ### Bitwise operations -->
### Побитовые операции

<!-- Kotlin provides bitwise operations for `Int` and `Long`. These operations are represented by a set of infix functions and `inv()`. -->
Kotlin предоставляет побитовые операции для `Int` и `Long`. Эти операции представлены набором
[инфиксных функций](functions.html#infix-notation) и функцией `inv()`.

```kotlin
fun main() {
    val x = 1

    println(x shl 2) // 4
    println(x and 0x000FF000) // 0
}
```

<!-- Bitwise operations include: -->
Побитовые операции включают:

* `shl()` — знаковый сдвиг влево
* `shr()` — знаковый сдвиг вправо
* `ushr()` — беззнаковый сдвиг вправо
* `and()` — побитовое И
* `or()` — побитовое ИЛИ
* `xor()` — побитовое исключающее ИЛИ
* `inv()` — побитовая инверсия

<a name="floating-point-numbers-comparison"></a>
<!-- ### Floating-point number comparison -->
### Сравнение чисел с плавающей точкой

<!-- In Kotlin, floating-point comparison depends on the static type of the operands. -->
В Kotlin сравнение чисел с плавающей точкой зависит от статического типа операндов.

<!-- When the operands are statically known to be `Float` or `Double`,
operations on the numbers and the range that they form
follow the IEEE 754 Standard for Floating-Point Arithmetic. -->
Когда статически известно, что операнды имеют тип `Float` или `Double`,
операции над числами и диапазоном, который они образуют, соответствуют
[стандарту IEEE 754 для арифметики с плавающей точкой](https://en.wikipedia.org/wiki/IEEE_754).

<!-- However, in generic use cases (such as `Any`, `Comparable<...>`, or `Collection<T>`), behavior differs for
operands that are not statically typed as floating-point numbers. In these cases, Kotlin
uses the `equals()` and `compareTo()` implementations for `Float` and `Double`. -->
Однако в обобщённых сценариях (например, `Any`, `Comparable<...>` или `Collection<T>`) поведение отличается
для операндов, которые статически не типизированы как числа с плавающей точкой. В этих случаях Kotlin
использует реализации `equals()` и `compareTo()` для `Float` и `Double`.

<!-- As a result: -->
В результате:

* `NaN` считается равным самому себе
* `NaN` считается больше любого другого элемента, включая `POSITIVE_INFINITY`
* `-0.0` считается меньше `0.0`

<!-- The following example shows the difference between operands statically typed as floating-point numbers
and operands used through generic types: -->
Следующий пример показывает различие между операндами, статически типизированными как числа с плавающей точкой,
и операндами, используемыми через обобщённые типы:

```kotlin
fun generalizedEquals(a: Any, b: Any): Boolean {
    return a == b
}

fun main() {
    // Операнды статически типизированы как числа с плавающей точкой
    println(Double.NaN == Double.NaN) // false
    println(0.0 == -0.0) // true

    // Операнды используются через статический тип, не являющийся типом с плавающей точкой
    println(generalizedEquals(Double.NaN, Double.NaN)) // true
    println(generalizedEquals(0.0, -0.0)) // false
}
```

<a name="numbers-representation-on-the-jvm"></a>
<a name="boxing-and-caching-numbers-on-the-jvm"></a>
<!-- ### Boxing and caching numbers on the JVM -->
### Упаковка и кэширование чисел в JVM

<!-- On the JVM, non-nullable numeric values are usually stored using primitive types, such as `int`, `long`, or `double`.
However, when you use generic types or nullable numeric types like `Int?`, the value is boxed and represented as an object. -->
В JVM числовые значения, не допускающие `null`, обычно хранятся с помощью примитивных типов,
таких как `int`, `long` или `double`. Однако при использовании [обобщённых типов](generics.html)
или nullable-числовых типов, таких как `Int?`, значение упаковывается и представляется как объект.

<!-- The JVM applies a memory optimization technique to small numbers by caching their boxed representations.
As a result, boxed numbers with the same value can be referentially equal. -->
JVM применяет к небольшим числам [технику оптимизации памяти](https://docs.oracle.com/javase/specs/jls/se22/html/jls-5.html#jls-5.1.7),
кэшируя их упакованные представления. В результате упакованные числа с одинаковым значением могут быть
[равны по ссылке](equality.html#referential-equality).

<!-- For example, the JVM caches boxed `Integer` values in the range `-128` to `127`.
Therefore, the following code returns `true`: -->
Например, JVM кэширует упакованные значения `Integer` в диапазоне от `-128` до `127`.
Поэтому следующий код возвращает `true`:

```kotlin
fun main() {
    val score: Int = 100
    val savedScore: Int? = score
    val displayedScore: Int? = score

    println(savedScore === displayedScore) // true
}
```

<!-- For values outside the cached range, boxed values are separate objects.
In that case, they are not referentially equal, even if their values are structurally equal.
For this reason, use `==` to compare numeric values: -->
Для значений вне кэшируемого диапазона упакованные значения являются отдельными объектами.
В этом случае они не равны по ссылке, даже если их значения [структурно равны](equality.html#structural-equality).
По этой причине для сравнения числовых значений используйте `==`:

```kotlin
fun main() {
    val score: Int = 10000
    val savedScore: Int? = score
    val displayedScore: Int? = score

    println(savedScore === displayedScore) // false
    println(savedScore == displayedScore) // true
}
```

<a name="unsigned-integers"></a>
<!-- ### Unsigned integer types -->
### Беззнаковые целочисленные типы

<!-- In addition to integer types, Kotlin provides the following types for unsigned integer numbers: -->
В дополнение к целочисленным типам Kotlin предоставляет следующие типы для беззнаковых целых чисел:

<!-- Type, Size (bits), Min value, Max value -->
| Тип      | Размер (биты) | Минимальное значение | Максимальное значение                          |
|----------|---------------|----------------------|------------------------------------------------|
| `UByte`  | 8             | 0                    | 255                                            |
| `UShort` | 16            | 0                    | 65,535                                         |
| `UInt`   | 32            | 0                    | 4,294,967,295 (2<sup>32</sup> - 1)             |
| `ULong`  | 64            | 0                    | 18,446,744,073,709,551,615 (2<sup>64</sup> - 1) |

<!-- Unsigned types support most of the operations of their signed counterparts. -->
Беззнаковые типы поддерживают большинство операций своих знаковых аналогов.

<!-- Unsigned numbers are implemented as inline classes with a single storage property that contains the corresponding
signed counterpart type of the same width. If you want to convert between unsigned and signed integer types,
make sure you update your code so that any function calls and operations support the new type. -->
> Беззнаковые числа реализованы как [inline-классы](inline-classes.html) с одним свойством хранения,
> которое содержит соответствующий знаковый тип той же разрядности. Если вы хотите преобразовывать
> беззнаковые и знаковые целочисленные типы друг в друга, убедитесь, что вызовы функций и операции
> в вашем коде поддерживают новый тип.

<a name="unsigned-arrays-and-ranges"></a>
<!-- #### Unsigned arrays and ranges -->
#### Беззнаковые массивы и диапазоны

<!-- Unsigned arrays and operations on them are in Beta. They can be changed incompatibly at any time.
Opt-in is required (see the details below). -->
> Беззнаковые массивы и операции над ними находятся в [стадии бета-тестирования](https://kotlinlang.org/docs/components-stability.html).
> Они могут быть несовместимо изменены в любое время. Требуется явное согласие на использование (подробности ниже).

<!-- Same as for primitives, each unsigned type has a corresponding type that represents arrays of that type: -->
Как и для примитивов, у каждого беззнакового типа есть соответствующий тип, представляющий массивы этого типа:

* `UByteArray`: массив беззнаковых байтов.
* `UShortArray`: массив беззнаковых short-значений.
* `UIntArray`: массив беззнаковых int-значений.
* `ULongArray`: массив беззнаковых long-значений.

<!-- Same as for signed integer arrays, they provide a similar API to the `Array` class without boxing overhead. -->
Как и массивы знаковых целых чисел, они предоставляют API, похожий на класс `Array`,
без накладных расходов на упаковку.

<!-- When you use unsigned arrays, you receive a warning that indicates that this feature is not stable yet.
To remove the warning, opt-in with the `@ExperimentalUnsignedTypes` annotation.
It's up to you to decide if your clients have to explicitly opt-in into usage of your API, but keep in mind that unsigned
arrays are not a stable feature, so an API that uses them can be broken by changes in the language.
Learn more about opt-in requirements. -->
При использовании беззнаковых массивов вы получите предупреждение о том, что эта функция ещё не стабильна.
Чтобы убрать предупреждение, согласитесь на использование с помощью аннотации `@ExperimentalUnsignedTypes`.
Вам решать, должны ли пользователи вашего API явно соглашаться на его использование, но учитывайте,
что беззнаковые массивы не являются стабильной функцией, поэтому API, который их использует,
может быть сломан изменениями в языке. Подробнее см. в разделе
[Требования к явному согласию](https://kotlinlang.org/docs/opt-in-requirements.html).

<!-- Ranges and progressions are supported for `UInt` and `ULong` by classes `UIntRange`, `UIntProgression`,
`ULongRange`, and `ULongProgression`. Together with the unsigned integer types, these classes are stable. -->
[Диапазоны и прогрессии](ranges.html) поддерживаются для `UInt` и `ULong` классами `UIntRange`,
`UIntProgression`, `ULongRange` и `ULongProgression`. Вместе с беззнаковыми целочисленными типами
эти классы стабильны.

<a name="literals"></a>
<a name="unsigned-integers-literals"></a>
<!-- #### Unsigned integer literals -->
#### Беззнаковые целочисленные литералы

<!-- To make unsigned integers easier to use, you can append a suffix to an integer literal
indicating a specific unsigned type (similarly to `F` for `Float` or `L` for `Long`): -->
Чтобы упростить использование беззнаковых целых чисел, к целочисленному литералу можно добавить суффикс,
указывающий определённый беззнаковый тип (аналогично `F` для `Float` или `L` для `Long`):

<!-- * `u` and `U` letters signify unsigned literals without specifying the exact type.
If no expected type is provided, the compiler uses `UInt` or `ULong` depending on the size of the literal: -->
* Буквы `u` и `U` обозначают беззнаковые литералы без указания точного типа.
  Если ожидаемый тип не задан, компилятор использует `UInt` или `ULong` в зависимости от размера литерала:

```kotlin
val b: UByte = 1u  // UByte, ожидаемый тип задан
val s: UShort = 1u // UShort, ожидаемый тип задан
val l: ULong = 1u  // ULong, ожидаемый тип задан

val a1 = 42u // UInt: ожидаемый тип не задан, константа помещается в UInt
val a2 = 0xFFFF_FFFF_FFFFu // ULong: ожидаемый тип не задан, константа не помещается в UInt
```

<!-- * `uL` and `UL` explicitly specify that literal should be an unsigned long: -->
* `uL` и `UL` явно указывают, что литерал должен быть беззнаковым `Long`:

```kotlin
val a = 1UL // ULong, хотя ожидаемый тип не задан и константа помещается в UInt
```

<a name="unsigned-integers-use-cases"></a>
<!-- #### Use cases -->
#### Сценарии использования

<!-- The main use case of unsigned numbers is utilizing the full bit range of an integer to represent positive values.
For example, to represent hexadecimal constants that do not fit in signed types such as color in 32-bit `AARRGGBB` format: -->
Основной сценарий использования беззнаковых чисел — задействовать полный битовый диапазон целого числа
для представления положительных значений. Например, так можно представить шестнадцатеричные константы,
которые не помещаются в знаковые типы, например цвет в 32-битном формате `AARRGGBB`:

```kotlin
data class Color(val representation: UInt)

val yellow = Color(0xFFCC00CCu)
```

<!-- You can use unsigned numbers to initialize byte arrays without explicit `toByte()` literal casts: -->
Беззнаковые числа можно использовать для инициализации массивов байтов без явных преобразований литералов через `toByte()`:

```kotlin
val byteOrderMarkUtf8 = ubyteArrayOf(0xEFu, 0xBBu, 0xBFu)
```

<!-- Another use case is interoperability with native APIs. Kotlin allows representing native declarations that contain
unsigned types in the signature. The mapping won't substitute unsigned integers with signed ones keeping the semantics unaltered. -->
Ещё один сценарий — взаимодействие с нативными API. Kotlin позволяет представлять нативные объявления,
сигнатура которых содержит беззнаковые типы. Такое отображение не заменяет беззнаковые целые числа
на знаковые и сохраняет семантику без изменений.

<a name="non-goals"></a>
<!-- ##### Non-goals -->
##### Не цели

<!-- While unsigned integers can only represent positive numbers and zero, it's not a goal to use them where application
domain requires non-negative integers. For example, as a type of collection size or collection index value. -->
Хотя беззнаковые целые числа могут представлять только положительные числа и ноль, они не предназначены
для всех случаев, где предметная область требует неотрицательных целых чисел. Например, их не стоит
использовать как тип размера коллекции или индекс коллекции.

<!-- There are a couple of reasons: -->
На это есть несколько причин:

<!-- * Using signed integers can help to detect accidental overflows and signal error conditions, such as `List.lastIndex` being -1 for an empty list.
* Unsigned integers cannot be treated as a range-limited version of signed ones because their range of values is not a subset of the signed integers range. Neither signed, nor unsigned integers are subtypes of each other. -->
* Использование знаковых целых чисел помогает обнаруживать случайные переполнения и сигнализировать об ошибочных состояниях,
  например [`List.lastIndex`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last-index.html) равен -1 для пустого списка.
* Беззнаковые целые числа нельзя считать версией знаковых чисел с ограниченным диапазоном,
  потому что их диапазон значений не является подмножеством диапазона знаковых целых чисел.
  Ни знаковые, ни беззнаковые целые числа не являются подтипами друг друга.

<a name="booleans"></a>
<!-- ## Booleans -->
## Логический тип

<!-- The `Boolean` type represents logical values: `true` and `false`. -->
Тип `Boolean` представляет логические значения: `true` и `false`.

<!-- Use `Boolean` values in functions that answer yes-or-no questions, and in the `while`, `if`, and `when` conditions. -->
Используйте значения `Boolean` в функциях, которые отвечают на вопросы вида «да или нет»,
а также в условиях `while`, `if` и `when`.

<a name="declare-a-boolean-variable"></a>
<!-- ### Declare a `Boolean` variable -->
### Объявление переменной `Boolean`

<!-- To declare a `Boolean` variable, assign it `true` or `false`.
You can specify the `Boolean` type explicitly or let Kotlin infer it from the value: -->
Чтобы объявить переменную `Boolean`, присвойте ей `true` или `false`.
Тип `Boolean` можно указать явно или позволить Kotlin вывести его из значения:

```kotlin
val isTrue: Boolean = true
val isFalse = false // Kotlin выводит Boolean
```

<!-- If a value can be `null`, use `Boolean?`: -->
Если значение может быть `null`, используйте `Boolean?`:

```kotlin
val isEnabled: Boolean? = null
```

<!-- You cannot assign an integer value to a `Boolean` variable.
In Kotlin, `0` and `1` are not `Boolean` values. -->
> Нельзя присвоить целочисленное значение переменной `Boolean`.
> В Kotlin `0` и `1` не являются значениями `Boolean`.

<a name="produce-boolean-values"></a>
<!-- ### Produce `Boolean` values -->
### Получение значений `Boolean`

<!-- You can use comparison expressions and functions to produce `Boolean` values: -->
Для получения значений `Boolean` можно использовать выражения сравнения и функции:

```kotlin
fun main() {
    val number = 10
    val isPositive = number > 0
    println(isPositive) // true

    val language = "Kotlin"
    val isEmpty = language.isEmpty()
    println(isEmpty) // false
}
```

<!-- You can use the results in conditions and other expressions as well: -->
Результаты также можно использовать в условиях и других выражениях:

```kotlin
fun main() {
    val number = 10
    val isPositive = number > 0 // true

    if (isPositive) {
        println("The number is positive.")
    }
}
```

<a name="boolean-operations"></a>
<!-- ### `Boolean` operations -->
### Операции с `Boolean`

<!-- Kotlin provides operators and infix functions for working with `Boolean` values.
You can use them to invert a `Boolean` value or combine multiple `Boolean` values into a single result. -->
Kotlin предоставляет операторы и инфиксные функции для работы со значениями `Boolean`.
С их помощью можно инвертировать логическое значение или объединить несколько значений `Boolean`
в один результат.

<a name="negation-not"></a>
<!-- #### Negation (NOT) -->
#### Отрицание (NOT)

<!-- The NOT operator inverts a `Boolean` value.
To use NOT, place the `!` operator before a `Boolean` value: -->
Оператор NOT инвертирует значение `Boolean`.
Чтобы использовать NOT, поставьте оператор `!` перед значением `Boolean`:

```kotlin
val isOn = true
val isOff = !isOn // isOff равно false
```

<a name="logical-and"></a>
<!-- #### Logical AND -->
#### Логическое AND

<!-- The AND operator returns `true` only if both operands are `true`.
To use logical AND, place the `&&` operator between operands: -->
Оператор AND возвращает `true` только если оба операнда равны `true`.
Чтобы использовать логическое AND, поместите оператор `&&` между операндами:

```kotlin
val a = false && false // false
val b = false && true // false
val c = true && false // false
val d = true && true  // true
```

<!-- If the first operand is `false`, the `&&` operator skips the second operand.
To evaluate both operands, use the `and` infix function instead. -->
> Если первый операнд равен `false`, оператор `&&` пропускает второй операнд.
> Чтобы вычислить оба операнда, используйте вместо него [инфиксную функцию](functions.html#infix-notation) `and`.

<a name="logical-or"></a>
<!-- #### Logical OR -->
#### Логическое OR

<!-- The OR operator returns `true` if at least one operand is `true`.
To use logical OR, place the `||` operator between operands: -->
Оператор OR возвращает `true`, если хотя бы один операнд равен `true`.
Чтобы использовать логическое OR, поместите оператор `||` между операндами:

```kotlin
val a = false || false // false
val b = false || true  // true
val c = true || false  // true
val d = true || true   // true
```

<!-- If the first operand is `true`, the `||` operator skips the second operand.
To evaluate both operands, use the `or` infix function instead. -->
> Если первый операнд равен `true`, оператор `||` пропускает второй операнд.
> Чтобы вычислить оба операнда, используйте вместо него [инфиксную функцию](functions.html#infix-notation) `or`.

<a name="exclusive-or-xor"></a>
<!-- #### Exclusive OR (XOR) -->
#### Исключающее OR (XOR)

<!-- The exclusive OR (XOR) operation returns `true` if the operands have different values.
To use XOR, write `xor` between operands: -->
Операция исключающего OR (XOR) возвращает `true`, если операнды имеют разные значения.
Чтобы использовать XOR, напишите `xor` между операндами:

```kotlin
val a = false xor false // false
val b = false xor true  // true
val c = true xor false  // true
val d = true xor true   // false
```

<!-- `xor` is an infix function, not an operator.
Learn more about `Boolean` functions in the API Reference. -->
> `xor` — это [инфиксная функция](functions.html#infix-notation), а не оператор.
> Подробнее о функциях `Boolean` см. в [справочнике API](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin/-boolean/).

<a name="operator-precedence"></a>
<!-- ### Operator precedence -->
### Приоритет операторов

<!-- If an expression contains multiple logical operations and no parentheses to specify the evaluation order,
Kotlin applies precedence rules. Operations with higher precedence are evaluated before operations with lower precedence. -->
Если выражение содержит несколько логических операций и в нём нет скобок, явно задающих порядок вычисления,
Kotlin применяет правила приоритета. Операции с более высоким приоритетом вычисляются раньше операций
с более низким приоритетом.

<!-- For the `Boolean` operations described in this section, the precedence order is as follows: -->
Для операций `Boolean`, описанных в этом разделе, порядок приоритета следующий:

1. `!`
2. `xor` (и другие инфиксные функции)
3. `&&`
4. `||`

<!-- In the following example, the compiler evaluates `&&` before `||`: -->
В следующем примере компилятор вычисляет `&&` раньше `||`:

```kotlin
fun main() {
    val result = true || false && false
    println(result) // true
}
```

<!-- To make evaluation order explicit, use parentheses: -->
Чтобы явно задать порядок вычисления, используйте скобки:

```kotlin
fun main() {
    val result = (true || false) && false
    println(result) // false
}
```

<a name="boolean-in-conditions"></a>
<!-- ### `Boolean` in conditions -->
### `Boolean` в условиях

<!-- `if`, `when`, and `while` evaluate `Boolean` expressions to direct program flow. -->
[`if`](control-flow.html#if-expression), [`when`](control-flow.html#when-expressions-and-statements)
и [`while`](control-flow.html#while-loops) вычисляют выражения `Boolean`, чтобы управлять ходом программы.

<a name="if-expressions"></a>
<!-- #### `if` expressions -->
#### Выражения `if`

```kotlin
fun main() {
    val number = 4
    val isEven = number % 2 == 0

    // Условие уже имеет тип `Boolean`
    // Его не нужно сравнивать с `true` или `false`
    if (isEven) {
        println("The number is even.")
    } else {
        println("The number is odd.")
    }
}
```

<a name="when-expressions"></a>
<!-- #### `when` expressions -->
#### Выражения `when`

```kotlin
fun main() {
    val number = 3

    when {
        number > 0 -> println("The number is positive.")
        number < 0 -> println("The number is negative.")
        else -> println("The number is zero.")
    }
}
```

<a name="while-loops"></a>
<!-- #### `while` loops -->
#### Циклы `while`

```kotlin
fun main() {
    var isCalculating = true

    while (isCalculating) {
        println("Calculating...")
        isCalculating = false
    }
}
```

<a name="characters"></a>
<!-- ## Characters -->
## Символы

<!-- The `Char` type represents a single character as a UTF-16 code unit. -->
Тип `Char` представляет один символ как кодовую единицу UTF-16.

<!-- Use `Char` for individual character values, such as letters, digits, punctuation marks, or whitespace.
For sequences of characters, use `String`. -->
Используйте `Char` для отдельных символьных значений: букв, цифр, знаков препинания или пробельных символов.
Для последовательностей символов используйте [`String`](#strings).

<!-- `Char` is not a numeric type, but each character has a numeric Unicode value that you can access. See Character conversion. -->
> `Char` не является числовым типом, но у каждого символа есть числовое значение Unicode,
> к которому можно получить доступ. См. раздел [Преобразование символов](#character-conversion).

<a name="syntax"></a>
<!-- ### Syntax -->
### Синтаксис

<!-- To declare a character, enclose the value in single quotes (`' '`). You can specify the `Char` type explicitly or
let Kotlin infer it from the value: -->
Чтобы объявить символ, заключите значение в одинарные кавычки (`' '`). Можно указать тип `Char` явно
или позволить Kotlin вывести его из значения:

```kotlin
val letter: Char = 'a'

// Kotlin выводит Char, потому что значения записаны в одинарных кавычках
val digit = '1'
val symbol = '!'
val space = ' '
val separator = ':'
```

<!-- A character literal must contain exactly one character. Otherwise, the Kotlin compiler reports an error: -->
Символьный литерал должен содержать ровно один символ. В противном случае компилятор Kotlin сообщит об ошибке:

```kotlin
val invalid = 'AB' // Ошибка
val invalidEmpty = '' // Ошибка
```

<a name="nullable-values"></a>
<!-- #### Nullable values -->
#### Nullable-значения

<!-- To store a nullable value, use `Char?`: -->
Чтобы хранить nullable-значение, используйте `Char?`:

```kotlin
val maybeAbsent: Char? = null
```

<!-- On the JVM, nullable `Char` values are boxed when needed. The same applies to numeric types. -->
> В JVM nullable-значения `Char` упаковываются при необходимости. То же относится к
> [числовым типам](#boxing-and-caching-numbers-on-the-jvm).

<a name="unicode-support"></a>
<!-- ### Unicode support -->
### Поддержка Unicode

<!-- Kotlin represents `Char` values as UTF-16 code units.
This means that a single `Char` stores one UTF-16 code unit, not necessarily one complete Unicode character. -->
Kotlin представляет значения `Char` как кодовые единицы UTF-16.
Это означает, что один `Char` хранит одну кодовую единицу UTF-16, а не обязательно один полный символ Unicode.

<a name="basic-multilingual-plane"></a>
<!-- #### Basic Multilingual Plane -->
#### Basic Multilingual Plane

<!-- A single `Char` can store values in the range from `\u0000` to `\uFFFF`.
This range covers the Basic Multilingual Plane (BMP) that includes characters for
almost all modern languages and a large number of symbols. -->
Один `Char` может хранить значения в диапазоне от `\u0000` до `\uFFFF`.
Этот диапазон покрывает Basic Multilingual Plane (BMP), включающий символы почти всех современных языков
и большое количество знаков.

<!-- To specify a character by the Unicode value, use `\u` followed by four-digit hexadecimal value from the Unicode table: -->
Чтобы задать символ по значению Unicode, используйте `\u`, за которым следует четырёхзначное
шестнадцатеричное значение из [таблицы Unicode](https://www.unicode.org/charts/):

```kotlin
val unicodeNumber = '\u0031' // Равно '1'
```

<a name="supplementary-characters"></a>
<!-- #### Supplementary characters -->
#### Дополнительные символы

<!-- Unicode characters outside the BMP, such as emojis and some historic scripts,
cannot be represented by a single `Char`. In UTF-16, they are encoded as a surrogate pair,
where two `Char` values together represent one Unicode character in a `String`: -->
Символы Unicode за пределами BMP, например эмодзи и некоторые исторические письменности,
нельзя представить одним `Char`. В UTF-16 они кодируются суррогатной парой:
два значения `Char` вместе представляют один символ Unicode в `String`:

```kotlin
fun main() {
    val emoji = "🥦"

    println(emoji.length) // 2
    println(emoji[0])     // Первый суррогат
    println(emoji[1])     // Второй суррогат
}
```

<!-- To handle 32-bit symbols individually, use Unicode code points stored as `Int` values. -->
> Чтобы обрабатывать 32-битные символы по отдельности, используйте кодовые точки Unicode,
> хранящиеся как значения `Int`.

<a name="escape-sequences"></a>
<!-- ### Escape sequences -->
### Escape-последовательности

<!-- Use escape sequences for special characters that are difficult to write directly in source code or have a special meaning.
Every escape sequence begins with a backslash (`\`). -->
Используйте escape-последовательности для специальных символов, которые трудно записать прямо в исходном коде
или которые имеют особое значение. Каждая escape-последовательность начинается с обратного слеша (`\`).

<!-- Supported sequence, Description -->
| Поддерживаемая последовательность | Описание |
|-----------------------------------|----------|
| `\t`                              | Табуляция |
| `\b`                              | Backspace |
| `\n`                              | Новая строка (LF) |
| `\r`                              | Возврат каретки (CR) |
| `\'`                              | Одинарная кавычка |
| `\"`                              | Двойная кавычка |
| `\\`                              | Обратный слеш |
| `\$`                              | Знак доллара |

<!-- For example: -->
Например:

```kotlin
val newLine = '\n'
val dollar = '\$'
val backslash = '\\'
```

<a name="character-operations"></a>
<!-- ### Operations -->
### Операции

<!-- `Char` supports comparison, inspection, case conversion, and explicit numeric conversion. -->
`Char` поддерживает сравнение, проверку свойств, изменение регистра и явное числовое преобразование.

<a name="character-comparison"></a>
<!-- #### Character comparison -->
#### Сравнение символов

<!-- To compare `Char` values, use standard comparison operators such as `==`, `!=`, `<`, `>`, `<=`, and `>=`.
Kotlin compares `Char` values by their numeric Unicode values and returns a `Boolean` value: -->
Чтобы сравнивать значения `Char`, используйте стандартные [операторы сравнения](keyword-reference.html#operators-and-special-symbols),
такие как `==`, `!=`, `<`, `>`, `<=` и `>=`.
Kotlin сравнивает значения `Char` по их числовым значениям Unicode и возвращает значение `Boolean`:

```kotlin
val before = 'a' < 'b' // true
val after = 'c' > 'd' // false
val different = 'A' == 'a' // false
val equal = 'A' == 'A' // true
```

<a name="character-processing"></a>
<!-- #### Character processing -->
#### Обработка символов

<!-- Kotlin provides functions for inspection and case conversion of character values. For example: -->
Kotlin предоставляет функции для проверки и преобразования регистра символьных значений. Например:

```kotlin
fun main() {
    val myChar = 'A'
    // Проверяет, представляет ли символ цифру
    println(myChar.isDigit()) // false
    // Проверяет, представляет ли символ заглавную букву
    println(myChar.isUpperCase()) // true
    // Возвращает версию в нижнем регистре
    println(myChar.lowercaseChar()) // 'a'
}
```

<!-- Learn more about available functions in the API Reference. -->
> Подробнее о доступных функциях см. в [справочнике API](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin/-char/).

<a name="character-arithmetic"></a>
<!-- #### Character arithmetic -->
#### Арифметика символов

<!-- You can create another character value by adding or subtracting an integer: -->
Можно создать другое символьное значение, прибавив или вычтя целое число:

```kotlin
fun main() {
    val a = 'a'

    println(a + 1)  // b
    println(a + 2)  // c
    println(a - 32) // A
}
```

<!-- These operations follow Unicode values, not language-specific alphabet rules. -->
> Эти операции следуют значениям Unicode, а не правилам алфавита конкретного языка.

<!-- You can also use the increment (`++`) and decrement (`--`) operators in the prefix and postfix forms with mutable variables: -->
С изменяемыми переменными также можно использовать операторы инкремента (`++`) и декремента (`--`)
в префиксной и постфиксной формах:

```kotlin
fun main() {
    var a = 'A'

    a += 10
    println(a)   // 'K'

    println(++a) // 'L'  префиксный инкремент
    println(a++) // 'L'  постфиксный инкремент
    println(a)   // 'M'

    println(--a) // 'L'  префиксный декремент
    println(a--) // 'L'  постфиксный декремент
    println(a)   // 'K'
}
```

<a name="character-conversion"></a>
<!-- #### Character conversion -->
#### Преобразование символов

<!-- To convert `Char` to a numeric type, use explicit conversion: -->
Чтобы преобразовать `Char` в числовой тип, используйте явное преобразование:

<!-- * Use `.code` to get the numeric Unicode value of a character: -->
* Используйте `.code`, чтобы получить числовое значение Unicode для символа:

```kotlin
fun main() {
    val letter = 'A'
    println(letter.code) // 65
}
```

<!-- * If a character represents a decimal digit, use `digitToInt()`: -->
* Если символ представляет десятичную цифру, используйте `digitToInt()`:

```kotlin
fun main() {
    val digit = '7'
    println(digit.digitToInt()) // 7
}
```

<!-- If the character may not be a valid digit, use `digitToIntOrNull()`. -->
> Если символ может не быть допустимой цифрой, используйте `digitToIntOrNull()`.

<a name="strings"></a>
<!-- ## Strings -->
## Строки

<!-- Strings in Kotlin are represented by the type `String`. -->
Строки в Kotlin представлены типом [`String`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/).

<!-- On the JVM, an object of `String` type in UTF-16 encoding uses approximately 2 bytes per character. -->
> В JVM объект типа `String` в кодировке UTF-16 использует примерно 2 байта на символ.

<!-- Generally, a string value is a sequence of characters in double quotes (`"`): -->
Как правило, строковое значение — это последовательность символов в двойных кавычках (`"`):

```kotlin
val str = "abcd 123"
```

<!-- Elements of a string are characters that you can access via the indexing operation: `s[i]`.
You can iterate over these characters with a `for` loop: -->
Элементы строки — это символы, к которым можно обратиться с помощью операции индексированного доступа: `s[i]`.
По ним можно пройти циклом `for`:

```kotlin
fun main() {
    val str = "abcd"
    for (c in str) {
        println(c)
    }
}
```

<!-- Strings are immutable. Once you initialize a string, you can't change its value or assign a new value to it.
All operations that transform strings return their results in a new `String` object, leaving the original string unchanged: -->
Строки неизменяемы. После инициализации строки нельзя изменить её значение или присвоить ей новое.
Все операции, преобразующие строки, возвращают результат в новом объекте `String`, оставляя исходную строку без изменений:

```kotlin
fun main() {
    val str = "abcd"

    // Создаёт и выводит новый объект String
    println(str.uppercase())
    // ABCD

    // Исходная строка остаётся прежней
    println(str)
    // abcd
}
```

<!-- To concatenate strings, use the `+` operator. This also works for concatenating strings with values of other types, as long
as the first element in the expression is a string: -->
Для конкатенации строк используйте оператор `+`. Он также работает для объединения строк со значениями других типов,
если первый элемент в выражении является строкой:

```kotlin
fun main() {
    val s = "abc" + 1
    println(s + "def")
    // abc1def
}
```

<!-- In most cases using string templates or multiline strings is preferable to string concatenation. -->
> В большинстве случаев [строковые шаблоны](#string-templates) или [многострочные строки](#multiline-strings)
> предпочтительнее конкатенации строк.

<a name="string-literals"></a>
<!-- ### String literals -->
### Строковые литералы

<!-- Kotlin has two types of string literals: -->
В Kotlin есть два типа строковых литералов:

<!-- * Escaped strings
* Multiline strings -->
* [экранированные строки](#escaped-strings)
* [многострочные строки](#multiline-strings)

<a name="escaped-strings"></a>
<!-- #### Escaped strings -->
#### Экранированные строки

<!-- Escaped strings can contain escaped characters.
Here's an example of an escaped string: -->
*Экранированные строки* могут содержать экранированные символы.
Пример экранированной строки:

```kotlin
val s = "Hello, world!\n"
```

<!-- Escaping is done in the conventional way, with a backslash (`\`).
See Characters page for the list of supported escape sequences. -->
Экранирование выполняется обычным способом — с помощью обратного слеша (`\`).
Список поддерживаемых escape-последовательностей см. в разделе [Символы](#characters).

<a name="multiline-strings"></a>
<!-- #### Multiline strings -->
#### Многострочные строки

<!-- Multiline strings can contain newlines and arbitrary text. It is delimited by a triple quote (`"""`),
contains no escaping and can contain newlines and any other characters: -->
*Многострочные строки* могут содержать переводы строк и произвольный текст. Такая строка ограничивается
тройными кавычками (`"""`), не содержит экранирования и может включать переводы строк и любые другие символы:

```kotlin
val text = """
    for (c in "foo")
        print(c)
    """
```

<!-- To remove leading whitespace from multiline strings, use the `trimMargin()` function: -->
Чтобы удалить начальные пробельные символы из многострочных строк, используйте функцию
[`trimMargin()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/trim-margin.html):

```kotlin
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
```

<!-- By default, a pipe symbol `|` is used as margin prefix, but you can choose another character and pass it as a parameter, like `trimMargin(">")`. -->
По умолчанию в качестве префикса поля используется символ вертикальной черты `|`, но можно выбрать другой символ
и передать его как параметр, например `trimMargin(">")`.

<a name="string-templates"></a>
<!-- ### String templates -->
### Строковые шаблоны

<!-- String literals may contain template expressions – pieces of code that are evaluated and whose results are concatenated into a string.
When a template expression is processed, Kotlin automatically calls the `.toString()` function on the expression's result
to convert it into a string. A template expression starts with a dollar sign (`$`) and consists of either a variable name: -->
Строковые литералы могут содержать *шаблонные выражения* — фрагменты кода, которые вычисляются,
а их результаты объединяются в строку. При обработке шаблонного выражения Kotlin автоматически вызывает
функцию `.toString()` для результата выражения, чтобы преобразовать его в строку. Шаблонное выражение
начинается со знака доллара (`$`) и состоит либо из имени переменной:

```kotlin
fun main() {
    val i = 10
    println("i = $i")
    // i = 10

    val letters = listOf("a", "b", "c", "d", "e")
    println("Letters: $letters")
    // Letters: [a, b, c, d, e]
}
```

<!-- or an expression in curly braces: -->
либо из выражения в фигурных скобках:

```kotlin
fun main() {
    val s = "abc"
    println("$s.length is ${s.length}")
    // abc.length is 3
}
```

<!-- You can use templates both in multiline and escaped strings. However, multiline strings don't support backslash escaping.
To insert the dollar sign `$` in a multiline string
before any symbol allowed at the beginning of an identifier, use the following syntax: -->
Шаблоны можно использовать как в многострочных, так и в экранированных строках. Однако многострочные строки
не поддерживают экранирование обратным слешом. Чтобы вставить знак доллара `$` в многострочную строку
перед любым символом, разрешённым в начале [идентификатора](https://kotlinlang.org/grammar/#identifiers),
используйте следующий синтаксис:

```kotlin
val price = """
${'$'}_9.99
"""
```

<!-- To avoid `${'$'}` sequences in strings, you can use the Experimental multi-dollar string interpolation feature. -->
> Чтобы не использовать последовательности `${'$'}` в строках, можно воспользоваться экспериментальной
> [многодолларовой строковой интерполяцией](#multi-dollar-string-interpolation).

<a name="multi-dollar-string-interpolation"></a>
<!-- #### Multi-dollar string interpolation -->
#### Многодолларовая строковая интерполяция

<!-- Multi-dollar string interpolation allows you to specify how many consecutive dollar signs are required to trigger interpolation.
Interpolation is the process of embedding variables or expressions directly into a string. -->
Многодолларовая строковая интерполяция позволяет указать, сколько последовательных знаков доллара требуется,
чтобы запустить интерполяцию. Интерполяция — это встраивание переменных или выражений прямо в строку.

<!-- While you can escape literals for single-line strings,
multiline strings in Kotlin don't support backslash escaping.
To include dollar signs (`$`) as literal characters, you must use the `${'$'}` construct to prevent string interpolation.
This approach can make code harder to read, especially when strings contain multiple dollar signs. -->
В однострочных строках можно экранировать литералы, но многострочные строки в Kotlin не поддерживают
экранирование обратным слешом. Чтобы включить знаки доллара (`$`) как литеральные символы,
нужно использовать конструкцию `${'$'}`, предотвращающую строковую интерполяцию.
Такой подход может ухудшать читаемость кода, особенно когда строки содержат несколько знаков доллара.

<!-- Multi-dollar string interpolation simplifies this by letting you treat dollar signs as literal characters in both single-line and multiline strings.
For example: -->
Многодолларовая строковая интерполяция упрощает это, позволяя рассматривать знаки доллара как литеральные символы
и в однострочных, и в многострочных строках. Например:

```kotlin
val KClass<*>.jsonSchema : String
    get() = $$"""
    {
      "$schema": "https://json-schema.org/draft/2020-12/schema",
      "$id": "https://example.com/product.schema.json",
      "$dynamicAnchor": "meta",
      "title": "$${simpleName ?: qualifiedName ?: "unknown"}",
      "type": "object"
    }
    """
```

<!-- Here, the `$$` prefix specifies that two consecutive dollar signs are required to trigger string interpolation.
Single dollar signs remain as literal characters. -->
Здесь префикс `$$` указывает, что для запуска строковой интерполяции требуются два последовательных знака доллара.
Одиночные знаки доллара остаются литеральными символами.

<!-- You can adjust how many dollar signs trigger interpolation.
For example, using three consecutive dollar signs (`$$$`) allows `$` and `$$` to remain as literals
while enabling interpolation with `$$$`: -->
Можно настроить, сколько знаков доллара запускает интерполяцию. Например, три последовательных знака доллара (`$$$`)
позволяют `$` и `$$` оставаться литералами, а интерполяцию включают с помощью `$$$`:

```kotlin
val productName = "carrot"
val requestedData =
    $$$"""{
      "currency": "$",
      "enteredAmount": "42.45 $$",
      "$$serviceField": "none",
      "product": "$$$productName"
    }
    """

println(requestedData)
//{
//    "currency": "$",
//    "enteredAmount": "42.45 $$",
//    "$$serviceField": "none",
//    "product": "carrot"
//}
```

<!-- Here, the `$$$` prefix allows the string to include `$` and `$$` without requiring the `${'$'}` construct for escaping. -->
Здесь префикс `$$$` позволяет строке включать `$` и `$$` без конструкции `${'$'}` для экранирования.

<!-- Multi-dollar string interpolation doesn't affect existing code that uses single-dollar string interpolation.
You can continue using a single `$` as before and apply multi-dollar signs when you need to handle literal dollar signs in strings. -->
Многодолларовая строковая интерполяция не влияет на существующий код, использующий обычную интерполяцию
с одним знаком доллара. Вы можете продолжать использовать один `$` как раньше и применять несколько знаков доллара,
когда нужно обрабатывать литеральные знаки доллара в строках.

<a name="string-formatting"></a>
<!-- ### String formatting -->
### Форматирование строк

<!-- String formatting with the `String.format()` function is only available in Kotlin/JVM. -->
> Форматирование строк с помощью функции `String.format()` доступно только в Kotlin/JVM.

<!-- To format a string to your specific requirements, use the `String.format()` function. -->
Чтобы отформатировать строку под конкретные требования, используйте функцию
[`String.format()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/format.html).

<!-- The `String.format()` function accepts a format string and one or more arguments. The format string contains one placeholder
(indicated by `%`) for a given argument, followed by format specifiers.
Format specifiers are formatting instructions for the respective argument, consisting of flags, width, precision, and
conversion type. Collectively, format specifiers shape the output's formatting. Common format specifiers include
`%d` for integers, `%f` for floating-point numbers, and `%s` for strings. You can also use the `argument_index$` syntax
to reference the same argument multiple times within the format string in different formats. -->
Функция `String.format()` принимает строку формата и один или несколько аргументов. Строка формата содержит
плейсхолдер для заданного аргумента (обозначается `%`), за которым следуют спецификаторы формата.
Спецификаторы формата — это инструкции форматирования для соответствующего аргумента; они состоят из флагов,
ширины, точности и типа преобразования. Вместе спецификаторы формата задают вид вывода. Часто используются
спецификаторы `%d` для целых чисел, `%f` для чисел с плавающей точкой и `%s` для строк. Также можно использовать
синтаксис `argument_index$`, чтобы несколько раз ссылаться на один и тот же аргумент в строке формата
в разных форматах.

<!-- For a detailed understanding and an extensive list of format specifiers, see Java's Class Formatter documentation. -->
> Подробное описание и полный список спецификаторов формата см. в документации Java
> [Class Formatter](https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html#summary).

<!-- Let's look at an example: -->
Рассмотрим пример:

```kotlin
fun main() {
    // Форматирует целое число, добавляя ведущие нули до длины в семь символов
    val integerNumber = String.format("%07d", 31416)
    println(integerNumber)
    // 0031416

    // Форматирует число с плавающей точкой со знаком + и четырьмя знаками после запятой
    val floatNumber = String.format("%+.4f", 3.141592)
    println(floatNumber)
    // +3.1416

    // Форматирует две строки в верхнем регистре, каждая занимает один плейсхолдер
    val helloString = String.format("%S %S", "hello", "world")
    println(helloString)
    // HELLO WORLD

    // Форматирует отрицательное число в скобках, затем повторяет это же число
    // в другом формате (без скобок), используя `argument_index$`.
    val negativeNumberInParentheses = String.format("%(d means %1\$d", -31416)
    println(negativeNumberInParentheses)
    // (31416) means -31416
}
```

<!-- The `String.format()` function provides similar functionality to string templates. However, the
`String.format()` function is more versatile because there are more formatting options available. -->
Функция `String.format()` предоставляет возможности, похожие на строковые шаблоны. Однако `String.format()`
более гибкая, потому что поддерживает больше вариантов форматирования.

<!-- In addition, you can assign the format string from a variable. This can be useful when the format string changes,
for example, in localization cases that depend on the user locale. -->
Кроме того, строку формата можно присваивать из переменной. Это полезно, когда строка формата меняется,
например в сценариях локализации, зависящих от локали пользователя.

<!-- Be careful when using the `String.format()` function because it can be easy to mismatch the number or position of the
arguments with their corresponding placeholders. -->
Будьте осторожны при использовании `String.format()`: в ней легко ошибиться с количеством или позицией аргументов
относительно соответствующих плейсхолдеров.

<a name="arrays"></a>
<!-- ## Arrays -->
## Массивы

<!-- An array is a data structure that holds a fixed number of values of the same type or its subtypes.
The most common type of array in Kotlin is the object-type array, represented by the `Array` class. -->
Массив — это структура данных, которая хранит фиксированное количество значений одного типа или его подтипов.
Самый распространённый тип массива в Kotlin — массив объектного типа, представленный классом
[`Array`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-array/).

<!-- If you use primitives in an object-type array, this has a performance impact because your primitives are boxed into objects.
To avoid boxing overhead, use primitive-type arrays instead. -->
> Если вы используете примитивы в массиве объектного типа, это влияет на производительность,
> потому что примитивы [упаковываются](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html) в объекты.
> Чтобы избежать накладных расходов на упаковку, используйте [массивы примитивных типов](#primitive-type-arrays).

<a name="when-to-use-arrays"></a>
<!-- ### When to use arrays -->
### Когда использовать массивы

<!-- Use arrays in Kotlin when you have specialized low-level requirements that you need to meet.
For example, if you have performance requirements beyond what is needed for regular applications, or you need to build custom data structures.
If you don't have these sorts of restrictions, use collections instead. -->
Используйте массивы в Kotlin, когда нужно выполнить специализированные низкоуровневые требования.
Например, если у вас есть требования к производительности, выходящие за рамки обычных приложений,
или если вам нужно создавать пользовательские структуры данных. Если таких ограничений нет,
используйте [коллекции](collections-overview.html).

<!-- Collections have the following benefits compared to arrays: -->
У коллекций по сравнению с массивами есть следующие преимущества:

<!-- * Collections can be read-only, which gives you more control and allows you to write robust code that has a clear intent.
* It is easy to add or remove elements from collections. In comparison, arrays are fixed in size. The only way to
add or remove elements from an array is to create a new array each time, which is very inefficient: -->
* Коллекции могут быть read-only, что даёт больше контроля и помогает писать надёжный код с понятным намерением.
* В коллекции легко добавлять элементы и удалять их. Массивы, напротив, имеют фиксированный размер.
  Единственный способ добавить элемент в массив или удалить его — каждый раз создавать новый массив,
  что очень неэффективно:

```kotlin
fun main() {
    var riversArray = arrayOf("Nile", "Amazon", "Yangtze")

    // Операция присваивания += создаёт новый riversArray,
    // копирует исходные элементы и добавляет "Mississippi"
    riversArray += "Mississippi"
    println(riversArray.joinToString())
    // Nile, Amazon, Yangtze, Mississippi
}
```

<!-- * You can use the equality operator (`==`) to check if collections are structurally equal.
You can't use this operator for arrays. Instead, you have to use a special function, which you can read more about in Compare arrays. -->
* Для проверки структурного равенства коллекций можно использовать оператор равенства (`==`).
  Для массивов этот оператор использовать нельзя. Вместо этого нужна специальная функция,
  подробнее см. в разделе [Сравнение массивов](#compare-arrays).

<!-- For more information about collections, see Collections overview. -->
Подробнее о коллекциях см. в разделе [Обзор коллекций](collections-overview.html).

<a name="create-arrays"></a>
<!-- ### Create arrays -->
### Создание массивов

<!-- To create arrays in Kotlin, you can use:
* functions, such as `arrayOf()`, `arrayOfNulls()` or `emptyArray()`.
* the `Array` constructor. -->
Для создания массивов в Kotlin можно использовать:

* функции, такие как [`arrayOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/array-of.html),
  [`arrayOfNulls()`](<https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/array-of-nulls.html#kotlin$arrayOfNulls(kotlin.Int)>)
  или [`emptyArray()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/empty-array.html)
* конструктор `Array`

<!-- This example uses the `arrayOf()` function and passes item values to it: -->
В этом примере используется функция `arrayOf()`, которой передаются значения элементов:

```kotlin
fun main() {
    // Создаёт массив со значениями [1, 2, 3]
    val simpleArray = arrayOf(1, 2, 3)
    println(simpleArray.joinToString())
    // 1, 2, 3
}
```

<!-- This example uses the `arrayOfNulls()` function to create an array of a given size filled with `null` elements: -->
В этом примере функция `arrayOfNulls()` создаёт массив заданного размера, заполненный элементами `null`:

```kotlin
fun main() {
    // Создаёт массив со значениями [null, null, null]
    val nullArray: Array<Int?> = arrayOfNulls(3)
    println(nullArray.joinToString())
    // null, null, null
}
```

<!-- This example uses the `emptyArray()` function to create an empty array: -->
В этом примере функция `emptyArray()` создаёт пустой массив:

```kotlin
var exampleArray = emptyArray<String>()
```

<!-- You can specify the type of the empty array on the left-hand or right-hand side of the assignment due to Kotlin's type inference. -->
> Благодаря выводу типов Kotlin тип пустого массива можно указать слева или справа от присваивания.
>
> Например:
>
> ```kotlin
> var exampleArray = emptyArray<String>()
>
> var exampleArray: Array<String> = emptyArray()
> ```

<!-- The `Array` constructor takes the array size and a function that returns values for array elements given its index: -->
Конструктор `Array` принимает размер массива и функцию, которая возвращает значения элементов массива по их индексу:

```kotlin
fun main() {
    // Создаёт Array<Int>, инициализированный нулями [0, 0, 0]
    val initArray = Array<Int>(3) { 0 }
    println(initArray.joinToString())
    // 0, 0, 0

    // Создаёт Array<String> со значениями ["0", "1", "4", "9", "16"]
    val asc = Array(5) { i -> (i * i).toString() }
    asc.forEach { print(it) }
    // 014916
}
```

<!-- Like in most programming languages, indices start from 0 in Kotlin. -->
> Как и в большинстве языков программирования, индексы в Kotlin начинаются с 0.

<a name="nested-arrays"></a>
<!-- #### Nested arrays -->
#### Вложенные массивы

<!-- Arrays can be nested within each other to create multidimensional arrays: -->
Массивы можно вкладывать друг в друга, создавая многомерные массивы:

```kotlin
fun main() {
    // Создаёт двумерный массив
    val twoDArray = Array(2) { Array<Int>(2) { 0 } }
    println(twoDArray.contentDeepToString())
    // [[0, 0], [0, 0]]

    // Создаёт трёхмерный массив
    val threeDArray = Array(3) { Array(3) { Array<Int>(3) { 0 } } }
    println(threeDArray.contentDeepToString())
    // [[[0, 0, 0], [0, 0, 0], [0, 0, 0]], [[0, 0, 0], [0, 0, 0], [0, 0, 0]], [[0, 0, 0], [0, 0, 0], [0, 0, 0]]]
}
```

<!-- Nested arrays don't have to be the same type or the same size. -->
> Вложенные массивы не обязаны иметь один и тот же тип или размер.

<a name="access-and-modify-elements"></a>
<!-- ### Access and modify elements -->
### Доступ к элементам и их изменение

<!-- Arrays are always mutable. To access and modify elements in an array, use the indexed access operator `[]`: -->
Массивы всегда изменяемы. Чтобы обращаться к элементам массива и изменять их, используйте
[оператор индексированного доступа](operator-overloading.html#indexed-access-operator) `[]`:

```kotlin
fun main() {
    val simpleArray = arrayOf(1, 2, 3)
    val twoDArray = Array(2) { Array<Int>(2) { 0 } }

    // Обращается к элементу и изменяет его
    simpleArray[0] = 10
    twoDArray[0][0] = 2

    // Выводит изменённый элемент
    println(simpleArray[0].toString()) // 10
    println(twoDArray[0][0].toString()) // 2
}
```

<!-- Arrays in Kotlin are invariant. This means that Kotlin doesn't allow you to assign an `Array<String>`
to an `Array<Any>` to prevent a possible runtime failure. Instead, you can use `Array<out Any>`.
For more information, see Type Projections. -->
Массивы в Kotlin *инвариантны*. Это значит, что Kotlin не позволяет присвоить `Array<String>`
переменной типа `Array<Any>`, чтобы предотвратить возможный сбой во время выполнения.
Вместо этого можно использовать `Array<out Any>`. Подробнее см. в разделе
[Проекции типов](generics.html#type-projections).

<a name="work-with-arrays"></a>
<!-- ### Work with arrays -->
### Работа с массивами

<!-- In Kotlin, you can work with arrays by using them to pass a variable number of arguments to a function or perform operations
on the arrays themselves. For example, comparing arrays, transforming their contents or converting them to collections. -->
В Kotlin массивы можно использовать для передачи переменного количества аргументов в функцию
или выполнять операции с самими массивами: например, сравнивать массивы, преобразовывать их содержимое
или преобразовывать их в коллекции.

<a name="pass-variable-number-of-arguments-to-a-function"></a>
<!-- #### Pass variable number of arguments to a function -->
#### Передача переменного количества аргументов в функцию

<!-- In Kotlin, you can pass a variable number of arguments to a function via the `vararg` parameter.
This is useful when you don't know the number of arguments in advance, like when formatting a message or creating an SQL query. -->
В Kotlin можно передать в функцию переменное количество аргументов с помощью параметра
[`vararg`](functions.html#variable-number-of-arguments-varargs). Это полезно, когда количество аргументов
заранее неизвестно, например при форматировании сообщения или создании SQL-запроса.

<!-- To pass an array containing a variable number of arguments to a function, use the spread operator (`*`).
The spread operator passes each element of the array as individual arguments to your chosen function: -->
Чтобы передать в функцию массив с переменным количеством аргументов, используйте оператор распаковки (`*`).
Он передаёт каждый элемент массива как отдельный аргумент выбранной функции:

```kotlin
fun main() {
    val lettersArray = arrayOf("c", "d")
    printAllStrings("a", "b", *lettersArray)
    // abcd
}

fun printAllStrings(vararg strings: String) {
    for (string in strings) {
        print(string)
    }
}
```

<!-- For more information, see Variable number of arguments (varargs). -->
Подробнее см. в разделе [Переменное количество аргументов (varargs)](functions.html#variable-number-of-arguments-varargs).

<a name="compare-arrays"></a>
<!-- #### Compare arrays -->
#### Сравнение массивов

<!-- To compare whether two arrays have the same elements in the same order, use the `.contentEquals()` and `.contentDeepEquals()` functions: -->
Чтобы проверить, содержат ли два массива одинаковые элементы в одинаковом порядке, используйте функции
[`.contentEquals()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/content-equals.html)
и [`.contentDeepEquals()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/content-deep-equals.html):

```kotlin
fun main() {
    val simpleArray = arrayOf(1, 2, 3)
    val anotherArray = arrayOf(1, 2, 3)

    // Сравнивает содержимое массивов
    println(simpleArray.contentEquals(anotherArray))
    // true

    // Используя инфиксную запись, сравнивает содержимое массивов
    // после изменения элемента
    simpleArray[0] = 10
    println(simpleArray contentEquals anotherArray)
    // false
}
```

<!-- Don't use equality (`==`) and inequality (`!=`) operators to compare the contents of arrays.
These operators check whether the assigned variables point to the same object.
To learn more about why arrays in Kotlin behave this way, see our blog post. -->
> Не используйте операторы равенства (`==`) и неравенства (`!=`) для сравнения содержимого массивов.
> Эти операторы проверяют, указывают ли присвоенные переменные на один и тот же объект.
> Подробнее о причинах такого поведения массивов в Kotlin см. в
> [публикации блога](https://blog.jetbrains.com/kotlin/2015/09/feedback-request-limitations-on-data-classes/#Appendix.Comparingarrays).

<a name="transform-arrays"></a>
<!-- #### Transform arrays -->
#### Преобразование массивов

<!-- Kotlin has many useful functions to transform arrays. This document highlights a few but this isn't an exhaustive list.
For the full list of functions, see our API reference. -->
В Kotlin есть много полезных функций для преобразования массивов. В этом документе выделены несколько из них,
но это не полный список. Полный список функций см. в [справочнике API](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-array/).

<a name="sum"></a>
<!-- ##### Sum -->
##### Сумма

<!-- To return the sum of all elements in an array, use the `.sum()` function: -->
Чтобы вернуть сумму всех элементов массива, используйте функцию
[`.sum()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sum.html):

```kotlin
fun main() {
    val sumArray = arrayOf(1, 2, 3)

    // Суммирует элементы массива
    println(sumArray.sum())
    // 6
}
```

<!-- The `.sum()` function can only be used with arrays of numeric data types, such as `Int`. -->
> Функцию `.sum()` можно использовать только с массивами числовых типов данных, например `Int`.

<a name="shuffle"></a>
<!-- ##### Shuffle -->
##### Перемешивание

<!-- To randomly shuffle the elements in an array, use the `.shuffle()` function: -->
Чтобы случайным образом перемешать элементы массива, используйте функцию
[`.shuffle()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/shuffle.html):

```kotlin
fun main() {
    val simpleArray = arrayOf(1, 2, 3)

    // Перемешивает элементы [3, 2, 1]
    simpleArray.shuffle()
    println(simpleArray.joinToString())

    // Снова перемешивает элементы [2, 3, 1]
    simpleArray.shuffle()
    println(simpleArray.joinToString())
}
```

<a name="convert-arrays-to-collections"></a>
<!-- #### Convert arrays to collections -->
#### Преобразование массивов в коллекции

<!-- If you work with different APIs where some use arrays and some use collections, then you can convert your arrays to collections and vice versa. -->
Если вы работаете с разными API, где одни используют массивы, а другие — коллекции, вы можете преобразовывать
массивы в [коллекции](collections-overview.html) и обратно.

<a name="convert-to-list-or-set"></a>
<!-- ##### Convert to List or Set -->
##### Преобразование в List или Set

<!-- To convert an array to a `List` or `Set`, use the `.toList()` and `.toSet()` functions. -->
Чтобы преобразовать массив в `List` или `Set`, используйте функции
[`.toList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-list.html)
и [`.toSet()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-set.html).

```kotlin
fun main() {
    val simpleArray = arrayOf("a", "b", "c", "c")

    // Преобразует в Set
    println(simpleArray.toSet())
    // [a, b, c]

    // Преобразует в List
    println(simpleArray.toList())
    // [a, b, c, c]
}
```

<a name="convert-to-map"></a>
<!-- ##### Convert to Map -->
##### Преобразование в Map

<!-- To convert an array to a `Map`, use the `.toMap()` function.
Only an array of `Pair<K,V>` can be converted to a `Map`.
The first value of a `Pair` instance becomes a key, and the second becomes a value.
This example uses the infix notation to call the `to` function to create tuples of `Pair`: -->
Чтобы преобразовать массив в `Map`, используйте функцию
[`.toMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-map.html).
В `Map` можно преобразовать только массив [`Pair<K,V>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-pair/).
Первое значение экземпляра `Pair` становится ключом, а второе — значением.
В этом примере используется [инфиксная запись](functions.html#infix-notation) для вызова функции
[`to`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/to.html), создающей кортежи `Pair`:

```kotlin
fun main() {
    val pairArray = arrayOf("apple" to 120, "banana" to 150, "cherry" to 90, "apple" to 140)

    // Преобразует в Map
    // Ключи — фрукты, значения — количество калорий
    // Обратите внимание: ключи должны быть уникальными, поэтому последнее значение "apple"
    // перезаписывает первое
    println(pairArray.toMap())
    // {apple=140, banana=150, cherry=90}
}
```

<a name="primitive-type-arrays"></a>
<!-- ### Primitive-type arrays -->
### Массивы примитивных типов

<!-- If you use the `Array` class with primitive values, these values are boxed into objects.
As an alternative, you can use primitive-type arrays, which allow you to store primitives in an array without the
side effect of boxing overhead: -->
Если использовать класс `Array` с примитивными значениями, эти значения упаковываются в объекты.
В качестве альтернативы можно использовать массивы примитивных типов, которые позволяют хранить примитивы
в массиве без побочного эффекта в виде накладных расходов на упаковку:

<!-- Primitive-type array, Equivalent in Java -->
| Массив примитивного типа | Эквивалент в Java |
|--------------------------|-------------------|
| [`BooleanArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-boolean-array/) | `boolean[]` |
| [`ByteArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-byte-array/) | `byte[]` |
| [`CharArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-char-array/) | `char[]` |
| [`DoubleArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-double-array/) | `double[]` |
| [`FloatArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-float-array/) | `float[]` |
| [`IntArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-int-array/) | `int[]` |
| [`LongArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-long-array/) | `long[]` |
| [`ShortArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-short-array/) | `short[]` |

<!-- These classes have no inheritance relation to the `Array` class, but they have the same set of functions and properties. -->
Эти классы не связаны наследованием с классом `Array`, но имеют тот же набор функций и свойств.

<!-- This example creates an instance of the `IntArray` class: -->
В этом примере создаётся экземпляр класса `IntArray`:

```kotlin
fun main() {
    // Создаёт массив Int размером 5 со значениями, инициализированными нулями
    val exampleArray = IntArray(5)
    println(exampleArray.joinToString())
    // 0, 0, 0, 0, 0
}
```

<!-- To convert primitive-type arrays to object-type arrays, use the `.toTypedArray()` function.
To convert object-type arrays to primitive-type arrays, use `.toBooleanArray()`, `.toByteArray()`, `.toCharArray()`, and so on. -->
> Чтобы преобразовать массивы примитивных типов в массивы объектных типов, используйте функцию
> [`.toTypedArray()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-typed-array.html).
>
> Чтобы преобразовать массивы объектных типов в массивы примитивных типов, используйте
> [`.toBooleanArray()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-boolean-array.html),
> [`.toByteArray()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-byte-array.html),
> [`.toCharArray()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-char-array.html) и так далее.

<a name="whats-next"></a>
<!-- ## What's next? -->
## Что дальше

<!-- To learn more about why we recommend using collections for most use cases, read Collections overview.
If you are a Java developer, read our Java to Kotlin migration guide for Collections. -->
* Чтобы подробнее узнать, почему для большинства случаев рекомендуется использовать коллекции, прочитайте
  [Обзор коллекций](collections-overview.html).
* Если вы Java-разработчик, прочитайте руководство по миграции с Java на Kotlin для
  [коллекций](https://kotlinlang.org/docs/java-to-kotlin-collections-guide.html).
