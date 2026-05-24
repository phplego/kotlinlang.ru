---
type: doc
layout: reference
title: "Отмена корутин и тайм-ауты"
url: https://kotlinlang.ru/docs/cancellation-and-timeouts.html
---

<!-- При переводе статьи оригинальная версия была актуализирована по официальной документации от 29 January 2026 -->

<!-- # Cancellation and timeouts -->
# Отмена корутин и тайм-ауты

<!-- Cancellation lets you stop a coroutine before it completes.
It stops work that's no longer needed, such as when a user closes a window or navigates away in a user interface while a coroutine is still running.
You can also use it to release resources early and to stop a coroutine from accessing objects past their disposal. -->
Отмена позволяет остановить корутину до её завершения. Она прекращает работу, которая больше не нужна: например, когда
пользователь закрывает окно или переходит на другой экран пользовательского интерфейса, пока корутина всё ещё выполняется.
Отмену также можно использовать, чтобы раньше освобождать ресурсы и не позволять корутине обращаться к объектам после их
освобождения.

<!-- You can use cancellation to stop long-running coroutines that keep producing values even after other coroutines no longer need them, for example, in pipelines. -->
> Отмену можно использовать, чтобы останавливать долгоживущие корутины, которые продолжают производить значения даже
> после того, как другим корутинам эти значения больше не нужны, например в [конвейерах](https://kotlinlang.org/docs/channels.html#pipelines).

<!-- Cancellation works through the Job handle, which represents the lifecycle of a coroutine and its parent-child relationships.
Job allows you to check whether the coroutine is active and allows you to cancel it, along with its children, as defined by structured concurrency. -->
Отмена работает через дескриптор [`Job`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/),
который представляет жизненный цикл корутины и её отношения с родительскими и дочерними корутинами. `Job` позволяет
проверять, активна ли корутина, и отменять её вместе с дочерними корутинами в соответствии со
[структурированным параллелизмом](coroutines-basics.html#coroutine-scope-and-structured-concurrency).

<a name="cancel-coroutines"></a>
<a name="cancelling-coroutine-execution"></a>

<!-- ## Cancel coroutines -->
## Отмена корутин

<!-- A coroutine is canceled when the cancel() function is invoked on its Job handle.
Coroutine builder functions such as .launch() return a Job. The .async() function returns a Deferred, which
implements Job and supports the same cancellation behavior. -->
Корутина отменяется, когда для её дескриптора `Job` вызывается функция
[`cancel()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/cancel.html).
[Функции-конструкторы корутин](coroutines-basics.html#coroutine-builder-functions), такие как
[`.launch()`](coroutines-basics.html#coroutinescope-launch), возвращают `Job`. Функция
[`.async()`](coroutines-basics.html#coroutinescope-async) возвращает
[`Deferred`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-deferred/),
который реализует `Job` и поддерживает такое же поведение при отмене.

<!-- You can call the cancel() function manually, or it can be invoked automatically through cancellation propagation when a parent coroutine is canceled. -->
Функцию `cancel()` можно вызвать вручную. Она также может быть вызвана автоматически через распространение отмены, когда
отменяется родительская корутина.

<!-- When a coroutine is canceled, it throws a CancellationException the next time it checks for cancellation.
For more information about how and when this happens, see Suspension points and cancellation. -->
Когда корутина отменяется, при следующей проверке отмены она выбрасывает
[`CancellationException`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-cancellation-exception/).
Подробнее о том, как и когда это происходит, читайте в разделе [Точки приостановки и отмена](#suspension-points-and-cancellation).

<!-- You can use the awaitCancellation() function to suspend a coroutine until it's canceled. -->
> Функцию [`awaitCancellation()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/await-cancellation.html)
> можно использовать, чтобы приостановить корутину до её отмены.

<!-- Here's an example on how to manually cancel coroutines: -->
Вот пример ручной отмены корутин:

```kotlin
import kotlinx.coroutines.*
import kotlin.time.Duration

//sampleStart
suspend fun main() {
    withContext(Dispatchers.Default) {
        // Used as a signal that the coroutine has started running
        val job1Started = CompletableDeferred<Unit>()

        val job1: Job = launch {

            println("The coroutine has started")

            // Completes the CompletableDeferred,
            // signaling that the coroutine has started running
            job1Started.complete(Unit)
            try {
                // Suspends indefinitely
                // Without cancellation, this call would never return
                delay(Duration.INFINITE)
            } catch (e: CancellationException) {
                println("The coroutine was canceled: $e")

                // Always rethrow cancellation exceptions!
                throw e
            }
            println("This line will never be executed")
        }

        // Waits for job1 to start before canceling it
        job1Started.await()

        // Cancels the coroutine, so delay() throws a CancellationException
        job1.cancel()

        // async returns a Deferred handle, which inherits from Job
        val job2 = async {
            // If the coroutine is canceled before its body starts executing,
            // this line may not be printed
            println("The second coroutine has started")

            try {
                // Equivalent to delay(Duration.INFINITE)
                // Suspends until this coroutine is canceled
                awaitCancellation()

            } catch (e: CancellationException) {
                println("The second coroutine was canceled")
                throw e
            }
        }
        job2.cancel()
    }
    // Coroutine builders such as withContext() or coroutineScope()
    // wait for all child coroutines to complete,
    // even when the children are canceled
    println("All coroutines have completed")
}
//sampleEnd
```

<!-- In this example, CompletableDeferred is used as a signal that the coroutine has started running.
The coroutine calls complete() when it starts executing, and await() only returns once that CompletableDeferred is completed. This way, cancellation happens only after the coroutine has started running.
The coroutine created by .async() doesn't have this check, so it may be canceled before it can run the code inside its block. -->
В этом примере [`CompletableDeferred`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-completable-deferred/)
используется как сигнал о том, что корутина начала выполняться. Корутина вызывает `complete()` при старте, а `await()`
возвращает результат только после завершения этого `CompletableDeferred`. Благодаря этому отмена происходит только после
того, как корутина начала выполняться. У корутины, созданной через `.async()`, такой проверки нет, поэтому её можно
отменить до того, как код внутри её блока начнёт выполняться.

<!-- Catching CancellationException can break the cancellation propagation.
If you must catch it, rethrow it to let the cancellation propagate correctly through the coroutine hierarchy.

For more information, see Coroutine exceptions handling. -->
> ***Внимание.*** Перехват `CancellationException` может нарушить распространение отмены. Если вам необходимо его
> перехватить, выбросьте его повторно, чтобы отмена корректно распространилась по иерархии корутин.
>
> Подробнее см. в разделе [Обработка исключений в корутинах](https://kotlinlang.org/docs/exception-handling.html#cancellation-and-exceptions).

<a name="cancellation-propagation"></a>

<!-- ### Cancellation propagation -->
### Распространение отмены

<!-- Structured concurrency ensures that canceling a coroutine also cancels all of its children.
This prevents child coroutines from working after the parent has already stopped. -->
[Структурированный параллелизм](coroutines-basics.html#coroutine-scope-and-structured-concurrency) гарантирует, что
отмена корутины также отменяет все её дочерние корутины. Это не позволяет дочерним корутинам продолжать работу после
остановки родительской корутины.

<!-- Here's an example: -->
Например:

```kotlin
import kotlinx.coroutines.*
import kotlin.time.Duration

//sampleStart
suspend fun main() {
    withContext(Dispatchers.Default) {
        // Used as a signal that the child coroutines have been launched
        val childrenLaunched = CompletableDeferred<Unit>()

        // Launches two child coroutines
        val parentJob = launch {
            launch {
                println("Child coroutine 1 has started running")
                try {
                    awaitCancellation()
                } finally {
                    println("Child coroutine 1 has been canceled")
                }
            }
            launch {
                println("Child coroutine 2 has started running")
                try {
                    awaitCancellation()
                } finally {
                    println("Child coroutine 2 has been canceled")
                }
            }
            // Completes the CompletableDeferred,
            // signaling that the child coroutines have been launched
            childrenLaunched.complete(Unit)
        }
        // Waits for the parent coroutine to signal that it has launched
        // all of its children
        childrenLaunched.await()

        // Cancels the parent coroutine, which cancels all its children
        parentJob.cancel()
    }
}
//sampleEnd
```

<!-- In this example, each child coroutine uses a finally block, so the code inside it runs when the coroutine is canceled.
Here, CompletableDeferred signals that the child coroutines are launched before they are canceled, but it doesn't guarantee that they start running. If they are canceled first, nothing is printed. -->
В этом примере каждая дочерняя корутина использует блок
[`finally`](exceptions.html#the-finally-block), поэтому код внутри него выполняется при отмене корутины. Здесь
`CompletableDeferred` сигнализирует, что дочерние корутины запущены до отмены, но не гарантирует, что они успели начать
выполняться. Если их отменить первыми, на экран ничего не будет выведено.

<a name="cancellation-is-cooperative"></a>

<!-- ## Make coroutines react to cancellation -->
## Как заставить корутины реагировать на отмену

<!-- In Kotlin, coroutine cancellation is cooperative.
This means that coroutines only react to cancellation when they cooperate by suspending or checking for cancellation explicitly. -->
В Kotlin отмена корутин _кооперативна_. Это значит, что корутины реагируют на отмену только тогда, когда взаимодействуют
с механизмом отмены: [приостанавливаются](#suspension-points-and-cancellation) или
[явно проверяют отмену](#check-for-cancellation-explicitly).

<!-- In this section, you can learn how to create cancelable coroutines. -->
В этом разделе показано, как создавать отменяемые корутины.

<a name="suspension-points-and-cancellation"></a>

<!-- ### Suspension points and cancellation -->
### Точки приостановки и отмена

<!-- When a coroutine is canceled, it continues running until it reaches a point in the code where it may suspend, also known as a suspension point.
If the coroutine suspends there, the suspending function checks whether it has been canceled.
If it has, the coroutine stops and throws CancellationException. -->
Когда корутина отменяется, она продолжает выполняться, пока не достигнет места в коде, где может приостановиться. Такое
место называется _точкой приостановки_. Если корутина приостанавливается в этой точке, suspend-функция проверяет, была
ли корутина отменена. Если была, корутина останавливается и выбрасывает `CancellationException`.

<!-- A call to a suspend function is a suspension point, but it doesn't always suspend.
For example, when awaiting a Deferred result, the coroutine only suspends if that Deferred isn't completed yet. -->
Вызов `suspend`-функции является точкой приостановки, но он не всегда действительно приостанавливает выполнение. Например,
при ожидании результата `Deferred` корутина приостанавливается только в том случае, если этот `Deferred` ещё не завершён.

<!-- Here's an example using common suspending functions that suspend, enabling the coroutine to check and stop when it's canceled: -->
Ниже приведён пример с обычными suspend-функциями, которые приостанавливают выполнение, позволяя корутине проверить
отмену и остановиться:

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.sync.Mutex
import kotlinx.coroutines.channels.Channel
import kotlin.time.Duration.Companion.milliseconds
import kotlin.time.Duration

suspend fun main() {
    withContext(Dispatchers.Default) {
        val childJobs = listOf(
            launch {
                // Suspends until canceled
                awaitCancellation()
            },
            launch {
                // Suspends until canceled
                delay(Duration.INFINITE)
            },
            launch {
                val channel = Channel<Int>()
                // Suspends while waiting for a value that is never sent
                channel.receive()
            },
            launch {
                val deferred = CompletableDeferred<Int>()
                // Suspends while waiting for a value that is never completed
                deferred.await()
            },
            launch {
                val mutex = Mutex(locked = true)
                // Suspends while waiting for a mutex that remains locked indefinitely
                mutex.lock()
            }
        )

        // Gives the child coroutines time to start and suspend
        delay(100.milliseconds)

        // Cancels all child coroutines
        childJobs.forEach { it.cancel() }
    }
    println("All child jobs completed!")
}
```

<!-- All suspending functions in the kotlinx.coroutines library cooperate with cancellation because they use suspendCancellableCoroutine() internally, which checks for cancellation when the coroutine suspends.
In contrast, custom suspending functions that use suspendCoroutine() don't react to cancellation. -->
> Все suspend-функции из библиотеки `kotlinx.coroutines` взаимодействуют с отменой, потому что внутри используют
> [`suspendCancellableCoroutine()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/suspend-cancellable-coroutine.html),
> которая проверяет отмену при приостановке корутины. В отличие от них, пользовательские suspend-функции, использующие
> [`suspendCoroutine()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.coroutines/suspend-coroutine.html), не
> реагируют на отмену.

<a name="check-for-cancellation-explicitly"></a>
<a name="making-computation-code-cancellable"></a>

<!-- ### Check for cancellation explicitly -->
### Явная проверка отмены

<!-- If a coroutine doesn't suspend for a long time, it doesn't stop when it's canceled unless it explicitly checks for cancellation. -->
Если корутина долго не [приостанавливается](#suspension-points-and-cancellation), она не остановится при отмене, пока явно
не проверит, была ли отменена.

<!-- To check for cancellation, use the following APIs: -->
Для проверки отмены используйте следующие API:

* Свойство [`isActive`](#isactive) равно `false`, когда корутина отменена.
* Функция [`ensureActive()`](#ensureactive) немедленно выбрасывает `CancellationException`, если корутина отменена.
* Функция [`yield()`](#yield) приостанавливает корутину, освобождая поток и давая другим корутинам шанс выполниться на
  нём. Приостановка позволяет корутине проверить отмену и выбросить `CancellationException`, если корутина отменена.

<!-- These APIs are useful when your coroutines run for a long time between suspension points or are unlikely to suspend at suspension points. -->
Эти API полезны, когда корутины долго работают между точками приостановки или, вероятно, не будут приостанавливаться в
этих точках.

<a name="isactive"></a>

<!-- #### isActive -->
#### isActive

<!-- Use the isActive property in long-running computations to periodically check for cancellation.
This property is false when the coroutine is no longer active, which you can use to gracefully stop the coroutine when it no longer needs to continue the operation: -->
Используйте свойство
[`isActive`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/is-active.html)
в долгих вычислениях, чтобы периодически проверять отмену. Это свойство равно `false`, когда корутина больше не активна,
и его можно использовать, чтобы аккуратно остановить корутину, когда ей больше не нужно продолжать операцию.

<!-- Here's an example: -->
Например:

```kotlin
import kotlinx.coroutines.*
import kotlin.time.Duration.Companion.milliseconds
import kotlin.random.Random

//sampleStart
suspend fun main() {
    withContext(Dispatchers.Default) {
        val unsortedList = MutableList(10) { Random.nextInt() }

        // Starts a long-running computation
        val listSortingJob = launch {
            var i = 0

            // Repeatedly sorts the list while the coroutine remains active
            while (isActive) {
                unsortedList.sort()
                ++i
            }
            println(
                "Stopped sorting the list after $i iterations"
            )
        }
        // Sorts the list for 100 milliseconds, then considers it sorted enough
        delay(100.milliseconds)

        // Cancels the sorting when the result is good enough
        listSortingJob.cancel()

        // Waits until the sorting coroutine finishes
        // before accessing the shared list to avoid data races
        listSortingJob.join()
        println("The list is probably sorted: $unsortedList")
    }
}
//sampleEnd
```

<!-- In this example, the join() function suspends the coroutine until it finishes. This ensures that the list isn't accessed while the sorting coroutine is still running. -->
В этом примере функция
[`join()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/join.html)
приостанавливает корутину до завершения другой корутины. Это гарантирует, что к списку не будет доступа, пока корутина
сортировки ещё выполняется.

<!-- You can use the cancelAndJoin() function to cancel a coroutine and wait for it to finish in a single call. -->
> Функцию
> [`cancelAndJoin()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/cancel-and-join.html)
> можно использовать, чтобы отменить корутину и дождаться её завершения одним вызовом.

<a name="ensureactive"></a>

<!-- #### ensureActive() -->
#### ensureActive()

<!-- Use the ensureActive() function to check for cancellation and stop the current computation by throwing CancellationException if the coroutine is canceled: -->
Используйте функцию
[`ensureActive()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/ensure-active.html),
чтобы проверить отмену и остановить текущее вычисление, выбросив `CancellationException`, если корутина отменена:

```kotlin
import kotlinx.coroutines.*
import kotlin.time.Duration.Companion.milliseconds

suspend fun main() {
    withContext(Dispatchers.Default) {
        val childJob = launch {
            var start = 0
            try {
                while (true) {
                    ++start
                    // Checks the Collatz conjecture for the current number
                    var n = start
                    while (n != 1) {
                        // Throws CancellationException if the coroutine is canceled
                        ensureActive()
                        n = if (n % 2 == 0) n / 2 else 3 * n + 1
                    }
                }
            } finally {
                println("Checked the Collatz conjecture for 0..${start-1}")
            }
        }
        // Runs the computation for one second
        delay(100.milliseconds)

        // Cancels the coroutine
        childJob.cancel()
    }
}
```

<a name="yield"></a>

<!-- #### yield() -->
#### yield()

<!-- The yield() function suspends the coroutine and checks for cancellation before resuming.
Without suspending, coroutines on the same thread run sequentially. -->
Функция [`yield()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/yield.html)
приостанавливает корутину и проверяет отмену перед возобновлением. Без приостановки корутины в одном потоке выполняются
последовательно.

<!-- Use yield to allow other coroutines to run on the same thread or thread pool before one of them finishes: -->
Используйте `yield`, чтобы дать другим корутинам выполниться в том же потоке или пуле потоков до того, как одна из них
завершится:

```kotlin
import kotlinx.coroutines.*

//sampleStart
fun main() {
    // runBlocking uses the current thread for running all coroutines
    runBlocking {
        val coroutineCount = 5
        repeat(coroutineCount) { coroutineIndex ->
            launch {
                val id = coroutineIndex + 1
                repeat(5) { iterationIndex ->
                    val iteration = iterationIndex + 1
                    // Temporarily suspends to give other coroutines a chance to run
                    // Without this, the coroutines run sequentially
                    yield()
                    // Prints the coroutine index and iteration index
                    println("$id * $iteration = ${id * iteration}")
                }
            }
        }
    }
}
//sampleEnd
```

<!-- In this example, each coroutine uses yield() to let other coroutines run between iterations. -->
В этом примере каждая корутина использует `yield()`, чтобы дать другим корутинам выполниться между итерациями.

<a name="interrupt-blocking-code-when-coroutines-are-canceled"></a>

<!-- ### Interrupt blocking code when coroutines are canceled -->
### Прерывание блокирующего кода при отмене корутин

<!-- On the JVM, some functions, such as Thread.sleep() or BlockingQueue.take(), can block the current thread.
These blocking functions can be interrupted, which stops them prematurely.
However, when you call them from a coroutine, cancellation doesn't interrupt the thread. -->
На JVM некоторые функции, например `Thread.sleep()` или `BlockingQueue.take()`, могут блокировать текущий поток. Такие
блокирующие функции можно прервать, из-за чего они завершатся раньше. Однако если вызвать их из корутины, отмена не
прервёт поток.

<!-- To interrupt the thread when canceling a coroutine, use the runInterruptible() function: -->
Чтобы при отмене корутины прервать поток, используйте функцию
[`runInterruptible()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-interruptible.html):

```kotlin
import kotlinx.coroutines.*

//sampleStart
suspend fun main() {
    withContext(Dispatchers.Default) {
        val childStarted = CompletableDeferred<Unit>()
        val childJob = launch {
            try {
                // Cancellation triggers a thread interruption
                runInterruptible {
                    childStarted.complete(Unit)
                    try {
                        // Blocks the current thread for a very long time
                        Thread.sleep(Long.MAX_VALUE)
                    } catch (e: InterruptedException) {
                        println("Thread interrupted (Java): $e")
                        throw e
                    }
                }
            } catch (e: CancellationException) {
                println("Coroutine canceled (Kotlin): $e")
                throw e
            }
        }
        childStarted.await()

        // Cancels the coroutine and interrupts the thread
        // by running Thread.sleep()
        childJob.cancel()
    }
}
//sampleEnd
```

<a name="handle-values-safely-when-canceling-coroutines"></a>
<a name="closing-resources-with-finally"></a>

<!-- ## Handle values safely when canceling coroutines -->
## Безопасная обработка значений при отмене корутин

<!-- When a suspended coroutine is canceled, it resumes with a CancellationException instead of returning any values, even if those values are already available.
This behavior is called prompt cancellation.
It prevents your code from continuing in a canceled coroutine's scope, such as updating a screen that's already closed. -->
Когда приостановленная корутина отменяется, она возобновляется с `CancellationException`, а не возвращает значения, даже
если эти значения уже доступны. Такое поведение называется _немедленной отменой_ (prompt cancellation). Оно не даёт коду
продолжить выполнение в области видимости отменённой корутины, например обновить экран, который уже закрыт.

<!-- Here's an example: -->
Например:

```kotlin
import java.nio.file.*
import java.nio.charset.*
import kotlinx.coroutines.*
import java.io.*

// Defines a coroutine scope that uses the UI thread
class ScreenWithFileContents(private val scope: CoroutineScope) {
    fun displayFile(path: Path) {
        scope.launch {
            val contents = withContext(Dispatchers.IO) {
                Files.newBufferedReader(
                    path, Charset.forName("US-ASCII")
                ).use {
                    it.readLines()
                }
            }
            // It's safe to call updateUi here,
            // In case of cancellation, withContext() wouldn't return any values
            updateUi(contents)
        }
    }

    // Throws an exception if called after the user left the screen
    private fun updateUi(contents: List<String>) {
      contents.forEach { line -> addOneLineToUi(line) }
    }

    private fun addOneLineToUi(line: String) {
        // Placeholder for code that adds one line to the UI
    }

    // Only callable from the UI thread
    fun leaveScreen() {
        // Cancels the scope when leaving the screen
        // You can no longer update the UI
        scope.cancel()
    }
}
```

<!-- In this example, withContext(Dispatchers.IO) cooperates with cancellation and prevents updateUI() from running if the leaveScreen() function cancels the coroutine before it returns the contents of the file. -->
В этом примере `withContext(Dispatchers.IO)` взаимодействует с отменой и не даёт `updateUi()` выполниться, если функция
`leaveScreen()` отменит корутину до того, как та вернёт содержимое файла.

<!-- While prompt cancellation prevents using values after they are no longer valid, it can also stop your code while an important value is still in use, which might lead to losing that value.
This can happen when a coroutine receives a value, such as an AutoCloseable resource, but is canceled before it can reach the part of the code that closes it.
To prevent this, keep cleanup logic in a place that's guaranteed to run even when the coroutine receiving the value is canceled. -->
Хотя немедленная отмена предотвращает использование значений после того, как они стали недействительными, она также может
остановить код, пока важное значение всё ещё используется, что может привести к потере этого значения. Такое возможно,
когда корутина получает значение, например ресурс `AutoCloseable`, но отменяется до того, как доходит до участка кода,
который его закрывает. Чтобы этого избежать, размещайте логику очистки там, где она гарантированно выполнится, даже если
корутина, получившая значение, будет отменена.

<!-- Here's an example: -->
Например:

```kotlin
import java.nio.file.*
import java.nio.charset.*
import kotlinx.coroutines.*
import java.io.*

// scope is a coroutine scope using the UI thread
class ScreenWithFileContents(private val scope: CoroutineScope) {
    fun displayFile(path: Path) {
        scope.launch {
            // Stores the reader in a variable, so the finally block can close it
            var reader: BufferedReader? = null

            try {
                withContext(Dispatchers.IO) {
                    reader = Files.newBufferedReader(
                        path, Charset.forName("US-ASCII")
                    )
                }
                // Uses the stored reader after withContext() completes
                updateUi(reader!!)
            } finally {
                // Ensures the reader is closed even when the coroutine is canceled
                reader?.close()
            }
        }
    }

    private suspend fun updateUi(reader: BufferedReader) {
        // Shows the file contents
        while (true) {
            val line = withContext(Dispatchers.IO) {
                reader.readLine()
            }
            if (line == null)
                break
            addOneLineToUi(line)
        }
    }

    private fun addOneLineToUi(line: String) {
        // Placeholder for code that adds one line to the UI
    }

    // Only callable from the UI thread
    fun leaveScreen() {
        // Cancels the scope when leaving the screen
        // You can no longer update the UI
        scope.cancel()
    }
}
```

<!-- In this example, storing the BufferedReader in a variable and closing it in the finally block ensures the resource is released even if the coroutine is canceled. -->
В этом примере сохранение `BufferedReader` в переменной и его закрытие в блоке `finally` гарантируют освобождение
ресурса, даже если корутина будет отменена.

<a name="run-non-cancelable-blocks"></a>
<a name="run-non-cancellable-block"></a>

<!-- ### Run non-cancelable blocks -->
### Запуск неотменяемых блоков

<!-- You can prevent cancellation from affecting certain parts of a coroutine.
To do so, pass NonCancellable as an argument to the withContext() coroutine builder function. -->
Можно запретить отмене влиять на отдельные части корутины. Для этого передайте
[`NonCancellable`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-non-cancellable/)
как аргумент функции-конструктору корутины `withContext()`.

<!-- Avoid using NonCancellable with other coroutine builders like .launch() or .async(). Doing so disrupts structured concurrency by breaking the parent-child relationship. -->
> ***Внимание.*** Не используйте `NonCancellable` с другими конструкторами корутин, такими как `.launch()` или
> `.async()`. Это нарушает структурированный параллелизм, разрывая отношение между родительской и дочерней корутинами.

<!-- NonCancellable is useful when you need to ensure that certain operations, such as closing resources with a suspending close() function, complete even if the coroutine is canceled before they finish. -->
`NonCancellable` полезен, когда нужно гарантировать, что некоторые операции, например закрытие ресурсов при помощи
suspend-функции `close()`, завершатся даже в случае отмены корутины до окончания этих операций.

<!-- Here's an example: -->
Например:

```kotlin
import kotlinx.coroutines.*
import kotlin.time.Duration.Companion.milliseconds

//sampleStart
val serviceStarted = CompletableDeferred<Unit>()

fun startService() {
    println("Starting the service...")
    serviceStarted.complete(Unit)
}

suspend fun shutdownServiceAndWait() {
    println("Shutting down...")
    delay(100.milliseconds)
    println("Successfully shut down!")
}

suspend fun main() {
    withContext(Dispatchers.Default) {
        val childJob = launch {
            startService()
            try {
                awaitCancellation()
            } finally {
                withContext(NonCancellable) {
                    // Without withContext(NonCancellable),
                    // This function doesn't complete because the coroutine is canceled
                    shutdownServiceAndWait()
                }
            }
        }
        serviceStarted.await()
        childJob.cancel()
    }
    println("Exiting the program")
}
//sampleEnd
```

<a name="timeout"></a>

<!-- ## Timeout -->
## Тайм-аут

<!-- Timeouts allow you to automatically cancel a coroutine after a specified duration.
They are useful for stopping operations that take too long, helping to keep your application responsive and avoid blocking threads unnecessarily. -->
Тайм-ауты позволяют автоматически отменять корутину после заданной продолжительности. Они полезны для остановки операций,
которые выполняются слишком долго, помогают сохранять отзывчивость приложения и избегать лишней блокировки потоков.

<!-- To specify a timeout, use the withTimeoutOrNull() function with a Duration: -->
Чтобы задать тайм-аут, используйте функцию
[`withTimeoutOrNull()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-timeout-or-null.html)
с `Duration`:

```kotlin
import kotlinx.coroutines.*
import kotlin.time.Duration.Companion.milliseconds

//sampleStart
suspend fun slowOperation(): Int {
    try {
        delay(300.milliseconds)
        return 5
    } catch (e: CancellationException) {
        println("The slow operation has been canceled: $e")
        throw e
    }
}

suspend fun fastOperation(): Int {
    try {
        delay(15.milliseconds)
        return 14
    } catch (e: CancellationException) {
        println("The fast operation has been canceled: $e")
        throw e
    }
}

suspend fun main() {
    withContext(Dispatchers.Default) {
        val slow = withTimeoutOrNull(100.milliseconds) {
            slowOperation()
        }
        println("The slow operation finished with $slow")
        val fast = withTimeoutOrNull(100.milliseconds) {
            fastOperation()
        }
        println("The fast operation finished with $fast")
    }
}
//sampleEnd
```

<!-- If the timeout exceeds the specified Duration, withTimeoutOrNull() returns null. -->
Если выполнение превышает указанную `Duration`, `withTimeoutOrNull()` возвращает `null`.
