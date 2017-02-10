---
type: doc
layout: reference
category: "Syntax"
title: "Functions"
---

<!--# Functions-->
#Функции

<!--## Function Declarations-->
## Объявление функций

<!--Functions in Kotlin are declared using the *fun*{: .keyword } keyword-->
В <b>Kotlin</b> функции объявляются с помощью ключевого слова *fun*

``` kotlin
fun double(x: Int): Int {
}
```

<!--## Function Usage-->
## Применение функций

<!--Calling functions uses the traditional approach-->
При вызове функции используется традиционный подход

``` kotlin
val result = double(2)
```

<!--Calling member functions uses the dot notation-->
Для вызова вложенной функции используется знак точки

``` kotlin
Sample().foo() //создаёт экземпляр класса Sample и вызывает foo
```

<!--### Infix notation-->
###Инфиксное обозначение

<!--Functions can also be called using infix notations when-->
Функции так же могут быть вызваны при помощи использования инфиксных обозначений, когда:

<!--* They are member functions or [extension functions](extensions.html)-->
* Они являются членом другой функции или [выражения](extensions.html)
<!--* They have a single parameter-->
* В них используется один параметр
<!--* They are marked with the `infix` keyword-->
* Когда они помечены ключевым словом `infix`

``` kotlin
// Определить выражение как Int
infix fun Int.shl(x: Int): Int {
...
}

// вызвать выражение функции, используя infix

1 shl 2

// то же самое, что

1.shl(2)
```

<!--### Parameters-->
### Параметры

<!--Function parameters are defined using Pascal notation, i.e. *name*: *type*. Parameters are separated using commas. Each parameter must be explicitly typed.-->
Параметры функции записываются аналогично системе обозначений в языке Pascal, *имя*:*тип*. Параметры разделены запятыми. Каждый параметр
должен быть явно указан.

``` kotlin
fun powerOf(number: Int, exponent: Int) {
...
}
```

<!--### Default Arguments-->
### Аргументы по умолчанию.

<!--Function parameters can have default values, which are used when a corresponding argument is omitted. This allows for a reduced number of overloads compared to
other languages.-->
Параметры функции могут иметь значения по умолчаню, которые используются в случае, если аргумент функции опушен при её вызове. Это позволяет снизить уровень перегруженности кода в сравнении с другими языками.

``` kotlin
fun read(b: Array<Byte>, off: Int = 0, len: Int = b.size()) {
...
}
```

<!--Default values are defined using the **=** after type along with the value.-->
Значения по умолчанию указываются с помощью **=** после типа.

<!--Overriding methods always use the same default parameter values as the base method.
When overriding a method with default parameters values, the default parameter values must be omitted from the signature:-->
Переопределённый методы всегда используют те же самые значения по умолчанию, что их базовые методы. При переопределении методов с значениями по умолчанию, эти параметры должны быть опущены:

``` kotlin
open class A {
    open fun foo(i: Int = 10) { ... }
}

class B : A() {
    override fun foo(i: Int) { ... }  // значение по умолчанию указать нельзя
}
```

<!--### Named Arguments-->
### Имена в названиях аргументов

<!--Function parameters can be named when calling functions. This is very convenient when a function has a high number of parameters or default ones.-->
Параметры функции могут быть названы в момент вызова функций. Это очень удобно, когда у функции большой список параметров, в том числе со значениями по умолчанию.

<!--Given the following function-->
Рассмотрим такую функцию

``` kotlin
fun reformat(str: String,
             normalizeCase: Boolean = true,
             upperCaseFirstLetter: Boolean = true,
             divideByCamelHumps: Boolean = false,
             wordSeparator: Char = ' ') {
...
}
```

<!--we could call this using default arguments-->
мы можем вызвать её, используя аргументы по умолчанию

``` kotlin
reformat(str)
```

<!--However, when calling it with non-default, the call would look something like-->
Однако, при вызове этой функции без аргументов по умолчанию, получится что-то вроде

``` kotlin
reformat(str, true, true, false, '_')
```

<!--With named arguments we can make the code much more readable-->
С названными аргументами мы можем сделать код намного более читаемым

``` kotlin
reformat(str,
    normalizeCase = true,
    upperCaseFirstLetter = true,
    divideByCamelHumps = false,
    wordSeparator = '_'
)
```

<!--and if we do not need all arguments-->
а, если нам не нужны все эти аргументы

``` kotlin
reformat(str, wordSeparator = '_')
```

<!--Note that the named argument syntax cannot be used when calling Java functions, because Java bytecode does not
always preserve names of function parameters.-->
Обратите внимание, что синтаксис названных аргументов не может быть использован при вызове <b>Java</b> функций, потому как байт-код <b>Java</b> всегда хранит имена параметров функции.


<!--### Unit-returning functions-->
### Функции с Unit-возвращаемым типом.

<!--If a function does not return any useful value, its return type is `Unit`. `Unit` is a type with only one value - `Unit`. This
value does not have to be returned explicitly-->
Если функция не возвращает никакого полезного значения, её возвращаемый тип - `Unit` . `Unit` - тип только с одним значением - `Unit`. 
Это вовзращаемое значение не нуждается в явном указании

``` kotlin
fun printHello(name: String?): Unit {
    if (name != null)
        println("Hello ${name}")
    else
        println("Hi there!")
    // `return Unit` или `return` необязательны
}
```

<!--The `Unit` return type declaration is also optional. The above code is equivalent to-->
Указание типа `Unit` в качестве возвращаемого значения тоже является необязательныи. Код, написанный выше, совершенно идентичен с 

