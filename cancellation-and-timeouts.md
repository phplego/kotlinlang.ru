---
type: doc
layout: reference
title: "Отмена корутин и тайм-ауты"
url: https://kotlinlang.ru/docs/cancellation-and-timeouts.html
---

<!-- При переводе статьи оригинальная версия была от 13 September 2021 -->

<!-- # Cancellation and timeouts -->
# Отмена корутин и тайм-ауты

<!-- This section covers coroutine cancellation and timeouts. -->
В этом разделе рассматривается отмена корутин и тайм-ауты.

<a name="cancelling-coroutine-execution"></a>

<!-- ## Cancelling coroutine execution -->
## Отмена выполнения корутин

<!-- In a long-running application you might need fine-grained control on your background coroutines.
For example, a user might have closed the page that launched a coroutine and now its result
is no longer needed and its operation can be cancelled. 
The [launch] function returns a [Job] that can be used to cancel the running coroutine: -->
В долго работающем приложении вам может понадобиться детальное управление фоновыми корутинами. Например, пользователь
может закрыть страницу, которая запускала корутину, из-за чего её результат больше не нужен, и её действие можно
отменить. Функция [`launch`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html)
возвращает [`Job`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html),
которую можно использовать для отмены запущенной корутины.

```kotlin
val job = launch {
    repeat(1000) { i ->
        println("job: I'm sleeping $i ...")
        delay(500L)
    }
}
delay(1300L) // delay a bit
println("main: I'm tired of waiting!")
job.cancel() // cancels the job
job.join() // waits for job's completion 
println("main: Now I can quit.")
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-cancel-01.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-cancel-01.kt).

<!-- It produces the following output: -->
Этот код выведет следующее:

```text
job: I'm sleeping 0 ...
job: I'm sleeping 1 ...
job: I'm sleeping 2 ...
main: I'm tired of waiting!
main: Now I can quit.
```

<!-- As soon as main invokes `job.cancel`, we don't see any output from the other coroutine because it was cancelled. 
There is also a [Job] extension function [cancelAndJoin] 
that combines [cancel][Job.cancel] and [join][Job.join] invocations. -->
Как только главная функция вызывает `job.cancel`, мы больше не видим какого-либо вывода с другой корутины, потому что
она была отменена. Существует также [cancelAndJoin](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/cancel-and-join.html)
функция-расширение [`Job`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html),
которая объединяет вызовы [cancel](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/cancel.html)
и [join](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/join.html).

<a name="cancellation-is-cooperative"></a>

<!-- ## Cancellation is cooperative -->
## Отмена кооперативна

<!-- Coroutine cancellation is _cooperative_. A coroutine code has to cooperate to be cancellable.
All the suspending functions in `kotlinx.coroutines` are _cancellable_. They check for cancellation of 
coroutine and throw [CancellationException] when cancelled. However, if a coroutine is working in 
a computation and does not check for cancellation, then it cannot be cancelled, like the following 
example shows: -->
Отмена корутин кооперативна. Код корутины должен взаимодействовать, чтобы его можно было отменить.
Все suspend-функции в `kotlinx.coroutines` - *отменяемые*. Они проверяют отмену
корутины, и в случае отмены выбрасывают исключение
[`CancellationException`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-cancellation-exception/index.html).
Однако, если корутина работает над вычислениями и не проверяет на отмену, то её нельзя отменить, как это происходит,
например, здесь:

```kotlin
val startTime = System.currentTimeMillis()
val job = launch(Dispatchers.Default) {
    var nextPrintTime = startTime
    var i = 0
    while (i < 5) { // computation loop, just wastes CPU
        // print a message twice a second
        if (System.currentTimeMillis() >= nextPrintTime) {
            println("job: I'm sleeping ${i++} ...")
            nextPrintTime += 500L
        }
    }
}
delay(1300L) // delay a bit
println("main: I'm tired of waiting!")
job.cancelAndJoin() // cancels the job and waits for its completion
println("main: Now I can quit.")
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-cancel-02.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-cancel-02.kt).

<!-- Run it to see that it continues to print "I'm sleeping" even after cancellation
until the job completes by itself after five iterations. -->
Запустив этот код, вы увидите как корутина продолжает выводить на экран "I'm sleeping" даже после отмены, пока `job` не
завершится после пяти итераций.

