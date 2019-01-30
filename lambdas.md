---
type: doc
layout: reference
title: "Лямбды"
url: https://kotlinlang.ru/docs/reference/lambdas.html
---

# Высокоуровневые функции и лямбды

## Функции высшего порядка
Функция высшего порядка - это функция, которая принимает функции как параметры, или возвращает функцию в качестве результата.
Хорошим примером такой функции является `lock()`, которая берёт блокирующий объект и функцию, получает блокировку, выполняет функцию и отпускает блокировку:

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

Проанализируем этот код: `body` имеет функциональный тип: `() -> T`, то есть параметр должен быть
функцией без параметров, возвращающей значение типа `T`. Она вызывается
внутри блока `try`, под защитой объекта `lock`, получившего блокировку вызовом функции `lock()`.

Если мы хотим вызвать метод `lock()`, можно передать другую функцию в качестве входящего аргумента 
(подробно читайте [Ссылки на функции](reflection.html#function-references)):

``` kotlin
fun toBeSynchronized() = sharedResource.operation()

val result = lock (lock, ::toBeSynchronized)
```

Обычно удобней передавать [лямбда-выражения](lambdas.html#lambda-expressions-and-anonymous-functions):
```kotlin
val result = lock(lock, { sharedResource.operation() })
```

Лямбда-выражения подробно описаны [здесь](lambdas.html#lambda-expressions-and-anonymous-functions), но ради продолжения этого раздела сделаем краткий обзор:
- Лямбда-выражение всегда заключено в фигурные скобки;
- Его параметры (если они есть) объявлены до знака `->` (допустимо не указывать параметры);
- Тело выражения следует после знака `->`.

В Kotlin есть конвенция, согласно которой, если последний параметр функции является функцией,
которая передается в виде лямбда-выражения, можно вынести его за скобки:
``` kotlin
lock (lock) {
    sharedResource.operation()
}
```

Следующим примером функции высшего порядка выберем функцию `map()`:
``` kotlin
fun <T, R> List<T>.map(transform: (T) -> R): List<R> {
    val result = arrayListOf<R>()
    for (item in this)
        result.add(transform(item))
    return result
}
```

Эту функцию можно вызвать так:
``` kotlin
val doubled = ints.map { it -> it * 2 }
```

Обратите внимание, что скобки можно вообще не указывать при вызове функции,
если лямбда является единственным аргументом.

<!--it: implicit name of a single parameter-->
<!--http://kotlinlang.org/docs/reference/lambdas.html#it-implicit-name-of-a-single-parameter-->

## Ключевое слово `it`: неявное имя единственного параметра

Ещё одна полезная конвенция состоит в том, что если функциональный литерал имеет ровно один параметр,
его объявление можно удалить (вместе с `->`), и обращаться к нему по имени `it`:
``` kotlin
ints.map { it * 2 }
```

Эти конвенции позволяют писать код в стиле [LINQ](http://msdn.microsoft.com/en-us/library/bb308959.aspx):
``` kotlin
strings.filter { it.length == 5 }.sortBy { it }.map { it.toUpperCase() }
```

### Символ подчеркивания для неиспользуемых переменных (since 1.1)

Если параметр лямбды не используется, разрешено применять подчеркивание вместо его имени

``` kotlin
map.forEach { _, value -> println("$value!") }
```

### Деструктуризация в лямбдах (since 1.1)

Деструктуризация в лямбдах описана в [деструктурирующие объявления](multi-declarations.html#destructuring-in-lambdas-since-11). 

## Инлайн функции
Иногда выгодно улучшить производительность функций высшего порядка, используя [инлайн функции](inline-functions.html).

<a name="lambda-expressions-and-anonymous-functions"></a>

<!--Lambda Expressions and Anonymous Functions-->
## Лямбда-выражения и анонимные функции

Лямбда-выражение, или анонимная функция, это "функциональный литерал", то есть необъявленная функция, которая немедленно
используется в качестве выражения. Рассмотрим следующий пример:

``` kotlin
max(strings, { a, b -> a.length < b.length })
```

Функция `max` является функцией высшего порядка, потому что она принимает функцию в качестве второго аргумента.
Этот второй аргумент является выражением, которое в свою очередь есть функция, то есть <i>функциональный литерал</i>.
Как функция он эквивалентен объявлению:

``` kotlin
fun compare(a: String, b: String): Boolean = a.length < b.length
```

## Функциональные типы

Чтобы функция могла принять функцию в качестве параметра, необходимо указать тип функции-параметра.
Например, вышеуказанная функция `max` определена так:

``` kotlin
fun <T> max(collection: Collection<T>, less: (T, T) -> Boolean): T? {
    var max: T? = null
    for (it in collection)
        if (max == null || less(max, it))
            max = it
    return max
}
```

Параметр `less` является `(T, T) -> Boolean` типом, то есть функцией, которая принимает два параметра типа `T` и возвращает Boolean: 'true', 
если первый параметр меньше второго.

В теле функции, строка 4, `less` используется как функция: она вызвана с двумя параметрами типа `T`. 

Функциональный тип записывается, как указано выше, или может иметь именованые параметры, если нужно выявить смысл каждого из параметров.

``` kotlin
val compare: (x: T, y: T) -> Int = ...
```

<a name="lambda-expression-syntax"></a>
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
Обратите внимание, что параметры анонимных функций всегда заключены в круглые скобки `(...)`. Приём, позволяющий оставлять параметры вне скобок, работает только с лямбда-выражениями. 

<!--One other difference between lambda expressions and anonymous functions is the behavior of non-local returns. A return statement without a label always returns from the function declared with the fun keyword. This means that a return inside a lambda expression will return from the enclosing function, whereas a return inside an anonymous function will return from the anonymous function itself.-->
Одним из отличий лямбда-выражений от анонимных функций является поведение оператора `return` ([non-local returns](inline-functions.html#non-local-returns)). Слово `return` , не имеющее метки (`@`), всегда возвращается из функции, объявленной ключевым словом `fun`. Это означает, что `return` внутри лямбда-выражения возвратит выполнение к функции, включающей в себя это лямбда-выражение. Внутри анонимных функций оператор `return`, в свою очередь, выйдет, собственно, из анонимной функции.

<!--## Closures-->
## Замыкания

<!--A lambda expression or anonymous function (as well as a [local function](http://kotlinlang.org/docs/reference/functions.html#local-functions) and an [object expression](http://kotlinlang.org/docs/reference/object-declarations.html#object-expressions)) can access its closure, i.e. the variables declared in the outer scope. Unlike Java, the variables captured in the closure can be modified:-->
Лямбда-выражение или анонимная функция (так же, как и [локальная функция](functions.html#local-functions) или [object expression](object-declarations.html#object-expressions)) имеет доступ к своему замыканию, то есть к переменным, объявленным вне этого выражения или функции. В отличие от Java, переменные, захваченные в замыкании, могут быть изменены:

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





