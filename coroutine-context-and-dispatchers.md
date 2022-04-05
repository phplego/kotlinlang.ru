---
type: doc
layout: reference
title: "Контекст корутины и диспетчеры"
url: https://kotlinlang.ru/docs/coroutine-context-and-dispatchers.html
---

<!-- При переводе статьи оригинальная версия была от 26 November 2021 -->

<!-- [//]: # (title: Coroutine context and dispatchers) -->
# Контекст корутины и диспетчеры

<!-- Coroutines always execute in some context represented by a value of the 
[CoroutineContext](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/-coroutine-context/) 
type, defined in the Kotlin standard library. -->
Корутины всегда выполняются в некотором контексте, представленном значением типа [CoroutineContext](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/-coroutine-context/), определенным в стандартной библиотеке Kotlin.

<!-- The coroutine context is a set of various elements. The main elements are the [Job] of the coroutine, 
which we've seen before, and its dispatcher, which is covered in this section. -->
Контекст корутины представляет собой набор различных элементов. Основными элементами являются [Job](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html) корутины, которую мы видели ранее, и ее диспетчер, который рассматривается в этом разделе.


<a name="dispatchers-and-threads"></a>

<!-- ## Dispatchers and threads -->
## Диспетчеры и потоки

<!-- The coroutine context includes a _coroutine dispatcher_ (see [CoroutineDispatcher]) that determines what thread or threads the corresponding coroutine uses for its execution. The coroutine dispatcher can confine coroutine execution 
to a specific thread, dispatch it to a thread pool, or let it run unconfined.  -->
Контекст корутины включает _диспетчер корутины_ (см. [CoroutineDispatcher](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-dispatcher/index.html)), который определяет, какой поток или потоки использует соответствующая корутина для своего выполнения. Диспетчер корутины может ограничить выполнение корутины определенным потоком, отправить ее в пул потоков или позволить ей выполняться без ограничений.

<!-- All coroutine builders like [launch] and [async] accept an optional 
[CoroutineContext](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/-coroutine-context/) 
parameter that can be used to explicitly specify the dispatcher for the new coroutine and other context elements.  -->
Все билдеры корутин, такие как [launch](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html) и [async](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html), принимают необязательный параметр [CoroutineContext](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/-coroutine-context/), который можно использовать для явного указания диспетчера для новой корутины и других элементов контекста.

<!-- Try the following example: -->
Попробуйте следующий пример:

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> {
    launch { // онтекст родителя, основная корутина runBlocking
        println("main runBlocking      : I'm working in thread ${Thread.currentThread().name}")
    }
    launch(Dispatchers.Unconfined) { // не ограничено -- будет работать с основным потоком
        println("Unconfined            : I'm working in thread ${Thread.currentThread().name}")
    }
    launch(Dispatchers.Default) { // будет отправлено в DefaultDispatcher 
        println("Default               : I'm working in thread ${Thread.currentThread().name}")
    }
    launch(newSingleThreadContext("MyOwnThread")) { // получит свой собственный новый поток
        println("newSingleThreadContext: I'm working in thread ${Thread.currentThread().name}")
    }
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-context-01.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-context-01.kt).


<!-- It produces the following output (maybe in different order): -->
Он выдает следующий вывод (возможно, в другом порядке):

```text
Unconfined            : I'm working in thread main
Default               : I'm working in thread DefaultDispatcher-worker-1
newSingleThreadContext: I'm working in thread MyOwnThread
main runBlocking      : I'm working in thread main
```

<!-- When `launch { ... }` is used without parameters, it inherits the context (and thus dispatcher)
from the [CoroutineScope] it is being launched from. In this case, it inherits the
context of the main `runBlocking` coroutine which runs in the `main` thread.  -->
Когда `launch { ... }` используется без параметров, он наследует контекст (и, следовательно, диспетчер) от [CoroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html), из которого он запускается. В этом случае он наследует контекст основной корутины `runBlocking`, которая выполняется в `main` потоке.

<!-- [Dispatchers.Unconfined] is a special dispatcher that also appears to run in the `main` thread, but it is,
in fact, a different mechanism that is explained later. -->
[Dispatchers.Unconfined](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-unconfined.html) — это специальный диспетчер, который также работает в `main` потоке, но на самом деле это другой механизм, который будет объяснен позже.

<!-- The default dispatcher is used when no other dispatcher is explicitly specified in the scope.
It is represented by [Dispatchers.Default] and uses a shared background pool of threads. -->
Диспетчер по умолчанию используется, когда в области действия явно не указан другой диспетчер. Он представлен [Dispatchers.Default](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-default.html) и использует общий фоновый пул потоков.
  
<!-- [newSingleThreadContext] creates a thread for the coroutine to run. A dedicated thread is a very expensive resource. 
In a real application it must be either released, when no longer needed, using the [close][ExecutorCoroutineDispatcher.close] function, or stored in a top-level variable and reused throughout the application.   -->
[newSingleThreadContext] создает поток для запуска корутины. Выделенный поток — очень дорогой ресурс. В реальном приложении он должен быть либо освобожден, когда он больше не нужен, с помощью функции [close](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-executor-coroutine-dispatcher/close.html), либо сохранен в переменной верхнего уровня и повторно использован во всем приложении.

<a name="unconfined-vs-confined-dispatcher"></a>

<!-- ## Unconfined vs confined dispatcher -->
## Неограниченный и ограниченный диспетчер
 
<!-- The [Dispatchers.Unconfined] coroutine dispatcher starts a coroutine in the caller thread, but only until the
first suspension point. After suspension it resumes the coroutine in the thread that is fully determined by the
suspending function that was invoked. The unconfined dispatcher is appropriate for coroutines which neither
consume CPU time nor update any shared data (like UI) confined to a specific thread.  -->
Диспетчер корутин [Dispatchers.Unconfined](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-unconfined.html) запускает корутину в вызывающем потоке, но только до первой точки приостановки. После приостановки он возобновляет корутину в потоке, который полностью определяется вызванной функцией приостановки. Неограниченный диспетчер подходит для корутин, которые не потребляют процессорное время и не обновляют какие-либо общие данные (например, пользовательский интерфейс), ограниченные определенным потоком.

<!-- On the other side, the dispatcher is inherited from the outer [CoroutineScope] by default. 
The default dispatcher for the [runBlocking] coroutine, in particular, is confined to the invoker thread, so inheriting it has the effect of confining execution to this thread with predictable FIFO scheduling. -->
С другой стороны, диспетчер по умолчанию наследуется от внешнего [CoroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html). Диспетчер по умолчанию для корутины [runBlocking](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-blocking.html), в частности, ограничен вызывающим потоком, поэтому его наследование приводит к ограничению выполнения этим потоком с предсказуемым планированием FIFO.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> {
    launch(Dispatchers.Unconfined) { // не ограничено -- будет работать с основным потоком
        println("Unconfined      : I'm working in thread ${Thread.currentThread().name}")
        delay(500)
        println("Unconfined      : After delay in thread ${Thread.currentThread().name}")
    }
    launch { // контекст родителя, основная корутина runBlocking
        println("main runBlocking: I'm working in thread ${Thread.currentThread().name}")
        delay(1000)
        println("main runBlocking: After delay in thread ${Thread.currentThread().name}")
    }
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-context-02.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-context-02.kt).



<!-- Produces the output:  -->
Он выдает следующий вывод:
 
```text
Unconfined      : I'm working in thread main
main runBlocking: I'm working in thread main
Unconfined      : After delay in thread kotlinx.coroutines.DefaultExecutor
main runBlocking: After delay in thread main
```

<!-- So, the coroutine with the context inherited from `runBlocking {...}` continues to execute
in the `main` thread, while the unconfined one resumes in the default executor thread that the [delay]
function is using. -->
Таким образом, корутина с контекстом, унаследованным от `runBlocking {...}`, продолжает выполняться в `main` потоке, а неограниченная возобновляется в потоке исполнителя по умолчанию, который использует функция [delay](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/delay.html).

<!-- > The unconfined dispatcher is an advanced mechanism that can be helpful in certain corner cases where
> dispatching of a coroutine for its execution later is not needed or produces undesirable side-effects,
> because some operation in a coroutine must be performed right away. 
> The unconfined dispatcher should not be used in general code.  -->
> Неограниченный диспетчер — это расширенный механизм, который может быть полезен в некоторых крайних случаях, когда диспетчеризация корутины для ее последующего выполнения не требуется или приводит к нежелательным побочным эффектам, поскольку некоторые операции в корутине должны выполняться сразу. Неограниченный диспетчер не должен использоваться в общем коде.

<a name="debugging-coroutines-and-threads"></a>

<!-- ## Debugging coroutines and threads -->
## Отладка корутин и потоков

<!-- Coroutines can suspend on one thread and resume on another thread. 
Even with a single-threaded dispatcher it might be hard to
figure out what the coroutine was doing, where, and when if you don't have special tooling.  -->
Корутины могут приостанавливаться в одном потоке и возобновляться в другом потоке. Даже с однопоточным диспетчером может быть сложно понять, что делала корутина, где и когда, если у вас нет специального инструментария.

<a name="debugging-with-idea"></a>

<!-- ### Debugging with IDEA -->
### Отладка с помощью IDEA

<!-- The Coroutine Debugger of the Kotlin plugin simplifies debugging coroutines in IntelliJ IDEA. -->
Плагин отдалки корутин для Kotlin упрощает отладку корутин в IntelliJ IDEA.

<!-- > Debugging works for versions 1.3.8 or later of `kotlinx-coroutines-core`. -->
> Отладка работает для версий 1.3.8 и выше `kotlinx-coroutines-core`.

<!-- The **Debug** tool window contains the **Coroutines** tab. In this tab, you can find information about both currently running and suspended coroutines. 
The coroutines are grouped by the dispatcher they are running on. -->

Окно инструмента **Debug** содержит вкладку **Coroutines**. На этой вкладке вы можете найти информацию как о запущенных, так и о приостановленных корутинах. Корутины группируются по диспетчеру, на котором они выполняются.

![Debugging coroutines](https://kotlinlang.org/docs/images/coroutine-idea-debugging-1.png)

<!-- With the coroutine debugger, you can: -->
С отладчиком корутин вы можете:
<!-- * Check the state of each coroutine. -->
* Проверить состояние каждой корутины.
<!-- * See the values of local and captured variables for both running and suspended coroutines. -->
* Смотреть значения локальных и захваченных переменных как для запущенных, так и для приостановленных корутин.
<!-- * See a full coroutine creation stack, as well as a call stack inside the coroutine. The stack includes all frames with variable values, even those that would be lost during standard debugging. -->
* Смотреть полный стек создания корутин, а также стек вызовов внутри корутин. В стек входят все кадры с переменными значениями, даже те, которые были бы потеряны при стандартной отладке.
<!-- * Get a full report that contains the state of each coroutine and its stack. To obtain it, right-click inside the **Coroutines** tab, and then click **Get Coroutines Dump**. -->
* Получить полный отчет, содержащий состояние каждой корутины и ее стека. Чтобы получить его, щелкните правой кнопкой мыши по вкладке **Coroutines** и выберите **Get Coroutines Dump**.

<!-- To start coroutine debugging, you just need to set breakpoints and run the application in debug mode. -->
Чтобы начать отладку корутины, вам просто нужно установить точки останова и запустить приложение в режиме отладки.

<!-- Learn more about coroutines debugging in the [tutorial](https://kotlinlang.org/docs/tutorials/coroutines/debug-coroutines-with-idea.html). -->
Узнайте больше об отладке корутин в [руководстве](https://kotlinlang.org/docs/tutorials/coroutines/debug-coroutines-with-idea.html).

<a name="debugging-using-logging"></a>

<!-- ### Debugging using logging -->
### Отладка с использованием логирования

<!-- Another approach to debugging applications with threads without Coroutine Debugger is to print the thread name in the log file on each log statement. This feature is universally supported by logging frameworks. When using coroutines, the thread name alone does not give much of a context, so `kotlinx.coroutines` includes debugging facilities to make it easier.  -->
Другой подход к отладке приложений с потоками без плагина отладки заключается в печати имени потока в файле журнала для каждого оператора журнала. Эта функция повсеместно поддерживается фреймворками ведения журналов. При использовании корутин само по себе имя потока не дает много контекста, поэтому `kotlinx.coroutines` включает средства отладки, чтобы упростить его.

<!-- Run the following code with `-Dkotlinx.coroutines.debug` JVM option: -->
Запустите следующий код с параметром JVM `-Dkotlinx.coroutines.debug`:

```kotlin
import kotlinx.coroutines.*

fun log(msg: String) = println("[${Thread.currentThread().name}] $msg")

fun main() = runBlocking<Unit> {

    val a = async {
        log("I'm computing a piece of the answer")
        6
    }
    val b = async {
        log("I'm computing another piece of the answer")
        7
    }
    log("The answer is ${a.await() * b.await()}")
 
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-context-03.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-context-03.kt).


<!-- There are three coroutines. The main coroutine (#1) inside `runBlocking` 
and two coroutines computing the deferred values `a` (#2) and `b` (#3).
They are all executing in the context of `runBlocking` and are confined to the main thread. The output of this code is: -->
Есть три корутины. Основная корутины (№1) внутри `runBlocking` и две корутины, вычисляющие отложенные значения `a` (№2) и `b` (№3). Все они выполняются в контексте `runBlocking` и ограничены основным потоком. Вывод этого кода:

```text
[main @coroutine#2] I'm computing a piece of the answer
[main @coroutine#3] I'm computing another piece of the answer
[main @coroutine#1] The answer is 42
```

<!-- The `log` function prints the name of the thread in square brackets, and you can see that it is the `main` thread with the identifier of the currently executing coroutine appended to it. This identifier  is consecutively assigned to all created coroutines when the debugging mode is on. -->
Функция `log` печатает имя потока в квадратных скобках, и вы можете видеть, что это основной поток с добавленным к нему идентификатором выполняемой в данный момент корутины. Этот идентификатор последовательно присваивается всем созданным корутинам при включенном режиме отладки.

<!-- > Debugging mode is also turned on when JVM is run with `-ea` option.
> You can read more about debugging facilities in the documentation of the [DEBUG_PROPERTY_NAME] property. -->
> Режим отладки также включается, когда JVM запускается с параметром `-ea`. Подробнее о средствах отладки можно прочитать в документации по свойству [DEBUG_PROPERTY_NAME](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-d-e-b-u-g_-p-r-o-p-e-r-t-y_-n-a-m-e.html).

<a name="jumping-between-hreads"></a>

<!-- ## Jumping between threads -->
## Перескакивание между потоками

<!-- Run the following code with the `-Dkotlinx.coroutines.debug` JVM option (see [debug](#debugging-coroutines-and-threads)): -->
Запустите следующий код с параметром JVM `-Dkotlinx.coroutines.debug` (см. [отладку](#debugging-coroutines-and-threads)):

```kotlin
import kotlinx.coroutines.*

fun log(msg: String) = println("[${Thread.currentThread().name}] $msg")

fun main() {
    newSingleThreadContext("Ctx1").use { ctx1 ->
        newSingleThreadContext("Ctx2").use { ctx2 ->
            runBlocking(ctx1) {
                log("Started in ctx1")
                withContext(ctx2) {
                    log("Working in ctx2")
                }
                log("Back to ctx1")
            }
        }
    }
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-context-04.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-context-04.kt).

<!-- It demonstrates several new techniques. One is using [runBlocking] with an explicitly specified context, and the other one is using the [withContext] function to change the context of a coroutine while still staying in the same coroutine, as you can see in the output below: -->
Он демонстрирует несколько новых методов. Один использует [runBlocking](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-blocking.html) с явно указанным контекстом, а другой использует функцию [withContext](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-context.html) для изменения контекста корутины, оставаясь при этом в той же самой корутине, как вы можете видеть в выводе ниже:

```text
[Ctx1 @coroutine#1] Started in ctx1
[Ctx2 @coroutine#1] Working in ctx2
[Ctx1 @coroutine#1] Back to ctx1
```

<!-- Note that this example also uses the `use` function from the Kotlin standard library to release threads created with [newSingleThreadContext] when they are no longer needed.  -->
Обратите внимание, что в этом примере также используется функция `use` из стандартной библиотеки Kotlin для освобождения потоков, созданных с помощью [newSingleThreadContext](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/new-single-thread-context.html), когда они больше не нужны.

<a name="job-in-the-context"></a>

<!-- ## Job in the context -->
## Job в контексте

<!-- The coroutine's [Job] is part of its context, and can be retrieved from it 
using the `coroutineContext[Job]` expression: -->
 [Job](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html) корутины является частью ее контекста и может быть извлечено из него с помощью выражения `coroutineContext[Job]`:

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> {
    println("My job is ${coroutineContext[Job]}")
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-context-05.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-context-05.kt).

<!-- In the [debug mode](#debugging-coroutines-and-threads), it outputs something like this: -->
В [режиме отладки](#debugging-coroutines-and-threads) выдает что-то вроде этого:
```
My job is "coroutine#1":BlockingCoroutine{Active}@6d311334
```

<!-- Note that [isActive] in [CoroutineScope] is just a convenient shortcut for
`coroutineContext[Job]?.isActive == true`. -->
Обратите внимание, что [isActive](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/is-active.html) в [CoroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html) — это просто удобный ярлык для `coroutineContext[Job]?.isActive == true`.

<a name="children-of-a-coroutine"></a>

<!-- ## Children of a coroutine -->
## Дети корутины

<!-- When a coroutine is launched in the [CoroutineScope] of another coroutine, it inherits its context via [CoroutineScope.coroutineContext] and the [Job] of the new coroutine becomes a _child_ of the parent coroutine's job. When the parent coroutine is cancelled, all its children are recursively cancelled, too.  -->
Когда корутина запускается в [CoroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html) другой корутины, она наследует свой контекст через [CoroutineScope.coroutineContext](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/coroutine-context.html), и [Job](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html) новой корутины становится _дочерним_ по отношению к [Job](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html) родительской корутины. Когда родительская корутина отменяется, все ее потомки также рекурсивно отменяются.

<!-- However, this parent-child relation can be explicitly overriden in one of two ways: -->
Однако это отношение родитель-потомок может быть явно переопределено одним из двух способов:

<!-- 1. When a different scope is explicitly specified when launching a coroutine (for example, `GlobalScope.launch`), then it does not inherit a `Job` from the parent scope. -->
1. Если при запуске корутины явно указана другая область видимости (например, `GlobalScope.launch`), то она не наследует `Job` от родительской области видимости видимости.
<!-- 2. When a different `Job` object is passed as the context for the new coroutine (as show in the example below), then it overrides the `Job` of the parent scope. -->
2. Когда в качестве контекста для новой корутины передается другой объект `Job` (как показано в примере ниже), он переопределяет `Job` родительской области видимости.

<!-- In both cases, the launched coroutine is not tied to the scope it was launched from and operates independently. -->
В обоих случаях запущенная корутина не привязана к области видимости, из которой она была запущена, и работает независимо.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> {
    // launch a coroutine to process some kind of incoming request
    val request = launch {
        // it spawns two other jobs
        launch(Job()) { 
            println("job1: I run in my own Job and execute independently!")
            delay(1000)
            println("job1: I am not affected by cancellation of the request")
        }
        // and the other inherits the parent context
        launch {
            delay(100)
            println("job2: I am a child of the request coroutine")
            delay(1000)
            println("job2: I will not execute this line if my parent request is cancelled")
        }
    }
    delay(500)
    request.cancel() // cancel processing of the request
    delay(1000) // delay a second to see what happens
    println("main: Who has survived request cancellation?")
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-context-06.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-context-06.kt).

<!-- The output of this code is: -->
Вывод этого кода:

```text
job1: I run in my own Job and execute independently!
job2: I am a child of the request coroutine
job1: I am not affected by cancellation of the request
main: Who has survived request cancellation?
```
<a name="parental-responsibilities"></a>

<!-- ## Parental responsibilities  -->
## Родительские обязанности

<!-- A parent coroutine always waits for completion of all its children. A parent does not have to explicitly track all the children it launches, and it does not have to use [Job.join] to wait for them at the end: -->
Родительская корутина всегда ожидает завершения всех своих потомков. Родителю не нужно явно отслеживать всех дочерних элементов, которые он запускает, и ему не нужно использовать [Job.join](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/join.html), чтобы дождаться их в конце:

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> {
    // launch a coroutine to process some kind of incoming request
    val request = launch {
        repeat(3) { i -> // launch a few children jobs
            launch  {
                delay((i + 1) * 200L) // variable delay 200ms, 400ms, 600ms
                println("Coroutine $i is done")
            }
        }
        println("request: I'm done and I don't explicitly join my children that are still active")
    }
    request.join() // wait for completion of the request, including all its children
    println("Now processing of the request is complete")
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-context-07.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-context-07.kt).

<!-- The result is going to be: -->
Результат будет:

```text
request: I'm done and I don't explicitly join my children that are still active
Coroutine 0 is done
Coroutine 1 is done
Coroutine 2 is done
Now processing of the request is complete
```
<a name="naming-coroutines-for-debugging"></a>

<!-- ## Naming coroutines for debugging -->
## Именование корутин для отладки


<!-- Automatically assigned ids are good when coroutines log often and you just need to correlate log records coming from the same coroutine. However, when a coroutine is tied to the processing of a specific request or doing some specific background task, it is better to name it explicitly for debugging purposes. The [CoroutineName] context element serves the same purpose as the thread name. It is included in the thread name that is executing this coroutine when the [debugging mode](#debugging-coroutines-and-threads) is turned on. -->
Автоматически назначаемые идентификаторы хороши, когда корутины часто логируются, и вам просто нужно сопоставить записи логов, поступающие от одной и той же корутины. Однако, когда корутина связана с обработкой определенного запроса или выполнением какой-либо конкретной фоновой задачи, лучше назвать ее явно для отладки. Элемент контекста [CoroutineName](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-name/index.html) служит той же цели, что и имя потока. Он включен в имя потока, выполняющего эту корутину, когда включен [режим отладки]((#debugging-coroutines-and-threads)).


<!-- The following example demonstrates this concept: -->
Следующий пример демонстрирует эту концепцию:

```kotlin
import kotlinx.coroutines.*

fun log(msg: String) = println("[${Thread.currentThread().name}] $msg")

fun main() = runBlocking(CoroutineName("main")) {
    log("Started main coroutine")
    // run two background value computations
    val v1 = async(CoroutineName("v1coroutine")) {
        delay(500)
        log("Computing v1")
        252
    }
    val v2 = async(CoroutineName("v2coroutine")) {
        delay(1000)
        log("Computing v2")
        6
    }
    log("The answer for v1 / v2 = ${v1.await() / v2.await()}")
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-context-08.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-context-08.kt).

<!-- The output it produces with `-Dkotlinx.coroutines.debug` JVM option is similar to: -->
Результат, с опцией JVM `-Dkotlinx.coroutines.debug`, будет:
 
```text
[main @main#1] Started main coroutine
[main @v1coroutine#2] Computing v1
[main @v2coroutine#3] Computing v2
[main @main#1] The answer for v1 / v2 = 42
```

<a name="combining-context-elements"></a>

<!-- ## Combining context elements -->
## Объединение элементов контекста

<!-- Sometimes we need to define multiple elements for a coroutine context. We can use the `+` operator for that. For example, we can launch a coroutine with an explicitly specified dispatcher and an explicitly specified name at the same time:  -->
Иногда нам нужно определить несколько элементов для контекста корутины. Для этого мы можем использовать оператор `+`. Например, мы можем запустить корутину с явно указанным диспетчером и с явно указанным именем одновременно:

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> {
    launch(Dispatchers.Default + CoroutineName("test")) {
        println("I'm working in thread ${Thread.currentThread().name}")
    }
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-context-09.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-context-09.kt).

<!-- The output of this code with the `-Dkotlinx.coroutines.debug` JVM option is:  -->
Результат, с опцией JVM `-Dkotlinx.coroutines.debug`, будет:

```text
I'm working in thread DefaultDispatcher-worker-1 @test#2
```

<a name="coroutine-scope"></a>

<!-- ## Coroutine scope -->
## Область видимости корутины

<!-- Let us put our knowledge about contexts, children and jobs together. Assume that our application has an object with a lifecycle, but that object is not a coroutine. For example, we are writing an Android application and launch various coroutines in the context of an Android activity to perform asynchronous operations to fetch and update data, do animations, etc. All of these coroutines must be cancelled when the activity is destroyed to avoid memory leaks. We, of course, can manipulate contexts and jobs manually to tie the lifecycles of the activity and its coroutines, but `kotlinx.coroutines` provides an abstraction encapsulating that: [CoroutineScope]. You should be already familiar with the coroutine scope as all coroutine builders are declared as extensions on it.  -->
Давайте объединим наши знания о контекстах, детях и jobs вместе. Предположим, что в нашем приложении есть объект с жизненным циклом, но этот объект не является корутиной. Например, мы пишем приложение для Android и запускаем различные корутины в контексте Android активити для выполнения асинхронных операций по выборке и обновлению данных, анимации и т. д. Все эти корутины должны быть отменены при уничтожении активити, чтобы избежать утечки памяти. Мы, конечно, можем манипулировать контекстами и jobs вручную, чтобы связать жизненные циклы activity и ее корутин, но `kotlinx.coroutines` предоставляет абстракцию, инкапсулирующую это: [CoroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html). Вы уже должны быть знакомы с областью действия корутины, так как все билдеры корутин объявляются в ней как расширения.

<!-- We manage the lifecycles of our coroutines by creating an instance of [CoroutineScope] tied to 
the lifecycle of our activity. A `CoroutineScope` instance can be created by the [CoroutineScope()] or [MainScope()] factory functions. The former creates a general-purpose scope, while the latter creates a scope for UI applications and uses [Dispatchers.Main] as the default dispatcher: -->
Мы управляем жизненными циклами наших корутин, создавая экземпляр [CoroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html), привязанный к жизненному циклу нашей активити. Экземпляр `CoroutineScope` может быть создан фабричными функциями [CoroutineScope()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope.html) или [MainScope()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-main-scope.html). Первая создает область общего назначения, а вторая создает область для приложений пользовательского интерфейса и использует [Dispatchers.Main](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-main.html) в качестве диспетчера по умолчанию:

```kotlin
class Activity {
    private val mainScope = MainScope()
    
    fun destroy() {
        mainScope.cancel()
    }
    // to be continued ...
```

<!-- Now, we can launch coroutines in the scope of this `Activity` using the defined `scope`.
For the demo, we launch ten coroutines that delay for a different time: -->
Теперь мы можем запускать корутину в рамках этого `Activity`, используя заданную `scope`. Для демонстрации мы запускаем десять корутин, которые задерживают на разное время:

```kotlin
    // class Activity continues
    fun doSomething() {
        // launch ten coroutines for a demo, each working for a different time
        repeat(10) { i ->
            mainScope.launch {
                delay((i + 1) * 200L) // variable delay 200ms, 400ms, ... etc
                println("Coroutine $i is done")
            }
        }
    }
} // class Activity ends
``` 

<!-- In our main function we create the activity, call our test `doSomething` function, and destroy the activity after 500ms. This cancels all the coroutines that were launched from `doSomething`. We can see that because after the destruction of the activity no more messages are printed, even if we wait a little longer. -->
В нашей основной функции мы создаем активити, вызываем нашу тестовую функцию `doSomething` и уничтожаем активити через 500 мс. Это отменяет все корутины, которые были запущены из `doSomething`. Мы видим это, потому что после уничтожения активити больше не печатаются сообщения, даже если мы подождем немного дольше.

```kotlin
import kotlinx.coroutines.*

class Activity {
    private val mainScope = CoroutineScope(Dispatchers.Default) // use Default for test purposes
    
    fun destroy() {
        mainScope.cancel()
    }

    fun doSomething() {
        // launch ten coroutines for a demo, each working for a different time
        repeat(10) { i ->
            mainScope.launch {
                delay((i + 1) * 200L) // variable delay 200ms, 400ms, ... etc
                println("Coroutine $i is done")
            }
        }
    }
} // class Activity ends

fun main() = runBlocking<Unit> {
    val activity = Activity()
    activity.doSomething() // run test function
    println("Launched coroutines")
    delay(500L) // delay for half a second
    println("Destroying activity!")
    activity.destroy() // cancels all coroutines
    delay(1000) // visually confirm that they don't work
}
```

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-context-10.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-context-10.kt).

<!-- The output of this example is: -->
Результат этого примера:

```text
Launched coroutines
Coroutine 0 is done
Coroutine 1 is done
Destroying activity!
```

<!-- As you can see, only the first two coroutines print a message and the others are cancelled 
by a single invocation of `job.cancel()` in `Activity.destroy()`. -->
Как видите, только первые две корутины печатают сообщение, а остальные отменяются одним вызовом `job.cancel()` в `Activity.destroy()`.

<!-- > Note, that Android has first-party support for coroutine scope in all entities with the lifecycle. See [the corresponding documentation](https://developer.android.com/topic/libraries/architecture/coroutines#lifecyclescope). -->
> Обратите внимание, что Android имеет собственную поддержку области действия корутины во всех сущностях с жизненным циклом. Смотрите [соответствующую документацию](https://developer.android.com/topic/libraries/architecture/coroutines#lifecyclescope).

<a name="thread-local-data"></a>

<!-- ### Thread-local data -->
### Локальных данные потока

<!-- Sometimes it is convenient to have an ability to pass some thread-local data to or between coroutines. However, since they are not bound to any particular thread, this will likely lead to boilerplate if done manually. -->
Иногда удобно иметь возможность передавать некоторые локальные данные потока в корутины или между ними. Однако, поскольку они не привязаны ни к какому конкретному потоку, это, скорее всего, приведет к шаблону, если сделать это вручную.

<!-- For [`ThreadLocal`](https://docs.oracle.com/javase/8/docs/api/java/lang/ThreadLocal.html), the [asContextElement] extension function is here for the rescue. It creates an additional context element which keeps the value of the given `ThreadLocal` and restores it every time the coroutine switches its context. -->
Для [`ThreadLocal`](https://docs.oracle.com/javase/8/docs/api/java/lang/ThreadLocal.html) на помощь приходит функция расширения [asContextElement](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/as-context-element.html). Она создает дополнительный элемент контекста, который сохраняет значение данного `ThreadLocal` и восстанавливает его каждый раз, когда корутина переключает свой контекст.

<!-- It is easy to demonstrate it in action: -->
Это легко продемонстрировать в действии:

```kotlin
import kotlinx.coroutines.*

val threadLocal = ThreadLocal<String?>() // declare thread-local variable

fun main() = runBlocking<Unit> {
    threadLocal.set("main")
    println("Pre-main, current thread: ${Thread.currentThread()}, thread local value: '${threadLocal.get()}'")
    val job = launch(Dispatchers.Default + threadLocal.asContextElement(value = "launch")) {
        println("Launch start, current thread: ${Thread.currentThread()}, thread local value: '${threadLocal.get()}'")
        yield()
        println("After yield, current thread: ${Thread.currentThread()}, thread local value: '${threadLocal.get()}'")
    }
    job.join()
    println("Post-main, current thread: ${Thread.currentThread()}, thread local value: '${threadLocal.get()}'")
}
```  

<!-- > You can get the full code [here](../../kotlinx-coroutines-core/jvm/test/guide/example-context-11.kt). -->
> Полный код находится [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/jvm/test/guide/example-context-11.kt).

<!-- In this example we launch a new coroutine in a background thread pool using [Dispatchers.Default], so it works on a different thread from the thread pool, but it still has the value of the thread local variable that we specified using `threadLocal.asContextElement(value = "launch")`, no matter which thread the coroutine is executed on. Thus, the output (with [debug](#debugging-coroutines-and-threads)) is: -->
В этом примере мы запускаем новую корутину в пуле фоновых потоков, используя [Dispatchers.Default](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-default.html), поэтому она работает в другом потоке из пула потоков, но по-прежнему имеет значение локальной переменной потока, которое мы указали с помощью `threadLocal.asContextElement(value = "launch")`, независимо от того, в каком потоке выполняется корутина. Таким образом, вывод (с [отладкой](#debugging-coroutines-and-threads)):

```text
Pre-main, current thread: Thread[main @coroutine#1,5,main], thread local value: 'main'
Launch start, current thread: Thread[DefaultDispatcher-worker-1 @coroutine#2,5,main], thread local value: 'launch'
After yield, current thread: Thread[DefaultDispatcher-worker-2 @coroutine#2,5,main], thread local value: 'launch'
Post-main, current thread: Thread[main @coroutine#1,5,main], thread local value: 'main'
```

<!-- It's easy to forget to set the corresponding context element. The thread-local variable accessed from the coroutine may then have an unexpected value, if the thread running the coroutine is different. To avoid such situations, it is recommended to use the [ensurePresent] method and fail-fast on improper usages. -->
Легко забыть установить соответствующий элемент контекста. Локальная переменная потока, доступ к которой осуществляется из корутины, может иметь неожиданное значение, если поток, выполняющий корутину, отличается. Чтобы избежать таких ситуаций, рекомендуется использовать метод [ensurePresent](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/ensure-present.html) и отказоустойчивость при неправильном использовании.

<!-- `ThreadLocal` has first-class support and can be used with any primitive `kotlinx.coroutines` provides. It has one key limitation, though: when a thread-local is mutated, a new value is not propagated to the coroutine caller (because a context element cannot track all `ThreadLocal` object accesses), and the updated value is lost on the next suspension. Use [withContext] to update the value of the thread-local in a coroutine, see [asContextElement] for more details.  -->
`ThreadLocal` имеет первоклассную поддержку и может использоваться с любыми примитивами, предоставляемыми `kotlinx.coroutines`. Однако у него есть одно ключевое ограничение: когда локальный поток изменяется, новое значение не распространяется на вызывающую корутину (поскольку элемент контекста не может отслеживать все обращения к объекту `ThreadLocal`), а обновленное значение теряется при следующей приостановке. Используйте [withContext](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-context.html) для обновления локальной переменной потока в корутине, дополнительные сведения смотрите в разделе [asContextElement](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/as-context-element.html).

<!-- Alternatively, a value can be stored in a mutable box like `class Counter(var i: Int)`, which is, in turn, stored in a thread-local variable. However, in this case you are fully responsible to synchronize potentially concurrent modifications to the variable in this mutable box. -->
В качестве альтернативы значение может храниться в классе-обертке, таком как `class Counter(var i: Int)`, который, в свою очередь, хранится в локальной переменной потока. Однако в этом случае вы несете полную ответственность за синхронизацию потенциально одновременных изменений переменной в этом классе-обертке.

<!-- For advanced usage, for example for integration with logging MDC, transactional contexts or any other libraries which internally use thread-locals for passing data, see the documentation of the [ThreadContextElement] interface that should be implemented. -->
Для расширенного использования, например для интеграции с логированием MDC, контекстами транзакций или любыми другими библиотеками, которые внутренне используют локальные потоки для передачи данных, смотрите документацию по интерфейсу [ThreadContextElement](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-thread-context-element/index.html), который необходимо реализовать.