<a name="making-computation-code-cancellable"></a>

<!-- ## Making computation code cancellable -->
## Делаем код с вычислениями отменяемым

<!-- There are two approaches to making computation code cancellable. The first one is to periodically 
invoke a suspending function that checks for cancellation. There is a [yield] function that is a good choice for that purpose.
The other one is to explicitly check the cancellation status. Let us try the latter approach. -->
Есть два способа сделать вычислительный код отменяемым. Первый – периодически вызвать suspend-функцию, которая
проверяет, активна ли корутина. Для этого хорошо подходит функция
[`yield`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/yield.html). Другой —
явно проверять статус отмены. Попробуем этот подход.

<!-- Replace `while (i < 5)` in the previous example with `while (isActive)` and rerun it. -->
Замените `while (i < 5)` в предыдущем примере на `while (isActive)` и запустите его ещё раз.

```kotlin
val startTime = System.currentTimeMillis()
val job = launch(Dispatchers.Default) {
    var nextPrintTime = startTime
    var i = 0
    while (isActive) { // cancellable computation loop
        // print a message twice a second
        if (System.currentTimeMillis() >= nextPrintTime) {
            println("job: I'm sleeping ${i++} ...")
            nextPrintTime += 500L
        }
    }
}
delay(1300L) // delay a bit
println("main: I'm tired of waiting!")
job.cancelAndJoin() // cancels the job and waits for its completion
println("main: Now I can quit.")
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-cancel-03.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-cancel-03.kt)

<!-- As you can see, now this loop is cancelled. [isActive] is an extension property 
available inside the coroutine via the [CoroutineScope] object. -->
Как вы можете увидеть, теперь цикл отменяется.
[`isActive`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/is-active.html) —
это extension-параметр, доступный внутри корутины, благодаря объекту
[`CoroutineScope`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html).

<a name="closing-resources-with-finally"></a>

<!-- ## Closing resources with `finally` -->
## Закрытие ресурсов при помощи finally

<!-- Cancellable suspending functions throw [CancellationException] on cancellation which can be handled in 
the usual way. For example, `try {...} finally {...}` expression and Kotlin `use` function execute their
finalization actions normally when a coroutine is cancelled: -->
Отменяемые suspend-функции при отмене выбрасывают исключение
[`CancellationException`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-cancellation-exception/index.html),
которое может быть обработано обычным путём. Например, выражение `try {...} finally {...}` и Kotlin-функция `use`
обыкновенно выполняют свои функции при завершении (отмене) корутин.

```kotlin
val job = launch {
    try {
        repeat(1000) { i ->
            println("job: I'm sleeping $i ...")
            delay(500L)
        }
    } finally {
        println("job: I'm running finally")
    }
}
delay(1300L) // delay a bit
println("main: I'm tired of waiting!")
job.cancelAndJoin() // cancels the job and waits for its completion
println("main: Now I can quit.")
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-cancel-04.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-cancel-04.kt).

<!-- Both [join][Job.join] and [cancelAndJoin] wait for all finalization actions to complete, 
so the example above produces the following output: -->
И [`join`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/join.html), и
[`cancelAndJoin`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/cancel-and-join.html)
ожидают завершения всех финальных стадий, поэтому приведённый выше пример даёт такой вывод:

```text
job: I'm sleeping 0 ...
job: I'm sleeping 1 ...
job: I'm sleeping 2 ...
main: I'm tired of waiting!
job: I'm running finally
main: Now I can quit.
```

<a name="run-non-cancellable-block"></a>

<!-- ## Run non-cancellable block -->
## Запуск неотменяемого блока

<!-- Any attempt to use a suspending function in the `finally` block of the previous example causes
[CancellationException], because the coroutine running this code is cancelled. Usually, this is not a 
problem, since all well-behaving closing operations (closing a file, cancelling a job, or closing any kind of a 
communication channel) are usually non-blocking and do not involve any suspending functions. However, in the 
rare case when you need to suspend in a cancelled coroutine you can wrap the corresponding code in
`withContext(NonCancellable) {...}` using [withContext] function and [NonCancellable] context as the following example shows: -->
Любая попытка использовать suspend-функцию в блоке `finally` предыдущего примера приводит к
[`CancellationException`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-cancellation-exception/index.html),
потому что корутина, выполняющая этот код, отменена. Обычно это не проблема, так как все нормально работающие операции
закрытия (закрытие файла, отмена Job или закрытие любого вида канала) обычно не блокируются и не требуют каких-либо
suspend-функций. Однако в редких случаях, когда вам нужно приостановить работу отмененной корутины, вы можете обернуть
соответствующий код в `withContext(NonCancellable) {...}` с использованием функции
[`withContext`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-context.html)
и контекста [`NonCancellable`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-non-cancellable/index.html),
как показано в следующем примере:

