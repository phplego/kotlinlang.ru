---
type: doc
layout: reference
category: "Syntax"
title: "Функции"
url: https://kotlinlang.ru/docs/functions.html
---

<!-- При переводе статьи оригинальная версия была от 10 November 2021 -->

<!-- # Functions -->
# Функции

<!-- Kotlin functions are declared using the `fun` keyword: -->
В Kotlin функции объявляются с помощью ключевого слова `fun`.

```kotlin
fun double(x: Int): Int {
    return 2 * x
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

<!-- Calling member functions uses dot notation: -->
Для вызова вложенной функции используется знак точки.

```kotlin
Stream().read() //создаёт экземпляр класса Stream и вызывает read()
```

<a name="parameters"></a>

<!-- ### Parameters -->
### Параметры

<!-- Function parameters are defined using Pascal notation - *name*: *type*. Parameters are separated using commas, and each
parameter must be explicitly typed: -->
Параметры функции записываются аналогично системе обозначений в языке Pascal - *имя*: *тип*. Параметры разделены
запятыми. Каждый параметр должен быть явно указан.

```kotlin
fun powerOf(number: Int, exponent: Int): Int { /*...*/ }
```

<!-- You can use a [trailing comma](coding-conventions.md#trailing-commas) when you declare function parameters: -->
Вы можете использовать [завершающую запятую](coding-conventions.html#trailing-commas) при объявлении параметров функции.

```kotlin
fun powerOf(
    number: Int,
    exponent: Int, // завершающая запятая
) { /*...*/ }
```

<a name="default-arguments"></a>

<!-- ### Default arguments -->
### Аргументы по умолчанию

<!-- Function parameters can have default values, which are used when you skip the corresponding argument. This reduces the number
of overloads: -->
Параметры функции могут иметь значения по умолчанию, которые используются в случае, если аргумент функции не указан при
её вызове. Это позволяет снизить уровень перегруженности кода.

```kotlin
fun read(
    b: ByteArray,
    off: Int = 0,
    len: Int = b.size,
) { /*...*/ }
```

<!-- A default value is defined using `=` after the type. -->
Значения по умолчанию указываются после типа знаком `=`.

<!-- Overriding methods always use the same default parameter values as the base method.
When overriding a method that has default parameter values, the default parameter values must be omitted from the signature: -->
Переопределённые методы всегда используют те же самые значения по умолчанию, что и их базовые методы. При
переопределении методов со значениями по умолчанию в сигнатуре эти параметры должны быть опущены.

```kotlin
open class A {
    open fun foo(i: Int = 10) { /*...*/ }
}

class B : A() {
    override fun foo(i: Int) { /*...*/ } // значение по умолчанию указать нельзя
}
```

<!-- If a default parameter precedes a parameter with no default value, the default value can only be used by calling
the function with [named arguments](#named-arguments): -->
Если параметр по умолчанию предшествует параметру без значения по умолчанию, значение по умолчанию можно использовать
только при вызове функции с [именованными аргументами](#named-arguments).

```kotlin
fun foo(
    bar: Int = 0,
    baz: Int,
) { /*...*/ }

foo(baz = 1) // Используется значение по умолчанию bar = 0
```

<!-- If the last argument after default parameters is a [lambda](lambdas.md#lambda-expression-syntax),
you can pass it either as a named argument or [outside the parentheses](lambdas.md#passing-trailing-lambdas): -->
Но если последний аргумент после параметров по умолчанию - [лямбда](lambdas.md#lambda-expression-syntax), вы можете
передать её либо как именованный аргумент, либо [за скобками](lambdas.md#passing-trailing-lambdas).

```kotlin
fun foo(
    bar: Int = 0,
    baz: Int = 1,
    qux: () -> Unit,
) { /*...*/ }

