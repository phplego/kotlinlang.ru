---
type: doc
layout: reference
title: "Составление функций приостановки"
url: https://kotlinlang.ru/docs/composing-suspending-functions.html
---

<!-- При переводе статьи оригинальная версия была от 06 July 2021 -->

<!-- [//]: # "title: Composing suspending functions" -->

# Составление функций приостановки

<!-- This section covers various approaches to composition of suspending functions. -->

В этом разделе рассматриваются различные подходы к композиции функций приостановки.

<a name="sequential-by-default"></a>

<!-- ## Sequential by default -->

## Последовательно по умолчанию

<!-- Assume that we have two suspending functions defined elsewhere that do something useful like some kind of
remote service call or computation. We just pretend they are useful, but actually each one just
delays for a second for the purpose of this example: -->

Предположим, что у нас есть две функции приостановки, определенные в другом месте, которые делают что-то полезное, например, какой-то удаленный вызов службы или вычисление. Мы просто притворяемся, что они полезны, но на самом деле каждый из них просто задерживает на секунду для целей этого примера:

```kotlin
suspend fun doSomethingUsefulOne(): Int {
    delay(1000L) // притворимся, что мы делаем что-то полезное здесь
    return 13
}

suspend fun doSomethingUsefulTwo(): Int {
    delay(1000L) // притворимся, что мы тоже делаем что-то полезное здесь
    return 29
}
```

<!-- What do we do if we need them to be invoked _sequentially_ &mdash; first `doSomethingUsefulOne` _and then_
`doSomethingUsefulTwo`, and compute the sum of their results?
In practice, we do this if we use the result of the first function to make a decision on whether we need
to invoke the second one or to decide on how to invoke it. -->

Что мы делаем, если нам нужно, чтобы они вызывались _последовательно_ &mdash; первый `doSomethingUsefulOne`, _затем_ `doSomethingUsefulTwo`, и вычислить сумму их результатов? На практике мы делаем это, если используем результат первой функции, чтобы принять решение о том, нужно ли нам вызывать вторую, или решить, как ее вызывать.

<!-- We use a normal sequential invocation, because the code in the coroutine, just like in the regular
code, is _sequential_ by default. The following example demonstrates it by measuring the total
time it takes to execute both suspending functions: -->

Мы используем обычный последовательный вызов, потому что код в корутине, как и в обычном коде, по умолчанию _последовательный_. Следующий пример демонстрирует это, измеряя общее время, необходимое для выполнения обеих функций приостановки:

```kotlin
import kotlinx.coroutines.*
import kotlin.system.*

fun main() = runBlocking<Unit> {
    val time = measureTimeMillis {
        val one = doSomethingUsefulOne()
        val two = doSomethingUsefulTwo()
        println("The answer is ${one + two}")
    }
    println("Completed in $time ms")
}

suspend fun doSomethingUsefulOne(): Int {
    delay(1000L) // притворимся, что мы делаем что-то полезное здесь
    return 13
}

suspend fun doSomethingUsefulTwo(): Int {
    delay(1000L) // притворимся, что мы тоже делаем что-то полезное здесь
    return 29
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-compose-01.kt). -->

> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-compose-01.kt).

<!-- It produces something like this: -->

Получается что-то вроде этого:

```text
The answer is 42
Completed in 2017 ms
```

<a name="concurrent-using-async"></a>

<!-- ## Concurrent using async -->

## Параллельное использование async

<!-- What if there are no dependencies between invocations of `doSomethingUsefulOne` and `doSomethingUsefulTwo` and
we want to get the answer faster, by doing both _concurrently_? This is where [async] comes to help. -->

Что, если между вызовами `doSomethingUsefulOne` и `doSomethingUsefulTwo` нет зависимостей, и мы хотим получить ответ быстрее, выполняя оба одновременно? Здесь на помощь приходит [async](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html).

<!-- Conceptually, [async] is just like [launch]. It starts a separate coroutine which is a light-weight thread
that works concurrently with all the other coroutines. The difference is that `launch` returns a [Job] and
does not carry any resulting value, while `async` returns a [Deferred] &mdash; a light-weight non-blocking future
that represents a promise to provide a result later. You can use `.await()` on a deferred value to get its eventual result,
but `Deferred` is also a `Job`, so you can cancel it if needed. -->

Концептуально [async](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html) аналогична [launch](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html). Она запускает отдельную корутину, представляющую собой облегченный поток, который работает одновременно со всеми другими корутинами. Разница в том, что `launch` возвращает [Job](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html) и не несет никакого результирующего значения, тогда как `async` возвращает [Deferred](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-deferred/index.html) — облегченное неблокирующее будущее значение, которое представляет собой обещание предоставить результат позже. Вы можете использовать `.await()` для отложенного значения, чтобы получить его окончательный результат, но `Deferred` также является `Job`, поэтому при необходимости вы можете отменить его.

```kotlin
import kotlinx.coroutines.*
import kotlin.system.*

fun main() = runBlocking<Unit> {
    val time = measureTimeMillis {
        val one = async { doSomethingUsefulOne() }
        val two = async { doSomethingUsefulTwo() }
        println("The answer is ${one.await() + two.await()}")
    }
    println("Completed in $time ms")
}

suspend fun doSomethingUsefulOne(): Int {
    delay(1000L) // притворимся, что мы делаем что-то полезное здесь
    return 13
}

suspend fun doSomethingUsefulTwo(): Int {
    delay(1000L) // притворимся, что мы тоже делаем что-то полезное здесь
    return 29
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-compose-02.kt). -->

> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-compose-02.kt).