```kotlin
val job = launch {
    try {
        repeat(1000) { i ->
            println("job: I'm sleeping $i ...")
            delay(500L)
        }
    } finally {
        withContext(NonCancellable) {
            println("job: I'm running finally")
            delay(1000L)
            println("job: And I've just delayed for 1 sec because I'm non-cancellable")
        }
    }
}
delay(1300L) // delay a bit
println("main: I'm tired of waiting!")
job.cancelAndJoin() // cancels the job and waits for its completion
println("main: Now I can quit.")

```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-cancel-05.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-cancel-05.kt).

<a name="timeout"></a>

<!-- ## Timeout -->
## Тайм-аут

<!-- The most obvious practical reason to cancel execution of a coroutine 
is because its execution time has exceeded some timeout.
While you can manually track the reference to the corresponding [Job] and launch a separate coroutine to cancel 
the tracked one after delay, there is a ready to use [withTimeout] function that does it.
Look at the following example: -->
Самая очевидная практическая причина отменить выполнение корутины - время её выполнения превысило некоторый тайм-аут.
Хотя можно вручную отслеживать обращение к соответствующему [`Job`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html)
и запускать отдельную корутину для отмены отслеживаемой после тайм-аута, есть готовая к использованию функция
[`withTimeout`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-timeout.html),
которая делает это. Посмотрите на следующий пример:

```kotlin
withTimeout(1300L) {
    repeat(1000) { i ->
        println("I'm sleeping $i ...")
        delay(500L)
    }
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-cancel-06.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-cancel-06.kt).

<!-- It produces the following output: -->
Этот код выведет следующее:

```text
I'm sleeping 0 ...
I'm sleeping 1 ...
I'm sleeping 2 ...
Exception in thread "main" kotlinx.coroutines.TimeoutCancellationException: Timed out waiting for 1300 ms
```

<!-- The `TimeoutCancellationException` that is thrown by [withTimeout] is a subclass of [CancellationException].
We have not seen its stack trace printed on the console before. That is because
inside a cancelled coroutine `CancellationException` is considered to be a normal reason for coroutine completion. 
However, in this example we have used `withTimeout` right inside the `main` function.  -->
Исключение `TimeoutCancellationException`, создаваемое [`withTimeout`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-timeout.html),
является подклассом [`CancellationException`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-cancellation-exception/index.html).
Мы никогда раньше не видели его трассировку стека, напечатанную на консоли. Это потому, что внутри отмененной корутины
`CancellationException` считается нормальной причиной её завершения. Однако в этом примере мы использовали `withTimeout`
прямо внутри функции `main`.

<!-- Since cancellation is just an exception, all resources are closed in the usual way. 
You can wrap the code with timeout in a `try {...} catch (e: TimeoutCancellationException) {...}` block if 
you need to do some additional action specifically on any kind of timeout or use the [withTimeoutOrNull] function
that is similar to [withTimeout] but returns `null` on timeout instead of throwing an exception: -->
Поскольку отмена является лишь исключением, все ресурсы закрываются в обычном порядке. Вы можете обернуть код с
тайм-аутом в блоке `try {...} catch (e: TimeoutCancellationException) {...}`, если вам нужно сделать какое-то
дополнительное действие специально для любого тайм-аута или использовать функцию [`withTimeoutOrNull`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-timeout-or-null.html).
Она похожа на [`withTimeout`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-timeout.html),
но возвращает `null` по тайм-ауту вместо создания исключения.

```kotlin
val result = withTimeoutOrNull(1300L) {
    repeat(1000) { i ->
        println("I'm sleeping $i ...")
        delay(500L)
    }
    "Done" // will get cancelled before it produces this result
}
println("Result is $result")
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-cancel-07.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-cancel-07.kt).

