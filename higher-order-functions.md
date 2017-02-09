---
type: doc
layout: reference
category: "Higher-Order Functions and Lambdas
title: "Функции высокого уровня и Лямбды."
source url: http://kotlinlang.org/docs/reference/lambdas.html
---

# Функции высокого уровня и Лямбды.

## Функции высокого уровня.
Функция высокого уровня - это функция, которая принимает другую функцию в качестве входного аргумента,
либо имеет функцию в качестве возвращаемого результата. Хорошим примером такой функции является `lock()`,
которая берёт залоченный объект и функцию, применяет лок, выполняет фкнуцию и отпускает `lock`:

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
что это функция, которая не имеет никаких входных аргументов и возвращает значение типа T. Она вызывается
внутри блока `try`, защищена `lock`, и её результат возвращается `lock()` функцией.

Если мы хотим вызвать метод lock(), мы можем подать другую функцию в качестве входящего аргумента 
(более подробно читайте [function references](http://kotlinlang.org/docs/reference/reflection.html#function-references)

``` kotlin
fun toBeSynchronized() = sharedResource.operation()

val result = lock (lock, ::toBeSynchronized)
```

Другой, наиболее удобный способ применения [лямбда-выражения](http://kotlinlang.org/docs/reference/lambdas.html#lambda-expressions-and-anonymous-functions):
```kotlin
val result = lock(lock, { sharedResource.operation() })
```

Лямбда-выражения более потробно описаны [здесь](http://kotlinlang.org/docs/reference/lambdas.html#lambda-expressions-and-anonymous-functions), но в целях продолжить этот раздел, давайте произведём краткий обзор:
- Лямбда-выражения всегда заключены в круглые скобки.
- Параметры этого выражения (если такие есть) объявлены до знака `->` (параметры могут быть опущены)
- Тело выражения идёт после знака `->`

В Kotlin существует конвенция, по которой, если последний параметр функции является функцией, и вы применяете лямбда-
выражение в качестве аргумнента, вы можете указать её вне скобок:
``` kotlin
lock (lock) {
    sharedResource.operation()
}
```

Другим примером функции высокого уровня будет `map()`:
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

Обратите внимание, что параметры могут быть проигнорированы при вызове, если лямбда является единственным аргументом
для вызова функции.

<!--it: implicit name of a single parameter-->
<!--http://kotlinlang.org/docs/reference/lambdas.html#it-implicit-name-of-a-single-parameter-->
## it: неявное имя единичного параметра

Ещё одной полезной особенностью синтаксиса является возможность опустить объявление параметра функции в случае, если он
единственный (вместе с `->`) и её имя будет `it`:
``` kotlin
ints.map { it * 2 }
```

Это соглашение позволяет писать код в [LINQ](http://msdn.microsoft.com/en-us/library/bb308959.aspx) стиле:
``` kotlin
strings.filter { it.lenght == 5 }.sortBy { it }.map { it.toUpperCase() }
```

##Инлайн Функции
Иногда необходимо улучшить производительность высокоуровневых функций, исползуя [инлайн функции](http://kotlinlang.org/docs/reference/inline-functions.html)

<!--Lambda Expressions and Anonymous Functions-->
##Лямбда-выражения и Анонимные Функции.

<!--A lambda expression or an anonymous function is a "function literal", i.e. a function that is not declared, but passed immediately as an expression. Consider the following example:-->
Лямбда-выражения или анонимные функции являются "функциональными константами"<i>(ориг. "functional literal")</i>, то есть функциями, которые не были объявлены, но сразу были передана в качестве выражения. Рассмотрим следующий пример: 
``` kotlin
max(strings, { a, b -> a.length < b.length })
```

<!--Function max is a higher-order function, i.e. it takes a function value as the second argument. This second argument is an expression that is itself a function, i.e. a function literal. As a function, it is equivalent to-->
Функция `max` - высокоуровневая функция, так как она принимает другую функцию в качестве входного аргумента. Этот второй аргумент является выражением, которое само по себе представляет из себя функцию, то есть <i>functional literal</i>
``` kotlin
fun compare(a: String, b: String): Boolean = a.length < b.length
Function Types
```

<!--For a function to accept another function as a parameter, we have to specify a function type for that parameter. For example the abovementioned function max is defined as follows:-->

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

<!--In the body, line 4, less is used as a function: it is called by passing two arguments of type T.-->

<!--A function type is written as above, or may have named parameters, if you want to document the meaning of each parameter.-->

``` kotlin
val compare: (x: T, y: T) -> Int = ...
```

<!--##Lambda Expression Syntax-->

<!--The full syntactic form of lambda expressions, i.e. literals of function types, is as follows:-->
``` kotlin
val sum = { x: Int, y: Int -> x + y }
```
<!--A lambda expression is always surrounded by curly braces, parameter declarations in the full syntactic form go inside parentheses and have optional type annotations, the body goes after an -> sign. If the inferred return type of the lambda is not Unit, the last (or possibly single) expression inside the lambda body is treated as the return value.-->

<!--If we leave all the optional annotations out, what's left looks like this:-->
``` kotlin
val sum: (Int, Int) -> Int = { x, y -> x + y }
```
<!--It's very common that a lambda expression has only one parameter. If Kotlin can figure the signature out itself, it allows us not to declare the only parameter, and will implicitly declare it for us under the name it:-->
``` kotlin
ints.filter { it > 0 } // this literal is of type '(it: Int) -> Boolean'
```
<!--We can explicitly return a value from the lambda using the qualified return syntax. Otherwise, the value of the last expression is implictly returned. Therefore, the two following snippets are equivalent:-->
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

<!--##Anonymous Functions-->

<!--One thing missing from the lambda expression syntax presented above is the ability to specify the return type of the function. In most cases, this is unnecessary because the return type can be inferred automatically. However, if you do need to specify it explicitly, you can use an alternative syntax: an anonymous function.-->
```kotlin
fun(x: Int, y: Int): Int = x + y
```
<!--An anonymous function looks very much like a regular function declaration, except that its name is omitted. Its body can be either an expression (as shown above) or a block:-->

``` kotlin
fun(x: Int, y: Int): Int {
    return x + y
}
```

<!--The parameters and the return type are specified in the same way as for regular functions, except that the parameter types can be omitted if they can be inferred from context:-->

```kotlin
ints.filter(fun(item) = item > 0)
```

<!--The return type inference for anonymous functions works just like for normal functions: the return type is inferred automatically for anonymous functions with an expression body and has to be specified explicitly (or is assumed to be Unit) for anonymous functions with a block body.-->

<!--Note that anonymous function parameters are always passed inside the parentheses. The shorthand syntax allowing to leave the function outside the parentheses works only for lambda expressions.-->

<!--One other difference between lambda expressions and anonymous functions is the behavior of non-local returns. A return statement without a label always returns from the function declared with the fun keyword. This means that a return inside a lambda expression will return from the enclosing function, whereas a return inside an anonymous function will return from the anonymous function itself.-->

<!--##Closures-->

<!--A lambda expression or anonymous function (as well as a local function and an object expression) can access its closure, i.e. the variables declared in the outer scope. Unlike Java, the variables captured in the closure can be modified:-->

``` kotlin
var sum = 0
ints.filter { it > 0 }.forEach {
    sum += it
}
print(sum)
```

<!--##Function Literals with Receiver-->

<!--Kotlin provides the ability to call a function literal with a specified receiver object. Inside the body of the function literal, you can call methods on that receiver object without any additional qualifiers. This is similar to extension functions, which allow you to access members of the receiver object inside the body of the function. One of the most important examples of their usage is Type-safe Groovy-style builders.-->

<!--The type of such a function literal is a function type with receiver:-->

``` kotlin
sum : Int.(other: Int) -> Int
```

<!--The function literal can be called as if it were a method on the receiver object:-->

``` kotlin
1.sum(2)
```

<!--The anonymous function syntax allows you to specify the receiver type of a function literal directly. This can be useful if you need to declare a variable of a function type with receiver, and to use it later.-->

``` koltin
val sum = fun Int.(other: Int): Int = this + other
```

<!--Lambda expressions can be used as function literals with receiver when the receiver type can be inferred from context.-->

``` koltin
class HTML {
    fun body() { ... }
}

fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()  // create the receiver object
    html.init()        // pass the receiver object to the lambda
    return html
}


html {       // lambda with receiver begins here
    body()   // calling a method on the receiver object
}
```