``` kotlin
fun printHello(name: String?) {
    ...
}
```

<!--### Single-Expression functions-->
### Функции с одним выражением

<!--When a function returns a single expression, the curly braces can be omitted and the body is specified after a **=** symbol-->
Когда функция возвращает одно-единственное выражение, круглые скобки `{ }` быть опущены и тело функции может быть описано после знака `=`

``` kotlin
fun double(x: Int): Int = x * 2
```

<!--Explicitly declaring the return type is [optional](#explicit-return-types) when this can be inferred by the compiler-->
Явное объявление типа возвращаемого значения также необязательно. В том случае, если компилятор в состоянии вычислить его сам.

``` kotlin
fun double(x: Int) = x * 2
```

<!--### Explicit return types-->
### Явные типы возвращаемых значений

<!--Functions with block body must always specify return types explicitly, unless it's intended for them to return `Unit`, [in which case it is optional](#unit-returning-functions).
Kotlin does not infer return types for functions with block bodies because such functions may have complex control flow in the body, and the return type will be non-obvious to the reader (and sometimes even for the compiler). -->
Функции, в которых есть тело, всегда должны указывать возвращаемый ими тип данных (если в этом качестве не указан тип `Unit`).
<b>Kotlin</b> не вычисляет самостоятельно тип возвращаемого значения для функций с заключённым в них блоком кода потому, что подобные функции могут иметь сложную структуру и возвращаемый тип неочевиден для читающего этот код человека (иногда даже для компилятора).

<!--### Variable number of arguments (Varargs)-->
### Нефиксированное число аргументов (Varargs)

<!--A parameter of a function (normally the last one) may be marked with `vararg` modifier:-->
Параметр функции (обчычно для этого используется последний) может быть помечен модификатором `vararg`:

``` kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts is an Array
        result.add(t)
    return result
}
```

allowing a variable number of arguments to be passed to the function:

``` kotlin
val list = asList(1, 2, 3)
```

Inside a function a `vararg`-parameter of type `T` is visible as an array of `T`, i.e. the `ts` variable in the example above has type `Array<out T>`.

Only one parameter may be marked as `vararg`. If a `vararg` parameter is not the last one in the list, values for the
following parameters can be passed using the named argument syntax, or, if the parameter has a function type, by passing
a lambda outside parentheses.

When we call a `vararg`-function, we can pass arguments one-by-one, e.g. `asList(1, 2, 3)`, or, if we already have an array
 and want to pass its contents to the function, we use the **spread** operator (prefix the array with `*`):

```kotlin
val a = arrayOf(1, 2, 3)
val list = asList(-1, 0, *a, 4)
```

## Function Scope

In Kotlin functions can be declared at top level in a file, meaning you do not need to create a class to hold a function, like languages such as Java, C# or Scala. In addition
to top level functions, Kotlin functions can also be declared local, as member functions and extension functions.

### Local Functions

Kotlin supports local functions, i.e. a function inside another function

``` kotlin
fun dfs(graph: Graph) {
    fun dfs(current: Vertex, visited: Set<Vertex>) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v, visited)
    }

    dfs(graph.vertices[0], HashSet())
}
```

Local function can access local variables of outer functions (i.e. the closure), so in the case above, the *visited* can be a local variable

``` kotlin
fun dfs(graph: Graph) {
    val visited = HashSet<Vertex>()
    fun dfs(current: Vertex) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v)
    }

    dfs(graph.vertices[0])
}
```

### Member Functions

A member function is a function that is defined inside a class or object

``` kotlin
class Sample() {
    fun foo() { print("Foo") }
}
```

Member functions are called with dot notation

``` kotlin
Sample().foo() // creates instance of class Sample and calls foo
```

For more information on classes and overriding members see [Classes](classes.html) and [Inheritance](classes.html#inheritance)

## Generic Functions

Functions can have generic parameters which are specified using angle brackets before the function name

``` kotlin
fun <T> singletonList(item: T): List<T> {
    // ...
}
```

For more information on generic functions see [Generics](generics.html)

## Inline Functions

Inline functions are explained [here](inline-functions.html)

## Extension Functions

Extension functions are explained in [their own section](extensions.html)

## Higher-Order Functions and Lambdas

Higher-Order functions and Lambdas are explained in [their own section](lambdas.html)

## Tail recursive functions

Kotlin supports a style of functional programming known as [tail recursion](https://en.wikipedia.org/wiki/Tail_call).
This allows some algorithms that would normally be written using loops to instead be written using a recursive function, but without the risk of stack overflow.
When a function is marked with the `tailrec` modifier and meets the required form the compiler optimises out the recursion, leaving behind a fast and efficient loop based version instead.

``` kotlin
tailrec fun findFixPoint(x: Double = 1.0): Double
        = if (x == Math.cos(x)) x else findFixPoint(Math.cos(x))
```

This code calculates the fixpoint of cosine, which is a mathematical constant. It simply calls Math.cos repeatedly starting at 1.0 until the result doesn't change any more, yielding a result of 0.7390851332151607. The resulting code is equivalent to this more traditional style:

``` kotlin
private fun findFixPoint(): Double {
    var x = 1.0
    while (true) {
        val y = Math.cos(x)
        if (x == y) return y
        x = y
    }
}
```

To be eligible for the `tailrec` modifier, a function must call itself as the last operation it performs. You cannot use tail recursion when there is more code after the recursive call, and you cannot use it within try/catch/finally blocks. Currently tail recursion is only supported in the JVM backend.