<!-- There is no longer an exception when running this code: -->
Теперь ошибки при выполнении этой корутины не будет:

```text
I'm sleeping 0 ...
I'm sleeping 1 ...
I'm sleeping 2 ...
Result is null
```

<a name="asynchronous-timeout-and-resources"></a>

<!-- ## Asynchronous timeout and resources -->
## Асинхронный тайм-аут и ресурсы

<!-- The timeout event in [withTimeout] is asynchronous with respect to the code running in its block and may happen at any time,
even right before the return from inside of the timeout block. Keep this in mind if you open or acquire some
resource inside the block that needs closing or release outside of the block. -->
Событие тайм-аута в [`withTimeout`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-timeout.html)
является асинхронным по отношению к коду, работающему в его блоке, и может произойти в любое время, даже прямо перед
возвратом из блока тайм-аута. Имейте это в виду, если вы открываете или приобретаете ресурс внутри блока, который
необходимо закрыть или освободить за пределами блока.

<!-- For example, here we imitate a closeable resource with the `Resource` class, that simply keeps track of how many times 
it was created by incrementing the `acquired` counter and decrementing this counter from its `close` function.
Let us run a lot of coroutines with the small timeout try acquire this resource from inside
of the `withTimeout` block after a bit of delay and release it from outside. -->
Например, здесь мы имитируем закрываемый ресурс с помощью класса `Resource`, который просто отслеживает, сколько раз он
был создан путем увеличения счетчика `acquired` и уменьшения этого счетчика из его функции `close`. Давайте запустим
много корутин с небольшим таймаутом, попробуем получить этот ресурс изнутри блока `withTimeout` после небольшой задержки
и освободить его извне.

```kotlin
var acquired = 0

class Resource {
    init { acquired++ } // Acquire the resource
    fun close() { acquired-- } // Release the resource
}

fun main() {
    runBlocking {
        repeat(100_000) { // Launch 100K coroutines
            launch { 
                val resource = withTimeout(60) { // Timeout of 60 ms
                    delay(50) // Delay for 50 ms
                    Resource() // Acquire a resource and return it from withTimeout block     
                }
                resource.close() // Release the resource
            }
        }
    }
    // Outside of runBlocking all coroutines have completed
    println(acquired) // Print the number of resources still acquired
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-cancel-08.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-cancel-08.kt).

<!-- If you run the above code you'll see that it does not always print zero, though it may depend on the timings 
of your machine you may need to tweak timeouts in this example to actually see non-zero values. -->
Если вы запустите приведенный выше код, вы увидите, что он не всегда выводит ноль, хотя это может зависеть от таймингов
вашей машины, вам может потребоваться настроить тайм-ауты в этом примере, чтобы действительно увидеть ненулевые значения.

<!-- > Note, that incrementing and decrementing `acquired` counter here from 100K coroutines is completely safe,
> since it always happens from the same main thread. More on that will be explained in the chapter
> on coroutine context. -->
> Обратите внимание, что увеличение и уменьшение счетчика `acquired` здесь из 100 000 корутин совершенно безопасно, так
> как это всегда происходит из одного и того же основного потока. Подробнее об этом будет рассказано в главе о контексте
> корутин.  

<!-- To workaround this problem you can store a reference to the resource in the variable as opposed to returning it 
from the `withTimeout` block. -->
Чтобы обойти эту проблему, вы можете сохранить ссылку на ресурс в переменной, а не возвращать ее из блока `withTimeout`.

```kotlin
runBlocking {
    repeat(100_000) { // Launch 100K coroutines
        launch { 
            var resource: Resource? = null // Not acquired yet
            try {
                withTimeout(60) { // Timeout of 60 ms
                    delay(50) // Delay for 50 ms
                    resource = Resource() // Store a resource to the variable if acquired      
                }
                // We can do something else with the resource here
            } finally {  
                resource?.close() // Release the resource if it was acquired
            }
        }
    }
}
// Outside of runBlocking all coroutines have completed
println(acquired) // Print the number of resources still acquired
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-cancel-09.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-cancel-09.kt).

<!-- This example always prints zero. Resources do not leak. -->
Этот пример всегда выводит 0, а ресурсы не утекают.
