---
type: doc
layout: reference
category: "Syntax"
title: "Основы корутин"
url: https://kotlinlang.ru/docs/coroutines-basics.html
---

<!-- При переводе статьи оригинальная версия была от 12 December 2025 -->

# Основы корутин

Чтобы создавать приложения, которые выполняют несколько задач одновременно, Kotlin использует _корутины_ — механизм конкурентного выполнения.
Корутина — это приостанавливаемое вычисление, которое позволяет писать конкурентный код в понятном последовательном стиле.
Корутины могут выполняться одновременно с другими корутинами, а иногда и параллельно.

На JVM и в Kotlin/Native весь конкурентный код, включая корутины, выполняется в _потоках_, которыми управляет операционная система.
Корутины могут приостанавливать своё выполнение, не блокируя поток.
Благодаря этому одна корутина может приостановиться в ожидании данных, а другая — продолжить работу в том же потоке, что помогает эффективно использовать ресурсы.

![Сравнение параллельных и конкурентных потоков](https://kotlinlang.org/docs/images/parallelism-and-concurrency.svg)

Подробнее о различиях между корутинами и потоками см. в разделе [Сравнение корутин и JVM-потоков](#comparing-coroutines-and-jvm-threads).

<a name="suspending-functions"></a>

## Функции приостановки

Базовый строительный блок корутин — _функция приостановки_.
Она позволяет выполняющейся операции приостановиться и возобновиться позже, не меняя структуру вашего кода.

Чтобы объявить функцию приостановки, используйте ключевое слово `suspend`:

```kotlin
suspend fun greet() {
    println("Hello world from a suspending function")
}
```

Функцию приостановки можно вызвать только из другой функции приостановки.
Чтобы вызывать функции приостановки из точки входа Kotlin-приложения, пометьте функцию `main()` ключевым словом `suspend`:

```kotlin
suspend fun main() {
    showUserInfo()
}

suspend fun showUserInfo() {
    println("Loading user...")
    greet()
    println("User: John Smith")
}

suspend fun greet() {
    println("Hello world from a suspending function")
}
```

В этом примере конкурентного выполнения ещё нет, но, помечая функции ключевым словом `suspend`, вы разрешаете им вызывать другие функции приостановки и запускать внутри конкурентный код.

Хотя ключевое слово `suspend` входит в ядро языка Kotlin, большинство возможностей корутин доступно через библиотеку [`kotlinx.coroutines`](https://github.com/Kotlin/kotlinx.coroutines).

<a name="add-the-kotlinx-coroutines-library-to-your-project"></a>

## Добавление библиотеки kotlinx.coroutines в проект

Чтобы подключить библиотеку `kotlinx.coroutines` к проекту, добавьте соответствующую зависимость для вашего инструмента сборки.

Gradle Kotlin DSL:

```kotlin
// build.gradle.kts
repositories {
    mavenCentral()
}

dependencies {
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.10.2")
}
```

Gradle Groovy DSL:

```groovy
// build.gradle
repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.10.2'
}
```

Maven:

```xml
<!-- pom.xml -->
<project>
    <dependencies>
        <dependency>
            <groupId>org.jetbrains.kotlinx</groupId>
            <artifactId>kotlinx-coroutines-core</artifactId>
            <version>1.10.2</version>
        </dependency>
    </dependencies>
    ...
</project>
```

<a name="create-your-first-coroutines"></a>

## Создание первых корутин

> В примерах на этой странице используется явное выражение `this` с функциями-конструкторами корутин `CoroutineScope.launch()` и `CoroutineScope.async()`.
> Эти конструкторы корутин являются [функциями-расширениями](extensions.html) для `CoroutineScope`, а выражение `this` указывает на текущий `CoroutineScope` как на приёмник.
>
> Практический пример см. в разделе [Извлечение конструкторов корутин из области видимости корутины](#extract-coroutine-builders-from-the-coroutine-scope).

Чтобы создать корутину в Kotlin, вам нужны:

* [функция приостановки](#suspending-functions);
* [область видимости корутин](#coroutine-scope-and-structured-concurrency), в которой она может выполняться, например внутри функции `withContext()`;
* [конструктор корутин](#coroutine-builder-functions), например `CoroutineScope.launch()`, чтобы запустить корутину;
* [диспетчер](#coroutine-dispatchers), который управляет тем, какие потоки будут использоваться.

Рассмотрим пример, в котором несколько корутин используются в многопоточной среде.

1. Импортируйте библиотеку `kotlinx.coroutines`:

    ```kotlin
    import kotlinx.coroutines.*
    ```

2. Пометьте функции, которые могут приостанавливаться и возобновляться, ключевым словом `suspend`:

    ```kotlin
    suspend fun greet() {
        println("The greet() on the thread: ${Thread.currentThread().name}")
    }

    suspend fun main() {}
    ```

    > В некоторых проектах функцию `main()` можно пометить как `suspend`, но при интеграции с существующим кодом или фреймворком это может быть невозможно.
    > В таком случае проверьте документацию фреймворка: возможно, он поддерживает вызов функций приостановки.
    > Если нет, используйте [`runBlocking()`](#runblocking), чтобы вызвать их, заблокировав текущий поток.

3. Добавьте функцию [`delay()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/delay.html#), чтобы имитировать приостанавливаемую задачу, например получение данных или запись в базу данных:

    ```kotlin
    suspend fun greet() {
        println("The greet() on the thread: ${Thread.currentThread().name}")
        delay(1000L)
    }
    ```

4. Используйте [`withContext(Dispatchers.Default)`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-context.html#), чтобы определить точку входа для многопоточного конкурентного кода, который выполняется в общем пуле потоков:

    ```kotlin
    suspend fun main() {
        withContext(Dispatchers.Default) {
            // Здесь будут конструкторы корутин
        }
    }
    ```

    > Приостанавливающая функция `withContext()` обычно используется для [переключения контекста](coroutine-context-and-dispatchers.html#jumping-between-threads), но в этом примере она также определяет неблокирующую точку входа для конкурентного кода.
    > Она использует [диспетчер `Dispatchers.Default`](#coroutine-dispatchers), чтобы выполнять код в общем пуле потоков для многопоточного выполнения.
    > По умолчанию этот пул использует до такого количества потоков, сколько ядер CPU доступно во время выполнения, но не меньше двух потоков.
    >
    > Корутины, запущенные внутри блока `withContext()`, разделяют одну область видимости корутин, что обеспечивает [структурированную конкурентность](#coroutine-scope-and-structured-concurrency).

5. Используйте [функцию-конструктор корутин](#coroutine-builder-functions), например [`CoroutineScope.launch()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html), чтобы запустить корутину:

    ```kotlin
    suspend fun main() {
        withContext(Dispatchers.Default) { // this: CoroutineScope
            // Запускает корутину внутри области видимости с помощью CoroutineScope.launch()
            this.launch { greet() }
            println("The withContext() on the thread: ${Thread.currentThread().name}")
        }
    }
    ```

6. Объедините эти части, чтобы запускать несколько корутин одновременно в общем пуле потоков:

    ```kotlin
    // Импорт библиотеки корутин
    import kotlinx.coroutines.*

    // Импорт kotlin.time.Duration для выражения длительности в секундах
    import kotlin.time.Duration.Companion.seconds

    // Определение функции приостановки
    suspend fun greet() {
        println("The greet() on the thread: ${Thread.currentThread().name}")
        // Приостанавливает выполнение на 1 секунду и освобождает поток
        delay(1.seconds)
        // Здесь delay() имитирует вызов приостанавливающего API
        // Здесь можно добавить приостанавливающие вызовы API, например сетевой запрос
    }

    suspend fun main() {
        // Выполняет код внутри этого блока в общем пуле потоков
        withContext(Dispatchers.Default) { // this: CoroutineScope
            this.launch() {
                greet()
            }

            // Запускает ещё одну корутину
            this.launch() {
                println("The CoroutineScope.launch() on the thread: ${Thread.currentThread().name}")
                delay(1.seconds)
                // Здесь delay() имитирует вызов приостанавливающего API
                // Здесь можно добавить приостанавливающие вызовы API, например сетевой запрос
            }

            println("The withContext() on the thread: ${Thread.currentThread().name}")
        }
    }
    ```

Попробуйте запустить пример несколько раз.
Вы можете заметить, что порядок вывода и имена потоков меняются от запуска к запуску, потому что операционная система сама решает, когда выполнять потоки.

> Вы можете вывести имена корутин рядом с именами потоков, чтобы получить дополнительную информацию.
> Для этого передайте VM-опцию `-Dkotlinx.coroutines.debug` в инструменте сборки или конфигурации запуска IDE.
>
> Подробнее см. в разделе [Отладка корутин](https://github.com/Kotlin/kotlinx.coroutines/blob/master/docs/topics/debugging.md).

<a name="coroutine-scope-and-structured-concurrency"></a>

## Область видимости корутин и структурированная конкурентность

Когда в приложении выполняется много корутин, нужен способ управлять ими как группами.
Корутины Kotlin опираются на принцип _структурированной конкурентности_, который даёт такую структуру.

Согласно этому принципу, корутины образуют древовидную иерархию родительских и дочерних задач со связанными жизненными циклами.
Жизненный цикл корутины — это последовательность состояний от её создания до завершения, отказа или отмены.

Родительская корутина ждёт завершения своих дочерних корутин перед собственным завершением.
Если родительская корутина завершается с ошибкой или отменяется, все её дочерние корутины также рекурсивно отменяются.
Такая связь между корутинами делает отмену и обработку ошибок предсказуемыми и безопасными.

Чтобы сохранять структурированную конкурентность, новые корутины можно запускать только в [`CoroutineScope`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/), который определяет их жизненный цикл и управляет им.
`CoroutineScope` включает _контекст корутины_, который задаёт диспетчер и другие свойства выполнения.
Когда вы запускаете корутину внутри другой корутины, она автоматически становится дочерней по отношению к родительской области видимости.

Вызов [функции-конструктора корутин](#coroutine-builder-functions), например `CoroutineScope.launch()` для `CoroutineScope`, запускает дочернюю корутину корутины, связанной с этой областью видимости.
Внутри блока конструктора [приёмником](lambdas.html#function-literals-with-receiver) является вложенный `CoroutineScope`, поэтому все корутины, которые вы запускаете там, становятся его дочерними корутинами.

<a name="create-a-coroutine-scope-with-the-coroutinescope-function"></a>

### Создание области видимости корутины с помощью функции `coroutineScope()`

Чтобы создать новую область видимости корутин с текущим контекстом корутины, используйте функцию [`coroutineScope()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/coroutine-scope.html).
Эта функция создаёт корневую корутину поддерева корутин.
Она становится прямым родителем корутин, запущенных внутри блока, и непрямым родителем любых корутин, которые они запускают.
`coroutineScope()` выполняет приостанавливающий блок и ждёт завершения самого блока и всех корутин, запущенных в нём.

Пример:

```kotlin
// Импорт kotlin.time.Duration для выражения длительности в секундах
import kotlin.time.Duration.Companion.seconds

import kotlinx.coroutines.*

// Если в контексте корутины не указан диспетчер,
// CoroutineScope.launch() использует Dispatchers.Default
suspend fun main() {
    // Корень поддерева корутин
    coroutineScope { // this: CoroutineScope
        this.launch {
            this.launch {
                delay(2.seconds)
                println("Child of the enclosing coroutine completed")
            }
            println("Child coroutine 1 completed")
        }
        this.launch {
            delay(1.seconds)
            println("Child coroutine 2 completed")
        }
    }
    // Выполняется только после завершения всех дочерних корутин в coroutineScope
    println("Coroutine scope completed")
}
```

Поскольку в этом примере [диспетчер](#coroutine-dispatchers) не указан, функции-конструкторы `CoroutineScope.launch()` внутри блока `coroutineScope()` наследуют текущий контекст.
Если в этом контексте нет заданного диспетчера, `CoroutineScope.launch()` использует `Dispatchers.Default`, который выполняется в общем пуле потоков.

<a name="extract-coroutine-builders-from-the-coroutine-scope"></a>

### Извлечение конструкторов корутин из области видимости корутины

В некоторых случаях может потребоваться извлечь вызовы конструкторов корутин, например [`CoroutineScope.launch()`](#coroutinescope-launch), в отдельные функции.

Рассмотрим пример:

```kotlin
suspend fun main() {
    coroutineScope { // this: CoroutineScope
        // Вызов CoroutineScope.launch(), где CoroutineScope является приёмником
        this.launch { println("1") }
        this.launch { println("2") }
    }
}
```

> `this.launch` также можно записать без явного выражения `this`: `launch`.
> В этих примерах явное `this` используется, чтобы подчеркнуть, что это функция-расширение для `CoroutineScope`.
>
> Подробнее о работе лямбд с приёмником см. в разделе [Литералы функций с приёмником](lambdas.html#function-literals-with-receiver).

Функция `coroutineScope()` принимает лямбду с приёмником `CoroutineScope`.
Внутри этой лямбды неявным приёмником является `CoroutineScope`, поэтому функции-конструкторы вроде `CoroutineScope.launch()` и [`CoroutineScope.async()`](#coroutinescope-async) разрешаются как [функции-расширения](extensions.html#extension-functions) для этого приёмника.

Чтобы извлечь конструкторы корутин в другую функцию, эта функция должна объявить приёмник `CoroutineScope`; иначе возникнет ошибка компиляции:

```kotlin
import kotlinx.coroutines.*

suspend fun main() {
    coroutineScope {
        launchAll()
    }
}

fun CoroutineScope.launchAll() { // this: CoroutineScope
    // Вызов .launch() для CoroutineScope
    this.launch { println("1") }
    this.launch { println("2") }
}

/* -- Вызов launch без объявления CoroutineScope приёмником приводит к ошибке компиляции --

fun launchAll() {
    // Ошибка компиляции: this is not defined
    this.launch { println("1") }
    this.launch { println("2") }
}
 */
```

<a name="coroutine-builder-functions"></a>

## Функции-конструкторы корутин

Функция-конструктор корутин — это функция, которая принимает `suspend`-[лямбду](lambdas.html), определяющую корутину для выполнения.
Примеры:

* [`CoroutineScope.launch()`](#coroutinescope-launch)
* [`CoroutineScope.async()`](#coroutinescope-async)
* [`runBlocking()`](#runblocking)
* [`withContext()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-context.html)
* [`coroutineScope()`](#create-a-coroutine-scope-with-the-coroutinescope-function)

Для выполнения функций-конструкторов корутин нужен `CoroutineScope`.
Это может быть существующая область видимости или область, созданная вспомогательными функциями, такими как `coroutineScope()`, [`runBlocking()`](#runblocking) или [`withContext()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-context.html#).
Каждый конструктор определяет, как запускается корутина и как вы взаимодействуете с её результатом.

<a name="coroutinescope-launch"></a>

### `CoroutineScope.launch()`

Функция-конструктор корутин [`CoroutineScope.launch()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html#) является функцией-расширением для `CoroutineScope`.
Она запускает новую корутину внутри существующей [области видимости корутин](#coroutine-scope-and-structured-concurrency), не блокируя остальную область видимости.

Используйте `CoroutineScope.launch()`, чтобы выполнять задачу параллельно с другой работой, когда результат не нужен или вы не хотите его ждать:

```kotlin
// Импорт kotlin.time.Duration для выражения длительности в миллисекундах
import kotlin.time.Duration.Companion.milliseconds

import kotlinx.coroutines.*

suspend fun main() {
    withContext(Dispatchers.Default) {
        performBackgroundWork()
    }
}

suspend fun performBackgroundWork() = coroutineScope { // this: CoroutineScope
    // Запускает корутину, которая выполняется без блокировки области видимости
    this.launch {
        // Приостанавливает выполнение, имитируя фоновую работу
        delay(100.milliseconds)
        println("Sending notification in background")
    }

    // Основная корутина продолжает работу, пока предыдущая приостановлена
    println("Scope continues")
}
```

После запуска этого примера видно, что функция `main()` не блокируется `CoroutineScope.launch()` и продолжает выполнять другой код, пока корутина работает в фоне.

> Функция `CoroutineScope.launch()` возвращает дескриптор [`Job`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/).
> Используйте этот дескриптор, чтобы дождаться завершения запущенной корутины.
> Подробнее см. в разделе [Отмена корутин и тайм-ауты](cancellation-and-timeouts.html#cancel-coroutines).

<a name="coroutinescope-async"></a>

### `CoroutineScope.async()`

Функция-конструктор корутин [`CoroutineScope.async()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html) является функцией-расширением для `CoroutineScope`.
Она запускает конкурентное вычисление внутри существующей [области видимости корутин](#coroutine-scope-and-structured-concurrency) и возвращает дескриптор [`Deferred`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-deferred/), представляющий будущий результат.
Используйте функцию `.await()`, чтобы приостановить код до готовности результата:

```kotlin
// Импорт kotlin.time.Duration для выражения длительности в миллисекундах
import kotlin.time.Duration.Companion.milliseconds

import kotlinx.coroutines.*

suspend fun main() = withContext(Dispatchers.Default) { // this: CoroutineScope
    // Начинает загрузку первой страницы
    val firstPage = this.async {
        delay(50.milliseconds)
        "First page"
    }

    // Начинает параллельную загрузку второй страницы
    val secondPage = this.async {
        delay(100.milliseconds)
        "Second page"
    }

    // Ожидает оба результата и сравнивает их
    val pagesAreEqual = firstPage.await() == secondPage.await()
    println("Pages are equal: $pagesAreEqual")
}
```

<a name="runblocking"></a>

### `runBlocking()`

Функция-конструктор корутин [`runBlocking()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-blocking.html) создаёт область видимости корутин и блокирует текущий [поток](#comparing-coroutines-and-jvm-threads), пока не завершатся корутины, запущенные в этой области видимости.

Используйте `runBlocking()` только когда нет другого способа вызвать приостанавливающий код из неприостанавливающего:

```kotlin
import kotlin.time.Duration.Companion.milliseconds
import kotlinx.coroutines.*

// Сторонний интерфейс, который нельзя изменить
interface Repository {
    fun readItem(): Int
}

object MyRepository : Repository {
    override fun readItem(): Int {
        // Мост к функции приостановки
        return runBlocking {
            myReadItem()
        }
    }
}

suspend fun myReadItem(): Int {
    delay(100.milliseconds)
    return 4
}
```

<a name="coroutine-dispatchers"></a>

## Диспетчеры корутин

[_Диспетчер корутин_](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/#) управляет тем, какой поток или пул потоков используют корутины для выполнения.
Корутины не всегда привязаны к одному потоку.
Они могут приостановиться в одном потоке и возобновиться в другом, в зависимости от диспетчера.
Это позволяет выполнять много корутин одновременно, не выделяя отдельный поток для каждой корутины.

> Хотя корутины могут приостанавливаться и возобновляться в разных потоках, значения, записанные перед приостановкой корутины, гарантированно доступны в той же корутине после её возобновления.

Диспетчер работает вместе с [областью видимости корутин](#coroutine-scope-and-structured-concurrency), чтобы определить, когда и где выполняются корутины.
Область видимости корутин управляет жизненным циклом корутины, а диспетчер — потоками, которые используются для выполнения.

> Вам не нужно указывать диспетчер для каждой корутины.
> По умолчанию корутины наследуют диспетчер от родительской области видимости.
> Вы можете указать диспетчер, чтобы выполнить корутину в другом контексте.
>
> Если контекст корутины не содержит диспетчер, конструкторы корутин используют `Dispatchers.Default`.

Библиотека `kotlinx.coroutines` включает разные диспетчеры для разных сценариев.
Например, [`Dispatchers.Default`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-default.html) выполняет корутины в общем пуле потоков, выполняя фоновую работу отдельно от основного потока.
Это хороший выбор для CPU-интенсивных операций, например обработки данных.

Чтобы указать диспетчер для конструктора корутин вроде `CoroutineScope.launch()`, передайте его как аргумент:

```kotlin
suspend fun runWithDispatcher() = coroutineScope { // this: CoroutineScope
    this.launch(Dispatchers.Default) {
        println("Running on ${Thread.currentThread().name}")
    }
}
```

Также можно использовать блок `withContext()`, чтобы выполнить весь код внутри него с заданным диспетчером:

```kotlin
// Импорт kotlin.time.Duration для выражения длительности в миллисекундах
import kotlin.time.Duration.Companion.milliseconds

import kotlinx.coroutines.*

suspend fun main() = withContext(Dispatchers.Default) { // this: CoroutineScope
    println("Running withContext block on ${Thread.currentThread().name}")

    val one = this.async {
        println("First calculation starting on ${Thread.currentThread().name}")
        val sum = (1L..500_000L).sum()
        delay(200L)
        println("First calculation done on ${Thread.currentThread().name}")
        sum
    }

    val two = this.async {
        println("Second calculation starting on ${Thread.currentThread().name}")
        val sum = (500_001L..1_000_000L).sum()
        println("Second calculation done on ${Thread.currentThread().name}")
        sum
    }

    // Ожидает оба вычисления и выводит результат
    println("Combined total: ${one.await() + two.await()}")
}
```

Чтобы узнать больше о диспетчерах корутин и их использовании, включая другие диспетчеры вроде [`Dispatchers.IO`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-i-o.html) и [`Dispatchers.Main`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-main.html), см. [Контекст корутин и диспетчеры](coroutine-context-and-dispatchers.html).

<a name="comparing-coroutines-and-jvm-threads"></a>

## Сравнение корутин и JVM-потоков

Хотя корутины — это приостанавливаемые вычисления, которые выполняют код конкурентно, как потоки на JVM, внутри они работают иначе.

_Потоком_ управляет операционная система.
Потоки могут выполнять задачи параллельно на нескольких ядрах CPU и являются стандартным подходом к конкурентному выполнению на JVM.
Когда вы создаёте поток, операционная система выделяет память для его стека и использует ядро для переключения между потоками.
Это делает потоки мощным, но ресурсоёмким механизмом.
Каждому потоку обычно нужно несколько мегабайт памяти, поэтому JVM, как правило, может одновременно обслуживать только несколько тысяч потоков.

Корутина, напротив, не привязана к конкретному потоку.
Она может приостановиться в одном потоке и возобновиться в другом, поэтому множество корутин могут разделять один и тот же пул потоков.
Когда корутина приостанавливается, поток не блокируется и остаётся свободным для других задач.
Это делает корутины гораздо легче потоков и позволяет запускать миллионы корутин в одном процессе без исчерпания системных ресурсов.

![Сравнение корутин и потоков](https://kotlinlang.org/docs/images/coroutines-and-threads.svg)

Рассмотрим пример, где 50 000 корутин ждут пять секунд, а затем каждая печатает точку (`.`):

```kotlin
import kotlin.time.Duration.Companion.seconds
import kotlinx.coroutines.*

suspend fun main() {
    withContext(Dispatchers.Default) {
        // Запускает 50 000 корутин, каждая из которых ждёт пять секунд, а затем печатает точку
        printPeriods()
    }
}

suspend fun printPeriods() = coroutineScope { // this: CoroutineScope
    // Запускает 50 000 корутин, каждая из которых ждёт пять секунд, а затем печатает точку
    repeat(50_000) {
        this.launch {
            delay(5.seconds)
            print(".")
        }
    }
}
```

Теперь посмотрим на тот же пример с JVM-потоками:

```kotlin
import kotlin.concurrent.thread

fun main() {
    repeat(50_000) {
        thread {
            Thread.sleep(5000L)
            print(".")
        }
    }
}
```

При запуске эта версия использует гораздо больше памяти, потому что каждому потоку нужен собственный стек.
Для 50 000 потоков это может потребовать до 100 ГБ памяти, тогда как для того же количества корутин нужно примерно 500 МБ.

В зависимости от операционной системы, версии JDK и настроек JVM версия с потоками может выбросить ошибку нехватки памяти или замедлить создание потоков, чтобы не запускать слишком много потоков одновременно.

<a name="what-s-next"></a>

## Что дальше

* Узнайте больше о комбинировании функций приостановки в разделе [Составление функций приостановки](composing-suspending-functions.html).
* Узнайте, как отменять корутины и обрабатывать тайм-ауты, в разделе [Отмена корутин и тайм-ауты](cancellation-and-timeouts.html).
* Глубже изучите выполнение корутин и управление потоками в разделе [Контекст корутин и диспетчеры](coroutine-context-and-dispatchers.html).
* Узнайте, как возвращать несколько асинхронно вычисляемых значений, в разделе [Асинхронные потоки](https://kotlinlang.org/docs/flow.html).
