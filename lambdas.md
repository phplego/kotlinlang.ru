---
type: doc
layout: reference
title: "Лямбды"
url: https://kotlinlang.ru/docs/reference/lambdas.html
---

<!-- # Higher-Order Functions and Lambdas -->
# Высокоуровневые функции и лямбды

<!-- Kotlin functions are [*first-class*](https://en.wikipedia.org/wiki/First-class_function), which means that they can
be stored in variables and data structures, passed as arguments to and returned from other
[higher-order functions](#higher-order-functions). You can operate with functions in any way that is possible for other
non-function values.  -->
В Kotlin функции являются [*функциями первого класса*](https://en.wikipedia.org/wiki/First-class_function). Это означает, что они могут храниться в переменных и структурах данных, передаваться в качестве аргументов и возвращаться из других [*функций высшего порядка*](#higher-order-functions). Вы можете работать с функциями любым способом, который возможен для других нефункциональных значений.

<!-- To facilitate this, Kotlin, as a statically typed programming language, uses a family of
[function types](#function-types) to represent functions and provides a set of specialized language constructs, such as [lambda expressions](#lambda-expressions-and-anonymous-functions). -->
Чтобы это облегчить, Kotlin, как статически типизированный язык программирования, использует семейство [функциональных типов](#function-types) для представления функций и предоставляет набор специализированных языковых конструкций, таких как [лямбда-выражения](#lambda-expressions-and-anonymous-functions).

<a name="higher-order-functions"></a>
<!-- ## Higher-Order Functions -->
## Функции высшего порядка

<!-- A higher-order function is a function that takes functions as parameters, or returns a function. -->
Функция высшего порядка - это функция, которая принимает функции как параметры, или возвращает функцию в качестве результата.

<!-- A good example is the [functional programming idiom `fold`](https://en.wikipedia.org/wiki/Fold_(higher-order_function))
for collections, which takes an initial accumulator value and a combining function and builds its return value by
consecutively combining current accumulator value with each collection element, replacing the accumulator: -->
Хорошим примером такой функции является идиома функционального программирования [`fold`](https://en.wikipedia.org/wiki/Fold_(higher-order_function)) для коллекций, которая принимает начальное значение - `accumulator` вместе с комбинирующей функцией и строит возвращаемое значение, последовательно комбинируя текущее значение `accumulator` с каждым элементом коллекции, заменяя значение `accumulator`:

```kotlin
fun <T, R> Collection<T>.fold(
    initial: R,
    combine: (acc: R, nextElement: T) -> R
): R {
    var accumulator: R = initial
    for (element: T in this) {
        accumulator = combine(accumulator, element)
    }
    return accumulator
}
```

<!-- In the code above, the parameter `combine` has a [function type](#function-types) `(R, T) -> R`, so it accepts a function that
takes two arguments of types `R` and `T` and returns a value of type `R`.
It is [invoked](#invoking-a-function-type-instance) inside the *for*{: .keyword }-loop, and the return value is
then assigned to `accumulator`. -->
В приведённом выше коде параметр `combine` имеет [функциональный тип](#function-types) `(R, T) -> R`, поэтому он принимает функцию, которая принимает два аргумента типа `R` и `T` и возвращает значение типа `R`.
Он [вызывается](#invoking-a-function-type-instance) внутри цикла <b class="keyword">for</b> и присваивает `accumulator` возвращаемое значение.

<!-- To call `fold`, we need to pass it an [instance of the function type](#instantiating-a-function-type) as an argument, and lambda expressions ([described in more detail below](#lambda-expressions-and-anonymous-functions)) are widely used for
this purpose at higher-order function call sites: -->
Чтобы вызвать `fold`, мы должны передать ему [экземпляр функционального типа](#instantiating-a-function-type) в качестве аргумента и лямбда-выражение ([описание ниже](#lambda-expressions-and-anonymous-functions)). Лямбда-выражения часто используются в качестве параметра функции высшего порядка.

```kotlin
fun main() {
    val items = listOf(1, 2, 3, 4, 5)

    // Лямбда - это блок кода, заключенный в фигурные скобки.
    items.fold(0, {
        // Если у лямбды есть параметры, то они указываются перед знаком '->'
        acc: Int, i: Int ->
        print("acc = $acc, i = $i, ")
        val result = acc + i
        println("result = $result")
        // Последнее выражение в лямбде считается возвращаемым значением:
        result
    })

    // Типы параметров в лямбде необязательны, если они могут быть выведены:
    val joinedToString = items.fold("Elements:", { acc, i -> acc + " " + i })

    // Ссылки на функции также могут использоваться для вызовов функций высшего порядка:
    val product = items.fold(1, Int::times)

    println("joinedToString = $joinedToString")
    println("product = $product")
}
```

<!-- The following sections explain in more detail the concepts mentioned so far. -->
Следующие разделы объясняют более подробно концепции, упомянутые выше.


<a name="function-types"></a>
<!-- ## Function types -->
## Функциональные типы

<!-- Kotlin uses a family of function types like `(Int) -> String` for declarations that deal with functions: `val onClick: () -> Unit = ...`. -->
Kotlin использует семейство функциональных типов, таких как `(Int) -> String`, для объявлений, которые являются частью функций: `val onClick: () -> Unit = ...`.

<!-- These types have a special notation that corresponds to the signatures of the functions, i.e. their parameters and return values: -->
Эти типы имеют специальные обозначения, которые соответствуют сигнатурам функций, то есть их параметрам и возвращаемым значениям:

<!-- * All function types have a parenthesized parameter types list and a return type: `(A, B) -> C` denotes a type that
 represents functions taking two arguments of types `A` and `B` and returning a value of type `C`.
 The parameter types list may be empty, as in `() -> A`. The [`Unit` return type](functions.html#unit-returning-functions)
 cannot be omitted.  -->
* У всех функциональных типов есть список с типами параметров, заключенный в скобки, и возвращаемый тип: `(A, B) -> C` обозначает тип, который предоставляет функции два принятых аргумента типа `A` и `B`, а также возвращает значение типа `C`.
Список с типами параметров может быть пустым, как, например, в `() -> A`. Возвращаемый тип [`Unit`](functions.html#unit-returning-functions) не может быть опущен.

 <!-- * Function types can optionally have an additional *receiver* type, which is specified before a dot in the notation:
 the type `A.(B) -> C` represents functions that can be called on a receiver object of `A` with a parameter of `B` and
 return a value of `C`.
 [Function literals with receiver](#function-literals-with-receiver) are often used along with these types. -->
* У функциональных типов может быть дополнительный тип - *получатель* (*receiver*), который указывается в объявлении перед точкой: тип `A.(B) -> C` описывает функции, которые могут быть вызваны для объекта-получателя `A` с параметром `B` и возвращаемым значением `C`. [Литералы функций с объектом-приёмником](#function-literals-with-receiver) часто используются вместе с этими типами.

<!-- * [Suspending functions](coroutines.html#suspending-functions) belong to function types of a special kind, which have a *suspend*{: .keyword} modifier in the
 notation, such as `suspend () -> Unit` or `suspend A.(B) -> C`. -->
* [Останавливаемые функции](coroutines.html#suspending-functions) (suspending functions) принадлежат к особому виду функциональных типов, у которых в объявлении присутствует модификатор <b class="keyword">suspend</b>, например, `suspend () -> Unit` или `suspend A.(B) -> C`.

<!-- The function type notation can optionally include names for the function parameters: `(x: Int, y: Int) -> Point`.
These names can be used for documenting the meaning of the parameters. -->
Объявление функционального типа также может включать именованные параметры: `(x: Int, y: Int) -> Point`.
Именованные параметры могут быть использованы для описания смысла каждого из параметров.

<!-- > To specify that a function type is [nullable](null-safety.html#nullable-types-and-non-null-types), use parentheses: `((Int, Int) -> Int)?`.
>
> Function types can be combined using parentheses: `(Int) -> ((Int) -> Unit)`
>
> The arrow notation is right-associative, `(Int) -> (Int) -> Unit` is equivalent to the previous example, but not to
`((Int) -> (Int)) -> Unit`. -->
> Чтобы указать, что функциональный тип может быть [nullable](null-safety.html#nullable-types-and-non-null-types), используйте круглые скобки: `((Int, Int) -> Int)?`.
>
> При помощи круглых скобок функциональные типы можно объединять: `(Int) -> ((Int) -> Unit)`.
>
> Стрелка в объявлении является правоассоциативной (right-associative), т.е. объявление `(Int) -> (Int) -> Unit` эквивалентно объявлению из предыдущего примера, а не `((Int) -> (Int)) -> Unit`.

<!-- You can also give a function type an alternative name by using [a type alias](type-aliases.html): -->
Вы также можете присвоить функциональному типу альтернативное имя, используя [псевдонимы типов](type-aliases.html):

```kotlin
typealias ClickHandler = (Button, ClickEvent) -> Unit
```


<a name="instantiating-a-function-type"></a>
<!-- ### Instantiating a function type -->
### Создание функционального типа

<!-- There are several ways to obtain an instance of a function type: -->
Существует несколько способов получить экземпляр функционального типа:

<!-- * Using a code block within a function literal, in one of the forms:
    * a [lambda expression](#lambda-expressions-and-anonymous-functions): `{ a, b -> a + b }`,
    * an [anonymous function](#anonymous-functions): `fun(s: String): Int { return s.toIntOrNull() ?: 0 }`

   [Function literals with receiver](#function-literals-with-receiver) can be used as values of function types with receiver. -->
* Используя блок с кодом внутри функционального литерала в одной из форм:
  * [лямбда-выражение](#lambda-expressions-and-anonymous-functions): `{ a, b -> a + b }`,
  * [анонимная функция](#anonymous-functions): `fun(s: String): Int { return s.toIntOrNull() ?: 0 }`

  [Литералы функций с объектом-приёмником](#function-literals-with-receiver) могут использоваться как значения функциональных типов с получателем.

<!-- * Using a callable reference to an existing declaration:
      * a top-level, local, member, or extension [function](reflection.html#function-references): `::isOdd`, `String::toInt`,
      * a top-level, member, or extension [property](reflection.html#property-references): `List<Int>::size`,
      * a [constructor](reflection.html#constructor-references): `::Regex`

     These include [bound callable references](reflection.html#bound-function-and-property-references-since-11) that point to a member of a particular instance: `foo::toString`. -->
* Используя вызываемую ссылку на существующее объявление:
  * функции верхнего уровня, локальной функции, функции-члена или [функции-расширения](reflection.html#function-references): `::isOdd`, `String::toInt`,
  * свойства верхнего уровня, члена или [свойства-расширения](reflection.html#property-references): `List<Int>::size`,
  * [конструктора](reflection.html#constructor-references): `::Regex`

  К ним относятся [привязанные вызываемые ссылки](reflection.html#bound-function-and-property-references-since-11), которые указывают на член конкретного экземпляра: `foo::toString`.

<!-- * Using instances of a custom class that implements a function type as an interface:  -->
* Используя экземпляр пользовательского класса, который реализует функциональный тип в качестве интерфейса:

```kotlin
class IntTransformer: (Int) -> Int {
    override operator fun invoke(x: Int): Int = TODO()
}

val intFunction: (Int) -> Int = IntTransformer()
```

<!-- The compiler can infer the function types for variables if there is enough information: -->
При достаточной информации компилятор может самостоятельно вывести функциональный тип для переменной:

```kotlin
val a = { i: Int -> i + 1 } // Выведенный тип - (Int) -> Int
```

<!-- *Non-literal* values of function types with and without receiver are interchangeable, so that the receiver can stand in
for the first parameter, and vice versa. For instance, a value of type `(A, B) -> C` can be passed or assigned
 where a `A.(B) -> C` is expected and the other way around: -->
*Небуквальные* (non-literal) значения функциональных типов с и без получателя являются взаимозаменяемыми, таким образом получатель может заменить первый параметр, и наоборот. Например, значение типа `(A, B) -> C` может быть передано или назначено там, где ожидается `A.(B) -> C`, и наоборот.

```kotlin
fun main() {
    val repeatFun: String.(Int) -> String = { times -> this.repeat(times) }
    val twoParameters: (String, Int) -> String = repeatFun // OK

    fun runTransformation(f: (String, Int) -> String): String {
        return f("hello", 3)
    }
    val result = runTransformation(repeatFun) // OK

    println("result = $result")
}
```

<!-- > Note that a function type with no receiver is inferred by default, even if a variable is initialized with a reference
> to an extension function.
> To alter that, specify the variable type explicitly. -->
> Обратите внимание, что функциональный тип без получателя выводится по умолчанию, даже если переменная инициализируется со ссылкой на функцию-расширение.
> Чтобы это изменить, укажите тип переменной явно.


<a name="invoking-a-function-type-instance"></a>
<!-- ### Invoking a function type instance  -->
### Вызов экземпляра функционального типа

<!-- A value of a function type can be invoked by using its [`invoke(...)` operator](operator-overloading.html#invoke): `f.invoke(x)` or just `f(x)`. -->
Значение функционального типа может быть вызвано с помощью оператора [`invoke(...)`](operator-overloading.html#invoke): `f.invoke(x)` или просто `f(x)`.

<!-- If the value has a receiver type, the receiver object should be passed as the first argument.
Another way to invoke a value of a function type with receiver is to prepend it with the receiver object,
as if the value were an [extension function](extensions.html): `1.foo(2)`, -->
Если значение имеет тип получателя, то объект-приёмник должен быть передан в качестве первого аргумента.
Другой способ вызвать значение функционального типа с получателем - это добавить его к объекту-приёмнику, как если бы это была [функция-расширение](extensions.html): `1.foo(2)`,

Пример:

```kotlin
fun main() {
    val stringPlus: (String, String) -> String = String::plus
    val intPlus: Int.(Int) -> Int = Int::plus

    println(stringPlus.invoke("<-", "->"))
    println(stringPlus("Hello, ", "world!"))

    println(intPlus.invoke(1, 1))
    println(intPlus(1, 2))
    println(2.intPlus(3)) // вызывается как функция-расширение
}
```


<!-- ### Inline functions -->
### Встроенные функции (Inline functions)

<!-- Sometimes it is beneficial to use [inline functions](inline-functions.html), which provide flexible control flow,
for higher-order functions. -->
Иногда выгодно улучшить производительность функций высшего порядка, используя [встроенные функции](inline-functions.html).


<a name="lambda-expressions-and-anonymous-functions"></a>
<!-- ## Lambda Expressions and Anonymous Functions -->
## Лямбда-выражения и анонимные функции

<!-- Lambda expressions and anonymous functions are 'function literals', i.e. functions that are not declared,
but passed immediately as an expression. Consider the following example: -->
Лямбда-выражения и анонимные функции - это "функциональный литерал", то есть необъявленная функция, которая немедленно
используется в качестве выражения. Рассмотрим следующий пример:

```kotlin
max(strings, { a, b -> a.length < b.length })
```

<!-- Function `max` is a higher-order function, it takes a function value as the second argument.
This second argument is an expression that is itself a function, i.e. a function literal, which is equivalent to
the following named function: -->
Функция `max` является функцией высшего порядка, потому что она принимает функцию в качестве второго аргумента.
Этот второй аргумент является выражением, которое в свою очередь есть функция, то есть <i>функциональный литерал</i>.
Как функция он эквивалентен объявлению:

```kotlin
fun compare(a: String, b: String): Boolean = a.length < b.length
```


<a name="lambda-expression-syntax"></a>
<!--### Lambda Expression Syntax-->
### Синтаксис лямбда-выражений

<!--The full syntactic form of lambda expressions, i.e. literals of function types, is as follows:-->
Полная синтаксическая форма лямбда-выражений, таких как <i>literals of function types</i>, может быть представлена следующим образом:

```kotlin
val sum = { x: Int, y: Int -> x + y }
```

<!--A lambda expression is always surrounded by curly braces, parameter declarations in the full syntactic form go inside parentheses and have optional type annotations, the body goes after an -> sign. If the inferred return type of the lambda is not Unit, the last (or possibly single) expression inside the lambda body is treated as the return value.-->
Лямбда-выражение всегда заключено в скобки `{...}`, объявление параметров при таком синтаксисе происходит внутри этих скобок и может включать в себя аннотации типов (опционально), тело функции начинается после знака `->`. Если тип возвращаемого значения не `Unit`, то в качестве возвращаемого типа принимается последнее (а возможно и единственное) выражение внутри тела лямбды.

<!--If we leave all the optional annotations out, what's left looks like this:-->
Если мы вынесем все необязательные объявления, то, что останется, будет выглядеть следующим образом:

```kotlin
val sum: (Int, Int) -> Int = { x, y -> x + y }
```


<a name="passing-a-lambda-to-the-last-parameter"></a>
<!-- ### Passing trailing lambdas -->
### Передача лямбды в качестве последнего параметра

<!-- In Kotlin, there is a convention: if the last parameter of a function is a function, then a lambda expression
passed as the corresponding argument can be placed outside the parentheses: -->
В Kotlin существует соглашение: если последний параметр функции является функцией, то лямбда-выражение, переданное в качестве соответствующего аргумента, может быть заключено в скобки:

```kotlin
val product = items.fold(1) { acc, e -> acc * e }
```

<!-- Such syntax is also known as _trailing lambda_. -->
Такой синтаксис также известен как _trailing lambda_.

<!-- If the lambda is the only argument to that call, the parentheses can be omitted entirely:  -->
Когда лямбда-выражение является единственным аргументом функции, круглые скобки могут быть опущены:

```kotlin
run { println("...") }
```


<!-- ### `it`: implicit name of a single parameter -->
### Ключевое слово `it`: неявное имя единственного параметра

<!-- It's very common that a lambda expression has only one parameter. -->
Очень часто лямбда-выражение имеет только один параметр.

<!-- If the compiler can figure the signature out itself, it is allowed not to declare the only parameter and omit `->`.
The parameter will be implicitly declared under the name `it`: -->
Если компилятор способен самостоятельно определить сигнатуру, то объявление параметра можно опустить вместе с `->`.
Параметр будет неявно объявлен под именем `it`:

```kotlin
ints.filter { it > 0 } // этот литерал имеет тип '(it: Int) -> Boolean'
```


<!-- ### Returning a value from a lambda expression -->
### Возврат значения из лямбда-выражения

<!-- We can explicitly return a value from the lambda using the [qualified return](returns.html#return-at-labels) syntax.
Otherwise, the value of the last expression is implicitly returned. -->
Мы можем вернуть значение из лямбды явно, используя оператор [return](returns.html#return-at-labels).
Либо неявно будет возвращено значение последнего выражения.

<!-- Therefore, the two following snippets are equivalent: -->
Таким образом, два следующих фрагмента равнозначны:

```kotlin
ints.filter {
    val shouldFilter = it > 0
    shouldFilter
}

ints.filter {
    val shouldFilter = it > 0
    return@filter shouldFilter
}
```

<!-- This convention, along with [passing a lambda expression outside parentheses](#passing-a-lambda-to-the-last-parameter), allows for
[LINQ-style](https://docs.microsoft.com/en-us/previous-versions/dotnet/articles/bb308959(v=msdn.10)) code: -->
Это соглашение, вместе с [передачей лямбда-выражения вне скобок](#passing-a-lambda-to-the-last-parameter), позволяет писать код в стиле [LINQ](https://docs.microsoft.com/en-us/previous-versions/dotnet/articles/bb308959(v=msdn.10)):

```kotlin
strings.filter { it.length == 5 }.sortedBy { it }.map { it.toUpperCase() }
```


<!-- ### Underscore for unused variables (since 1.1) -->
### Символ подчеркивания для неиспользуемых переменных (since 1.1)

<!-- If the lambda parameter is unused, you can place an underscore instead of its name: -->
Если параметр лямбды не используется, то разрешено его имя заменить на символ подчёркивания:

```kotlin
map.forEach { _, value -> println("$value!") }
```

<!-- ### Destructuring in lambdas (since 1.1) -->
### Деструктуризация в лямбдах (since 1.1)

<!-- Destructuring in lambdas is described as a part of [destructuring declarations](multi-declarations.html#destructuring-in-lambdas-since-11). -->
Деструктуризация в лямбдах описана в [деструктурирующие объявления](multi-declarations.html#destructuring-in-lambdas-since-11).


<a name="anonymous-functions"></a>
<!-- ### Anonymous functions -->
### Анонимные функции

<!--One thing missing from the lambda expression syntax presented above is the ability to specify the return type of the function. In most cases, this is unnecessary because the return type can be inferred automatically. However, if you do need to specify it explicitly, you can use an alternative syntax: an _anonymous function_.-->
Единственной особенностью синтаксиса лямбда-выражений, о которой ещё не было сказано, является способность определять и назначать возвращаемый функцией тип. В большинстве случаев в этом нет особой необходимости, потому что он может быть вычислен автоматически. Однако, если у вас есть потребность в определении возвращаемого типа, вы можете воспользоваться альтернативным синтаксисом: _анонимной функцией_.

```kotlin
fun(x: Int, y: Int): Int = x + y
```
<!--An anonymous function looks very much like a regular function declaration, except that its name is omitted. Its body can be either an expression (as shown above) or a block:-->
Объявление анонимной функции выглядит очень похоже на обычное объявление функции, за исключением того, что её имя опущено. Тело такой функции может быть описано и выражением (как показано выше), и блоком:

```kotlin
fun(x: Int, y: Int): Int {
    return x + y
}
```

<!--The parameters and the return type are specified in the same way as for regular functions, except that the parameter types can be omitted if they can be inferred from context:-->
Параметры функции и возвращаемый тип обозначаются таким же образом, как в обычных функциях. Правда, тип параметра может быть опущен, если его значение следует из контекста:

```kotlin
ints.filter(fun(item) = item > 0)
```

<!--The return type inference for anonymous functions works just like for normal functions: the return type is inferred automatically for anonymous functions with an expression body and has to be specified explicitly (or is assumed to be Unit) for anonymous functions with a block body.-->
Аналогично и с типом возвращаемого значения: он вычисляется автоматически для функций-выражений или же должен быть определён вручную (если не является типом `Unit`) для анонимных функций, которые имеют в себе блок.

<!--Note that anonymous function parameters are always passed inside the parentheses. The shorthand syntax allowing to leave the function outside the parentheses works only for lambda expressions.-->
Обратите внимание, что параметры анонимных функций всегда заключены в круглые скобки `(...)`. Приём, позволяющий оставлять параметры вне скобок, работает только с лямбда-выражениями.

<!--One other difference between lambda expressions and anonymous functions is the behavior of non-local returns. A return statement without a label always returns from the function declared with the fun keyword. This means that a return inside a lambda expression will return from the enclosing function, whereas a return inside an anonymous function will return from the anonymous function itself.-->
Одним из отличий лямбда-выражений от анонимных функций является поведение оператора `return` ([non-local returns](inline-functions.html#non-local-returns)). Слово <b class="keyword">return</b> , не имеющее метки (`@`), всегда возвращается из функции, объявленной ключевым словом <b class="keyword">fun</b>. Это означает, что <b class="keyword">return</b> внутри лямбда-выражения возвратит выполнение к функции, включающей в себя это лямбда-выражение. Внутри анонимных функций оператор <b class="keyword">return</b>, в свою очередь, выйдет, собственно, из анонимной функции.


<!--### Closures-->
### Замыкания

<!--A lambda expression or anonymous function (as well as a [local function](http://kotlinlang.org/docs/reference/functions.html#local-functions) and an [object expression](http://kotlinlang.org/docs/reference/object-declarations.html#object-expressions)) can access its closure, i.e. the variables declared in the outer scope. The variables captured in the closure can be modified in the lambda:-->
Лямбда-выражение или анонимная функция (так же, как и [локальная функция](functions.html#local-functions) или [object expression](object-declarations.html#object-expressions)) имеет доступ к своему замыканию, то есть к переменным, объявленным вне этого выражения или функции. Переменные, захваченные в замыкании, могут быть изменены в лямбде:

```kotlin
var sum = 0
ints.filter { it > 0 }.forEach {
    sum += it
}
print(sum)
```


<a name="function-literals-with-receiver"></a>
<!--## Function Literals with Receiver-->
## Литералы функций с объектом-приёмником

<!-- [Function types](#function-types) with receiver, such as `A.(B) -> C`, can be instantiated with a special form of function literals –
function literals with receiver. -->
[Функциональные типы](#function-types) с получателем, такие как `A.(B) -> C`, могут быть вызваны с помощью особой формы - литералов функций с объектом-приёмником.

<!-- As said above, Kotlin provides the ability [to call an instance](#invoking-a-function-type-instance) of a function type with receiver providing the _receiver object_. -->
Как было сказано выше, Kotlin позволяет вызывать [экземпляр](#invoking-a-function-type-instance) функционального типа с получателем, предоставляющим объект-приёмник.

<!-- Inside the body of the function literal, the receiver object passed to a call becomes an *implicit* *this*{: .keyword}, so that you
can access the members of that receiver object without any additional qualifiers, or access the receiver object
using a [`this` expression](this-expressions.html). -->
Внутри тела литерала объект-приёмник, переданный при вызове функции, становится *неявным* <b class="keyword">this</b>, поэтому вы можете получить доступ к членам этого объекта-приёмника без каких-либо дополнительных определителей, а обращение к самому объекту-приёмнику осуществляется с помощью выражения [`this`](this-expressions.html).

<!-- This behavior is similar to [extension functions](extensions.html), which also allow you to access the members of the receiver object
inside the body of the function. -->
Это схоже с принципом работы [функций-расширений](extensions.html), которые позволяют получить доступ к членам объекта-приёмника внутри тела функции.

<!-- Here is an example of a function literal with receiver along with its type, where `plus` is called on the
receiver object: -->
Ниже приведён пример литерала с получателем вместе с его типом, где `plus` вызывается для объекта-приёмника:

```kotlin
val sum: Int.(Int) -> Int = { other -> plus(other) }
```

<!--The anonymous function syntax allows you to specify the receiver type of a function literal directly. This can be useful if you need to declare a variable of a function type with receiver, and to use it later.-->
Синтаксис анонимной функции позволяет вам явно указать тип приёмника. Это может быть полезно в случае, если вам нужно объявить переменную типа нашей функции для использования в дальнейшем.

```kotlin
val sum = fun Int.(other: Int): Int = this + other
```

<!--Lambda expressions can be used as function literals with receiver when the receiver type can be inferred from context.
One of the most important examples of their usage is [type-safe builders](type-safe-builders.html):-->
Лямбда-выражения могут быть использованы как литералы функций с приёмником, когда тип приёмника может быть выведен из контекста.
Один из самых важных примеров их использования это [типобезопасные строители](type-safe-builders.html) (type-safe builders):

```kotlin
class HTML {
    fun body() { ... }
}

fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()  // создание объекта-приёмника
    html.init()        // передача приёмника в лямбду
    return html
}


html {       // лямбда с приёмником начинается тут
    body()   // вызов метода объекта-приёмника
}
```
