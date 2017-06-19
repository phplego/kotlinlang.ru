---
type: doc
layout: reference
title: "Лямбды"
url: https://kotlinlang.ru/docs/reference/lambdas.html
---

# Высокоуровневые функции и лямбды

## Функции высшего порядка
Высокоуровневая функция - это функция, которая принимает другую функцию в качестве входного аргумента,
либо имеет функцию в качестве возвращаемого результата. Хорошим примером такой функции является `lock()`,
которая берёт залоченный объект и функцию, применяет лок, выполняет функцию и отпускает `lock`:

``` kotlin
fun <T> lock(lock: Lock, body: () -> T): T{
  lock.lock()
  try{
      return body()
  }
  finally {
      lock.unlock()
  }
}
```

Давайте проанализируем этот блок. Параметр `body` имеет функциональный тип: `() -> T`, то есть предполагается,
что это функция, которая не имеет никаких входных аргументов и возвращает значение типа `T`. Она вызывается
внутри блока `try`, защищена `lock`, и её результат возвращается функцией `lock()`.

Если мы хотим вызвать метод lock(), мы можем подать другую функцию в качестве входящего аргумента 
(более подробно читайте [function references](reflection.html#function-references)):

``` kotlin
fun toBeSynchronized() = sharedResource.operation()

val result = lock (lock, ::toBeSynchronized)
```

Другой, наиболее удобный способ применения [лямбда-выражения](lambdas.html#lambda-expressions-and-anonymous-functions):
```kotlin
val result = lock(lock, { sharedResource.operation() })
```

Лямбда-выражения более подробно описаны [здесь](lambdas.html#lambda-expressions-and-anonymous-functions), но в целях продолжить этот раздел, давайте произведём краткий обзор:
- Лямбда-выражения всегда заключены в фигурные скобки,
- Параметры этого выражения (если такие есть) объявлены до знака `->` (параметры могут быть опущены),
- Тело выражения идёт после знака `->`.

В Kotlin существует конвенция, по которой, если последний параметр функции является функцией, и вы применяете лямбда-
выражение в качестве аргумента, вы можете указать её вне скобок:
``` kotlin
lock (lock) {
    sharedResource.operation()
}
```

Другим примером функции высшего порядка служит функция `map()`:
``` kotlin
fun <T, R> List<T>.map(transform: (T) -> R): List<R> {
    val result = arrayListOf<R>()
    for (item in this)
        result.add(transform(item))
    return result
}
```

Эта функция может быть вызвана следующим образом:
``` kotlin
val doubled = ints.map { it -> it * 2 }
```

Обратите внимание, что параметры могут быть проигнорированы при вызове функции в том случае, если лямбда является единственным аргументом
для её вызова.

<!--it: implicit name of a single parameter-->
<!--http://kotlinlang.org/docs/reference/lambdas.html#it-implicit-name-of-a-single-parameter-->
## Ключевое слово `it`: неявное имя единственного параметра

Ещё одной полезной особенностью синтаксиса является возможность опустить объявление параметра функции в случае, если он
единственный (вместе с `->`). Слово `it` будет принято в качестве имени для такой функции:
``` kotlin
ints.map { it * 2 }
```

Это соглашение позволяет писать код в [LINQ](http://msdn.microsoft.com/en-us/library/bb308959.aspx) стиле:
``` kotlin
strings.filter { it.lenght == 5 }.sortBy { it }.map { it.toUpperCase() }
```

## Инлайн функции
Иногда необходимо улучшить производительность высокоуровневых функций, используя [инлайн функции](inline-functions.html).

<a name="lambda-expressions-and-anonymous-functions"></a>

<!--Lambda Expressions and Anonymous Functions-->
## Лямбда-выражения и анонимные функции

<!--A lambda expression or an anonymous function is a "function literal", i.e. a function that is not declared, but passed immediately as an expression. Consider the following example:-->
Лямбда-выражения или анонимные функции являются ~~"функциональными константами"~~<i>(ориг. "functional literal")</i>, то есть функциями, которые не были объявлены, но сразу были переданы в качестве выражения. Рассмотрим следующий пример:
``` kotlin
max(strings, { a, b -> a.length < b.length })
```

<!--Function max is a higher-order function, i.e. it takes a function value as the second argument. This second argument is an expression that is itself a function, i.e. a function literal. As a function, it is equivalent to-->


Функция `max` - высокоуровневая функция, так как она принимает другую функцию в качестве входного аргумента. Этот второй аргумент является выражением, которое само по себе представляет из себя функцию, то есть <i>functional literal</i>.
``` kotlin
fun compare(a: String, b: String): Boolean = a.length < b.length
```

<!--Function Types-->
## Типы функций

<!--For a function to accept another function as a parameter, we have to specify a function type for that parameter. For example the abovementioned function max is defined as follows:-->
Для того, чтобы функция принимала другую функцию в качестве входного параметра, нам необходимо указать её (входящей функции) тип. К примеру, вышеуказанная функция `max` определена следующим образом:
``` kotlin
fun <T> max(collection: Collection<T>, less: (T, T) -> Boolean): T? {
    var max: T? = null
    for (it in collection)
        if (max == null || less(max, it))
            max = it
    return max
}
```

<!--The parameter less is of type (T, T) -> Boolean, i.e. a function that takes two parameters of type T and returns a Boolean: true if the first one is smaller than the second one.-->
Параметр 'less' является `(T, T) -> Boolean` типом, то есть функцией, которая принимает два параметра типа `T` и возвращает 'Boolean':'true', если первый параметр меньше, чем второй. 

<!--In the body, line 4, less is used as a function: it is called by passing two arguments of type T.-->
В теле функции, строка 4, `less` используется в качестве функции: она вызывается путём передачи двух аргументов типа `T`. 

<!--A function type is written as above, or may have named parameters, if you want to document the meaning of each parameter.-->
Тип функции может быть написан так, как указано выше, или же может иметь определённые параметры, если вы хотите обозначить значения каждого из параметров.
``` kotlin
val compare: (x: T, y: T) -> Int = ...
```

<!--##Lambda Expression Syntax-->
## Синтаксис лямбда-выражений

<!--The full syntactic form of lambda expressions, i.e. literals of function types, is as follows:-->
Полная синтаксическая форма лямбда-выражений, таких как <i>literals of function types</i>, может быть представлена следующим образом:
``` kotlin
val sum = { x: Int, y: Int -> x + y }
```

<!--A lambda expression is always surrounded by curly braces, parameter declarations in the full syntactic form go inside parentheses and have optional type annotations, the body goes after an -> sign. If the inferred return type of the lambda is not Unit, the last (or possibly single) expression inside the lambda body is treated as the return value.-->
Лямбда-выражение всегда заключено в скобки `{...}`, объявление параметров при таком синтаксисе происходит внутри этих скобок и может включать в себя аннотации типов (опционально), тело функции начинается после знака `->`. Если тип возвращаемого значения не `Unit`, то в качестве возвращаемого типа принимается последнее (а возможно и единственное) выражение внутри тела лямбды. 

<!--If we leave all the optional annotations out, what's left looks like this:-->
Если мы вынесем все необязательные объявления, то, что останется, будет выглядеть следующим образом:
``` kotlin
val sum: (Int, Int) -> Int = { x, y -> x + y }
```

<!--It's very common that a lambda expression has only one parameter. If Kotlin can figure the signature out itself, it allows us not to declare the only parameter, and will implicitly declare it for us under the name it:-->
Обычное дело, когда лямбда-выражение имеет только один параметр. Если <b>Kotlin</b> может определить сигнатуру метода сам, он позволит нам не объявлять этот единственный параметр, и объявит его сам под именем `it`:
``` kotlin
ints.filter { it > 0 } //Эта константа имеет тип '(it: Int) -> Boolean'
```

<!--We can explicitly return a value from the lambda using the qualified return syntax. Otherwise, the value of the last expression is implictly returned. Therefore, the two following snippets are equivalent:-->
Мы можем явно вернуть значение из лямбды, используя [qualified return](returns.html#return-at-labels) синтаксис:
``` kotlin
ints.filter {
    val shouldFilter = it > 0 
    shouldFilter
}

ints.filter {
    val shouldFilter = it > 0 
    return@filter shouldFilter
}
```
<!--Note that if a function takes another function as the last parameter, the lambda expression argument can be passed outside the parenthesized argument list. See the grammar for callSuffix.-->
Обратите внимение, что функция принимает другую функцию в качестве своего последнего параметра, аргумент лямбда-выражения в таком случае может быть принят вне списка аргументов, заключённого в скобках. См. [callSuffix](grammar.html#call-suffix).

<a name="anonymous-functions"></a>

<!--##Anonymous Functions-->
## Анонимные функции

<!--One thing missing from the lambda expression syntax presented above is the ability to specify the return type of the function. In most cases, this is unnecessary because the return type can be inferred automatically. However, if you do need to specify it explicitly, you can use an alternative syntax: an anonymous function.-->
Единственной особенностью синтаксиса лямбда-выражений, о которой ещё не было сказано, является способность определять и назначать возвращаемый функцией тип. В большинстве случаев в этом нет особой необходимости, потому что он может быть вычислен автоматически. Однако, если у вас есть потребность в определении возвращаемого типа, вы можете воспользоваться альтернативным синтаксисом:
```kotlin
fun(x: Int, y: Int): Int = x + y
```
<!--An anonymous function looks very much like a regular function declaration, except that its name is omitted. Its body can be either an expression (as shown above) or a block:-->
Объявление анонимной функции выглядит очень похоже на обычное объявление функции, за исключением того, что её имя опущено. Тело такой функции может быть описано и выражением (как показано выше), и блоком:
``` kotlin
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
Обратите внимание, что параметры анонимных функций всегда заключены в скобки `{...}`. Приём, позволяющий оставлять параметры вне скобок, работает только с лямбда-выражениями. 

<!--One other difference between lambda expressions and anonymous functions is the behavior of non-local returns. A return statement without a label always returns from the function declared with the fun keyword. This means that a return inside a lambda expression will return from the enclosing function, whereas a return inside an anonymous function will return from the anonymous function itself.-->
Одним из отличий лямбда-выражений от анонимных функций является поведение оператора `return` ([non-local returns](inline-functions.html#non-local-returns)). Слово `return` , не имеющее метки (`@`), всегда возвращается из функции, объявленной ключевым словом `fun`. Это означает, что `return` внутри лямбда-выражения возвратит выполнение к функции, включающей в себя это лямбда-выражение. Внутри анонимных функций оператор `return`, в свою очередь, выйдет, собственно, из анонимной функции.

<!--## Closures-->
## Замыкания

<!--A lambda expression or anonymous function (as well as a [local function](http://kotlinlang.org/docs/reference/functions.html#local-functions) and an [object expression](http://kotlinlang.org/docs/reference/object-declarations.html#object-expressions)) can access its closure, i.e. the variables declared in the outer scope. Unlike Java, the variables captured in the closure can be modified:-->
Лямбда-выражение или анонимная функция (так же, как и [локальная функция](functions.html#local-functions) или [object expression](object-declarations.html#object-expressions)) имеет доступ к своему замыканию, то есть к переменным, объявленным вне этого выражения или функции. В отличае от Java, переменные, захваченные в замыкании, могут быть изменены:

``` kotlin
var sum = 0
ints.filter { it > 0 }.forEach {
    sum += it
}
print(sum)
```


<!--## Function Literals with Receiver-->
<a name="function-literals-with-receiver"></a>
## Литералы функций с объектом-приёмником
<!--Kotlin provides the ability to call a function literal with a specified receiver object. Inside the body of the function literal, you can call methods on that receiver object without any additional qualifiers. This is similar to extension functions, which allow you to access members of the receiver object inside the body of the function. One of the most important examples of their usage is [Type-safe Groovy-style builders](http://kotlinlang.org/docs/reference/type-safe-builders.html).-->
Kotlin предоставляет возможность вызывать литерал функции с указаным объектом-приёмником. Внутри тела литерала вы можете вызывать методы объекта-приёмника без дополнительных определителей. Это схоже с принципом работы [расширений](extensions.html), которые позволяют получить доступ к членам объекта-приёмника внутри тела функции. Один из самых важных примеров использования литералов с объектом-приёмником это [Type-safe Groovy-style builders](type-safe-builders.html).

<!--The type of such a function literal is a function type with receiver:-->
Тип такого литерала — это тип функции с приёмником:
``` kotlin
sum : Int.(other: Int) -> Int
```

<!--The function literal can be called as if it were a method on the receiver object:-->
По аналогии с расширениями, литерал функции может быть вызван так, будто он является методом объекта-приёмника:

``` kotlin
1.sum(2)
```

<!--The anonymous function syntax allows you to specify the receiver type of a function literal directly. This can be useful if you need to declare a variable of a function type with receiver, and to use it later.-->
Синтаксис анонимной функции позволяет вам явно указать тип приёмника. Это может быть полезно в случае, если вам нужно объявить переменную типа нашей функции для использования в дальнейшем.

``` koltin
val sum = fun Int.(other: Int): Int = this + other
```

<!--Lambda expressions can be used as function literals with receiver when the receiver type can be inferred from context.-->
Лямбда-выражения могут быть использованы как литералы функций с приёмником, когда тип приёмника может быть выведен из контекста.

``` kotlin
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





