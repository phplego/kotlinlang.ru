---
type: doc
layout: reference
category: "Syntax"
title: "Функции"
url: https://kotlinlang.ru/docs/reference/functions.html
---

<!--# Functions-->
#Функции

<!--## Function Declarations-->
## Объявление функций

<!--Functions in Kotlin are declared using the *fun*{: .keyword } keyword-->
В <b>Kotlin</b> функции объявляются с помощью ключевого слова *fun*

``` kotlin
fun double(x: Int): Int {
...
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

<a name="infix-notation"></a>

<!--### Infix notation-->   
### Инфиксная запись

<!--Functions can also be called using infix notations when-->
Функции так же могут быть вызваны при помощи инфиксной записи, при условии, что:

<!--* They are member functions or [extension functions](extensions.html)-->
* Они являются членом другой функции или [расширения](http://kotlinlang.ru/docs/reference/extensions.html)
<!--* They have a single parameter-->
* В них используется один параметр
<!--* They are marked with the `infix` keyword-->
* Когда они помечены ключевым словом `infix`

``` kotlin
// Определяем выражение как Int
infix fun Int.shl(x: Int): Int {
...
}

// вызываем функцию, используя инфиксную запись

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
### Аргументы по умолчанию

<!--Function parameters can have default values, which are used when a corresponding argument is omitted. This allows for a reduced number of overloads compared to
other languages.-->
Параметры функции могут иметь значения по умолчанию, которые используются в случае, если аргумент функции не указан при её вызове. Это позволяет снизить уровень перегруженности кода по сравнению с другими языками.

``` kotlin
fun read(b: Array<Byte>, off: Int = 0, len: Int = b.size()) {
...
}
```

<!--Default values are defined using the **=** after type along with the value.-->
Значения по умолчанию указываются после типа знаком **=**.

<!--Overriding methods always use the same default parameter values as the base method.
When overriding a method with default parameters values, the default parameter values must be omitted from the signature:-->
Переопределённые методы всегда используют те же самые значения по умолчанию, что и их базовые методы. При переопределении методов со значениями по умолчанию эти параметры должны быть опущены:

``` kotlin
open class A {
    open fun foo(i: Int = 10) { ... }
}

class B : A() {
    override fun foo(i: Int) { ... }  // значение по умолчанию указать нельзя
}
```

<!--If a default parameter precedes a parameter with no default value, the default value can be used only by calling the function with named arguments:-->
Если параметр по умолчанию предшествует параметру без значения по умолчанию, значение по умолчанию можно использовать только при вызове функции с именованными аргументами:

```kotlin
fun foo(bar: Int = 0, baz: Int) { ... }

foo(baz = 1) // Используется значение по умолчанию bar = 0
```

