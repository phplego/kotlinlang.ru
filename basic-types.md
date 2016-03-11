---
type: doc
layout: reference
category: "Syntax"
title: "Basic Types"
---

# Основные типы

В Kotlin всё является объектом, в том смысле, что пользователь может вызвать функцию или получить доступ к свойству любой переменной. Некоторые типы являются встроенными, т. к. их реализация оптимизирована, хотя для пользователя они выглядять как обычные классы. В данном разделе описывается большинство этих типов: числа, символы, логические переменные и массивы.

## Числа

Kotlin обрабатывает численные типы примерно так же, как и Java, хотя некоторые различия всё же присутствуют. Например, отсутствует неявное расширяющее преобразование для чисел, а литералы в некоторых случаях немного отличаются.

Для представления чисел в Kotlin используются следующие встроенные типы (подобные типам в Java):

| Тип	 | Количество бит|
|--------|----------|
| Double | 64       |
| Float	 | 32       |
| Long	 | 64       |
| Int	 | 32       |
| Short	 | 16       |
| Byte	 | 8        |

Обратите внимание, что символы (characters) в языке Kotlin не являются числами (в отличие от Java).

### Символьные постоянные

В языке Kotlin присутствуют следующие виды символьных постоянных (констант) для целых значений:

* Десятичные числа: `123`
  * Тип Long обозначается заглавной `L`: `123L`
* Шестнадцатеричные числа: `0x0F`
* Двоичные числа: `0b00001011`

ВНИМАНИЕ: Восьмиричные литералы не поддерживаются.

Также Kotlin поддерживает числа с плавающей запятой:
 
* Тип Double по умолчанию: `123.5`, `123.5e10`
* Тип Float обозначается с помощью `f` или `F`: `123.5f` 

### Представление

Обычно платформа Java хранит числа в виде примитивных типов JVM; если же нам необходима ссылка, которая может принимать значение null (например, `Int?`), то используются обёртки. 
В приведённом ниже примере показано использование обёрток.

Обратите внимание, что использование обёрток для одного и того же числа не гарантирует равенства ссылок на них:

``` kotlin
val a: Int = 10000
print(a === a) // Prints 'true'
val boxedA: Int? = a
val anotherBoxedA: Int? = a
print(boxedA === anotherBoxedA) // !!!Prints 'false'!!!
```

Однако, равенство по значению сохраняется:

``` kotlin
val a: Int = 10000
print(a == a) // Prints 'true'
val boxedA: Int? = a
val anotherBoxedA: Int? = a
print(boxedA == anotherBoxedA) // Prints 'true'
```

### Явные преобразования

Из-за разницы в представлениях меньшие типы не являются подтипами бОльших типов.
В проивном случае, у нас возникли бы сложности:

``` kotlin
// Возможный код, который на самом деле не скомпилируется:
val a: Int? = 1 // "Обёрнутый" Int (java.lang.Integer)
val b: Long? = a // неявное преобразование возвращает "обёрнутый" Long (java.lang.Long)
print(a == b) // Нежданчик! Данное выражение выведет "false" т. к. метод equals() типа Long предполагает, что вторая часть выражения также имеет тип Long
```

Таким образом, будет утрачена не только тождественность (равенсто по ссылке), но и равенство по значению.

Как следствие, неявное преобразование меньших типов в большие НЕ происходит.
Это значит, что мы не можем присвоить значение типа `Byte`переменной типа `Int` без явного преобразования:

``` kotlin
val b: Byte = 1 // порядок, литералы проверяются статически
val i: Int = b // ОШИБКА
```

Мы можем использовать явное преобразование для "сужения" чисел

``` kotlin
val i: Int = b.toInt() // порядок: число явно сужено
```

Каждый численный тип поддерживает следующие преобразования:

* `toByte(): Byte`
* `toShort(): Short`
* `toInt(): Int`
* `toLong(): Long`
* `toFloat(): Float`
* `toDouble(): Double`
* `toChar(): Char`

Отсутствие неявного преобразования редко просается в глаза, поскольку тип выводится из контекста, а арифметические действия перегружаются для подходящих преобразований, например:

``` kotlin
val l = 1L + 3 // Long + Int => Long
```

### Арифметические действия

Kotlin поддерживает обычный набор арифметических действий над числами, которые объявлены членами соответствующего класса (тем не менее, компилятор оптимизирует вызовы вплоть до соответствущих инструкций).
См. [Перегрузка операторов](operator-overloading.html).

Что касается битовых операций, то вместо особых обозначений для них исползуются именованые функции, котоыре могут быть вызваны в инфиксной форме, к примеру:

``` kotlin
val x = (1 shl 2) and 0x000FF000
```

