---
type: doc
layout: reference
category: "Syntax"
title: "Основы корутин"
url: https://kotlinlang.ru/docs/coroutines-basics.html
---

<!-- # Coroutine Basics -->
# Основы корутин

<!-- This section covers basic coroutine concepts. -->
В этом разделе рассматриваются основные концепции корутин.

<!-- ## Your first coroutine -->
## Ваша первая корутина

<!-- Run the following code: -->
Запустите следующий код:

```kotlin
import kotlinx.coroutines.*

fun main() {
    GlobalScope.launch { // запуск новой корутины в фоне
        delay(1000L) // неблокирующая задержка на 1 секунду
        println("World!") // вывод результата после задержки
    }
    println("Hello,") // пока корутина проводит вычисления, основной поток продолжает свою работу
    Thread.sleep(2000L) // блокировка основного потока на 2 секунды, чтобы корутина успела произвести вычисления
}
```

<!-- > You can get the full code [here](../kotlinx-coroutines-core/jvm/test/guide/example-basic-01.kt). -->
> Полный код можно посмотреть [здесь](https://github.com/kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-basic-01.kt).

<!-- You will see the following result: -->
Результат выполнения кода будет следующим:

```text
Hello,
World!
```

<!-- Essentially, coroutines are light-weight threads.
They are launched with [launch] _coroutine builder_ in a context of some [CoroutineScope].
Here we are launching a new coroutine in the [GlobalScope], meaning that the lifetime of the new
coroutine is limited only by the lifetime of the whole application.   -->
В сущности, корутины — это легковесные потоки.
Они запускаются с помощью _конструктора корутин_ [launch] в контексте какого-то [CoroutineScope].
Здесь мы запускаем новую корутину в [GlobalScope], что означает, что время жизни новой корутины ограничено только временем жизни всего приложения.

<!-- You can achieve the same result by replacing
`GlobalScope.launch { ... }` with `thread { ... }`, and `delay(...)` with `Thread.sleep(...)`.
Try it (don't forget to import `kotlin.concurrent.thread`). -->
Вы можете получить тот же результат, заменив `GlobalScope.launch {...}` на `thread {...}` или на `delay(...)` с `Thread.sleep (...)`. Попробуйте (не забудьте импортировать `kotlin.concurrent.thread`).

<!-- If you start by replacing `GlobalScope.launch` with `thread`, the compiler produces the following error: -->
Если вы начнете с замены `GlobalScope.launch` на `thread`, то компилятор выдаст следующую ошибку:

```
Error: Kotlin: Suspend functions are only allowed to be called from a coroutine or another suspend function
```

<!-- That is because [delay] is a special _suspending function_ that does not block a thread, but _suspends_ the
coroutine, and it can be only used from a coroutine. -->
Это связано с тем, что [delay] является _функцией приостановки_, которая не блокирует поток, а _приостанавливает_ корутину. Использовать её можно только из корутины.

<!-- ## Bridging blocking and non-blocking worlds -->
## Связываем блокирующий и неблокирующий миры

<!-- The first example mixes _non-blocking_ `delay(...)` and _blocking_ `Thread.sleep(...)` in the same code.
It is easy to lose track of which one is blocking and which one is not.
Let's be explicit about blocking using the [runBlocking] coroutine builder: -->
В первом примере смешаны две функции: _неблокирующая_ `delay(...)` и _блокирующая_ `Thread.sleep(...)`. Легко забыть, какая из них блокирует основной поток, а какая нет. Давайте подробно рассмотрим блокировку с помощью билдера [runBlocking]:

```kotlin
import kotlinx.coroutines.*

fun main() {
    GlobalScope.launch { // запуск новой корутины в фоне
        delay(1000L)
        println("World!")
    }
    println("Hello,") // основной поток продолжает свою работу
    runBlocking {     // но это выражение блокирует основной поток
        delay(2000L)  // на 2 секунды
    }
}
```


<!-- > You can get the full code [here](../kotlinx-coroutines-core/jvm/test/guide/example-basic-02.kt). -->
> Полный код можно посмотреть [здесь](https://github.com/kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-basic-02.kt)


<!-- The result is the same, but this code uses only non-blocking [delay].
The main thread invoking `runBlocking` _blocks_ until the coroutine inside `runBlocking` completes. -->
Результат выполнения кода будет тот же, но здесь используется только неблокирующая [delay]. Основной поток, вызывающий `runBlocking`, _блокируется_ до завершения корутины внутри `runBlocking`.

<!-- This example can be also rewritten in a more idiomatic way, using `runBlocking` to wrap
the execution of the main function: -->
Этот пример можно переписать более идиоматическим способом, используя `runBlocking`, чтобы обернуть выполнение функции `main`:

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> { // запуск основной корутины
    GlobalScope.launch { // запуск новой корутины в фоне
        delay(1000L)
        println("World!")
    }
    println("Hello,") // основная корутина продолжает свою работу
    delay(2000L)      // задержка на 2 секунды
}
```

<!-- > You can get the full code [here](../kotlinx-coroutines-core/jvm/test/guide/example-basic-03.kt). -->
> Полный код можно посмотреть [здесь](https://github.com/kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-basic-03.kt)

<!-- Here `runBlocking<Unit> { ... }` works as an adaptor that is used to start the top-level main coroutine.
We explicitly specify its `Unit` return type, because a well-formed `main` function in Kotlin has to return `Unit`. -->
Здесь `runBlocking<Unit> { ... }` работает как адаптер, который используется для запуска основной корутины верхнего уровня.
Мы явно указываем возвращаемый тип `Unit`, потому что правильно сформированная функция `main` в Kotlin должна возвращать `Unit`.

<!-- This is also a way to write unit tests for suspending functions: -->
Таким же образом можно писать модульные тесты для функций приостановки:

```kotlin
class MyTest {
    @Test
    fun testMySuspendingFunction() = runBlocking<Unit> {
        // здесь мы можем использовать функции приостановки
    }
}
```

<!-- ## Waiting for a job -->
## Job

<!-- Delaying for a time while another coroutine is working is not a good approach. Let's explicitly
wait (in a non-blocking way) until the background [Job] that we have launched is complete: -->
Использование задержки для того, чтобы корутина успела выполнить свою работу - не лучший подход.
Давайте явно подождем (неблокирующим способом), пока [Job], запущенный в фоне, не будет выполнен.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val job = GlobalScope.launch { // запуск новой корутины с сохранением ссылки на нее в Job
        delay(1000L)
        println("World!")
    }
    println("Hello,")
    job.join() // ждем завершения вложенной корутины  
}
```

<!-- > You can get the full code [here](../kotlinx-coroutines-core/jvm/test/guide/example-basic-04.kt). -->
> Полный код можно посмотреть [здесь](https://github.com/kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-basic-04.kt).

<!-- Now the result is still the same, but the code of the main coroutine is not tied to the duration of
the background job in any way. Much better. -->
Результат прежний, но код основной корутины никак не привязан к длительности фонового задания - `job`.

<!-- ## Structured concurrency -->
## Структурированный параллелизм

<!-- There is still something to be desired for practical usage of coroutines.
When we use `GlobalScope.launch`, we create a top-level coroutine. Even though it is light-weight, it still
consumes some memory resources while it runs. If we forget to keep a reference to the newly launched
coroutine, it still runs. What if the code in the coroutine hangs (for example, we erroneously
delay for too long), what if we launched too many coroutines and ran out of memory?
Having to manually keep references to all the launched coroutines and [join][Job.join] them is error-prone. -->
Есть еще пара вещей, которые мы бы хотели получить перед тем, как использовать корутины на практике.
Когда мы используем `GlobalScope.launch`, мы создаем корутину верхнего уровня.
Несмотря на то, что корутина легковесна, она всё равно потребляет некоторые ресурсы во время своей работы.
Если мы забудем сохранить ссылку на только что запущенную корутину, она все равно продолжит работать.
Что делать, если код в корутине зависает (например, мы по ошибке указали слишком большую задержку), что если мы запустили слишком много корутин и исчерпали память?
Необходимость вручную сохранять ссылки на все запущенные корутины и [присоединяться][Job.join] к ним чревата ошибками.

<!-- There is a better solution. We can use structured concurrency in our code.
Instead of launching coroutines in the [GlobalScope], just like we usually do with threads (threads are always global),
we can launch coroutines in the specific scope of the operation we are performing. -->
Есть более хорошее решение. В нашем коде можно использовать структурированный параллелизм. Вместо запуска корутин в [GlobalScope], как мы обычно делаем с потоками (потоки всегда глобальные), мы можем запускать корутины в области видимости выполняемой нами операции.

<!-- In our example, we have a `main` function that is turned into a coroutine using the [runBlocking] coroutine builder.
Every coroutine builder, including `runBlocking`, adds an instance of [CoroutineScope] to the scope of its code block.
We can launch coroutines in this scope without having to `join` them explicitly, because
an outer coroutine (`runBlocking` in our example) does not complete until all the coroutines launched
in its scope complete. Thus, we can make our example simpler: -->
В нашем примере есть функция `main`, которая превращается в корутину с помощью билдера [runBlocking].
Каждый билдер, включая `runBlocking`, добавляет экземпляр [CoroutineScope] к области видимости своего блока с кодом.
Мы можем запускать корутины в этой области видимости без явного использования `join`, потому что внешняя корутина (`runBlocking` в нашем примере) не завершится, пока не будут выполнены все корутины, запущенные в ее области видимости.
Таким образом, мы можем упростить наш пример:

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking { // this: CoroutineScope
    launch { // запуск корутины в области видимости runBlocking
        delay(1000L)
        println("World!")
    }
    println("Hello,")
}
```

<!-- > You can get the full code [here](../kotlinx-coroutines-core/jvm/test/guide/example-basic-05.kt). -->
> Полный код можно посмотреть [здесь](https://github.com/kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-basic-05.kt).

## Scope builder

<!-- In addition to the coroutine scope provided by different builders, it is possible to declare your own scope using the
[coroutineScope][_coroutineScope] builder. It creates a coroutine scope and does not complete until all launched children complete. -->
В дополнение к [CoroutineScope], предоставляемой разными билдерами, можно объявить свою собственную область видимости с помощью билдера [coroutineScope][_coroutineScope]. Он создает область видимости и не завершается, пока не завершатся все запущенные дочерние корутины.

<!-- [runBlocking] and [coroutineScope][_coroutineScope] may look similar because they both wait for their body and all its children to complete.
The main difference is that the [runBlocking] method _blocks_ the current thread for waiting,
while [coroutineScope][_coroutineScope] just suspends, releasing the underlying thread for other usages.
Because of that difference, [runBlocking] is a regular function and [coroutineScope][_coroutineScope] is a suspending function. -->
[runBlocking] и [coroutineScope][_coroutineScope] могут выглядеть одинаково, потому что они обе ждут завершения всех операций внутри своего блока и завершения всех запущенных дочерних корутин.
Основное отличие заключается в том, что метод [runBlocking] блокирует текущий поток, в то время как [coroutineScope][_coroutineScope] лишь приостанавливает работу, высвобождая основной поток для других целей.
Из-за этой разницы [runBlocking] является обычной функцией, а [coroutineScope][_coroutineScope] - функцией приостановки.

<!-- It can be demonstrated by the following example: -->
Следующий пример это демонстрирует:

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking { // this: CoroutineScope
    launch {
        delay(200L)
        println("Task from runBlocking")
    }

    coroutineScope { // Создание coroutine scope
        launch {
            delay(500L)
            println("Task from nested launch")
        }

        delay(100L)
        println("Task from coroutine scope") // Эта строка будет выведена перед вложенным launch
    }

    println("Coroutine scope is over") // Эта строка не будет выведена пока не выполнится вложенный launch
}
```

<!-- > You can get the full code [here](../kotlinx-coroutines-core/jvm/test/guide/example-basic-06.kt). -->
> Полный код можно посмотреть [здесь](https://github.com/kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-basic-06.kt).

<!-- Note that right after the "Task from coroutine scope" message (while waiting for nested launch)
 "Task from runBlocking" is executed and printed — even though the [coroutineScope][_coroutineScope] is not completed yet. -->
 Обратите внимание, что сразу после сообщения "Task from coroutine scope" (во время ожидания выполнения вложенного launch) выполняется и выдаётся "Task from runBlocking", хотя выполнение [coroutineScope][_coroutineScope] еще не завершилось.

<!-- ## Extract function refactoring -->
## Извлечение функции

<!-- Let's extract the block of code inside `launch { ... }` into a separate function. When you
perform "Extract function" refactoring on this code, you get a new function with the `suspend` modifier.
This is your first _suspending function_. Suspending functions can be used inside coroutines
just like regular functions, but their additional feature is that they can, in turn,
use other suspending functions (like `delay` in this example) to _suspend_ execution of a coroutine. -->
Извлечём блок кода внутри `launch { ... }` в отдельную функцию. Когда вы выполните рефакторинг кода внутри `launch { ... }` с помощью пункта "Extract function", то вы получите новую функцию с модификатором `suspend`.
Это ваша первая _функция приостановки_. Функции приостановки могут использоваться внутри корутин, как обычные функции, но их дополнительная особенность заключается в том, что они, в свою очередь, могут использовать другие функции приостановки (например, функцию `delay`) для _приостановки_ выполнения корутины.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    launch { doWorld() }
    println("Hello,")
}

// это ваша первая функция приостановки
suspend fun doWorld() {
    delay(1000L)
    println("World!")
}
```

<!-- > You can get the full code [here](../kotlinx-coroutines-core/jvm/test/guide/example-basic-07.kt). -->
> Полный код можно посмотреть [здесь](https://github.com/kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-basic-07.kt).

<!-- But what if the extracted function contains a coroutine builder which is invoked on the current scope?
In this case, the `suspend` modifier on the extracted function is not enough. Making `doWorld` an extension
method on `CoroutineScope` is one of the solutions, but it may not always be applicable as it does not make the API clearer.
The idiomatic solution is to have either an explicit `CoroutineScope` as a field in a class containing the target function
or an implicit one when the outer class implements `CoroutineScope`.
As a last resort, [CoroutineScope(coroutineContext)][CoroutineScope()] can be used, but such an approach is structurally unsafe
because you no longer have control on the scope of execution of this method. Only private APIs can use this builder. -->
Но что, если извлечённая функция содержит билдер корутины, который вызывается в текущей области видимости?
В этом случае модификатора `suspend` для извлечённой функции недостаточно. Сделать `doWorld` методом расширения на `CoroutineScope` - одно из решений, но оно не всегда может быть применимо, поскольку не делает API более понятным.
Идиоматическое решение состоит в том, чтобы иметь либо явный `CoroutineScope` в качестве поля в классе, содержащем целевую функцию, либо неявный, когда внешний класс реализует `CoroutineScope`.
В крайнем случае можно использовать [CoroutineScope(coroutineContext)][CoroutineScope()], но такой подход структурно небезопасен, потому что вы теряете контроль над выполняемой областью видимости этого метода. Только приватные API могут использовать этот конструктор.

<!-- ## Coroutines ARE light-weight -->
## Легковесные корутины

<!-- Run the following code: -->
Запустите следующий код:

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    repeat(100_000) { // запуск большого количества корутин
        launch {
            delay(5000L)
            print(".")
        }
    }
}
```

<!-- > You can get the full code [here](../kotlinx-coroutines-core/jvm/test/guide/example-basic-08.kt). -->
> Полный код можно посмотреть [здесь](https://kotlinx-coroutines-core/jvm/test/guide/example-basic-08.kt).

<!-- It launches 100K coroutines and, after 5 seconds, each coroutine prints a dot. -->
Данный код запускает 100 тысяч корутин, каждая из которых через 5 секунд печатает точку.

<!-- Now, try that with threads. What would happen? (Most likely your code will produce some sort of out-of-memory error) -->
А теперь попробуйте сделать то же самое с потоками. Что произойдёт? (Скорее всего это вызовет ошибку, связанную с нехваткой памяти).

<!-- ## Global coroutines are like daemon threads -->
## Глобальные корутины похожи на демон-потоки

<!-- The following code launches a long-running coroutine in [GlobalScope] that prints "I'm sleeping" twice a second and then
returns from the main function after some delay: -->
Нижеприведённый код запускает длительную корутину в [GlobalScope], которая два раза в секунду выводит сообщение "I'm sleeping", а затем, после некоторой задержки, происходит возврат из функции `main`:

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    GlobalScope.launch {
        repeat(1000) { i ->
            println("I'm sleeping $i ...")
            delay(500L)
        }
    }
    delay(1300L) // выход после некоторой задержки
}
```

<!-- > You can get the full code [here](../kotlinx-coroutines-core/jvm/test/guide/example-basic-09.kt). -->
> Полный код можно посмотреть [здесь](https://kotlinx-coroutines-core/jvm/test/guide/example-basic-09.kt).

<!-- You can run and see that it prints three lines and terminates: -->
Если вы запустите данный код, то увидите, что он трижды выводит сообщение и завершается:

```text
I'm sleeping 0 ...
I'm sleeping 1 ...
I'm sleeping 2 ...
```

<!-- Active coroutines that were launched in [GlobalScope] do not keep the process alive. They are like daemon threads. -->
Активные корутины, запущенные в [GlobalScope], не поддерживают "жизнь" процесса. В этом они похожи на демон-потоки.


[launch]: https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html
[CoroutineScope]: https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html
[GlobalScope]: https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-global-scope/index.html
[delay]: https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/delay.html
[runBlocking]: https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-blocking.html
[Job]: https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html
[Job.join]: https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/join.html
[_coroutineScope]: https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/coroutine-scope.html
[CoroutineScope()]: https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope.html