<!-- It produces something like this: -->

Получается что-то вроде этого:

```text
The answer is 42
Completed in 1017 ms
```

<!-- This is twice as fast, because the two coroutines execute concurrently.
Note that concurrency with coroutines is always explicit. -->

Это в два раза быстрее, потому что две корутины выполняются одновременно. Обратите внимание, что параллелизм с корутинами всегда явный.

<a name="lazily-started-async"></a>

<!-- ## Lazily started async -->

## Ленивый старт async

<!-- Optionally, [async] can be made lazy by setting its `start` parameter to [CoroutineStart.LAZY].
In this mode it only starts the coroutine when its result is required by
[await][deferred.await], or if its `Job`'s [start][job.start] function
is invoked. Run the following example: -->

При желании [async](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html) можно сделать ленивой, установив для параметра `start` значение [CoroutineStart.LAZY](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-start/-l-a-z-y/index.html). В этом режиме он запускает корутину только тогда, когда ее результат требуется для [await](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-deferred/await.html) или если вызывается функция [start](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/start.html) его `Job`. Запустите следующий пример:

```kotlin
import kotlinx.coroutines.*
import kotlin.system.*

fun main() = runBlocking<Unit> {
    val time = measureTimeMillis {
        val one = async(start = CoroutineStart.LAZY) { doSomethingUsefulOne() }
        val two = async(start = CoroutineStart.LAZY) { doSomethingUsefulTwo() }
        // some computation
        one.start() // start the first one
        two.start() // start the second one
        println("The answer is ${one.await() + two.await()}")
    }
    println("Completed in $time ms")
}

suspend fun doSomethingUsefulOne(): Int {
    delay(1000L) // притворимся, что мы делаем что-то полезное здесь
    return 13
}

suspend fun doSomethingUsefulTwo(): Int {
    delay(1000L) // притворимся, что мы тоже делаем что-то полезное здесь
    return 29
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-compose-03.kt). -->

> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-compose-03.kt).

<!-- It produces something like this: -->

Получается что-то вроде этого:

```text
The answer is 42
Completed in 1017 ms
```

<!-- So, here the two coroutines are defined but not executed as in the previous example, but the control is given to
the programmer on when exactly to start the execution by calling [start][job.start]. We first
start `one`, then start `two`, and then await for the individual coroutines to finish. -->

Итак, здесь две корутины определены, но не выполняются, как в предыдущем примере, а программист получает контроль над тем, когда именно начинать выполнение, вызывая [start](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/start.html). Сначала мы запускаем `one`, затем `two`, а затем ждем завершения отдельных корутин.

<!-- Note that if we just call [await][deferred.await] in `println` without first calling [start][job.start] on individual
coroutines, this will lead to sequential behavior, since [await][deferred.await] starts the coroutine
execution and waits for its finish, which is not the intended use-case for laziness.
The use-case for `async(start = CoroutineStart.LAZY)` is a replacement for the
standard `lazy` function in cases when computation of the value involves suspending functions. -->

Обратите внимание, что если мы просто вызовем [await](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-deferred/await.html) в `println` без предварительного вызова [start](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/start.html) для отдельных корутин, это приведет к последовательному поведению, поскольку [await](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-deferred/await.html) запускает выполнение корутины и ожидает ее завершения, что не является предполагаемым "ленивым" вариантом использования. Вариант использования для `async(start = CoroutineStart.LAZY)` — это замена стандартной функции `lazy` в случаях, когда вычисление значения включает приостановку функций.

<a name="async-style-functions"></a>

<!-- ## Async-style functions -->

## Функции в асинхронном стиле

<!-- We can define async-style functions that invoke `doSomethingUsefulOne` and `doSomethingUsefulTwo`
_asynchronously_ using the [async] coroutine builder using a [GlobalScope] reference to
opt-out of the structured concurrency.
We name such functions with the
"...Async" suffix to highlight the fact that they only start asynchronous computation and one needs
to use the resulting deferred value to get the result. -->

Мы можем определить функции в асинхронном стиле, которые вызывают `doSomethingUsefulOne` и `doSomethingUsefulTwo` _асинхронно_, используя построитель [async](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html) корутины с использованием ссылки на [GlobalScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-global-scope/index.html) для отказа от структурированного параллелизма. Мы называем такие функции суффиксом «...Async», чтобы подчеркнуть тот факт, что они только запускают асинхронные вычисления, и для получения результата необходимо использовать полученное отложенное значение.

<!-- > [GlobalScope] is a delicate API that can backfire in non-trivial ways, one of which will be explained
> below, so you must explicitly opt-in into using `GlobalScope` with `@OptIn(DelicateCoroutinesApi::class)`. -->

> [GlobalScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-global-scope/index.html) — это деликатный API, который может иметь нетривиальные последствия, один из которых будет объяснен ниже, поэтому вы должны явным образом согласиться на использование `GlobalScope` с помощью `@OptIn(DelicateCoroutinesApi::class)`.

```kotlin
// The result type of somethingUsefulOneAsync is Deferred<Int>
@OptIn(DelicateCoroutinesApi::class)
fun somethingUsefulOneAsync() = GlobalScope.async {
    doSomethingUsefulOne()
}