Ниже приведён полный список битовых операций (доступны только для типов `Int` и `Long`):

* `shl(bits)` – сдвиг влево с учётом знака (`<<` в Java)
* `shr(bits)` – сдвиг вправо с учётом знака (`>>` в Java)
* `ushr(bits)` – сдвиг вправо без учёта знака (`>>>` в Java)
* `and(bits)` – побитовое И
* `or(bits)` – побитовое ИЛИ
* `xor(bits)` – побитовое исключающее ИЛИ
* `inv()` – побитовое отрицание

## Символы

Символы в Kotlin представлены типом `Char`. Напрямую они не могут рассматриваться в качестве чисел

``` kotlin
fun check(c: Char) {
  if (c == 1) { // ОШИБКА: несовместимый тип
    // ...
  }
}
```

Символьные литералы записываются в одинарных кавычках: `'1'`, `'\n'`, `'\uFF00'`.
Мы можем явно привести символ в число типа `Int`

``` kotlin
fun decimalDigitValue(c: Char): Int {
  if (c !in '0'..'9')
    throw IllegalArgumentException("Вне диапазона")
  return c.toInt() - '0'.toInt() // Явные преобразования в число
}
```

Подобно числам, символы оборачиваются при необходимости использования nullable ссылки. При использовании обёрток тождественность (равенство по ссылке) не сохраняется.

## Логический тип

Тип `Boolean` представляет логический тип данных и принимает два значения: **true**<!--keyword--> и **false**<!--keyword-->.

При необходимости использования nullable ссылок логические переменные оборачиваются.

Встроенные действия над логическими переменными включают

* `||` – ленивое логическое ИЛИ
* `&&` – ленивое логическое И
* `!` - отрицание

## Массивы

Массивы в Kotlin представлены классом `Array`, обладающего функциями `get` и `set` (которые обозначаются `[]` согласно соглашению о перегрузке операторов), и свойством `size`, а также несколькими полезными встроенными функциями:

``` kotlin
class Array<T> private constructor() {
  val size: Int
  fun get(index: Int): T
  fun set(index: Int, value: T): Unit

  fun iterator(): Iterator<T>
  // ...
}
```

To create an array, we can use a library function `arrayOf()` and pass the item values to it, so that `arrayOf(1, 2, 3)` creates an array [1, 2, 3].
Alternatively, the `arrayOfNulls()` library function can be used to create an array of a given size filled with null elements.

Another option is to use a factory function that takes the array size and the function that can return the initial value
of each array element given its index:

``` kotlin
// Creates an Array<String> with values ["0", "1", "4", "9", "16"]
val asc = Array(5, { i -> (i * i).toString() })
```

As we said above, the `[]` operation stands for calls to member functions `get()` and `set()`.

Note: unlike Java, arrays in Kotlin are invariant. This means that Kotlin does not let us assign an `Array<String>`
to an `Array<Any>`, which prevents a possible runtime failure (but you can use `Array<out Any>`, 
see [Type Projections](generics.html#type-projections)).

Kotlin also has specialized classes to represent arrays of primitive types without boxing overhead: `ByteArray`,
`ShortArray`, `IntArray` and so on. These classes have no inheritance relation to the `Array` class, but they
have the same set of methods and properties. Each of them also has a corresponding factory function:

``` kotlin
val x: IntArray = intArrayOf(1, 2, 3)
x[0] = x[1] + x[2]
```

## Strings

Strings are represented by the type `String`. Strings are immutable.
Elements of a string are characters that can be accessed by the indexing operation: `s[i]`.
A string can be iterated over with a **for**<!--keyword-->-loop:

``` kotlin
for (c in str) {
  println(c)
}
```

### String Literals

Kotlin has two types of string literals: escaped strings that may have escaped characters in them and raw strings that can contain newlines and arbitrary text. An escaped string is very much like a Java string:

``` kotlin
val s = "Hello, world!\n"
```

Escaping is done in the conventional way, with a backslash.

A raw string is delimited by a triple quote (`"""`), contains no escaping and can contain newlines and any other characters:

``` kotlin
val text = """
  for (c in "foo")
    print(c)
"""
```


### String Templates

Strings may contain template expressions, i.e. pieces of code that are evaluated and whose results are concatenated into the string.
A template expression starts with a dollar sign ($) and consists of either a simple name:

``` kotlin
val i = 10
val s = "i = $i" // evaluates to "i = 10"
```

or an arbitrary expression in curly braces:

``` kotlin
val s = "abc"
val str = "$s.length is ${s.length}" // evaluates to "abc.length is 3"
```

Templates are supported both inside raw strings and inside escaped strings.
If you need to represent a literal `$` character, you can use the following syntax:

``` kotlin
val price = "${'$'}9.99"
```
