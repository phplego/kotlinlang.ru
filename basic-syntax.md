---
type: doc
layout: reference
category: "Basics"
title: "Основной синтаксис"
url: https://kotlinlang.ru/docs/basic-syntax.html
---
      
<!-- При переводе статьи оригинальная версия была от 13 September 2021 -->

# Основной синтаксис

<!-- This is a collection of basic syntax elements with examples. At the end of every section, you'll find a link to
a detailed description of the related topic. -->
Это подборка базового синтаксиса с примерами.
В конце каждого раздела вы найдете ссылку на более подробное описание соответствующей темы.

<!-- You can also learn all the Kotlin essentials with the free [Kotlin Basics track](https://hyperskill.org/tracks/18)
on JetBrains Academy. -->
Вы также можете изучить все основы Kotlin в бесплатном курсе [Основы Kotlin](https://hyperskill.org/tracks/18) от JetBrains Academy.

<a name="package-definition-and-imports"></a>
<!-- ## Package definition and imports -->
## Определение имени пакета и импорт

<!-- Package specification should be at the top of the source file. -->
Имя пакета указывается в начале исходного файла, так же как и в Java.

```kotlin
package my.demo

import java.util.*

// ...
```

<!-- It is not required to match directories and packages: source files can be placed arbitrarily in the file system. -->
Но в отличие от Java, нет необходимости, чтобы структура пакетов совпадала со структурой папок:
исходные файлы могут располагаться в произвольном месте на диске.

<!-- See [Packages](packages.md). -->
См. [Пакеты](packages.html).

<a name="program-entry-point"></a>
<!-- ## Program entry point -->
## Точка входа в программу

<!-- An entry point of a Kotlin application is the `main` function. -->
В Kotlin точкой входа в программу является функция `main`.

```kotlin
fun main() {
    println("Hello world!")
}
```

<!-- Another form of `main` accepts a variable number of `String` arguments.  -->
Другая форма `main` может принимать массив строк `String`.

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

<a name="functions"></a>
<!-- ## Functions -->
## Функции

<!-- A function with two `Int` parameters and `Int` return type. -->
Функция принимает два аргумента `Int` и возвращает `Int`.

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
Функция, не возвращающая никакого значения (`void` в Java).

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

<!-- Read-only local variables are defined using the keyword `val`. They can be assigned a value only once. -->
Неизменяемые (только для чтения) локальные переменные определяются с помощью ключевого слова `val`. Присвоить им значение можно только один раз.

```kotlin
val a: Int = 1   // Инициализация при объявлении
val b = 1        // Тип `Int` определен автоматически
val c: Int       // Указывать тип обязательно, если переменная не инициализирована сразу
c = 1            // Последующее присвоение
```

<!-- Variables that can be reassigned use the `var` keyword. -->
Изменяемые переменные объявляются с помощью ключевого слова `var`.

```kotlin
var x = 5 // Тип `Int` определен автоматически
x += 1
```

<!-- You can declare variables at the top level. -->
Вы можете объявлять глобальные переменные.

```kotlin
val PI = 3.14
var x = 0

fun incrementX() { 
    x += 1 
}
```

<!-- See also [Properties](properties.md). -->
См. [Свойства и поля](properties.html).

<a name="creating-classes-and-instances"></a>
<!-- ## Creating classes and instances -->
## Создание классов и экземпляров

<!-- To define a class, use the `class` keyword. -->
Для создания класса используйте ключевое слово `class`.

```kotlin
class Shape
```

<!-- Properties of a class can be listed in its declaration or body. -->
Свойства класса могут быть перечислены при его объявлении или в его теле.

```kotlin
class Rectangle(var height: Double, var length: Double) {
    var perimeter = (height + length) * 2 
}
```

<!-- The default constructor with parameters listed in the class declaration is available automatically. -->
Конструктор по умолчанию с параметрами, перечисленными при объявлении класса, доступен автоматически.

```kotlin
val rectangle = Rectangle(5.0, 2.0)
println("Периметр равен ${rectangle.perimeter}")
```

<!-- Inheritance between classes is declared by a colon (`:`). Classes are final by default; to make a class inheritable, 
mark it as `open`. -->
Чтобы объявить наследование между класслами используйте двоеточие (`:`). По умолчанию классы являются финальными,
поэтому, чтобы сделать класс наследуемым, используйте `open`.

```kotlin
open class Shape

class Rectangle(var height: Double, var length: Double): Shape() {
    var perimeter = (height + length) * 2 
}
```

<!-- See [classes](classes.md) and [objects and instances](object-declarations.md). -->
См. [Классы и наследование](classes.html) и [Объекты и экземпляры](object-declarations.html).

<a name="comments"></a>
<!-- ## Comments -->
## Комментарии

<!-- Just like most modern languages, Kotlin supports single-line (or _end-of-line_) and multi-line (_block_) comments. -->
Также, как любой другой популярный современный язык, Kotlin поддерживает однострочные и многострочные (блочные) комментарии.

```kotlin
// Это однострочный комментарий

/* Это блочный комментарий
   из нескольких строк. */
```

<!-- Block comments in Kotlin can be nested. -->
Блочные комментарии в Kotlin могут быть вложенными.

```kotlin
/* Этот комментарий начинается здесь
/* содержит вложенный комментарий * /
и заканчивается здесь. */
```

<!-- See [Documenting Kotlin Code](kotlin-doc.md) for information on the documentation comment syntax. -->
См. [Документация Kotlin кода](kotlin-doc.html) для информации о документации в комментариях.

<a name="string-templates"></a>
<!-- ## String templates -->
## Строковые шаблоны

Допустимо использование переменных внутри строк в формате `$name` или `${name}`:

```kotlin
fun main(args: Array<String>) {
  if (args.size == 0) return

  print("Первый аргумент: ${args[0]}")
}
```

```
var a = 1
// просто имя переменной в шаблоне:
val s1 = "a равно $a" 

a = 2
// произвольное выражение в шаблоне:
val s2 = "${s1.replace("равно", "было равно")}, но теперь равно $a"

/*
  Результат работы программы:
  a было равно 1, но теперь равно 2
*/
```

<!-- See [String templates](basic-types.md#string-templates) for details. -->
См. [Строковые шаблоны](basic-types.html#string-templates).

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
В Kotlin `if` может быть использован как выражение (т. е. `if` ... `else` возвращает значение):

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
    println("${index} фрукт - это ${items[index]}")
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
    println("${index} фрукт - это ${items[index]}")
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
        else       -> "Unknown"
    }
```

<!-- See [when expression](control-flow.md#when-expression). -->
См. [Выражение when](control-flow.html#when-expression).

<a name="ranges"></a>
<!-- ## Ranges -->
## Интервалы

<!-- Check if a number is within a range using `in` operator. -->
Проверка на вхождение числа в интервал с помощью оператора `in`.

```kotlin
val x = 10
val y = 9
if (x in 1..y+1) {
    println("принадлежит диапазону")
}
```

<!-- Check if a number is out of range. -->
Проверка значения на выход за пределы интервала.

```kotlin
val list = listOf("a", "b", "c")

if (-1 !in 0..list.lastIndex) {
    println("-1 не принадлежит диапазону")
}
if (list.size !in list.indices) {
    println("размер списка также выходит за допустимый диапазон индексов списка")
}
```

<!-- Iterate over a range. -->
Перебор значений в заданном интервале.

```kotlin
for (x in 1..5) {
    print(x)
}
```
<!-- Or over a progression. -->
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
См. [Интервалы](ranges.html).

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
    "apple" in items -> println("яблоко тоже подойдет")
}
```

<!-- Using lambda expressions to filter and map collections: -->
Использование лямбда-выражения для фильтрации и модификации коллекции.

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

<!-- A reference must be explicitly marked as nullable when `null` value is possible. Nullable type names have `?` at the end. -->
Ссылка должна быть явно объявлена как nullable (символ `?` в конце имени), когда она может принимать значение `null`.

<!-- Return `null` if `str` does not hold an integer: -->
Возвращает `null`, если `str` не содержит числа.

```kotlin
fun parseInt(str: String): Int? {
  // ...
}
```

<!-- Use a function returning nullable value: -->
Использование функции, возвращающей `null`.

```kotlin
fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)
    
    // Использование `x * y` приведет к ошибке, потому что они могут содержать null
    if (x != null && y != null) {
        // x и y автоматически приведены к не-nullable после проверки на null
    print(x * y)
    }
    else {
        println("'$arg1' или '$arg2' не число")
    }
}
```

<!-- or -->
или

```kotlin
// ...
if (x == null) {
    print("Неверный формат числа arg1: '$arg1'")
    return
}
if (y == null) {
    print("Неверный формат числа arg2: '$arg2'")
    return
}

// x и y автоматически приведены к не-nullable после проверки на null
  print(x * y)
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
        // в этом блоке `obj` автоматически преобразован в `String`
        return obj.length
    }

    // `obj` имеет тип `Any` вне блока проверки типа
    return null
}
```

<!-- or -->
или

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null

    // в этом блоке `obj` автоматически преобразован в `String`
    return obj.length
}
```

<!-- or even -->
или даже

```kotlin
fun getStringLength(obj: Any): Int? {
    // `obj` автоматически преобразован в `String` справа от оператора `&&`
    if (obj is String && obj.length > 0) {
        return obj.length
    }

    return null
}
```

<!-- See [Classes](classes.md) and [Type casts](typecasts.md). -->
См. [Классы](classes.html) и [Приведение типов](typecasts.html).