Но если последний аргумент [lambda](http://kotlinlang.ru/docs/reference/lambdas.html#lambda-expression-syntax) передается в вызов функции вне скобок, передача значений параметров по умолчанию не допускается:

``` kotlin
fun foo(bar: Int = 0, baz: Int = 1, qux: () -> Unit) { ... }

foo(1) { println("hello") } // Использует значение по умолчанию baz = 1 
foo { println("hello") }    // Использует два значения по умолчанию bar = 0 и baz = 1
```

<!--### Named Arguments-->
### Именованные аргументы

<!--Function parameters can be named when calling functions. This is very convenient when a function has a high number of parameters or default ones.-->
Имена параметров могут быть явно указаны при вызове функций. Это очень удобно, когда у функции большой список параметров, в том числе со значениями по умолчанию.

<!--Given the following function-->
Рассмотрим следующую функцию:

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
С помощью именованных аргументов мы можем сделать код более читабельным:

``` kotlin
reformat(str,
    normalizeCase = true,
    upperCaseFirstLetter = true,
    divideByCamelHumps = false,
    wordSeparator = '_'
)
```

<!--and if we do not need all arguments-->
Или, если нам не нужны все эти аргументы

``` kotlin
reformat(str, wordSeparator = '_')
```

<!--When a function is called with both positional and named arguments, all the positional arguments should be placed before the first named one. For example, the call `f(1, y = 2)` is allowed, but `f(x = 1, 2)` is not.-->
При вызове функции как с позиционными, так и с именованными аргументами все позиционные аргументы должны располагаться перед первым именованным аргументом. Например, вызов `f(1, y = 2)` разрешен, а `f(x = 1, 2)` - нет.

<!--[Variable number of arguments (*vararg*{: .keyword })](#variable-number-of-arguments-varargs) can be passed in the named form by using the **spread** operator:-->
[Переменное число аргументов (**vararg**)](#variable-number-of-arguments-varargs) можно передать в именованной форме с помощью оператора **spread**:

```kotlin
fun foo(vararg strings: String) { ... }

foo(strings = *arrayOf("a", "b", "c"))
```

<!--Note that the named argument syntax cannot be used when calling Java functions, because Java bytecode does not
always preserve names of function parameters.-->
Обратите внимание, что синтаксис именованных аргументов не может быть использован при вызове <b>Java</b> функций, потому как байт-код <b>Java</b> не всегда сохраняет имена параметров функции.


<!--### Unit-returning functions-->
### Функции с возвращаемым типом `Unit`

<!--If a function does not return any useful value, its return type is `Unit`. `Unit` is a type with only one value - `Unit`. This
value does not have to be returned explicitly-->
Если функция не возвращает никакого полезного значения, её возвращаемый тип - `Unit`._ `Unit` - тип только с одним значением - `Unit`_. 
Это возвращаемое значение не нуждается в явном указании

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
Указание типа `Unit` в качестве возвращаемого значения тоже не является обязательным. Код, написанный выше, совершенно идентичен с

``` kotlin
fun printHello(name: String?) {
    ...
}
```

<!--### Single-Expression functions-->
### Функции с одним выражением

<!--When a function returns a single expression, the curly braces can be omitted and the body is specified after a **=** symbol-->
Когда функция возвращает одно-единственное выражение, фигурные скобки `{ }` могут быть опущены, и тело функции может быть описано после знака `=`

``` kotlin
fun double(x: Int): Int = x * 2
```

<!--Explicitly declaring the return type is [optional](#explicit-return-types) when this can be inferred by the compiler-->
Компилятор способен сам определить типа возвращаемого значения.

``` kotlin
fun double(x: Int) = x * 2
```

<!--### Explicit return types-->
### Явные типы возвращаемых значений

<!--Functions with block body must always specify return types explicitly, unless it's intended for them to return `Unit`, [in which case it is optional](#unit-returning-functions).
Kotlin does not infer return types for functions with block bodies because such functions may have complex control flow in the body, and the return type will be non-obvious to the reader (and sometimes even for the compiler). -->
Функции, в которых есть тело, всегда должны указывать возвращаемый ими тип данных (если в этом качестве не указан тип `Unit`).
<b>Kotlin</b> не вычисляет самостоятельно тип возвращаемого значения для функций с заключённым в них блоком кода потому, что подобные функции могут иметь сложную структуру и возвращаемый тип неочевиден для читающего этот код человека (иногда даже для компилятора).


<a name="variable-number-of-arguments-varargs"></a>
<!--### Variable number of arguments (Varargs)-->
### Нефиксированное число аргументов (Varargs)

<!--A parameter of a function (normally the last one) may be marked with `vararg` modifier:-->
Параметр функции (обычно для этого используется последний) может быть помечен модификатором `vararg`:

``` kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts - это массив (Array)
        result.add(t)
    return result
}
```

<!--allowing a variable number of arguments to be passed to the function:-->
это позволит указать множество значений в качестве аргументов функции:

``` kotlin
val list = asList(1, 2, 3)
```

<!--Inside a function a `vararg`-parameter of type `T` is visible as an array of `T`, i.e. the `ts` variable in the example above has type `Array<out T>`.-->
Внутри функции параметр с меткой `vararg` и типом `T` виден как массив элементов `T`, таким образом переменная `ts` в вышеуказанном примере имеет тип `Array<out T>`.

<!--Only one parameter may be marked as `vararg`. If a `vararg` parameter is not the last one in the list, values for the
following parameters can be passed using the named argument syntax, or, if the parameter has a function type, by passing
a lambda outside parentheses.-->
Только один параметр может быть помечен меткой `vararg`. Если параметр с именем `vararg` не стоит на последнем месте в списке аргументов,
значения для соответствующих параметров могут быть переданы с использованием <i>named argument</i> синтаксиса. В случае, если параметр является функцией, для этих целей можно вынести лямбду за фигурные скобки.

<!--When we call a `vararg`-function, we can pass arguments one-by-one, e.g. `asList(1, 2, 3)`, or, if we already have an array
 and want to pass its contents to the function, we use the **spread** operator (prefix the array with `*`):-->
 При вызове `vararg` функции мы можем передать аргументы один-за-одним, например `asList(1, 2, 3)`, или, если у нас уже есть необходимый массив элементов и мы хотим передать его содержимое в нашу функцию, использовать оператор *spread* (необходимо пометить массив знаком `*`):

```kotlin
val a = arrayOf(1, 2, 3)
val list = asList(-1, 0, *a, 4)
```

<!--## Function Scope-->
## Область действия функций

<!--In Kotlin functions can be declared at top level in a file, meaning you do not need to create a class to hold a function, like languages such as Java, C# or Scala. In addition
to top level functions, Kotlin functions can also be declared local, as member functions and extension functions.-->
В <b>Kotlin</b> функции могут быть объявлены в самом начале файла. Подразумевается, что вам не обязательно создавать объект какого-либо класса, чтобы воспользоваться его функцией (как в <b>Java</b>, <b>C#</b> или <b>Scala</b>). В дополнение к этому, функции в языке <b>Kotlin</b> могут быть объявлены локально, как функции-члены (ориг. <i>"member functions"</i>) и функции-расширения (<i>"extension functions"</i>).

<a name="local-functions"></a>

<!--### Local Functions-->
### Локальные функции 

<!--Kotlin supports local functions, i.e. a function inside another function-->
<b>Koltin</b> поддерживает локальные функции. Например, функции, вложенные в другие функции

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

<!--Local function can access local variables of outer functions (i.e. the closure), so in the case above, the *visited* can be a local variable-->
Такие локальные функции могут иметь доступ к локальным переменным внешних по отношению к ним функций (типа *closure*). Таким образом, в примере, приведённом выше, *visited* может быть локальной переменной.

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

<a name="member-functions"></a>
<!--### Member Functions-->
### Функции-элементы

<!--A member function is a function that is defined inside a class or object-->
Функции-элементы - это функции, объявленные внутри классов или объектов

``` kotlin
class Sample() {
    fun foo() { print("Foo") }
}
```

<!--Member functions are called with dot notation-->
Функции-элементы вызываются с использованием точки

``` kotlin
Sample().foo() // создаёт инстанс класса Sample и вызвает его функцию foo
```

<!--For more information on classes and overriding members see [Classes](classes.html) and [Inheritance](classes.html#inheritance)-->
Для более подробной информации о классах и их элементах см. [Классы](http://kotlinlang.ru/docs/reference/classes.html) 

<!--## Generic Functions-->
## Функции-обобщения (<i>Generic Functions</i>)

<!--Functions can have generic parameters which are specified using angle brackets before the function name-->
Функции могут иметь обобщённые параметры, которые задаются треугольными скобками и помещаются перед именем функции

``` kotlin
fun <T> singletonList(item: T): List<T> {
    // ...
}
```

<!--For more information on generic functions see [Generics](generics.html)-->
Для более подробной информации об обобщениях см. [Обобщения](http://kotlinlang.ru/docs/reference/generics.html)

<!--## Inline Functions-->
## Встроенные функции (<i>Inline Functions</i>)

<!--Inline functions are explained [here](inline-functions.html)-->
О встроенных функциях рассказано [здесь](http://kotlinlang.ru/docs/reference/inline-functions.html)

<!--## Extension Functions-->
## Функции-расширения (<i>Extension Functions</i>)

<!--Extension functions are explained in [their own section](extensions.html)-->
О расширениях подробно написано в [отдельной статье](http://kotlinlang.ru/docs/reference/extensions.html)

<!--## Higher-Order Functions and Lambdas-->
## Высокоуровневые функции и лямбды

<!--Higher-Order functions and Lambdas are explained in [their own section](lambdas.html)-->
О лямбдах и высокоуровневых функциях см. раздел [Лямбды](http://kotlinlang.ru/docs/reference/lambdas.html)

<!--## Tail recursive functions-->
## Функции с хвостовой рекурсией

<!--Kotlin supports a style of functional programming known as [tail recursion](https://en.wikipedia.org/wiki/Tail_call).
This allows some algorithms that would normally be written using loops to instead be written using a recursive function, but without the risk of stack overflow.
When a function is marked with the `tailrec` modifier and meets the required form the compiler optimises out the recursion, leaving behind a fast and efficient loop based version instead.-->
<b>Kotlin</b> поддерживает такой стиль функционального программирования, более известный как ["хвостовая рекурсия"](https://ru.wikipedia.org/wiki/%D0%A5%D0%B2%D0%BE%D1%81%D1%82%D0%BE%D0%B2%D0%B0%D1%8F_%D1%80%D0%B5%D0%BA%D1%83%D1%80%D1%81%D0%B8%D1%8F).
Это позволяет использовать циклические алгоритмы вместо рекурсивных функции, но без риска переполнения стэка.
Когда функция помечена модификатором `tailrec` и её форма отвечает требованиям компилятора, он оптимизирует рекурсию, оставляя вместо неё быстрое и эффективное решение этой задачи, основанное на циклах. 

``` kotlin
tailrec fun findFixPoint(x: Double = 1.0): Double
        = if (x == Math.cos(x)) x else findFixPoint(Math.cos(x))
```

<!--This code calculates the fixpoint of cosine, which is a mathematical constant. It simply calls Math.cos repeatedly starting at 1.0 until the result doesn't change any more, yielding a result of 0.7390851332151607. The resulting code is equivalent to this more traditional style:-->
Этот код высчитывает `fixpoint` косинуса, который является математической константой. Он просто напросто постоянно вызывает Math.cos, начиная с 1.0 до тех пор, пока результат не изменится, приняв значение 0.7390851332151607. Получившийся код эквивалентен вот этому более традиционному стилю:

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

<!--To be eligible for the `tailrec` modifier, a function must call itself as the last operation it performs. You cannot use tail recursion when there is more code after the recursive call, and you cannot use it within try/catch/finally blocks. Currently tail recursion is only supported in the JVM backend.-->
Для соответствия требованиям модификатора `tailrec`, функция должна вызывать сама себя в качестве последней операции, которую она предпринимает. Вы не можете использовать хвостовую рекурсию, когда существует ещё какой-то код после вызова этой самой рекурсии. Также нельзя использовать её внутри блоков try/catch/finally. На данный момент, хвостовая рекурсия поддерживается только в backend виртуальной машины <b>Java</b>(<b>JVM</b>).