foo(1) { println("hello") }     // Используется значение по умолчанию baz = 1 
foo(qux = { println("hello") }) // Используется оба значения по умолчанию: bar = 0 и baz = 1
foo { println("hello") }        // Используется оба значения по умолчанию: bar = 0 и baz = 1
```

<a name="named-arguments"></a>

<!-- ### Named arguments -->
### Именованные аргументы

<!-- When calling a function, you can name one or more of its arguments. This can be helpful when a function has many
arguments and it's difficult to associate a value with an argument, especially if it's a boolean or `null` value. -->
При вызове функции вы можете явно указать имена одного или нескольких аргументов. Это может быть полезно, когда у функции
большой список аргументов, и сложно связать значение с аргументом, особенно если это логическое или `null` значение.

<!-- When you use named arguments in a function call, you can freely change the order they are listed in, and if you want to
use their default values, you can just leave these arguments out altogether. -->
При явном указывании имен аргументов в вызове функции, вы можете свободно изменять порядок их перечисления, и, если вы
хотите использовать их значения по умолчанию, вы можете просто пропустить эти аргументы.

<!--Consider the following function, `reformat()`, which has 4 arguments with default values.-->
Рассмотрим следующую функцию `reformat()`, которая имеет 4 аргумента со значениями по умолчанию:

```kotlin
fun reformat(
    str: String,
    normalizeCase: Boolean = true,
    upperCaseFirstLetter: Boolean = true,
    divideByCamelHumps: Boolean = false,
    wordSeparator: Char = ' ',
) { /*...*/ }
```

<!--When calling this function, you don’t have to name all its arguments:-->
При её вызове, вам не нужно явно указывать все имена аргументов.

```kotlin
reformat(
    "String!",
    false,
    upperCaseFirstLetter = false,
    divideByCamelHumps = true,
    '_'
)
```

<!-- You can skip all the ones with default values: -->
Вы можете пропустить все аргументы со значением по умолчанию.

```kotlin
reformat("This is a long String!")
```

<!-- You are also able to skip specific arguments with default values, rather than omitting them all. However, after the first
skipped argument, you must name all subsequent arguments: -->
Вы также можете пропустить не только *все* аргументы со значениями по умолчанию, но и лишь некоторые из них. Однако после
первого пропущенного аргумента вы должны указывать имена всех последующих аргументов.

```kotlin
reformat("This is a short String!", upperCaseFirstLetter = false, wordSeparator = '_')
```

<!-- You can pass a [variable number of arguments (`vararg`)](#variable-number-of-arguments-varargs) with names using the
`spread` operator: -->
Вы можете передать [переменное количество аргументов (`vararg`)](#variable-number-of-arguments-varargs) с именами,
используя оператор `spread`.

```kotlin
fun foo(vararg strings: String) { /*...*/ }

