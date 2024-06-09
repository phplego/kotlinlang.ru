---
type: doc
layout: reference
category: "Syntax"
title: "Основные типы"
url: https://kotlinlang.ru/docs/basic-types.html
---

<!-- При переводе статьи оригинальная версия была от 25 October 2021 -->

<!-- Basic types -->
# Основные типы

<!-- In Kotlin, everything is an object in the sense that we can call member functions and properties on any variable.
Some types can have a special internal representation - for example, numbers, characters and booleans can be
represented as primitive values at runtime - but to the user they look like ordinary classes. 
In this section we describe the basic types used in Kotlin: [numbers](#numbers), [booleans](#booleans), 
[characters](#characters), [strings](#strings), and [arrays](#arrays). -->
В Kotlin всё является объектом, в том смысле, что пользователь может вызвать функцию или получить доступ
к свойству любой переменной. Некоторые типы являются встроенными, т.к. их реализация оптимизирована,
хотя для пользователя они выглядят как обычные классы. В данном разделе описываются основные типы:
[числа](#numbers), [логические переменные](#booleans), [символы](#characters), [строки](#strings)
и [массивы](#arrays).

<a name="numbers"></a>
<!-- ## Numbers -->
## Числа

<a name="integer-types"></a>
<!-- ### Integer types -->
### Целочисленные типы

<!-- Kotlin provides a set of built-in types that represent numbers.  
For integer numbers, there are four types with different sizes and, hence, value ranges. -->
В Kotlin есть набор встроенных типов, которые представляют числа.
Для целых чисел существует четыре типа с разными размерами и, следовательно, разными диапазонами значений.

<!-- Type, Size (bits), Min value, Max value -->
| Тип     | Размер (биты) | Минимальное значение             | Максимальное значение                          |
|---------|---------------|----------------------------------|------------------------------------------------|
| `Byte`  | 8             | -128                             | 127                                            |
| `Short` | 16            | -32768                           | 32767                                          |
| `Int`   | 32            | -2,147,483,648 (-2<sup>31</sup>) | 2,147,483,647 (2<sup>31</sup> - 1)             |
| `Long`  | 64            | -9,223,372,036,854,775,808 (-2<sup>63</sup>) | 9,223,372,036,854,775,807 (2<sup>63</sup> - 1) |

<!-- All variables initialized with integer values not exceeding the maximum value of `Int`
have the inferred type `Int`. If the initial value exceeds this value, then the type is `Long`.
To specify the `Long` value explicitly, append the suffix `L` to the value. -->
Все переменные, инициализированные целыми значениями, не превышающими максимальное значение `Int`,
имеют предполагаемый тип `Int`. Если начальное значение превышает это значение, то тип `Long`.
Чтобы явно указать тип `Long`, добавьте после значения `L`.

```kotlin
val one = 1 // Int
val threeBillion = 3000000000 // Long
val oneLong = 1L // Long
val oneByte: Byte = 1
```

<a name="floating-point-types"></a>
<!-- ### Floating-point types -->
### Типы с плавающей точкой

<!-- For real numbers, Kotlin provides floating-point types `Float` and `Double`.
According to the [IEEE 754 standard](https://en.wikipedia.org/wiki/IEEE_754),
floating point types differ by their _decimal place_, that is, how many decimal digits they can store.
`Float` reflects the IEEE 754 _single precision_, while `Double` provides _double precision_. -->
Для действительных чисел в Kotlin есть типы с плавающей точкой `Float` и `Double`.
Согласно [стандарту IEEE 754](https://en.wikipedia.org/wiki/IEEE_754), типы с плавающей точкой
различаются своим *десятичным разрядом*, то есть количеством десятичных цифр, которые они могут хранить.
С точки зрения IEEE 754 `Float` является одинарно точным, а `Double` обеспечивает двойную точность.

<!-- Type, Size (bits), Significant bits, Exponent bits, Decimal digits -->
| Тип      | Размер (биты) | Значимые биты | Биты экспоненты | Разряды |
|----------|---------------|---------------|-----------------|---------|
| `Float`  | 32            | 24            | 8               | 6-7     |
| `Double` | 64            | 53            | 11              | 15-16   |

<!-- You can initialize `Double` and `Float` variables  with numbers having a fractional part.
It's separated from the integer part by a period (`.`) 
For variables initialized with fractional numbers, the compiler infers the `Double` type. -->
Вы можете инициализировать переменные `Double` и `Float` числами, имеющими дробную часть.
Она должна быть отделена от целой части точкой (`.`).
Для переменных, инициализированных дробными числами, компилятор автоматически определяет тип `Double`.

```kotlin
val pi = 3.14 // Double
// val one: Double = 1 // Ошибка: несоответствие типов
val oneDouble = 1.0 // Double
```

<!-- To explicitly specify the `Float` type for a value, add the suffix `f` or `F`.
If such a value contains more than 6-7 decimal digits, it will be rounded. -->
Чтобы явно указать тип `Float`, добавьте после значения `f` или `F`.
Если такое значение содержит более 6-7 разрядов, оно будет округлено.

```kotlin
val e = 2.7182818284 // Double
val eFloat = 2.7182818284f // Float, фактическое значение 2.7182817
```

<!-- Note that unlike some other languages, there are no implicit widening conversions for numbers in Kotlin.
For example, a function with a `Double` parameter can be called only on `Double` values, but not `Float`, 
`Int`, or other numeric values. -->
Обратите внимание, что в отличие от некоторых других языков, в Kotlin нет неявных преобразований для чисел.
Например, функция с `Double` параметром может вызываться только для `Double`, но не для `Float`,
`Int` или других числовых значений.

```kotlin
fun main() {
    fun printDouble(d: Double) { print(d) }

    val i = 1    
    val d = 1.0
    val f = 1.0f 

    printDouble(d)
//  printDouble(i) // Ошибка: несоответствие типов
//  printDouble(f) // Ошибка: несоответствие типов
}
```

<!-- To convert numeric values to different types, use [Explicit conversions](#explicit-conversions). -->
Чтобы преобразовать числовые значения в различные типы, используйте [Явные преобразования](#explicit-conversions).

<a name="literal-constants"></a>
<!-- ### Literal constants -->
### Символьные постоянные

<!-- There are the following kinds of literal constants for integral values: -->
В языке Kotlin присутствуют следующие виды символьных постоянных (констант) для целых значений:

<!-- * Decimals: `123`
  * Longs are tagged by a capital `L`: `123L`
* Hexadecimals: `0x0F`
* Binaries: `0b00001011` -->
* Десятичные числа: `123`
  * Тип `Long` обозначается заглавной `L`: `123L`
* Шестнадцатеричные числа: `0x0F`
* Двоичные числа: `0b00001011`

<!-- >Octal literals are not supported. -->
> ВНИМАНИЕ: Восьмеричные литералы не поддерживаются.

<!-- Kotlin also supports a conventional notation for floating-point numbers: -->
Также Kotlin поддерживает числа с плавающей запятой:

<!-- * Doubles by default: `123.5`, `123.5e10`
* Floats are tagged by `f` or `F`: `123.5f` --> 
* Тип `Double` по умолчанию: `123.5`, `123.5e10`
* Тип `Float` обозначается с помощью `f` или `F`: `123.5f` 

<!-- You can use underscores to make number constants more readable: -->
Вы можете использовать нижние подчеркивания, чтобы сделать числовые константы более читаемыми:

```kotlin
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```

<a name="numbers-representation-on-the-jvm"></a>
<!-- ### Numbers representation on the JVM -->
### Представление чисел в JVM

<!-- On the JVM platform, numbers are stored as primitive types: `int`, `double`, and so on. 
Exceptions are cases when you create a nullable number reference such as `Int?` or use generics.
In these cases numbers are boxed in Java classes `Integer`, `Double`, and so on. -->
Обычно платформа JVM хранит числа в виде примитивных типов: `int`, `double` и так далее.
Если же вам необходима ссылка, которая может принимать значение null (например, `Int?`),
то используйте обёртки. В этих случаях числа помещаются в Java классы как `Integer`, `Double` и так далее.

<!-- Note that nullable references to the same number can be different objects: -->
Обратите внимание, что использование обёрток для одного и того же числа не гарантирует равенства ссылок на них.

```kotlin
val a: Int = 100
val boxedA: Int? = a
val anotherBoxedA: Int? = a

val b: Int = 10000
val boxedB: Int? = b
val anotherBoxedB: Int? = b

println(boxedA === anotherBoxedA) // true
println(boxedB === anotherBoxedB) // false
```

<!-- All nullable references to `a` are actually the same object because of the memory optimization that JVM applies to `Integer`s
between `-128` and `127`. It doesn't apply to the `b` references, so they are different objects. -->
Все nullable-ссылки на `a` на самом деле являются одним и тем же объектом из-за оптимизации памяти,
которую JVM применяет к `Integer` между "-128" и "127". Но `b` больше этих значений,
поэтому ссылки на `b` являются разными объектами.

<!-- On the other hand, they are still equal: -->
Однако, равенство по значению сохраняется.

```kotlin
val b: Int = 10000
println(b == b) // Prints 'true'
val boxedB: Int? = b
val anotherBoxedB: Int? = b
println(boxedB == anotherBoxedB) // Prints 'true'
```

<a name="explicit-conversions"></a>
<!-- ### Explicit conversions -->
### Явные преобразования

<!-- Due to different representations, smaller types *are not subtypes* of bigger ones.
If they were, we would have troubles of the following sort: -->
Из-за разницы в представлениях меньшие типы *не являются подтипами* бОльших типов.
В противном случае возникли бы сложности.

```kotlin
// Возможный код, который на самом деле не скомпилируется:
val a: Int? = 1 // "Обёрнутый" Int (java.lang.Integer)
val b: Long? = a // неявное преобразование возвращает "обёрнутый" Long (java.lang.Long)
print(b == a) // Внимание! Данное выражение выведет "false" т. к. метод equals() типа Long предполагает, что вторая часть выражения также имеет тип Long
```

<!-- So equality would have been lost silently, not to mention identity. -->
Таким образом, будет утрачена не только тождественность (равенство по ссылке), но и равенство по значению.

<!-- As a consequence, smaller types _are NOT implicitly converted_ to bigger types.
This means that assigning a value of type `Byte` to an `Int` variable requires an explicit conversion. -->
Как следствие, неявное преобразование меньших типов в большие НЕ происходит.
Это значит, что мы не можем присвоить значение типа `Byte` переменной типа `Int` без явного преобразования.

```kotlin
val b: Byte = 1 // всё хорошо, литералы проверяются статически
// val i: Int = b // ОШИБКА
val i1: Int = b.toInt()
```

<!-- All number types support conversions to other types: -->
Каждый численный тип поддерживает следующие преобразования:

* `toByte(): Byte`
* `toShort(): Short`
* `toInt(): Int`
* `toLong(): Long`
* `toFloat(): Float`
* `toDouble(): Double`
* `toChar(): Char`

<!-- In many cases, there is no need for explicit conversions because the type is inferred from the context,
and arithmetical operations are overloaded for appropriate conversions, for example: -->
Часто необходимости в явных преобразованиях нет, поскольку тип выводится из контекста,
а арифметические действия перегружаются для подходящих преобразований.

```kotlin
val l = 1L + 3 // Long + Int => Long
```

<a name="operations"></a>
<!-- ### Operations -->
### Операции

<!-- Kotlin supports the standard set of arithmetical operations over numbers: `+`, `-`, `*`, `/`, `%`. They are declared 
as members of appropriate classes. -->
Котлин поддерживает стандартный набор арифметических операций над числами: `+`, `-`, `*`, `/`, `%`.
Они объявляются членами соответствующих классов.

```kotlin
println(1 + 2)
println(2_500_000_000L - 1L)
println(3.14 * 2.71)
println(10.0 / 3)
```

<!-- You can also override these operators for custom classes. See [Operator overloading](operator-overloading.md) for details. -->
Вы также можете переопределить эти операторы для пользовательских классов. См. [Перегрузка операторов](operator-overloading.html) для деталей.

<a name="division-of-integers"></a>
<!-- #### Division of integers -->
#### Деление целых чисел

<!-- Division between integers numbers always returns an integer number. Any fractional part is discarded. -->
Деление целых чисел всегда возвращает целое число. Любая дробная часть отбрасывается.

```kotlin
val x = 5 / 2
// println(x == 2.5) // ОШИБКА: Оператор '==' не может быть применен к 'Int' и 'Double'
println(x == 2) // true
```

<!-- This is true for a division between any two integer types. -->
Это справедливо для деления любых двух целочисленных типов.

```kotlin
val x = 5L / 2
println(x == 2L) // true
```

<!-- To return a floating-point type, explicitly convert one of the arguments to a floating-point type. -->
Чтобы вернуть тип с плавающей точкой, явно преобразуйте один из аргументов в тип с плавающей точкой.

```kotlin
val x = 5 / 2.toDouble()
println(x == 2.5) // true
```

<a name="bitwise-operations"></a>
<!-- #### Bitwise operations  -->
#### Побитовые операции

<!-- Kotlin provides a set of _bitwise operations_ on integer numbers. They operate on the binary level directly with 
bits of the numbers' representation.
Bitwise operations are represented by functions that can be called in infix form. They can be applied only to `Int` and `Long`. -->
Kotlin поддерживает обычный набор *побитовых операций* над целыми числами.
Они работают на двоичном уровне непосредственно с битовыми представлениями чисел.
Побитовые операции представлены функциями, которые могут быть вызваны в инфиксной форме.
Они могут быть применены только к `Int` и `Long`.

```kotlin
val x = (1 shl 2) and 0x000FF000
```

<!-- Here is the complete list of bitwise operations: -->
Ниже приведён полный список битовых операций:

* `shl(bits)` – сдвиг влево с учётом знака (`<<` в Java)
* `shr(bits)` – сдвиг вправо с учётом знака (`>>` в Java)
* `ushr(bits)` – сдвиг вправо без учёта знака (`>>>` в Java)
* `and(bits)` – побитовое И
* `or(bits)` – побитовое ИЛИ
* `xor(bits)` – побитовое исключающее ИЛИ
* `inv()` – побитовое отрицание

<a name="floating-point-numbers-comparison"></a>
<!-- ### Floating-point numbers comparison -->
### Сравнение чисел с плавающей точкой

<!-- The operations on floating-point numbers discussed in this section are: -->
В этом разделе обсуждаются следующие операции над числами с плавающей запятой:

<!-- * Equality checks: `a == b` and `a != b`
* Comparison operators: `a < b`, `a > b`, `a <= b`, `a >= b`
* Range instantiation and range checks: `a..b`, `x in a..b`, `x !in a..b` -->
* Проверки на равенство: `a == b` и `a != b`
* Операторы сравнения: `a < b`, `a > b`, `a <= b`, `a >= b`
* Создание диапазона и проверка диапазона: `a..b`, `x in a..b`, `x !in a..b`

<!-- When the operands `a` and `b` are statically known to be `Float` or `Double` or their nullable counterparts (the type is 
declared or inferred or is a result of a [smart cast](typecasts.md#smart-casts)), the operations on the 
numbers and the range that they form follow the [IEEE 754 Standard for Floating-Point Arithmetic](https://en.wikipedia.org/wiki/IEEE_754). -->
Когда статически известно, что операнды `a` и `b` являются `Float` или `Double`
или их аналогами с nullable-значением (тип объявлен или является результатом
[умного приведения](typecasts.html#smart-casts)), операции с числами и диапазоном, который они образуют,
соответствуют стандарту [IEEE 754 для арифметики с плавающей точкой](https://en.wikipedia.org/wiki/IEEE_754).

<!-- However, to support generic use cases and provide total ordering, when the operands are **not** statically typed as 
floating point numbers (e.g. `Any`, `Comparable<...>`, a type parameter), the operations use the 
`equals` and `compareTo` implementations for `Float` and `Double`, which disagree with the standard, so that: -->
Однако для поддержки общих вариантов использования и обеспечения полного упорядочивания,
когда операнды статически *не* объявлены как числа с плавающей запятой (например, `Any`, `Comparable<...>`,
параметр типа), операции используют реализации `equals` и `compareTo` для `Float` и `Double`,
которые не согласуются со стандартом, так что:

<!-- * `NaN` is considered equal to itself
* `NaN` is considered greater than any other element including `POSITIVE_INFINITY`
* `-0.0` is considered less than `0.0` -->
* `NaN` считается равным самому себе
* `NaN` считается больше, чем любой другой элемент, включая "POSITIVE_INFINITY"
* `-0.0` считается меньше, чем `0.0`

<a name="unsigned-integers"></a>
<!-- ### Unsigned integers -->
### Целые беззнаковые числа

<!-- In addition to [integer types](#integer-types), Kotlin provides the following types for unsigned integer numbers: -->
В дополнение к [целочисленным типам](#integer-types), в Kotlin есть следующие типы целых беззнаковых чисел:

<!-- * `UByte`: an unsigned 8-bit integer, ranges from 0 to 255
* `UShort`: an unsigned 16-bit integer, ranges from 0 to 65535
* `UInt`: an unsigned 32-bit integer, ranges from 0 to 2^32 - 1
* `ULong`: an unsigned 64-bit integer, ranges from 0 to 2^64 - 1 -->
* `UByte`: беззнаковое 8-битное целое число, в диапазоне от 0 до 255
* `UShort`: беззнаковое 16-битное целое число, в диапазоне от 0 до 65535
* `UInt`: беззнаковое 32-битное целое число, в диапазоне от 0 до 2<sup>32</sup> - 1
* `ULong`: беззнаковое 64-битное целое число, в диапазоне от 0 до 2<sup>64</sup> - 1

<!-- Unsigned types support most of the operations of their signed counterparts. -->
Беззнаковые типы поддерживают большинство операций своих знаковых аналогов.

<!-- >Changing type from unsigned type to signed counterpart (and vice versa) is a *binary incompatible* change. -->
> Изменение типа с беззнакового типа на его знаковый аналог (и наоборот) является *двоично несовместимым* изменением.

<a name="unsigned-arrays-and-ranges"></a>
<!-- #### Unsigned arrays and ranges -->
#### Беззнаковые массивы и диапазоны

<!-- > Unsigned arrays and operations on them are in [Beta](components-stability.md). They can be changed incompatibly at any time.
> Opt-in is required (see the details below). -->
> Беззнаковые массивы и операции над ними находятся в [стадии бета-тестирования](components-stability.html).
> Они могут быть несовместимо изменены в любое время.

<!-- As with primitives, each unsigned type has a corresponding type that represents arrays of that type: -->
Как и в случае с примитивами, каждому типу без знака соответствует тип массивов знаковых типов:

<!-- * `UByteArray`: an array of unsigned bytes
* `UShortArray`: an array of unsigned shorts
* `UIntArray`: an array of unsigned ints
* `ULongArray`: an array of unsigned longs -->
* `UByteArray`: массив беззнаковых `byte`
* `UShortArray`: массив беззнаковых  `short`
* `UIntArray`: массив беззнаковых `int`
* `ULongArray`: массив беззнаковых `long`

<!-- Like signed integer arrays, they provide an API similar to the `Array` class without boxing overhead.  -->
Как и целочисленные массивы со знаком, такие массивы предоставляют API, аналогичный классу `Array`,
без дополнительных затрат на оборачивание.

<!-- When you use unsigned arrays, you'll get a warning that indicates that this feature is not stable yet.
To remove the warning, opt in using the `@ExperimentalUnsignedTypes` annotation. 
It's up to you to decide if your clients have to explicitly opt-in into usage of your API, but keep in mind that unsigned
array are not a stable feature, so an API which uses them can be broken by changes in the language.
[Learn more about opt-in requirements](opt-in-requirements.md). -->
При использовании массивов без знака вы получите предупреждение, что эта функция еще не стабильна.
Чтобы удалить предупреждение используйте аннотацию `@ExperimentalUnsignedTypes`. Вам решать,
должны ли ваши пользователи явно соглашаться на использование вашего API, но имейте в виду,
что беззнаковый массив не является стабильной функцией, поэтому API, который он использует,
может быть нарушен изменениями в языке. [Узнайте больше о требованиях регистрации](opt-in-requirements.html).

<!-- [Ranges and progressions](ranges.md) are supported for `UInt` and `ULong` by classes `UIntRange`,`UIntProgression`,
`ULongRange`, and `ULongProgression`. Together with the unsigned integer types, these classes are stable. -->
[Диапазоны и прогрессии](ranges.html) поддерживаются для `UInt` и `ULong`
классами `UIntRange`,`UIntProgression`, `ULongRange` и `ULongProgression`.
Вместе с целочисленными беззнаковыми типами эти классы стабильны.

<a name="literals"></a>
<!-- #### Literals -->
#### Литералы

<!-- To make unsigned integers easier to use, Kotlin provides an ability to tag an integer literal with a suffix
indicating a specific unsigned type (similarly to `Float` or `Long`): -->
Чтобы целые беззнаковые числа было легче использовать, в Kotlin можно помечать целочисленный литерал суффиксом,
указывающим на определенный беззнаковый тип (аналогично `Float` или `Long`):

<!-- * `u` and `U` tag unsigned literals. The exact type is determined based on the expected type.
If no expected type is provided, compiler will use `UInt` or `ULong` depending on the size of literal. -->
* `u` и `U` помечают беззнаковые литералы. Точный тип определяется на основе ожидаемого типа. Если
ожидаемый тип не указан, компилятор будет использовать `UInt` или `ULong` в зависимости от размера литерала.

```kotlin
val b: UByte = 1u  // UByte, есть ожидаемый тип
val s: UShort = 1u // UShort, есть ожидаемый тип
val l: ULong = 1u  // ULong, есть ожидаемый тип

val a1 = 42u // UInt: ожидаемого типа нет, константе подходит тип UInt
val a2 = 0xFFFF_FFFF_FFFFu // ULong: ожидаемого типа нет, тип UInt не подходит константе
```

<!-- * `uL` and `UL` explicitly tag literal as unsigned long. -->
* `uL` and `UL` явно помечают литерал как `unsigned long`.

```kotlin
val a = 1UL // ULong, даже несмотря на то, что ожидаемого типа нет и константа вписывается в UInt
```

<a name="further-discussion"></a>
<!-- #### Further discussion -->
#### Дальнейшее обсуждение

<!-- See [language proposal for unsigned types](https://github.com/Kotlin/KEEP/blob/master/proposals/unsigned-types.md)
for technical details and further discussion. -->
См. [предложения для беззнаковых типов](https://github.com/Kotlin/KEEP/blob/master/proposals/unsigned-types.md)
для технических деталей и дальнейшего обсуждения.

<a name="booleans"></a>
<!-- ## Booleans -->
## Логический тип

<!-- The type `Boolean` represents boolean objects that can have two values: `true` and `false`. -->
Тип `Boolean` представляет логический тип данных и принимает два значения: `true` и `false`.

<!-- `Boolean` has a nullable counterpart `Boolean?` that also has the `null` value. -->
При необходимости использования nullable-ссылок логические переменные оборачиваются `Boolean?`.

<!-- Built-in operations on booleans include: -->
Встроенные действия над логическими переменными включают:

* `||` – ленивое логическое *ИЛИ*
* `&&` – ленивое логическое *И*
* `!` – отрицание

```kotlin
val myTrue: Boolean = true
val myFalse: Boolean = false
val boolNull: Boolean? = null

println(myTrue || myFalse)
println(myTrue && myFalse)
println(!myTrue)
```

<!-- >**On JVM**: nullable references to boolean objects are boxed similarly to [numbers](#numbers-representation-on-the-jvm). -->
> В JVM: nullable-ссылки на логические объекты заключены в рамки аналогично числам.

<a name="characters"></a>
<!-- ## Characters -->
## Символы

<!-- Characters are represented by the type `Char`. Character literals go in single quotes: `'1'`. -->
Символы в Kotlin представлены типом `Char`. Символьные литералы заключаются в одинарные кавычки: `'1'`.

<!-- Special characters start from an escaping backslash `\`. 
The following escape sequences are supported: `\t`, `\b`, `\n`, `\r`, `\'`, `\"`, `\\` and `\$`. -->
Специальные символы начинаются с обратного слеша `\`.
Поддерживаются следующие escape-последовательности: `\t`, `\b`, `\n`, `\r`, `\'`, `\"`, `\\` и `\$`.

<!-- To encode any other character, use the Unicode escape sequence syntax: `'\uFF00'`. -->
Для кодирования любого другого символа используйте синтаксис escape-последовательности Юникода: `'\uFF00'`.

```kotlin
val aChar: Char = 'a'

println(aChar)
println('\n') // выводит дополнительный символ новой строки
println('\uFF00')
```

<!-- If a value of character variable is a digit, you can explicitly convert it to an `Int` number using the [`digitToInt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/digit-to-int.html) function. -->
Если значение символьной переменной – цифра, её можно явно преобразовать в `Int` с помощью функции
[`digitToInt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/digit-to-int.html).

<!-- >**On JVM**: Like [numbers](#numbers-representation-on-the-jvm), characters are boxed when a nullable reference is needed.
>Identity is not preserved by the boxing operation. -->
> В JVM: Подобно числам, символы оборачиваются при необходимости использования nullable-ссылки. При использовании обёрток тождественность (равенство по ссылке) не сохраняется.

<a name="strings"></a>
<!-- ## Strings -->
## Строки

<!-- Strings in Kotlin are represented by the type `String`. Generally, a string value is a sequence of characters in double quotes (`"`). -->
Строки в Kotlin представлены типом `String`. Как правило, строка представляет собой последовательность
символов в двойных кавычках (`"`).

```kotlin
val str = "abcd 123"
```

<!-- Elements of a string are characters that you can access via the indexing operation: `s[i]`.
You can iterate over these characters with a `for` loop: -->
Строки состоят из символов, которые могут быть получены по порядковому номеру: `s[i]`.
Проход по строке выполняется циклом `for`.

```kotlin
for (c in str) {
    println(c)
}
```

<!-- Strings are immutable. Once you initialize a string, you can't change its value or assign a new value to it.
All operations that transform strings return their results in a new `String` object, leaving the original string unchanged. -->
Строки являются неизменяемыми. После инициализации строки вы не можете изменить ее значение
или присвоить ей новое. Все операции, преобразующие строки, возвращают новый объект `String`,
оставляя исходную строку неизменной.

```kotlin
val str = "abcd"
println(str.uppercase()) // Создается и выводится новый объект String
println(str) // исходная строка остается прежней
```

<!-- To concatenate strings, use the `+` operator. This also works for concatenating strings with values of other types, as long
as the first element in the expression is a string: -->
Для объединения строк используется оператор `+`. Это работает и для объединения строк с другими типами,
если первый элемент в выражении является строкой.

```kotlin
val s = "abc" + 1
println(s + "def") // abc1def
```

<!-- Note that in most cases using [string templates](#string-templates) or raw strings is preferable to string concatenation. -->
Обратите внимание, что в большинстве случаев использование [строковых шаблонов](#string-templates)
или обычных строк предпочтительнее объединения строк.

<a name="string-literals"></a>
<!-- ### String literals -->
### Строковые литералы

<!-- Kotlin has two types of string literals: -->
В Kotlin представлены два типа строковых литералов:

<!-- * _escaped_ strings that may contain escaped characters
* _raw_ strings that can contain newlines and arbitrary text -->
* *экранированные* строки с экранированными символами 
* *обычные* строки, которые могут содержать символы новой строки и произвольный текст

<!-- Here's an example of an escaped string: -->
Вот пример экранированной строки:

```kotlin
val s = "Hello, world!\n"
```

<!-- Escaping is done in the conventional way, with a backslash (`\`). See [Characters](#characters) above for the list of supported escape sequences. -->
Экранирование выполняется общепринятым способом, а именно с помощью обратного слеша (`\`).
Список поддерживаемых escape-последовательностей см. в разделе [Символы](#characters) выше.

<!-- A raw string is delimited by a triple quote (`"""`), contains no escaping and can contain newlines and any other characters: -->
Обычная строка выделена тройной кавычкой (`"""`), не содержит экранированных символов,
но может содержать символы новой строки и любые другие символы:

```kotlin
val text = """
  for (c in "foo")
    print(c)
"""
```

<!-- To remove leading whitespace from raw strings, use the [`trimMargin()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/trim-margin.html) function: -->
Чтобы удалить пробелы в начале обычных строк, используйте функцию
[`trimMargin()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/trim-margin.html).

```kotlin
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
```

<!-- By default, `|` is used as margin prefix, but you can choose another character and pass it as a parameter, like `trimMargin(">")`. -->
По умолчанию `|` используется в качестве префикса поля, но вы можете выбрать другой символ и передать его в качестве параметра, например, `trimMargin(">")`.

<a name="string-templates"></a>
<!-- ### String templates -->
### Строковые шаблоны

<!-- String literals may contain _template_ expressions - pieces of code that are evaluated and whose results are concatenated into the string.
A template expression starts with a dollar sign (`$`) and consists of either a name: -->
Строки могут содержать *шаблонные* выражения, т.е. участки кода, которые выполняются, а полученный результат встраивается в строку.
Шаблон начинается со знака доллара (`$`) и состоит либо из простого имени (например, переменной),

```kotlin
val i = 10
println("i = $i") // выведет "i = 10"
```

<!-- or an expression in curly braces: -->
либо из произвольного выражения в фигурных скобках.

```kotlin
val s = "abc"
println("$s.length is ${s.length}") // выведет "abc.length is 3"
```

<!-- You can use templates both in raw and escaped strings.
To insert the `$` character in a raw string (which doesn't support backslash escaping) before any symbol, which is allowed as a beginning of an [identifier](https://kotlinlang.org/docs/reference/grammar.html#identifiers), use the following syntax: -->
Шаблоны поддерживаются как в обычных, так и в экранированных строках.
При необходимости вставить символ `$` в обычную строку (такие строки не поддерживают экранирование
обратным слешом) перед любым символом, который разрешен в качестве начала идентификатора,
используйте следующий синтаксис:

```kotlin
val price = """
${'$'}_9.99
"""
```

<a name="arrays"></a>
<!-- ## Arrays -->
## Массивы

<!-- Arrays in Kotlin are represented by the `Array` class. It has `get` and `set` functions that turn into `[]` by operator overloading conventions,
 and the `size` property, along with other useful member functions: -->
Массивы в Kotlin представлены классом `Array`, обладающим функциями `get` и `set` (которые обозначаются `[]` согласно соглашению о перегрузке операторов), и свойством `size`, а также несколькими полезными встроенными функциями.

```kotlin
class Array<T> private constructor() {
    val size: Int
    operator fun get(index: Int): T
    operator fun set(index: Int, value: T): Unit

    operator fun iterator(): Iterator<T>
    // ...
}
```

<!-- To create an array, use the function `arrayOf()` and pass the item values to it, so that `arrayOf(1, 2, 3)` creates an array `[1, 2, 3]`.
Alternatively, the `arrayOfNulls()` function can be used to create an array of a given size filled with `null` elements. -->
Для создания массива используйте функцию `arrayOf()`, которой в качестве аргумента передаются элементы массива,
т.е. выполнение `arrayOf(1, 2, 3)` создаёт массив `[1, 2, 3]`.
С другой стороны функция `arrayOfNulls()` может быть использована для создания массива заданного размера, заполненного значениями `null`.

<!-- Another option is to use the `Array` constructor that takes the array size and the function that returns values
of array elements given its index: -->
Также для создания массива можно использовать фабричную функцию, которая принимает размер массива и функцию, возвращающую начальное значение каждого элемента по его индексу.

```kotlin
// создаёт массив типа Array<String> со значениями ["0", "1", "4", "9", "16"]
val asc = Array(5) { i -> (i * i).toString() }
asc.forEach { println(it) }
```

<!-- As we said above, the `[]` operation stands for calls to member functions `get()` and `set()`. -->
Как отмечено выше, оператор `[]` используется вместо вызовов встроенных функций `get()` и `set()`.

<!-- Arrays in Kotlin are _invariant_. This means that Kotlin does not let us assign an `Array<String>`
to an `Array<Any>`, which prevents a possible runtime failure (but you can use `Array<out Any>`, 
see [Type Projections](generics.md#type-projections)). -->
Обратите внимание: в отличие от Java массивы в Kotlin являются *инвариантными*. Это значит,
что Kotlin запрещает нам присваивать массив `Array<String>` переменной типа `Array<Any>`,
предотвращая таким образом возможный отказ во время исполнения (хотя вы можете использовать `Array<out Any>`, 
см. [Проекции типов](generics.html#type-projections)).

<a name="primitive-type-arrays"></a>
<!-- ### Primitive type arrays -->
### Массивы примитивных типов

<!-- Kotlin also has classes that represent arrays of primitive types without boxing overhead: `ByteArray`,
`ShortArray`, `IntArray`, and so on. These classes have no inheritance relation to the `Array` class, but they
have the same set of methods and properties. Each of them also has a corresponding factory function: -->
Также в Kotlin есть особые классы для представления массивов примитивных типов без дополнительных затрат на оборачивание:
`ByteArray`, `ShortArray`, `IntArray` и т.д. Данные классы не наследуют класс `Array`, хотя и обладают тем же набором методов и свойств. У каждого из них есть соответствующая фабричная функция:

```kotlin
val x: IntArray = intArrayOf(1, 2, 3)
x[0] = x[1] + x[2]
```

```kotlin
// int массив, размером 5 со значениями [0, 0, 0, 0, 0]
val arr = IntArray(5)

// инициализация элементов массива константой
// int массив, размером 5 со значениями [42, 42, 42, 42, 42]
val arr = IntArray(5) { 42 }

// инициализация элементов массива лямбда-выражением
// int массив, размером 5 со значениями [0, 1, 2, 3, 4] (элементы инициализированы своим индексом)
var arr = IntArray(5) { it * 1 } 
```