// The result type of somethingUsefulTwoAsync is Deferred<Int>
@OptIn(DelicateCoroutinesApi::class)
fun somethingUsefulTwoAsync() = GlobalScope.async {
    doSomethingUsefulTwo()
}
```

<!-- Note that these `xxxAsync` functions are **not** _suspending_ functions. They can be used from anywhere.
However, their use always implies asynchronous (here meaning _concurrent_) execution of their action
with the invoking code. -->

Обратите внимание, что эти функции `xxxAsync` **не являются** функциями _приостановки_. Их можно использовать откуда угодно. Однако их использование всегда подразумевает асинхронное (здесь это означает _параллельное_) выполнение их действия с вызывающим кодом.

<!-- The following example shows their use outside of coroutine: -->

В следующем примере показано их использование вне корутины:

```kotlin
import kotlinx.coroutines.*
import kotlin.system.*

// обратите внимание, что в этом примере у нас нет `runBlocking` справа от `main`
fun main() {
    val time = measureTimeMillis {
        // мы можем инициировать асинхронные действия вне корутины
        val one = somethingUsefulOneAsync()
        val two = somethingUsefulTwoAsync()
        // но ожидание результата должно включать либо приостановку, либо блокировку.
        // здесь мы используем `runBlocking { ... }`, чтобы заблокировать основной поток в ожидании результата
        runBlocking {
            println("The answer is ${one.await() + two.await()}")
        }
    }
    println("Completed in $time ms")
}

@OptIn(DelicateCoroutinesApi::class)
fun somethingUsefulOneAsync() = GlobalScope.async {
    doSomethingUsefulOne()
}

@OptIn(DelicateCoroutinesApi::class)
fun somethingUsefulTwoAsync() = GlobalScope.async {
    doSomethingUsefulTwo()
}

suspend fun doSomethingUsefulOne(): Int {
    delay(1000L) // притворимся, что мы делаем что-то полезное здесь
    return 13
}

suspend fun doSomethingUsefulTwo(): Int {
    delay(1000L) // притворимся, что мы тоже делаем что-то полезное здесь
    return 29
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-compose-04.kt). -->

> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-compose-04.kt).

<!-- > This programming style with async functions is provided here only for illustration, because it is a popular style
> in other programming languages. Using this style with Kotlin coroutines is **strongly discouraged** for the
> reasons explained below. -->

> Этот стиль программирования с асинхронными функциями приведен здесь только для иллюстрации, так как он популярен в других языках программирования. Использование этого стиля с корутинами Kotlin **настоятельно не рекомендуется** по причинам, описанным ниже.

