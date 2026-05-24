---
type: doc
layout: reference
title: "Корутины"
url: https://kotlinlang.ru/docs/coroutines-overview.html
---
      
<!-- При переводе статьи оригинальная версия была от 26 August 2025 -->

<!-- # Coroutines -->
# Корутины

<!-- Applications often need to perform multiple tasks at the same time, such as responding to user input, loading data, or updating the screen.
To support this, they rely on concurrency, which allows operations to run independently without blocking each other. -->
Приложениям часто нужно выполнять несколько задач одновременно: реагировать на ввод пользователя, загружать данные или
обновлять экран. Для этого они используют конкурентное выполнение, при котором операции могут выполняться независимо и не
блокировать друг друга.

<!-- The most common way to run tasks concurrently is by using threads, which are independent paths of execution managed by the operating system.
However, threads are relatively heavy, and creating many of them can lead to performance issues. -->
Самый распространенный способ выполнять задачи конкурентно — использовать потоки: независимые пути выполнения, которыми
управляет операционная система. Однако потоки относительно тяжелые, и создание большого количества потоков может привести
к проблемам с производительностью.

<!-- To support efficient concurrency, Kotlin uses asynchronous programming built around _coroutines_, which let you write asynchronous code in a natural, sequential style using suspending functions.
Coroutines are lightweight alternatives to threads.
They can suspend without blocking system resources and are resource-friendly, making them better suited for fine-grained concurrency. -->
Чтобы поддерживать эффективное конкурентное выполнение, Kotlin использует асинхронное программирование, построенное вокруг
_корутин_. Корутины позволяют писать асинхронный код в естественном последовательном стиле с помощью приостанавливающих
функций. Корутины — легковесная альтернатива потокам: они могут приостанавливаться, не блокируя системные ресурсы,
экономно расходуют эти ресурсы и поэтому лучше подходят для мелкозернистой конкурентности.

