---
type: doc
layout: reference
category: "Basics"
title: "Основной синтаксис"
url: https://kotlinlang.ru/docs/basic-syntax.html
---

<!-- При обновлении статьи оригинальная версия была от 01 April 2026 -->

# Обзор основного синтаксиса

<!-- This is a collection of basic syntax elements with examples. At the end of every section, you'll find a link to
a detailed description of the related topic. -->
Это подборка базового синтаксиса с примерами.
В конце каждого раздела вы найдете ссылку на более подробное описание соответствующей темы.

<!-- You can also learn all the Kotlin essentials with the free [Kotlin Core track](https://hyperskill.org/tracks?category=4&utm_source=jbkotlin_hs&utm_medium=referral&utm_campaign=kotlinlang-docs&utm_content=button_1&utm_term=22.03.23)
by JetBrains Academy. -->
Вы также можете изучить все основы Kotlin в бесплатном курсе [Kotlin Core](https://hyperskill.org/tracks?category=4&utm_source=jbkotlin_hs&utm_medium=referral&utm_campaign=kotlinlang-docs&utm_content=button_1&utm_term=22.03.23)
от JetBrains Academy.

<a name="package-definition-and-imports"></a>
<!-- ## Package definition and imports -->
## Определение имени пакета и импорт

<!-- Package specification should be at the top of the source file: -->
Имя пакета должно указываться в начале исходного файла:

```kotlin
package my.demo

import kotlin.text.*

// ...
```

<!-- It is not required to match directories and packages: source files can be placed arbitrarily in the file system. -->
Сопоставлять каталоги и пакеты не обязательно: исходные файлы могут располагаться в произвольном месте
в файловой системе.

<!-- See [Packages](packages.md). -->
См. [Пакеты](packages.html).

<a name="program-entry-point"></a>
<!-- ## Program entry point -->
## Точка входа в программу

<!-- An entry point of a Kotlin application is the `main` function: -->
В Kotlin точкой входа в программу является функция `main`.

```kotlin
fun main() {
    println("Hello world!")
}
```

<!-- Another form of `main` accepts a variable number of `String` arguments:  -->
Другая форма `main` принимает переменное количество аргументов типа `String`.

```kotlin
fun main(args: Array<String>) {
    println(args.contentToString())
}
```

<a name="print-to-the-standard-output"></a>
<!-- ## Print to the standard output -->
## Вывод в стандартный поток

<!-- `print` prints its argument to the standard output. -->
`print` выводит свой аргумент в стандартный поток вывода.

```kotlin
print("Hello ")
print("world!")
```

<!-- `println` prints its arguments and adds a line break, so that the next thing you print appears on the next line. -->
`println` выводит свой аргумент и добавляет перевод строки, так что следующее, что вы выведите, появится на следующей строке.

```kotlin
println("Hello world!")
println(42)
```

<a name="read-from-the-standard-input"></a>
<!-- ## Read from the standard input -->
## Чтение из стандартного потока ввода

<!-- The `readln()` function reads from the standard input. This function reads the entire line the user enters as a string. -->
Функция `readln()` читает данные из стандартного потока ввода.
Она считывает всю введенную пользователем строку как строковое значение.

<!-- You can use the `println()`, `readln()`, and `print()` functions together to print messages requesting
and showing user input: -->
Функции `println()`, `readln()` и `print()` можно использовать вместе, чтобы выводить запросы на ввод
и показывать введенные пользователем данные:

```kotlin
// Выводит сообщение с запросом на ввод
println("Введите любое слово: ")

// Читает и сохраняет пользовательский ввод. Например: Счастье
val yourWord = readln()

// Выводит сообщение с введенным значением
print("Вы ввели слово: ")
print(yourWord)
// Вы ввели слово: Счастье
```

<!-- For more information, see [Read standard input](read-standard-input.md). -->
Подробнее см. [Чтение стандартного ввода](https://kotlinlang.org/docs/read-standard-input.html).

<a name="functions"></a>
<!-- ## Functions -->
## Функции

<!-- A function with two `Int` parameters and `Int` return type: -->
Функция с двумя параметрами типа `Int` и возвращаемым значением типа `Int`.

```kotlin
fun sum(a: Int, b: Int): Int {
    return a + b
}
```

<!-- A function body can be an expression. Its return type is inferred. -->
В качестве тела функции может выступать выражение. Тогда тип возвращаемого значения определяется автоматически.

```kotlin
fun sum(a: Int, b: Int) = a + b
```

<!-- A function that returns no meaningful value. -->
Функция, которая не возвращает осмысленного значения.

```kotlin
fun printSum(a: Int, b: Int): Unit {
    println("сумма $a и $b равна ${a + b}")
}
```

<!-- `Unit` return type can be omitted. -->
Тип возвращаемого значения `Unit` может быть опущен.

```kotlin
fun printSum(a: Int, b: Int) {
    println("сумма $a и $b равна ${a + b}")
}
```

<!-- See [Functions](functions.md). -->
См. [Функции](functions.html).

<a name="variables"></a>
<!-- ## Variables -->
## Переменные

<!-- In Kotlin, you declare a variable starting with a keyword, `val` or `var`, followed by the name of the variable. -->
В Kotlin объявление переменной начинается с ключевого слова `val` или `var`, после которого указывается имя переменной.

<!-- Use the `val` keyword to declare variables that are assigned a value only once. These are immutable, read-only local variables that can't be reassigned a different value after initialization: -->
Используйте ключевое слово `val` для объявления переменных, которым значение присваивается только один раз.
Это неизменяемые локальные переменные только для чтения: после инициализации им нельзя присвоить другое значение.

```kotlin
val x: Int = 5
```

<!-- Use the `var` keyword to declare variables that can be reassigned. These are mutable variables, and you can change their values after initialization: -->
Используйте ключевое слово `var` для объявления переменных, которым можно присваивать новые значения.
Это изменяемые переменные: их значения можно менять после инициализации.

```kotlin
var x: Int = 5
x += 1
```

<!-- Kotlin supports type inference and automatically identifies the data type of a declared variable. When declaring a variable, you can omit the type after the variable name: -->
Kotlin поддерживает вывод типов и автоматически определяет тип данных объявленной переменной.
При объявлении переменной можно опустить тип после ее имени.

```kotlin
val x = 5
```

<!-- You can use variables only after initializing them. You can either initialize a variable at the moment of declaration or declare a variable first and initialize it later.
In the second case, you must specify the data type: -->
Переменные можно использовать только после инициализации.
Переменную можно инициализировать в момент объявления или сначала объявить, а затем инициализировать позже.
Во втором случае тип данных нужно указывать явно.

```kotlin
val x = 5
val c: Int
c = 3
```

<!-- You can declare variables at the top level: -->
Вы можете объявлять переменные на верхнем уровне.

```kotlin
val PI = 3.14
var x = 0

fun incrementX() {
    x += 1
}
```

<!-- For information about declaring properties, see [Properties](properties.md). -->
Информацию об объявлении свойств см. в разделе [Свойства](properties.html).

<a name="creating-classes-and-instances"></a>
<!-- ## Creating classes and instances -->
## Создание классов и экземпляров

<!-- To define a class, use the `class` keyword: -->
Чтобы определить класс, используйте ключевое слово `class`.

```kotlin
class Shape
```

<!-- Properties of a class can be listed in its declaration or body. -->
Свойства класса могут быть перечислены при его объявлении или в его теле.

```kotlin
class Rectangle(val height: Double, val length: Double) {
    val perimeter = (height + length) * 2
}
```

<!-- The default constructor with parameters listed in the class declaration is available automatically. -->
Конструктор по умолчанию с параметрами, перечисленными при объявлении класса, доступен автоматически.

```kotlin
class Rectangle(val height: Double, val length: Double) {
    val perimeter = (height + length) * 2
}

val rectangle = Rectangle(5.0, 2.0)
println("Периметр равен ${rectangle.perimeter}")
```

<!-- Inheritance between classes is declared by a colon (`:`). Classes are `final` by default; to make a class inheritable,
mark it as `open`. -->
Чтобы объявить наследование между классами используйте двоеточие (`:`). По умолчанию классы являются финальными,
поэтому, чтобы сделать класс наследуемым, используйте `open`.

```kotlin
open class Shape

class Rectangle(val height: Double, val length: Double): Shape() {
    val perimeter = (height + length) * 2
}
```

<!-- For more information about constructors and inheritance, see [Classes](classes.md) and [Objects and instances](object-declarations.md). -->
Подробнее о конструкторах и наследовании см. в разделах [Классы](classes.html) и [Объекты и экземпляры](object-declarations.html).

<a name="comments"></a>
<!-- ## Comments -->
## Комментарии

<!-- Just like most modern languages, Kotlin supports single-line (or _end-of-line_) and multi-line (_block_) comments. -->
Как и большинство современных языков, Kotlin поддерживает однострочные (до конца строки) и многострочные (блочные) комментарии.

```kotlin
// Это однострочный комментарий

/* Это блочный комментарий
   из нескольких строк. */
```

<!-- Block comments in Kotlin can be nested. -->
Блочные комментарии в Kotlin могут быть вложенными.

```kotlin
/* Этот комментарий начинается здесь
/* содержит вложенный комментарий */
и заканчивается здесь. */
```

<!-- See [Documenting Kotlin Code](kotlin-doc.md) for information on the documentation comment syntax. -->
См. [Документация Kotlin кода](kotlin-doc.html) для информации о документации в комментариях.

<a name="string-templates"></a>
<!-- ## String templates -->
## Строковые шаблоны

```kotlin
fun main() {
    var a = 1
    // простое имя в шаблоне:
    val s1 = "a равно $a"

    a = 2
    // произвольное выражение в шаблоне:
    val s2 = "${s1.replace("равно", "было равно")}, но теперь равно $a"

    println(s2)
}
```

<!-- See [String templates](strings.md#string-templates) for details. -->
См. [Строковые шаблоны](https://kotlinlang.org/docs/strings.html#string-templates).

<a name="conditional-expressions"></a>
<!-- ## Conditional expressions -->
## Условные выражения

```kotlin
fun maxOf(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}
```

<!-- In Kotlin, `if` can also be used as an expression. -->
В Kotlin `if` также может использоваться как выражение:

```kotlin
fun maxOf(a: Int, b: Int) = if (a > b) a else b
```

<!-- See [`if`-expressions](control-flow.md#if-expression). -->
См. [Выражение `if`](control-flow.html#if-expression).

<a name="for-loop"></a>
<!-- ## for loop -->
## Цикл for

```kotlin
val items = listOf("яблоко", "банан", "киви")
for (item in items) {
    println(item)
}
```

<!-- or -->
или

```kotlin
val items = listOf("яблоко", "банан", "киви")
for (index in items.indices) {
    println("элемент с индексом $index - это ${items[index]}")
}
```

<!-- See [for loop](control-flow.md#for-loops). -->
См. [Цикл for](control-flow.html#for-loops).

<a name="while-loop"></a>
<!-- ## while loop -->
## Цикл while

```kotlin
val items = listOf("яблоко", "банан", "киви")
var index = 0
while (index < items.size) {
    println("элемент с индексом $index - это ${items[index]}")
    index++
}
```

<!-- See [while loop](control-flow.md#while-loops). -->
См. [Цикл while](control-flow.html#while-loops).

<a name="when-expression"></a>
<!-- ## when expression -->
## Выражение when

```kotlin
fun describe(obj: Any): String =
    when (obj) {
        1          -> "Один"
        "Hello"    -> "Приветствие"
        is Long    -> "Long"
        !is String -> "Не строка"
        else       -> "Неизвестно"
    }
```

<!-- See [when expressions and statements](control-flow.md#when-expressions-and-statements). -->
См. [Выражения и инструкции when](control-flow.html#when-expressions-and-statements).

<a name="ranges"></a>
<!-- ## Ranges -->
## Интервалы

<!-- Check if a number is within a range using `in` operator. -->
Проверка на вхождение числа в интервал с помощью оператора `in`.

```kotlin
val x = 10
val y = 9
if (x in 1..y+1) {
    println("попадает в диапазон")
}
```

<!-- Check if a number is out of range. -->
Проверка значения на выход за пределы интервала.

```kotlin
val list = listOf("a", "b", "c")

if (-1 !in 0..list.lastIndex) {
    println("-1 вне диапазона")
}
if (list.size !in list.indices) {
    println("размер списка тоже вне допустимого диапазона индексов")
}
```

<!-- Iterate over a range. -->
Перебор значений в заданном интервале.

```kotlin
for (x in 1..5) {
    print(x)
}
```

<!-- Or over a progression: -->
Или по арифметической прогрессии.

```kotlin
for (x in 1..10 step 2) {
    print(x)
}
println()
for (x in 9 downTo 0 step 3) {
    print(x)
}
```

<!-- See [Ranges and progressions](ranges.md). -->
См. [Интервалы и прогрессии](ranges.html).

<a name="collections"></a>
<!-- ## Collections -->
## Коллекции

<!-- Iterate over a collection. -->
Итерация по коллекции.

```kotlin
for (item in items) {
    println(item)
}
```

<!-- Check if a collection contains an object using `in` operator. -->
Проверка, содержит ли коллекция данный объект, с помощью оператора `in`.

```kotlin
val items = setOf("яблоко", "банан", "киви")
when {
    "апельсин" in items -> println("сочно")
    "яблоко" in items -> println("яблоко тоже подойдет")
}
```

<!-- Use [lambda expressions](lambdas.md) to filter and map collections: -->
Используйте [лямбда-выражения](lambdas.html), чтобы фильтровать и преобразовывать коллекции.

```kotlin
val fruits = listOf("банан", "авокадо", "яблоко", "киви")
fruits
    .filter { it.startsWith("а") }
    .sortedBy { it }
    .map { it.uppercase() }
    .forEach { println(it) }
```

<!-- See [Collections overview](collections-overview.md). -->
См. [Коллекции](collections-overview.html).

<a name="nullable-values-and-null-checks"></a>
<!-- ## Nullable values and null checks -->
## Nullable-значения и проверка на null

<!-- A reference must be explicitly marked as nullable when a `null` value is possible. Nullable type names have `?` at the end.
For example, `Int?`. -->
Ссылка должна быть явно помечена как nullable, когда для нее возможно значение `null`.
Имена nullable-типов заканчиваются символом `?`, например `Int?`.

<!-- Return `null` if `str` does not hold an integer: -->
Возвращает `null`, если `str` не содержит целое число.

```kotlin
fun parseInt(str: String): Int? {
    return str.toIntOrNull()
}
```

<!-- Use a function returning nullable value: -->
Использование функции, возвращающей nullable-значение.

```kotlin
fun parseInt(str: String): Int? {
    return str.toIntOrNull()
}

fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)

    // Использование `x * y` приведет к ошибке, потому что они могут содержать null.
    if (x != null && y != null) {
        // x и y автоматически приведены к не-nullable после проверки на null
        println(x * y)
    }
    else {
        println("'$arg1' или '$arg2' не является числом")
    }
}
```

<!-- or -->
или

```kotlin
fun parseInt(str: String): Int? {
    return str.toIntOrNull()
}

fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)

    // ...
    if (x == null) {
        println("Неверный формат числа в arg1: '$arg1'")
        return
    }
    if (y == null) {
        println("Неверный формат числа в arg2: '$arg2'")
        return
    }

    // x и y автоматически приведены к не-nullable после проверки на null
    println(x * y)
}
```

<!-- See [Null-safety](null-safety.md). -->
См. [Null-безопасность](null-safety.html).

<a name="type-checks-and-automatic-casts"></a>
<!-- ## Type checks and automatic casts -->
## Проверка типа и автоматическое приведение типов

<!-- The `is` operator checks if an expression is an instance of a type.
If an immutable local variable or property is checked for a specific type, there's no need to cast it explicitly: -->
Оператор `is` проверяет, является ли выражение экземпляром заданного типа.
Если неизменяемая локальная переменная или свойство уже проверены на определенный тип, то в дальнейшем нет необходимости
явно приводить к этому типу:

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        // в этой ветке `obj` автоматически преобразован в `String`
        return obj.length
    }

    // вне ветки проверки типа `obj` по-прежнему имеет тип `Any`
    return null
}
```

<!-- or -->
или

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null

    // в этой ветке `obj` автоматически преобразован в `String`
    return obj.length
}
```

<!-- or even -->
или даже

```kotlin
fun getStringLength(obj: Any): Int? {
    // `obj` автоматически преобразован в `String` справа от оператора `&&`
    if (obj is String && obj.length >= 0) {
        return obj.length
    }

    return null
}
```

<!-- See [Classes](classes.md) and [Type casts](typecasts.md). -->
См. [Классы](classes.html) и [Приведение типов](typecasts.html).