<!-- Consider what happens if between the `val one = somethingUsefulOneAsync()` line and `one.await()` expression there is some logic
error in the code, and the program throws an exception, and the operation that was being performed by the program aborts.
Normally, a global error-handler could catch this exception, log and report the error for developers, but the program
could otherwise continue doing other operations. However, here we have `somethingUsefulOneAsync` still running in the background,
even though the operation that initiated it was aborted. This problem does not happen with structured
concurrency, as shown in the section below. -->

Рассмотрим, что произойдет, если между строкой `val one = somethingUsefulOneAsync()` и выражением `one.await()` в коде будет какая-то логическая ошибка, и программа выдаст исключение, а операция, которую выполняла программа, прервется. Обычно глобальный обработчик ошибок может перехватывать это исключение, регистрировать и сообщать об ошибке разработчикам, но в противном случае программа может продолжать выполнять другие операции. Однако здесь у нас `somethingUsefulOneAsync` все еще работает в фоновом режиме, даже несмотря на то, что операция, которая его инициировала, была прервана. Эта проблема не возникает при структурированном параллелизме, как показано в разделе ниже.

<a name="structured-concurrency-with-async"></a>

<!-- ## Structured concurrency with async -->

## Структурированный параллелизм с async

<!-- Let us take the [Concurrent using async](#concurrent-using-async) example and extract a function that concurrently performs `doSomethingUsefulOne` and `doSomethingUsefulTwo` and returns the sum of their results. Because the [async] coroutine builder is defined as an extension on [CoroutineScope], we need to have it in the scope and that is what the [coroutineScope][_coroutinescope] function provides: -->

Давайте возьмем пример [Параллельное использование async](#concurrent-using-async) и извлечем функцию, которая одновременно выполняет `doSomethingUsefulOne` и `doSomethingUsefulTwo` и возвращает сумму их результатов. Поскольку построитель [async](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html) корутин определен как расширение [CoroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html), нам нужно иметь его в области действия, и это то, что предоставляет функция [coroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/coroutine-scope.html):

```kotlin
suspend fun concurrentSum(): Int = coroutineScope {
    val one = async { doSomethingUsefulOne() }
    val two = async { doSomethingUsefulTwo() }
    one.await() + two.await()
}
```

<!-- This way, if something goes wrong inside the code of the `concurrentSum` function, and it throws an exception, all the coroutines that were launched in its scope will be cancelled. -->

Таким образом, если внутри кода функции `concurrentSum` что-то пойдет не так, и она выдаст исключение, все корутины, запущенные в ее области действия, будут отменены.

```kotlin
import kotlinx.coroutines.*
import kotlin.system.*

fun main() = runBlocking<Unit> {
    val time = measureTimeMillis {
        println("The answer is ${concurrentSum()}")
    }
    println("Completed in $time ms")
}

suspend fun concurrentSum(): Int = coroutineScope {
    val one = async { doSomethingUsefulOne() }
    val two = async { doSomethingUsefulTwo() }
    one.await() + two.await()
}

suspend fun doSomethingUsefulOne(): Int {
    delay(1000L) // притворимся, что мы делаем что-то полезное здесь
    return 13
}

suspend fun doSomethingUsefulTwo(): Int {
    delay(1000L) // притворимся, что мы тоже делаем что-то полезное здесь
    return 29
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-compose-05.kt). -->

> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-compose-05.kt).

<!-- We still have concurrent execution of both operations, as evident from the output of the above `main` function: -->

У нас все еще есть параллельное выполнение обеих операций, как видно из вывода вышеприведенной функции `main`:

```text
The answer is 42
Completed in 1017 ms
```

<!-- Cancellation is always propagated through coroutines hierarchy: -->

Отмена всегда распространяется через иерархию корутин:

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> {
    try {
        failedConcurrentSum()
    } catch(e: ArithmeticException) {
        println("Computation failed with ArithmeticException")
    }
}

suspend fun failedConcurrentSum(): Int = coroutineScope {
    val one = async<Int> {
        try {
            delay(Long.MAX_VALUE) // Эмулирует очень долгие вычисления
            42
        } finally {
            println("First child was cancelled")
        }
    }
    val two = async<Int> {
        println("Second child throws an exception")
        throw ArithmeticException()
    }
    one.await() + two.await()
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-compose-06.kt). -->

> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-compose-06.kt).

<!-- Note how both the first `async` and the awaiting parent are cancelled on failure of one of the children (namely, `two`): -->

Обратите внимание, как первый `async` и ожидающий родитель отменяются при сбое одного из дочерних элементов (а именно `two`):

```text
Second child throws an exception
First child was cancelled
Computation failed with ArithmeticException
```