<!-- Most coroutine features are provided by the [`kotlinx.coroutines`](https://github.com/Kotlin/kotlinx.coroutines) library,
which includes tools for launching coroutines, handling concurrency, working with asynchronous streams, and more. -->
Большинство возможностей корутин предоставляет библиотека [`kotlinx.coroutines`](https://github.com/Kotlin/kotlinx.coroutines).
Она включает инструменты для запуска корутин, управления конкурентным выполнением, работы с асинхронными потоками данных и
других задач.

<!-- If you're new to coroutines in Kotlin, start with the [Coroutine basics](coroutines-basics.md) guide before diving into more complex topics.
This guide introduces the key concepts of suspending functions, coroutine builders, and structured concurrency through simple examples: -->
Если вы только знакомитесь с корутинами в Kotlin, начните с руководства [Основы корутин](coroutines-basics.html), прежде чем
переходить к более сложным темам. В нем на простых примерах представлены ключевые понятия: приостанавливающие функции,
билдеры корутин и структурированная конкурентность.

<!-- <a href="coroutines-basics.md"><img src="get-started-coroutines.svg" width="700" alt="Get started with coroutines" style="block"/></a> -->

<!-- > Check out the [KotlinConf app](https://github.com/JetBrains/kotlinconf-app) for a sample project to see how coroutines are used in practice. -->
> Посмотрите [приложение KotlinConf](https://github.com/JetBrains/kotlinconf-app) как пример проекта, в котором корутины
> используются на практике.

<a name="coroutine-concepts"></a>

<!-- ## Coroutine concepts -->
## Концепции корутин

<!-- The `kotlinx.coroutines` library provides the core building blocks for running tasks concurrently, structuring coroutine execution, and managing shared state. -->
Библиотека `kotlinx.coroutines` предоставляет основные строительные блоки для конкурентного выполнения задач, структурирования
выполнения корутин и управления разделяемым состоянием.

<a name="suspending-functions-and-coroutine-builders"></a>

<!-- ### Suspending functions and coroutine builders -->
### Приостанавливающие функции и билдеры корутин

<!-- Coroutines in Kotlin are built on suspending functions, which allow code to pause and resume without blocking a thread.
The `suspend` keyword marks functions that can perform long-running operations asynchronously. -->
Корутины в Kotlin построены на приостанавливающих функциях: они позволяют коду приостанавливаться и возобновляться, не
блокируя поток. Ключевое слово `suspend` помечает функции, которые могут асинхронно выполнять длительные операции.

<!-- To launch new coroutines, use coroutine builders like [`.launch()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html) and [`.async()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html).
These builders are extension functions on [`CoroutineScope`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/),
which defines the coroutine's lifecycle and provides the coroutine context. -->
Чтобы запускать новые корутины, используйте билдеры корутин, например
[`.launch()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html) и
[`.async()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html). Эти билдеры
являются функциями-расширениями для
[`CoroutineScope`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/),
который определяет жизненный цикл корутины и предоставляет контекст корутины.

<!-- You can learn more about these builders in [Coroutine basics](coroutines-basics.md) and [Composing suspend functions](composing-suspending-functions.md). -->
Подробнее об этих билдерах можно узнать в разделах [Основы корутин](coroutines-basics.html) и
[Составление функций приостановки](composing-suspending-functions.html).

<a name="coroutine-context-and-behavior"></a>

<!-- ### Coroutine context and behavior -->
### Контекст и поведение корутин

<!-- Launching a coroutine from a `CoroutineScope` creates a context that governs its execution.
Builder functions like `.launch()` and `.async()` automatically create a set of elements that define how the coroutine behaves: -->
Запуск корутины из `CoroutineScope` создает контекст, управляющий ее выполнением. Функции-билдеры, такие как `.launch()` и
`.async()`, автоматически создают набор элементов, которые определяют поведение корутины:

<!-- * The [`Job`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/) interface tracks the coroutine's lifecycle and enables structured concurrency.
* [`CoroutineDispatcher`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-dispatcher/) controls where the coroutine runs, such as on a background thread or the main thread in UI applications.
* [`CoroutineExceptionHandler`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-exception-handler/) handles uncaught exceptions. -->
* Интерфейс [`Job`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/)
  отслеживает жизненный цикл корутины и обеспечивает структурированную конкурентность.
* [`CoroutineDispatcher`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-dispatcher/)
  управляет тем, где выполняется корутина: например, в фоновом потоке или в главном потоке UI-приложения.
* [`CoroutineExceptionHandler`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-exception-handler/)
  обрабатывает неперехваченные исключения.

<!-- These, along with other possible elements, make up the [_coroutine context_](coroutine-context-and-dispatchers.md), which is inherited by default from the coroutine's parent.
This context forms a hierarchy that enables structured concurrency, where related coroutines can be [canceled](cancellation-and-timeouts.md) together or [handle exceptions](exception-handling.md) as a group. -->
Эти и другие возможные элементы образуют [_контекст корутины_](coroutine-context-and-dispatchers.html), который по умолчанию
наследуется от родительской корутины. Такой контекст формирует иерархию, обеспечивающую структурированную конкурентность:
связанные корутины можно [отменять](cancellation-and-timeouts.html) вместе или
[обрабатывать исключения](https://kotlinlang.org/docs/exception-handling.html) как группу.

<a name="asynchronous-flow-and-shared-mutable-state"></a>

<!-- ### Asynchronous flow and shared mutable state -->
### Асинхронный поток и разделяемое изменяемое состояние

<!-- Kotlin provides several ways for coroutines to communicate.
Use one of the following options based on how you want to share values between coroutines: -->
Kotlin предоставляет несколько способов коммуникации между корутинами. Выберите один из следующих вариантов в зависимости
от того, как нужно передавать значения между корутинами:

<!-- * [`Flow`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/) produces values only when a coroutine actively collects them.
* [`Channel`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.channels/-channel/) allows multiple coroutines to send and receive values, with each value delivered to exactly one coroutine.
* [`SharedFlow`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/) continuously shares every value with all active collecting coroutines. -->
* [`Flow`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/) создает
  значения только тогда, когда корутина активно их собирает.
* [`Channel`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.channels/-channel/)
  позволяет нескольким корутинам отправлять и получать значения, при этом каждое значение доставляется ровно одной корутине.
* [`SharedFlow`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/)
  непрерывно передает каждое значение всем активным собирающим корутинам.

<!-- When multiple coroutines need to access or update the same data, they _share mutable state_.
Without coordination, this can lead to race conditions, where operations interfere with each other in unpredictable ways.
To safely manage shared mutable state, use [`StateFlow`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/#) to wrap the shared data.
Then, you can update it from one coroutine and collect its latest value from others. -->
Когда нескольким корутинам нужно обращаться к одним и тем же данным или обновлять их, они _разделяют изменяемое состояние_.
Без координации это может привести к состояниям гонки, когда операции непредсказуемо мешают друг другу. Чтобы безопасно
управлять разделяемым изменяемым состоянием, оберните общие данные в
[`StateFlow`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/#).
После этого можно обновлять их из одной корутины и собирать последнее значение из других.

<!-- Learn more in [Shared mutable state and concurrency](shared-mutable-state-and-concurrency.md). -->

<!-- For more information, see [Asynchronous flow](flow.md), [Channels](channels.md), and the [Coroutines and channels tutorial](coroutines-and-channels.md). -->
Подробнее см. в разделах [Асинхронный поток](https://kotlinlang.org/docs/flow.html),
[Каналы](https://kotlinlang.org/docs/channels.html) и в руководстве
[Корутины и каналы](https://kotlinlang.org/docs/coroutines-and-channels.html).

<a name="what-s-next"></a>

<!-- ## What's next -->
## Что дальше

<!-- * Learn the fundamentals of coroutines, suspending functions, and builders in the [Coroutine basics guide](coroutines-basics.md).
* Explore how to combine suspending functions and build coroutine pipelines in [Composing suspending functions](composing-suspending-functions.md).
* Learn how to [debug coroutines](debug-coroutines-with-idea.md) using built-in tools in IntelliJ IDEA.
* For flow-specific debugging, see the [Debug Kotlin Flow using IntelliJ IDEA](debug-flow-with-idea.md) tutorial.
* Read the [Guide to UI programming with coroutines](https://github.com/Kotlin/kotlinx.coroutines/blob/master/ui/coroutines-guide-ui.md) to learn about coroutine-based UI development.
* Review [best practices for using coroutines in Android](https://developer.android.com/kotlin/coroutines/coroutines-best-practices).
* Check out the [`kotlinx.coroutines` API reference](https://kotlinlang.org/api/kotlinx.coroutines/). -->
* Изучите основы корутин, приостанавливающих функций и билдеров в руководстве
  [Основы корутин](coroutines-basics.html).
* Разберитесь, как комбинировать приостанавливающие функции и строить конвейеры корутин, в разделе
  [Составление функций приостановки](composing-suspending-functions.html).
* Узнайте, как [отлаживать корутины](https://kotlinlang.org/docs/debug-coroutines-with-idea.html) с помощью встроенных инструментов IntelliJ IDEA.
* Для отладки, связанной с Flow, см. руководство
  [Отладка Kotlin Flow с помощью IntelliJ IDEA](https://kotlinlang.org/docs/debug-flow-with-idea.html).
* Прочитайте [руководство по UI-программированию с корутинами](https://github.com/Kotlin/kotlinx.coroutines/blob/master/ui/coroutines-guide-ui.md),
  чтобы узнать о разработке UI на основе корутин.
* Ознакомьтесь с [лучшими практиками использования корутин в Android](https://developer.android.com/kotlin/coroutines/coroutines-best-practices).
* Посмотрите [справочник API `kotlinx.coroutines`](https://kotlinlang.org/api/kotlinx.coroutines/).