foo(strings = *arrayOf("a", "b", "c"))
```

<!-- > On the JVM: You can't use the named argument syntax when calling Java functions because Java bytecode does not
> always preserve the names of function parameters. -->
> В JVM: синтаксис именованных аргументов не может быть использован при вызове Java функций, потому как байт-код Java не
> всегда сохраняет имена параметров функции.

<a name="unit-returning-functions"></a>

<!-- ### Unit-returning functions -->
### Функции с возвращаемым типом Unit

<!-- If a function does not return a useful value, its return type is `Unit`. `Unit` is a type with only one value - `Unit`.
This value does not have to be returned explicitly: -->
Если функция не возвращает никакого полезного значения, её возвращаемый тип - `Unit`. `Unit` - тип только с одним
значением - `Unit`. Это значение не нуждается в явном указании возвращения функции.

```kotlin
fun printHello(name: String?): Unit {
    if (name != null)
        println("Hello $name")
    else
        println("Hi there!")
    // `return Unit` или `return` необязательны
}
```

<!-- The `Unit` return type declaration is also optional. The above code is equivalent to: -->
Указание типа `Unit` в качестве возвращаемого значения тоже не является обязательным. Код, написанный выше, и следующий
код совершенно идентичны:

```kotlin
fun printHello(name: String?) { /*...*/ }
```

<a name="single-expression-functions"></a>

<!-- ### Single-expression functions -->
### Функции с одним выражением

<!-- When a function returns a single expression, the curly braces can be omitted and the body is specified after a `=` symbol: -->
Когда функция возвращает одно единственное выражение, фигурные скобки `{ }` могут быть опущены, и тело функции может
быть описано после знака `=`.

```kotlin
fun double(x: Int): Int = x * 2
```

<!-- Explicitly declaring the return type is [optional](#explicit-return-types) when this can be inferred by the compiler: -->
Явное объявление возвращаемого типа является [необязательным](#explicit-return-types), когда он может быть определен
компилятором.

```kotlin
fun double(x: Int) = x * 2
```

<a name="explicit-return-types"></a>

<!-- ### Explicit return types -->
### Явные типы возвращаемых значений

<!-- Functions with block body must always specify return types explicitly, unless it's intended for them to return `Unit`,
[in which case specifying the return type is optional](#unit-returning-functions). -->
Функции с блочным телом всегда должны иметь явно указанный возвращаемый ими тип данных, если только они не предназначены
для возврата `Unit`, [тогда указание типа возвращаемого значения необязательно](#unit-returning-functions).

<!-- Kotlin does not infer return types for functions with block bodies because such functions may have complex control flow
in the body, and the return type will be non-obvious to the reader (and sometimes even for the compiler). -->
Kotlin самостоятельно не вычисляет тип возвращаемого значения для функций с блочным телом, потому что подобные функции
могут иметь сложную структуру и возвращаемый тип будет неочевидным для читающего этот код человека (иногда даже для
компилятора).

<a name="variable-number-of-arguments-varargs"></a>

<!-- ### Variable number of arguments (varargs) -->
### Нефиксированное число аргументов (varargs)

<!-- You can mark a parameter of a function (usually the last one) with the `vararg` modifier: -->
Параметр функции (обычно для этого используется последний) может быть помечен модификатором `vararg`.

```kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts - это массив (Array)
        result.add(t)
    return result
}
```

<!-- In this case, you can pass a variable number of arguments to the function: -->
Это позволит указать несколько значений в качестве аргументов функции.

```kotlin
val list = asList(1, 2, 3)
```

<!-- Inside a function, a `vararg`-parameter of type `T` is visible as an array of `T`, as in the example above, where the `ts`
variable has type `Array<out T>`. -->
Внутри функции параметр с меткой `vararg` и типом `T` виден как массив элементов `T`, таким образом переменная `ts` в
вышеуказанном примере имеет тип `Array<out T>`.

<!-- Only one parameter can be marked as `vararg`. If a `vararg` parameter is not the last one in the list, values for the
subsequent parameters can be passed using named argument syntax, or, if the parameter has a function type, by passing
a lambda outside the parentheses. -->
Только один параметр может быть помечен как `vararg`. Если параметр с именем `vararg` не стоит на последнем месте в
списке аргументов, значения для последующих параметров могут быть переданы только с использованием синтаксиса именованных
аргументов. В случае, если параметр является функцией, для этих целей можно вынести лямбду за фигурные скобки.

<!-- When you call a `vararg`-function, you can pass arguments individually, for example `asList(1, 2, 3)`. If you already have
an array and want to pass its contents to the function, use the *spread* operator (prefix the array with `*`): -->
При вызове `vararg`-функции вы можете передать аргументы один за одним, например `asList(1, 2, 3)`, или, если у нас уже
есть необходимый массив элементов и вы хотите передать его содержимое в функцию, используйте оператор `spread`
(необходимо пометить массив знаком `*`).

```kotlin
val a = arrayOf(1, 2, 3)
val list = asList(-1, 0, *a, 4)
```

<!-- If you want to pass a [primitive type array](https://kotlinlang.org/docs/basic-types.html#primitive-type-arrays)
into `vararg`, you need to convert it to a regular (typed) array using the `toTypedArray()` function: -->
Если вы хотите передать [массив примитивного типа](basic-types.html#primitive-type-arrays) в `vararg`, вам необходимо
преобразовать его в обычный (типизированный) массив с помощью функции `toTypedArray()`.

```kotlin
val a = intArrayOf(1, 2, 3) // IntArray - массив примитивного типа
val list = asList(-1, 0, *a.toTypedArray(), 4)
```

<a name="infix-notation"></a>

<!-- ### Infix notation -->
### Инфиксная запись

<!-- Functions marked with the `infix` keyword can also be called using the infix notation (omitting the dot and the parentheses
for the call). Infix functions must meet the following requirements: -->
Функции, помеченные ключевым словом `infix`, могут вызываться с использованием инфиксной записи (без точки и
скобок для вызова). Инфиксные функции должны соответствовать следующим требованиям:

<!-- * They must be member functions or [extension functions](extensions.md).
* They must have a single parameter.
* The parameter must not [accept variable number of arguments](#variable-number-of-arguments-varargs) and must have
no [default value](#default-arguments). -->

* Они должны являться членом другой функции или [расширения](extensions.html);
* В них должен использоваться только один параметр;
* Параметр не должен принимать [переменное количество аргументов](#variable-number-of-arguments-varargs) и не должен
иметь [значения по умолчанию](#default-arguments).

```kotlin
infix fun Int.shl(x: Int): Int { /*...*/ }

