---
type: doc
layout: reference
title: "Методы асинхронного программирования"
url: https://kotlinlang.ru/docs/async-programming.html
---
      
<!-- При переводе статьи оригинальная версия была от 13 September 2021 -->

<!-- # Asynchronous programming techniques -->
# Методы асинхронного программирования

<!-- For decades, as developers we are confronted with a problem to solve - how to prevent our applications from blocking. Whether 
we're developing desktop, mobile, or even server-side applications, we want to avoid having the user wait or what's worse cause 
bottlenecks that would prevent an application from scaling. -->
На протяжении десятилетий мы, разработчики, сталкиваемся с проблемой, которую необходимо решить, - как предотвратить
блокировку наших приложений. Независимо от того, разрабатываем ли мы десткопные, мобильные или даже серверные
приложения, мы хотим избежать того, чтобы пользователь ждал или, что еще хуже, создавал узкие места (ориг.:
*bottlenecks*), которые мешали бы масштабированию приложения.

<!-- There have been many approaches to solving this problem, including: -->
Существует множество подходов к решению этой проблемы, в том числе:

<!-- * [Threading](#threading)
* [Callbacks](#callbacks)
* [Futures, promises, and others](#futures-promises-and-others)
* [Reactive Extensions](#reactive-extensions)
* [Coroutines](#coroutines) -->

* [Потоки (*нити*)](#threading),
* [Коллбэки (*обратные вызовы*)](#callbacks),
* [Futures (*фьючерс*), обещания и другое](#futures-promises-and-others),
* [Реактивные расширения](#reactive-extensions),
* [Корутины](#coroutines).

<!-- Before explaining what coroutines are, let's briefly review some of the other solutions. -->
Прежде чем объяснить, что такое корутины, давайте кратко рассмотрим некоторые другие решения.

<a name="threading"></a>

<!-- ## Threading -->
## Потоки

<!-- Threads are by far probably the most well-known approach to avoid applications from blocking. -->
Потоки (они же нити, ориг.: threads), безусловно, являются наиболее известным подходом, позволяющим избежать блокировки
приложений.

```kotlin
fun postItem(item: Item) {
    val token = preparePost()
    val post = submitPost(token, item)
    processPost(post)
}

fun preparePost(): Token {
    // делает запрос и, следовательно, блокирует основной поток
    return token
}
```

<!-- Let's assume in the code above that `preparePost` is a long-running process and consequently would block the user interface. What we can do is launch it in a separate thread. This would then
allow us to avoid the UI from blocking. This is a very common technique, but has a series of drawbacks: -->
Давайте предположим в приведенном выше коде, что `preparePost` является длительным процессом и, следовательно, он
блокирует пользовательский интерфейс. Его можно запустить в отдельном потоке. Это позволило бы избежать блокировки
пользовательского интерфейса. Это очень распространенный метод, но он имеет ряд недостатков:

<!-- * Threads aren't cheap. Threads require context switches which are costly.
* Threads aren't infinite. The number of threads that can be launched is limited by the underlying operating system. In server-side applications, this could cause a major bottleneck.
* Threads aren't always available. Some platforms, such as JavaScript do not even support threads.
* Threads aren't easy. Debugging threads, avoiding race conditions are common problems we suffer in multi-threaded programming.  -->

* Потоки недешевые. Потоки требуют переключения контекста, что является дорогостоящим.
* Потоки не бесконечны. Количество потоков, которые могут быть запущены, ограничено базовой операционной системой. В
  серверных приложениях это может стать серьезным узким местом.
* Потоки не всегда доступны. Некоторые платформы, такие как JavaScript, не поддерживают потоки.
* Потоки - это непросто. Отладка потоков, избежание состояния гонки (конкуренции) - это распространенные проблемы, с
которыми мы сталкиваемся при многопоточном программировании.

<a name="callbacks"></a>

<!-- ## Callbacks -->
## Коллбэки

<!-- With callbacks, the idea is to pass one function as a parameter to another function, and have this one invoked once the process has completed. -->
Идея коллбэков (обрытных вызовов, ориг.: callbacks) состоит в том, чтобы передать одну функцию в качестве параметра
другой функции и вызвать ее после завершения процесса.

```kotlin
fun postItem(item: Item) {
    preparePostAsync { token -> 
        submitPostAsync(token, item) { post -> 
            processPost(post)
        }
    }
}

fun preparePostAsync(callback: (Token) -> Unit) {
    // делает запрос и немедленно возвращается
    // организует коллбэк для последующего вызова
}
```

<!-- This in principle feels like a much more elegant solution, but once again has several issues: -->
В принципе, это выглядит более элегантным решением, но опять же имеет несколько проблем:

<!-- * Difficulty of nested callbacks. Usually a function that is used as a callback, often ends up needing its own callback. This leads to a series of nested callbacks which
lead to incomprehensible code. The pattern is often referred to as the titled christmas tree (braces represent branches of the tree).
* Error handling is complicated. The nesting model makes error handling and propagation of these somewhat more complicated.  -->

* Сложность вложенных коллбэков. Обычно функция, которая используется в качестве коллбэка, часто заканчивается тем, что
ей требуется собственный обратный вызов. Это приводит к серии вложенных обратных вызовов, из-за чего код становится
малопонятным. Этот паттерн часто называют рождественской елкой (фигурные скобки представляют ветви дерева).
* Обработка ошибок сложна. Модель вложенности несколько усложняет обработку ошибок и их воспроизведение.

<!-- Callbacks are quite common in event-loop architectures such as JavaScript, but even there, generally people have moved away to using other approaches such as promises or reactive extensions. -->
Коллбэки довольно распространены в архитектурах цикла событий (ориг.: *event-loop*), таких как JavaScript, но даже там,
как правило, люди перешли к использованию других подходов, таких как обещания или реактивные расширения.

<a name="futures-promises-and-others"></a>

<!-- ## Futures, promises, and others -->
## Фьючерс, обещания и другое

<!-- The idea behind futures or promises (there are also other terms these can be referred to depending on language/platform), is that when we make a call, we're promised 
that at some point it will return with an object called a Promise, which can then be operated on. -->
Идея, лежащая в основе фьючерсов (ориг.: *futures*) или обещаний (есть и другие термины, на которые можно ссылаться в
зависимости от языка/платформы), заключается в том, что когда мы совершаем вызов, нам обещают, что в какой-то момент он
вернет объект, который называется Promise (обещание) и с которым затем можно работать.

```kotlin
fun postItem(item: Item) {
    preparePostAsync() 
        .thenCompose { token -> 
            submitPostAsync(token, item)
        }
        .thenAccept { post -> 
            processPost(post)
        }
         
}

fun preparePostAsync(): Promise<Token> {
    // делает запрос и возвращает обещание, которое будет выполнено позже
    return promise 
}
```

<!-- This approach requires a series of changes in how we program, in particular: -->
Этот подход требует ряда изменений в том, как мы программируем, в частности:

<!-- * Different programming model. Similar to callbacks, the programming model moves away from a top-down imperative approach to a compositional model with chained calls. Traditional program structures 
such as loops, exception handling, etc. usually are no longer valid in this model.
* Different APIs. Usually there's a need to learn a completely new API such as `thenCompose` or `thenAccept`, which can also vary across platforms.
* Specific return type. The return type moves away from the actual data that we need and instead returns a new type `Promise` which has to be introspected. 
* Error handling can be complicated. The propagation and chaining of errors aren't always straightforward. -->

* Другая модель программирования. Подобно коллбэкам, модель программирования отходит от императивного подхода сверхувниз к композиционной модели с цепными вызовами. Традиционные программные структуры, такие как циклы, обработка исключений и т.д., обычно не применимы в этой модели;
* Различные API. Обычно возникает необходимость изучить совершенно новый API, такие как `thenCompose` или `thenAccept`, которые также могут варьироваться в зависимости от платформы;
* Определенный возвращаемый тип. Возвращаемый тип отклоняется от фактических данных, которые нам нужны, и вместо этого возвращает новый тип `Promise`, который необходимо проанализировать;
* Обработка ошибок может быть сложной. Распространение и цепочка ошибок не всегда просты.

<a name="reactive-extensions"></a>

<!-- ## Reactive extensions -->
## Реактивные расширения

<!-- Reactive Extensions (Rx) were introduced to C# by [Erik Meijer](https://en.wikipedia.org/wiki/Erik_Meijer_(computer_scientist)). While it was definitely used on the .NET platform
it really didn't reach mainstream adoption until Netflix ported it over to Java, naming it RxJava. From then on, numerous ports have been provided for a variety of platforms including JavaScript (RxJS). -->
Реактивные расширения (ориг.: *Reactive Extensions, Rx*) были введены в C#
[Эриком Мейером](https://en.wikipedia.org/wiki/Erik_Meijer_(computer_scientist)). Хотя Rx определенно использовались на
платформе .NET, в реальности они не получил широкого распространения, пока Netflix не перенес их на Java, назвав RxJava.
С тех пор было предоставлено множество портов для различных платформ, включая JavaScript (RxJS).

<!-- The idea behind Rx is to move towards what's called `observable streams` whereby we now think of data as streams (infinite amounts of data) and these streams can be observed. In practical terms, Rx is simply 
the [Observer Pattern](https://en.wikipedia.org/wiki/Observer_pattern) with a series of extensions which allow us to operate on the data. -->
Идея Rx состоит в том, чтобы перейти к так называемым `observable streams` (наблюдаемым потокам), благодаря которым
теперь мы думаем о данных как о потоках (бесконечных объемах данных), и эти потоки можно наблюдать. С практической точки
зрения, Rx - это просто [шаблон наблюдателя](https://en.wikipedia.org/wiki/Observer_pattern) с рядом расширений, которые
позволяют нам оперировать данными.

<!-- In approach it's quite similar to Futures, but one can think of a Future as returning a discrete element, whereas Rx returns a stream. However, similar to the previous, it also introduces 
a complete new way of thinking about our programming model, famously phrased as  -->
По подходу они очень похожи на фьючерс, но можно думать о фьючере как о возврате дискретного элемента, тогда как Rx
возвращают поток. Однако, как и фьючерс, они представляют совершенно новый взгляд на нашу модель программирования,
хорошо сформулированный как

<!--    "everything is a stream, and it's observable" -->
    "всё - поток, который можно наблюдать"

<!-- This implies a different way to approach problems and quite a significant shift from what we're used to when writing synchronous code. One benefit as opposed to Futures is that given it's ported to 
so many platforms, generally we can find a consistent API experience no matter what we use, be it C#, Java, JavaScript, or any other language where Rx is available. -->
Это подразумевает иной подход к решению проблем и довольно значительный сдвиг от того, к чему мы привыкли при написании
синхронного кода. Одно из преимуществ по сравнению с фьючерс заключается в том, что, учитывая, что Rx портированы на
множество платформ, как правило, мы можем найти согласованный API независимо от того, что мы используем, будь то C#,
Java, JavaScript или любой другой язык, где доступны Rx.

<!-- In addition, Rx does introduce a somewhat nicer approach to error handling.  -->
Кроме того, Rx действительно предлагают несколько более приятный подход к обработке ошибок.

<a name="coroutines"></a>

<!-- ## Coroutines  -->
## Корутины

<!-- Kotlin's approach to working with asynchronous code is using coroutines, which is the idea of suspendable computations, i.e. the idea that a function can suspend its execution at some point and resume later on.  -->
Подход Kotlin к работе с асинхронным кодом заключается в использовании корутинов (сопрограмм, ориг.: *coroutines*),
которые представляют собой идею приостанавливаемых вычислений, то есть идею о том, что функция может приостановить свое
выполнение в какой-то момент и возобновить позже.

<!-- One of the benefits however of coroutines is that when it comes to the developer, writing non-blocking code is essentially the same as writing blocking code. The programming model
in itself doesn't really change.  -->
Однако одно из преимуществ корутинов заключается в том, что, когда дело доходит до разработчика, написание
неблокирующего кода по сути совпадает с написанием блокирующего кода. Модель программирования сама по себе не меняется.

<!-- Take for instance the following code: -->
Возьмем, к примеру, следующий код:

```kotlin
fun postItem(item: Item) {
    launch {
        val token = preparePost()
        val post = submitPost(token, item)
        processPost(post)
    }
}

suspend fun preparePost(): Token {
    // делает запрос и приостанавливает выполнение корутины
    возвращает suspendCoroutine { /* ... */ }
}
```

<!-- This code will launch a long-running operation without blocking the main thread. The `preparePost` is what's called a 
`suspendable function`, thus the keyword `suspend` prefixing it. What this means as stated above, is that the function will 
execute, pause execution and resume at some point in time.  -->
Этот код запустит длительную операцию, не блокируя основной поток. `preparePost` - это так называемая
`suspendable function` (приостанавливаемая функция), поэтому в качестве префикса для неё выступает ключевое слово
`suspend`. Это означает, как указано выше, что функция будет выполняться, приостанавливать выполнение и возобновляться в
какой-то момент времени.

<!-- * The function signature remains exactly the same. The only difference is `suspend` being added to it. The return type however is the type we want to be
returned.
* The code is still written as if we were writing synchronous code, top-down, without the need of any special syntax, beyond the use of a function called `launch` which essentially kicks off
the coroutine (covered in other tutorials).
* The programming model and APIs remain the same. We can continue to use loops, exception handling, etc. and there's no need to learn a complete set of new APIs.
* It is platform independent. Whether we're targeting JVM, JavaScript or any other platform, the code we write is the same. Under the covers the compiler takes care of adapting it to each platform. -->

* Сигнатура функции остается точно такой же. Единственная разница заключается в том, что к ней добавляется `suspend`.
Однако возвращаемый тип - это тот тип, который мы хотели бы вернуть;
* Код по-прежнему написан так, как если бы мы писали синхронный код сверху вниз, без необходимости какого-либо
специального синтаксиса, помимо использования функции `launch`, которая, по сути, запускает сопрограмму (она описана в
других руководствах);
* Модель программирования и API остаются прежними. Мы можем продолжать использовать циклы, обработку исключений и т.д.,
и нет необходимости изучать полный набор новых API.
* Независимость от платформы. Независимо от того, ориентируемся ли мы на JVM, JavaScript или любую другую платформу,
код, который мы пишем, один и тот же. Компилятор "под капотом" заботится о том, чтобы адаптировать его к каждой платформе.

<!-- Coroutines are not a new concept, let alone invented by Kotlin. They've been around for decades and are popular in some other programming languages such as Go. What is important to note though
is that the way they're implemented in Kotlin, most of the functionality is delegated to libraries. In fact, beyond the `suspend` keyword, no other keywords are added to the language. This is somewhat different from
languages such as C# that have `async` and `await` as part of the syntax. With Kotlin, these are just library functions. -->
Корутины не являются новой концепцией, не говоря уже о том, что они не были изобретены Kotlin. Они существуют уже
несколько десятилетий и популярны в некоторых других языках программирования, таких как Go. Однако важно отметить, что
при их реализации в Kotlin большая часть функций делегируется библиотекам. На самом деле, кроме ключевого слова `suspend`,
в язык не добавляется никаких других ключевых слов. Это несколько отличается от таких языков, как C#, в которых `async`
и `await` являются частью синтаксиса. В Kotlin это всего лишь библиотечные функции.

<!-- For more information, see the [Coroutines reference](coroutines-overview.md). -->
Для дополнительной информации обратитесь к [справочнику сопрограмм](coroutines-overview.html).
