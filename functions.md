---
type: doc
layout: reference
category: "Syntax"
title: "Функции"
url: https://kotlinlang.ru/docs/functions.html
---

<!-- При переводе статьи оригинальная версия была от 16 December 2025 -->

<!-- # Functions -->
# Функции

<!-- To declare a function in Kotlin:
* Use the `fun` keyword.
* Specify the parameters in parentheses `()`.
* Include the [return type](#return-types) if needed. -->
Чтобы объявить функцию в Kotlin:

* используйте ключевое слово `fun`;
* укажите параметры в круглых скобках `()`;
* добавьте [тип возвращаемого значения](#return-types), если он нужен.

<!-- For example: -->
Например:

```kotlin
// 'double' - имя функции
// 'x' - параметр типа Int
// ожидаемый тип возвращаемого значения тоже Int
fun double(x: Int): Int {
    return 2 * x
}

fun main() {
    println(double(5))
    // 10
}
```

<a name="function-usage"></a>

<!-- ## Function usage -->
## Использование функций

<!-- Functions are called using the standard approach: -->
При вызове функции используется традиционный подход:

```kotlin
val result = double(2)
```

<!-- To call a member or extension function, use a period `.`: -->
Чтобы вызвать [функцию-член](classes.html) или [функцию-расширение](extensions.html#extension-functions), используйте
точку `.`:

```kotlin
// Создаёт экземпляр класса Stream и вызывает read()
Stream().read()
```

<a name="parameters"></a>

<!-- ### Parameters -->
### Параметры

<!-- Declare function parameters using Pascal notation: `name: Type`.
You must separate parameters using commas and give each parameter a type explicitly: -->
Параметры функции объявляются с помощью Pascal-нотации: `имя: Тип`. Параметры разделяются запятыми, и тип каждого
параметра должен быть указан явно:

```kotlin
fun powerOf(number: Int, exponent: Int): Int { /*...*/ }
```

<!-- Inside the body of a function, received arguments are read-only (implicitly declared as `val`): -->
Внутри тела функции полученные аргументы доступны только для чтения (они неявно объявлены как `val`):

```kotlin
fun powerOf(number: Int, exponent: Int): Int {
    number = 2 // Ошибка: 'val' нельзя переназначить
}
```

<!-- You can use a [trailing comma](coding-conventions.md#trailing-commas) when declaring function parameters: -->
Вы можете использовать [завершающую запятую](coding-conventions.html#trailing-commas) при объявлении параметров функции:

```kotlin
fun powerOf(
    number: Int,
    exponent: Int, // завершающая запятая
) { /*...*/ }
```

<!-- Trailing commas help with refactorings and code maintenance:
you can move parameters within the declaration without worrying about which is going to be the last one. -->
Завершающие запятые помогают при рефакторинге и сопровождении кода: вы можете перемещать параметры внутри объявления и
не думать о том, какой из них окажется последним.

<!-- Kotlin functions can receive other functions as parameters — and be passed as arguments.
For more information, see Higher-order functions and lambdas. -->
> Функции Kotlin могут принимать другие функции в качестве параметров, а также передаваться как аргументы. Подробнее см.
> [Функции высшего порядка и лямбды](lambdas.html).

<a name="default-arguments"></a>
<a name="parameters-with-default-values"></a>

<!-- ### Parameters with default values -->
### Параметры со значениями по умолчанию

<!-- You can make a function parameter optional by specifying a default value for it.
Kotlin uses the default value when you call the function without providing an argument that corresponds to that parameter.
Parameters with default values are also known as optional parameters. -->
Параметр функции можно сделать необязательным, указав для него значение по умолчанию. Kotlin использует значение по
умолчанию, когда вы вызываете функцию без аргумента, соответствующего этому параметру. Параметры со значениями по
умолчанию также называют опциональными параметрами.

<!-- Optional parameters reduce the need for multiple overloads, since you don't have to declare different versions of a function
just to allow skipping a parameter with a reasonable default. -->
Опциональные параметры уменьшают потребность в перегрузках: вам не нужно объявлять несколько версий одной функции только
для того, чтобы разрешить пропуск параметра с разумным значением по умолчанию.

<!-- Set a default value by appending `=` to the parameter declaration: -->
Чтобы задать значение по умолчанию, добавьте `=` к объявлению параметра:

```kotlin
fun read(
    b: ByteArray,
    // Значение 'off' по умолчанию равно 0
    off: Int = 0,
    // Значение 'len' по умолчанию вычисляется
    // как размер массива 'b'
    len: Int = b.size,
) { /*...*/ }
```

<!-- When you declare a parameter with a default value before a parameter without a default value,
you can only use the default value by naming the argument: -->
Когда параметр **со** значением по умолчанию объявлен перед параметром **без** значения по умолчанию, использовать это
значение можно только через [именованный аргумент](#named-arguments):

```kotlin
fun greeting(
    userId: Int = 0,
    message: String,
) { /*...*/ }

fun main() {
    // Используется значение 0 по умолчанию для 'userId'
    greeting(message = "Hello!")

    // Ошибка: для параметра 'userId' не передано значение
    greeting("Hello!")
}
```

<!-- Trailing lambdas are an exception to this rule, since the last parameter must correspond to the passed function: -->
[Завершающие лямбды](lambdas.html#passing-trailing-lambdas) являются исключением из этого правила, поскольку последний
параметр должен соответствовать переданной функции:

```kotlin
fun main() {
    fun greeting(
        userId: Int = 0,
        message: () -> Unit,
    ) {
        println(userId)
        message()
    }

    // Используется значение по умолчанию для 'userId'
    greeting() { println("Hello!") }
    // 0
    // Hello!
}
```

<!-- Overriding methods always use the base method's default parameter values.
When you override a method that has default parameter values, you must omit the default parameter values from the signature: -->
[Переопределённые методы](inheritance.html#overriding-methods) всегда используют значения параметров по умолчанию из
базового метода. При переопределении метода, у которого есть значения параметров по умолчанию, эти значения должны быть
опущены из сигнатуры:

```kotlin
open class Shape {
    open fun draw(width: Int = 10, height: Int = 5) { /*...*/ }
}

class Rectangle : Shape() {
    // Здесь нельзя указывать значения по умолчанию,
    // но эта функция всё равно по умолчанию использует
    // 10 для 'width' и 5 для 'height'.
    override fun draw(width: Int, height: Int) { /*...*/ }
}
```

<a name="non-constant-expressions-as-default-values"></a>

<!-- #### Non-constant expressions as default values -->
#### Неконстантные выражения как значения по умолчанию

<!-- You can assign a parameter a default value that isn't constant.
For example, the default can be the result of a function call or a calculation that uses the values of other arguments,
like the `len` parameter in this example: -->
Параметру можно назначить значение по умолчанию, которое не является константой. Например, значением по умолчанию может
быть результат вызова функции или вычисления, использующего значения других аргументов, как параметр `len` в этом
примере:

```kotlin
fun read(
    b: ByteArray,
    off: Int = 0,
    len: Int = b.size,
) { /*...*/ }
```

<!-- Parameters that refer to the values of other parameters must be declared later in the order.
In this example, `len` must be declared after `b`. -->
Параметры, которые ссылаются на значения других параметров, должны быть объявлены позже них. В этом примере `len` должен
быть объявлен после `b`.

<!-- In general, you can assign any expression as the default value of a parameter.
However, default values are only evaluated when the function is called without the corresponding parameter
and a default value needs to be assigned.
For example, this function prints out a line only when it is called without the `print` parameter: -->
В общем случае значением параметра по умолчанию может быть любое выражение. Однако значения по умолчанию вычисляются
только тогда, когда функция вызывается без соответствующего параметра и нужно подставить значение по умолчанию. Например,
эта функция выводит строку только при вызове без параметра `print`:

```kotlin
fun main() {
    fun read(
        b: Int,
        print: Unit? = println("Аргумент 'print' не передан")
    ) {
        println(b)
    }

    // Выводит "Аргумент 'print' не передан", затем "1"
    read(1)
    // Выводит только "1"
    read(1, null)
}
```

<!-- If the last parameter in a function declaration has a functional type,
you can pass the corresponding lambda argument either as a named argument or outside the parentheses: -->
Если последний параметр в объявлении функции имеет функциональный тип, соответствующий
[лямбда-аргумент](lambdas.html#lambda-expression-syntax) можно передать либо как именованный аргумент, либо
[за скобками](lambdas.html#passing-trailing-lambdas):

```kotlin
fun main() {
    fun log(
        level: Int = 0,
        code: Int = 1,
        action: () -> Unit,
    ) {
        println(level)
        println(code)
        action()
    }

    // Передаёт 1 для 'level' и использует значение 1 по умолчанию для 'code'
    log(1) { println("Connection established") }

    // Использует оба значения по умолчанию: 0 для 'level' и 1 для 'code'
    log(action = { println("Connection established") })

    // Эквивалентно предыдущему вызову, использует оба значения по умолчанию
    log { println("Connection established") }
}
```

<a name="named-arguments"></a>

<!-- ### Named arguments -->
### Именованные аргументы

<!-- You can name one or more of a function's arguments when calling it.
This can be helpful when a function call has many arguments.
In such cases, it's difficult to associate a value with an argument, especially if it's `null` or a boolean value. -->
При вызове функции вы можете явно указать имена одного или нескольких аргументов. Это может быть полезно, когда у вызова
функции много аргументов. В таких случаях сложно связать значение с конкретным аргументом, особенно если это `null` или
логическое значение.

<!-- When you use named arguments in a function call, you can list them in any order. -->
При использовании именованных аргументов в вызове функции их можно перечислять в любом порядке.

<!-- Consider the `reformat()` function, which has 4 arguments with default values: -->
Рассмотрим функцию `reformat()`, у которой есть четыре аргумента со значениями по умолчанию:

```kotlin
fun reformat(
    str: String,
    normalizeCase: Boolean = true,
    upperCaseFirstLetter: Boolean = true,
    divideByCamelHumps: Boolean = false,
    wordSeparator: Char = ' ',
) { /*...*/ }
```

<!-- When calling this function, you can name some of the arguments: -->
При вызове этой функции можно явно указать имена некоторых аргументов:

```kotlin
reformat(
    "String!",
    normalizeCase = false,
    upperCaseFirstLetter = false,
    divideByCamelHumps = true,
    '_'
)
```

<!-- You can skip all the arguments with default values: -->
Вы можете пропустить все аргументы со значениями по умолчанию:

```kotlin
reformat("This is a long String!")
```

<!-- You can also skip some arguments with default values, rather than omitting them all.
However, after the first skipped argument, you must name all subsequent arguments: -->
Вы также можете пропустить только *некоторые* аргументы со значениями по умолчанию, а не все сразу. Однако после первого
пропущенного аргумента вы должны указывать имена всех последующих аргументов:

```kotlin
reformat(
    "This is a short String!",
    upperCaseFirstLetter = false,
    wordSeparator = '_'
)
```

<!-- You can pass a variable number of arguments (`vararg`) by naming the corresponding argument.
In this example, it's an array: -->
Вы можете передать [переменное количество аргументов](#variable-number-of-arguments-varargs) (`vararg`), указав имя
соответствующего аргумента. В этом примере передаётся массив:

```kotlin
fun mergeStrings(vararg strings: String) { /*...*/ }

mergeStrings(strings = arrayOf("a", "b", "c"))
```

<!-- When calling Java functions on the JVM, you can't use the named argument syntax because Java bytecode does not
always preserve the names of function parameters. -->
> При вызове Java-функций на JVM нельзя использовать синтаксис именованных аргументов, потому что байт-код Java не всегда
> сохраняет имена параметров функции.

<a name="return-types"></a>
<a name="explicit-return-types"></a>

<!-- ### Return types -->
### Типы возвращаемых значений

<!-- When you declare a function with a block body (by putting instructions within curly braces `{}`),
you must always specify a return type explicitly.
The only exception is when they return `Unit`, in which case specifying the return type is optional. -->
Когда вы объявляете функцию с блочным телом (инструкциями в фигурных скобках `{}`), тип возвращаемого значения всегда
нужно указывать явно. Единственное исключение - функции, возвращающие `Unit`: [в этом случае тип можно не указывать](#unit-returning-functions).

<!-- Kotlin doesn't infer return types for functions with block bodies.
Their control flow can be complex, which makes the return type unclear to the reader and sometimes even to the compiler.
However, Kotlin can infer the return type for single-expression functions if you don't specify it. -->
Kotlin не выводит типы возвращаемых значений для функций с блочным телом. Их поток управления может быть сложным, из-за
чего тип возвращаемого значения становится неочевидным для читателя, а иногда даже для компилятора. Однако Kotlin может
вывести тип для [функций с одним выражением](#single-expression-functions), если вы не указали его явно.

<a name="single-expression-functions"></a>

<!-- ### Single-expression functions -->
### Функции с одним выражением

<!-- When the function body consists of a single expression, you can omit the curly braces and specify the body after an `=` symbol: -->
Когда тело функции состоит из одного выражения, фигурные скобки `{}` можно опустить, а тело указать после символа `=`:

```kotlin
fun double(x: Int): Int = x * 2
```

<!-- Most of the time you don't have to explicitly declare the return type: -->
В большинстве случаев [тип возвращаемого значения](#return-types) не нужно объявлять явно:

```kotlin
// Компилятор выводит, что функция возвращает Int
fun double(x: Int) = x * 2
```

<!-- The compiler can sometimes run into problems when inferring return types from single expressions.
In such cases, you should add the return type explicitly.
For example, functions that are recursive or mutually recursive (calling each other)
and functions with typeless expressions like `fun empty() = null` always require a return type. -->
Иногда компилятор может столкнуться с проблемами при выводе типа возвращаемого значения из одного выражения. В таких
случаях тип следует добавить явно. Например, функции, которые являются рекурсивными или взаимно рекурсивными (вызывают
друг друга), а также функции с нетипизированными выражениями вроде `fun empty() = null`, всегда требуют явного типа
возвращаемого значения.

<!-- When you do use an inferred return type,
make sure to check the actual result because the compiler may infer a type that is less useful to you.
In the example above, if you want the `double()` function to return `Number` instead of `Int`,
you have to declare this explicitly. -->
Когда вы используете выведенный тип возвращаемого значения, проверяйте фактический результат: компилятор может вывести
тип, который окажется менее полезным для вас. В примере выше, если вы хотите, чтобы функция `double()` возвращала
`Number`, а не `Int`, это нужно указать явно.

<a name="unit-returning-functions"></a>

<!-- ### Unit-returning functions -->
### Функции с возвращаемым типом Unit

<!-- If a function has a block body (instructions within curly braces `{}`) and does not return a useful value,
the compiler assumes its return type is `Unit`.
`Unit` is a type that has only one value, also called `Unit`. -->
Если у функции есть блочное тело (инструкции в фигурных скобках `{}`), и она не возвращает полезного значения, компилятор
считает, что её тип возвращаемого значения - `Unit`. `Unit` - это тип, у которого есть только одно значение, также
называемое `Unit`.

<!-- You don't have to specify `Unit` as a return type, except for functional type parameters.
You never have to return `Unit` explicitly. -->
Указывать `Unit` как возвращаемый тип самой функции не нужно. Исключение - параметры функционального типа, например
`() -> Unit`. Возвращать `Unit` явно никогда не требуется.

<!-- For example, you can declare a `printHello()` function without returning `Unit`: -->
Например, функцию `printHello()` можно объявить без возвращения `Unit`:

```kotlin
// В объявлении параметра функционального типа ('action')
// всё равно нужен явный возвращаемый тип
fun printHello(name: String?, action: () -> Unit) {
    if (name != null)
        println("Hello $name")
    else
        println("Hi there!")

    action()
}

fun main() {
    printHello("Kodee") {
        println("This runs after the greeting.")
    }
    // Hello Kodee
    // This runs after the greeting.

    printHello(null) {
        println("No name provided, but action still runs.")
    }
    // No name provided, but action still runs
}
```

<!-- Which is equivalent to this verbose declaration: -->
Это эквивалентно следующему более подробному объявлению:

```kotlin
fun printHello(name: String?, action: () -> Unit): Unit {
    if (name != null)
        println("Hello $name")
    else
        println("Hi there!")

    action()
    return Unit
}

fun main() {
    printHello("Kodee") {
        println("This runs after the greeting.")
    }
    // Hello Kodee
    // This runs after the greeting.

    printHello(null) {
        println("No name provided, but action still runs.")
    }
    // No name provided, but action still runs
}
```

<!-- You can use a `return` statement inside an expression body if the function's return type is specified explicitly: -->
В теле-выражении можно использовать оператор `return`, если тип возвращаемого значения функции указан явно:

```kotlin
fun getDisplayNameOrDefault(userId: String?): String =
    getDisplayName(userId ?: return "default")
```

<a name="variable-number-of-arguments-varargs"></a>

<!-- ### Variable number of arguments (varargs) -->
### Нефиксированное число аргументов (varargs)

<!-- To pass a variable number of arguments to a function, you can mark one of its parameters
(usually the last one) with the `vararg` modifier.
Inside a function, you can use a `vararg`-parameter of type `T` as an array of `T`: -->
Чтобы передать в функцию переменное количество аргументов, один из её параметров (обычно последний) можно пометить
модификатором `vararg`. Внутри функции `vararg`-параметр типа `T` можно использовать как массив элементов `T`:

```kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts - это массив
        result.add(t)
    return result
}
```

<!-- Then you can pass a variable number of arguments to the function: -->
После этого в функцию можно передать переменное количество аргументов:

```kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts - это массив
        result.add(t)
    return result
}

fun main() {
    val list = asList(1, 2, 3)
    println(list)
    // [1, 2, 3]
}
```

<!-- Only one parameter can be marked as `vararg`.
If you declare a `vararg` parameter anywhere other than last in the parameter list, you must pass values for the following
parameters using named arguments.
If a parameter has a function type, you can also pass its value by placing a lambda outside the parentheses. -->
Только один параметр может быть помечен как `vararg`. Если `vararg`-параметр объявлен не последним в списке параметров,
значения для следующих параметров нужно передавать с помощью именованных аргументов. Если параметр имеет функциональный
тип, его значение также можно передать, поместив лямбду за скобками.

<!-- When you call a `vararg`-function, you can pass arguments individually, as in the example of `asList(1, 2, 3)`.
If you already have an array and want to pass its contents to a function as a `vararg` parameter or as a part of it,
use the spread operator by prefixing the array name with `*`: -->
При вызове `vararg`-функции можно передавать аргументы по отдельности, как в примере `asList(1, 2, 3)`. Если у вас уже
есть массив и вы хотите передать его содержимое в функцию как `vararg`-параметр или его часть, используйте оператор
`spread`, добавив `*` перед именем массива:

```kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts)
        result.add(t)
    return result
}

fun main() {
    val a = arrayOf(1, 2, 3)

    // Функция получает массив [-1, 0, 1, 2, 3, 4]
    val list = asList(-1, 0, *a, 4)

    println(list)
    // [-1, 0, 1, 2, 3, 4]
}
```

<!-- If you want to pass a primitive type array as `vararg`, you need to convert it to a regular (typed) array
using the `.toTypedArray()` function: -->
Если вы хотите передать [массив примитивного типа](basic-types.html#primitive-type-arrays) как `vararg`, его нужно
преобразовать в обычный (типизированный) массив с помощью функции
[`toTypedArray()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/to-typed-array.html):

```kotlin
// 'a' - это IntArray, массив примитивного типа
val a = intArrayOf(1, 2, 3)
val list = asList(-1, 0, *a.toTypedArray(), 4)
```

<a name="infix-notation"></a>

<!-- ### Infix notation -->
### Инфиксная запись

<!-- You can declare functions that can be called without parentheses or the period by using the `infix` keyword.
This can help make simple function calls in your code easier to read. -->
С помощью ключевого слова `infix` можно объявлять функции, которые вызываются без круглых скобок и точки. Это может
сделать простые вызовы функций в коде более читаемыми.

```kotlin
infix fun Int.shl(x: Int): Int { /*...*/ }

// Вызов функции с помощью обычной записи
1.shl(2)

// Вызов функции с помощью инфиксной записи
1 shl 2
```

<!-- Infix functions must meet the following requirements: -->
Инфиксные функции должны соответствовать следующим требованиям:

<!-- * They must be member functions of a class or extension functions.
* They must have a single parameter.
* The parameter must not accept a variable number of arguments (`vararg`) and must have no default value. -->
* они должны быть функциями-членами класса или [функциями-расширениями](extensions.html);
* они должны иметь только один параметр;
* параметр не должен принимать [переменное количество аргументов](#variable-number-of-arguments-varargs) (`vararg`) и
  не должен иметь [значения по умолчанию](#parameters-with-default-values).

<!-- Infix function calls have lower precedence than arithmetic operators, type casts, and the `rangeTo` operator.
The following expressions are equivalent:
* `1 shl 2 + 3` is equivalent to `1 shl (2 + 3)`
* `0 until n * 2` is equivalent to `0 until (n * 2)`
* `xs union ys as Set<*>` is equivalent to `xs union (ys as Set<*>)`

On the other hand, an infix function call's precedence is higher than that of the boolean operators `&&` and `||`, `is`-
and `in`-checks, and some other operators. These expressions are equivalent as well:
* `a && b xor c` is equivalent to `a && (b xor c)`
* `a xor b in c` is equivalent to `(a xor b) in c` -->
> Вызовы инфиксных функций имеют более низкий приоритет, чем арифметические операторы, приведение типов и оператор
> `rangeTo`. Следующие выражения эквивалентны:
>
> * `1 shl 2 + 3` эквивалентно `1 shl (2 + 3)`,
> * `0 until n * 2` эквивалентно `0 until (n * 2)`,
> * `xs union ys as Set<*>` эквивалентно `xs union (ys as Set<*>)`.
>
> С другой стороны, приоритет вызова инфиксной функции выше, чем у логических операторов `&&` и `||`, `is`- и
> `in`-проверок и некоторых других операторов. Эти выражения также эквивалентны:
>
> * `a && b xor c` эквивалентно `a && (b xor c)`,
> * `a xor b in c` эквивалентно `(a xor b) in c`.

<!-- Note that infix functions always require both the receiver and the parameter to be specified.
When you call a method on the current receiver using the infix notation, use `this` explicitly.
This ensures unambiguous parsing. -->
Обратите внимание, что инфиксные функции всегда требуют указания и получателя, и параметра. Когда вы вызываете метод на
текущем получателе с помощью инфиксной записи, явно используйте `this`. Это обеспечивает однозначный синтаксический
разбор.

```kotlin
class MyStringCollection {
    val items = mutableListOf<String>()

    infix fun add(s: String) {
        println("Adding: $s")
        items += s
    }

    fun build() {
        add("first")      // Верно: обычный вызов функции
        this add "second" // Верно: инфиксный вызов с явным получателем
        // add "third"    // Ошибка компиляции: нужен явный получатель
    }

    fun printAll() = println("Items = $items")
}

fun main() {
    val myStrings = MyStringCollection()
    // Добавляет "first" и "second" в список
    myStrings.build()

    myStrings.printAll()
    // Adding: first
    // Adding: second
    // Items = [first, second]
}
```

<a name="function-scope"></a>

<!-- ## Function scope -->
## Область видимости функций

<!-- You can declare Kotlin functions at the top level in a file, meaning you do not need to create a class to hold a function.
Functions can also be declared locally as member functions or extension functions. -->
Функции Kotlin можно объявлять на верхнем уровне файла: вам не нужно создавать класс только для того, чтобы поместить в
него функцию. Функции также можно объявлять локально, как функции-члены или функции-расширения.

<a name="local-functions"></a>

<!-- ### Local functions -->
### Локальные функции

<!-- Kotlin supports local functions, which are functions declared inside other functions.
For example, the following code implements the Depth-first search algorithm for a given graph.
The local `dfs()` function inside the outer `dfs()` function hides the implementation and handles recursive calls: -->
Kotlin поддерживает локальные функции, то есть функции, объявленные внутри других функций. Например, следующий код
реализует алгоритм поиска в глубину для заданного графа. Локальная функция `dfs()` внутри внешней функции `dfs()`
скрывает реализацию и обрабатывает рекурсивные вызовы:

```kotlin
class Person(val name: String) {
    val friends = mutableListOf<Person>()
}

class SocialGraph(val people: List<Person>)

fun dfs(graph: SocialGraph) {
    fun dfs(current: Person, visited: MutableSet<Person>) {
        if (!visited.add(current)) return
        println("Visited ${current.name}")
        for (friend in current.friends)
            dfs(friend, visited)
    }
    dfs(graph.people[0], HashSet())
}

fun main() {
    val alice = Person("Alice")
    val bob = Person("Bob")
    val charlie = Person("Charlie")
    alice.friends += bob
    bob.friends += charlie
    charlie.friends += alice
    val network = SocialGraph(listOf(alice, bob, charlie))
    dfs(network)
}
```

<!-- A local function can access local variables of outer functions (the closure).
In the case above, the `visited` function parameter can be a local variable: -->
Локальная функция может получать доступ к локальным переменным внешних функций (замыкание). В примере выше параметр
функции `visited` может быть локальной переменной:

```kotlin
class Person(val name: String) {
    val friends = mutableListOf<Person>()
}

class SocialGraph(val people: List<Person>)

fun dfs(graph: SocialGraph) {
    val visited = HashSet<Person>()
    fun dfs(current: Person) {
        if (!visited.add(current)) return
        println("Visited ${current.name}")
        for (friend in current.friends)
            dfs(friend)
    }
    dfs(graph.people[0])
}

fun main() {
    val alice = Person("Alice")
    val bob = Person("Bob")
    val charlie = Person("Charlie")
    alice.friends += bob
    bob.friends += charlie
    charlie.friends += alice
    val network = SocialGraph(listOf(alice, bob, charlie))
    dfs(network)
}
```

<a name="member-functions"></a>

<!-- ### Member functions -->
### Функции-члены

<!-- A member function is a function that is defined inside a class or object: -->
Функция-член - это функция, объявленная внутри класса или объекта:

```kotlin
class Sample {
    fun foo() { print("Foo") }
}
```

<!-- To call member functions, write the instance or object name, then add a `.` and write the function name: -->
Чтобы вызвать функцию-член, напишите имя экземпляра или объекта, затем добавьте `.` и имя функции:

```kotlin
// Создаёт экземпляр класса Stream и вызывает read()
Stream().read()
```

<!-- For more information on classes and overriding members see Classes and Inheritance. -->
Для более подробной информации о классах и переопределении членов см. [Классы](classes.html) и
[Наследование](classes.html#inheritance).

<a name="generic-functions"></a>

<!-- ## Generic functions -->
## Функции-обобщения

<!-- You can specify generic parameters for a function by using angle brackets `<>` before the function name: -->
Обобщённые параметры функции можно указать с помощью угловых скобок `<>` перед именем функции:

```kotlin
fun <T> singletonList(item: T): List<T> { /*...*/ }
```

<!-- For more information on generic functions, see Generics. -->
Для более подробной информации об обобщённых функциях см. [Обобщения](generics.html).

<a name="tail-recursive-functions"></a>

<!-- ## Tail recursive functions -->
## Функции с хвостовой рекурсией

<!-- Kotlin supports a style of functional programming known as tail recursion.
For some algorithms that would normally use loops, you can use a recursive function instead without the risk of stack overflow.
When a function is marked with the `tailrec` modifier and meets the required formal conditions, the compiler optimizes out
the recursion, leaving behind a fast and efficient loop based version instead: -->
Kotlin поддерживает стиль функционального программирования, известный как [хвостовая рекурсия](https://ru.wikipedia.org/wiki/%D0%A5%D0%B2%D0%BE%D1%81%D1%82%D0%BE%D0%B2%D0%B0%D1%8F_%D1%80%D0%B5%D0%BA%D1%83%D1%80%D1%81%D0%B8%D1%8F).
Для некоторых алгоритмов, которые обычно реализуют с помощью циклов, можно использовать рекурсивную функцию без риска
переполнения стека. Когда функция помечена модификатором `tailrec` и соответствует формальным требованиям, компилятор
оптимизирует рекурсию, оставляя вместо неё быструю и эффективную версию на основе цикла:

```kotlin
import kotlin.math.cos
import kotlin.math.abs

// Произвольная "достаточно хорошая" точность
val eps = 1E-10

tailrec fun findFixPoint(x: Double = 1.0): Double =
    if (abs(x - cos(x)) < eps) x else findFixPoint(cos(x))
```

<!-- This code calculates the fixed point of cosine (a mathematical constant).
The function calls `cos()` repeatedly starting at `1.0` until the result no longer changes,
yielding a result of `0.7390851332151611` for the specified `eps` precision.
The code is equivalent to this more traditional style: -->
Этот код вычисляет неподвижную точку косинуса (математическую константу). Функция многократно вызывает `cos()`, начиная с
`1.0`, пока результат не перестанет изменяться, и для указанной точности `eps` получает значение `0.7390851332151611`.
Код эквивалентен следующей более традиционной форме:

```kotlin
import kotlin.math.cos
import kotlin.math.abs

// Произвольная "достаточно хорошая" точность
val eps = 1E-10

private fun findFixPoint(): Double {
    var x = 1.0
    while (true) {
        val y = cos(x)
        if (abs(x - y) < eps) return x
        x = cos(x)
    }
}
```

<!-- You can apply the `tailrec` modifier to a function only when it calls itself as its final operation.
You cannot use tail recursion when there is more code after the recursive call,
within `try`/`catch`/`finally` blocks, or when the function is open. -->
Модификатор `tailrec` можно применять к функции только тогда, когда она вызывает саму себя в качестве последней
операции. Хвостовую рекурсию нельзя использовать, если после рекурсивного вызова есть другой код, внутри блоков
[`try`/`catch`/`finally`](exceptions.html) или когда функция является [open](inheritance.html).

<!-- **See also**: -->
**См. также:**

<!-- * [Inline functions](inline-functions.md)
* [Extension functions](extensions.md)
* [Higher-order functions and lambdas](lambdas.md) -->
* [Встроенные функции](inline-functions.html)
* [Функции-расширения](extensions.html)
* [Функции высшего порядка и лямбды](lambdas.html)