// вызов функции, с использованием инфиксной записи
1 shl 2

// то же самое, что
1.shl(2)
```

<!-- > Infix function calls have lower precedence than arithmetic operators, type casts, and the `rangeTo` operator.
> The following expressions are equivalent:
> * `1 shl 2 + 3` is equivalent to `1 shl (2 + 3)`
> * `0 until n * 2` is equivalent to `0 until (n * 2)`
> * `xs union ys as Set<*>` is equivalent to `xs union (ys as Set<*>)`
>
> On the other hand, an infix function call's precedence is higher than that of the boolean operators `&&` and `||`, `is`-
> and `in`-checks, and some other operators. These expressions are equivalent as well:
> * `a && b xor c` is equivalent to `a && (b xor c)`
> * `a xor b in c` is equivalent to `(a xor b) in c` -->
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

<!-- Note that infix functions always require both the receiver and the parameter to be specified. When you're
calling a method on the current receiver using the infix notation, use `this` explicitly. This is required to ensure
unambiguous parsing. -->
Обратите внимание, что инфиксные функции всегда требуют указания как получателя, так и параметра. Когда вы вызываете
метод на текущем приемнике, используя инфиксную запись, явно используйте `this`. Это необходимо для обеспечения
однозначного синтаксического анализа.

```kotlin
class MyStringCollection {
    infix fun add(s: String) { /*...*/ }

    fun build() {
        this add "abc" // Верно
        add("abc")     // Верно
        //add "abc"    // Не верно: получатель должен быть указан
    }
}
```

<a name="function-scope"></a>

<!-- ## Function scope -->
## Область видимости функций

<!-- Kotlin functions can be declared at the top level in a file, meaning you do not need to create a class to hold a function,
which you are required to do in languages such as Java, C#, and Scala. In addition
to top level functions, Kotlin functions can also be declared locally as member functions and extension functions. -->
В Kotlin функции могут быть объявлены в самом начале файла, что значит, что вам не обязательно создавать класс, чтобы
воспользоваться его функцией (как в Java, C# или Scala). В дополнение к этому, функции в Kotlin могут быть объявлены
локально, как функции-члены и функции-расширения.

<a name="local-functions"></a>

<!-- ### Local functions -->
### Локальные функции

<!-- Kotlin supports local functions, which are functions inside other functions: -->
Kotlin поддерживает локальные функции, т.е. функции, вложенные в другие функции.

```kotlin
fun dfs(graph: Graph) {
    fun dfs(current: Vertex, visited: MutableSet<Vertex>) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v, visited)
    }

    dfs(graph.vertices[0], HashSet())
}
```

<!-- A local function can access local variables of outer functions (the closure). In the case above, `visited` can be a local variable: -->
Локальная функция может иметь доступ к локальным переменным внешних по отношению к ним функций (типа *closure*). Таким
образом, в примере, приведённом выше, `visited` может быть локальной переменной.

```kotlin
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

<a name="member-functions"></a>

<!-- ### Member functions -->
### Функции-члены

<!--A member function is a function that is defined inside a class or object:-->
Функции-члены - это функции, объявленные внутри классов или объектов.

```kotlin
class Sample {
    fun foo() { print("Foo") }
}
```

<!-- Member functions are called with dot notation: -->
Функции-члены вызываются с использованием точки.

```kotlin
Sample().foo() // создаёт инстанс класса Sample и вызвает его функцию foo
```

<!-- For more information on classes and overriding members see [Classes](classes.md) and [Inheritance](classes.md#inheritance). -->
Для более подробной информации о классах и их элементах см. [Классы](classes.html) и [Наследование](classes.html#inheritance).

<a name="generic-functions"></a>

<!-- ## Generic functions -->
## Функции-обобщения

<!-- Functions can have generic parameters, which are specified using angle brackets before the function name: -->
Функции могут иметь обобщённые параметры, которые задаются треугольными скобками и помещаются перед именем функции.

```kotlin
fun <T> singletonList(item: T): List<T> { /*...*/ }
```

<!-- For more information on generic functions, see [Generics](generics.md). -->
Для более подробной информации см. [Обобщения](generics.html).

<a name="tail-recursive-functions"></a>

<!-- ## Tail recursive functions -->
## Функции с хвостовой рекурсией

<!-- Kotlin supports a style of functional programming known as [tail recursion](https://en.wikipedia.org/wiki/Tail_call).
For some algorithms that would normally use loops, you can use a recursive function instead without the risk of stack overflow.
When a function is marked with the `tailrec` modifier and meets the required formal conditions, the compiler optimizes out
the recursion, leaving behind a fast and efficient loop based version instead: -->
Kotlin поддерживает стиль функционального программирования, известный как ["хвостовая рекурсия"](https://ru.wikipedia.org/wiki/%D0%A5%D0%B2%D0%BE%D1%81%D1%82%D0%BE%D0%B2%D0%B0%D1%8F_%D1%80%D0%B5%D0%BA%D1%83%D1%80%D1%81%D0%B8%D1%8F).
Это позволяет использовать циклические алгоритмы вместо рекурсивных функции, но без риска переполнения стэка. Когда
функция помечена модификатором `tailrec` и её форма отвечает требованиям компилятора, он оптимизирует рекурсию, оставляя
вместо неё быстрое и эффективное решение этой задачи, основанное на циклах.

```kotlin
val eps = 1E-10 // этого достаточно, может быть 10^-15

tailrec fun findFixPoint(x: Double = 1.0): Double =
    if (Math.abs(x - Math.cos(x)) < eps) x else findFixPoint(Math.cos(x))
```

<!-- This code calculates the `fixpoint` of cosine, which is a mathematical constant. It simply calls `Math.cos` repeatedly
starting at `1.0` until the result no longer changes, yielding a result of `0.7390851332151611` for the specified
`eps` precision. The resulting code is equivalent to this more traditional style: -->
Этот код высчитывает `fixpoint` косинуса, который является математической константой. Он просто напросто постоянно
вызывает `Math.cos`, начиная с `1.0` до тех пор, пока результат не изменится, приняв значение `0.7390851332151611` для
заданной точности `eps`. Получившийся код эквивалентен вот этому более традиционному стилю:

```kotlin
val eps = 1E-10 // этого достаточно, может быть 10^-15

private fun findFixPoint(): Double {
    var x = 1.0
    while (true) {
        val y = Math.cos(x)
        if (Math.abs(x - y) < eps) return x
        x = Math.cos(x)
    }
}
```

<!-- To be eligible for the `tailrec` modifier, a function must call itself as the last operation it performs. You cannot use
tail recursion when there is more code after the recursive call, within `try`/`catch`/`finally` blocks, or on open functions.
Currently, tail recursion is supported by Kotlin for the JVM and Kotlin/Native. -->
Для соответствия требованиям модификатора `tailrec`, функция должна вызывать сама себя в качестве последней операции,
которую она предпринимает. Вы не можете использовать хвостовую рекурсию, когда существует ещё какой-то код после вызова
этой самой рекурсии. Также нельзя использовать её внутри блоков `try`/`catch`/`finally` или в `open` функциях. На данный
момент, хвостовая рекурсия поддерживается только в backend виртуальной машины Java (JVM) и в Kotlin/Native.

<!-- **See also**: -->
**См. также:**

<!-- * [Inline functions](inline-functions.md)
* [Extension functions](extensions.md)
* [Higher-order functions and lambdas](lambdas.md) -->

* [Встроенные функции](inline-functions.html),
* [Функции-расширения](extensions.html),
* [Высокоуровневые функции и лямбды](lambdas.html).
