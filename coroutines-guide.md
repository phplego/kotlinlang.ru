---
type: doc
layout: reference
title: "Руководство по корутинам"
url: https://kotlinlang.ru/docs/coroutines-guide.html
---

<!-- При переводе статьи оригинальная версия была от 26 August 2021 -->

<!-- # Coroutines guide -->
# Руководство по корутинам

<!-- Kotlin, as a language, provides only minimal low-level APIs in its standard library to enable various other 
libraries to utilize coroutines. Unlike many other languages with similar capabilities, `async` and `await`
are not keywords in Kotlin and are not even part of its standard library. Moreover, Kotlin's concept
of _suspending function_ provides a safer and less error-prone abstraction for asynchronous 
operations than futures and promises. -->
Kotlin, как язык, предоставляет только минимальные низкоуровневые API в своей стандартной библиотеке, чтобы позволить
другим библиотекам использовать корутины. В отличие от многих других языков с аналогичными возможностями, `async` и
`await` не являются ключевыми в Kotlin и даже не входят в его стандартную библиотеку. Более того, концепция
`suspend` функций Kotlin обеспечивает более безопасную и менее подверженную ошибкам абстракцию для асинхронных операций,
чем Future и Promise.

<!-- `kotlinx.coroutines` is a rich library for coroutines developed by JetBrains. It contains a number of high-level 
coroutine-enabled primitives that this guide covers, including `launch`, `async` and others. -->
`kotlinx.coroutines` — это богатая библиотека для корутин, разработанная JetBrains. Она содержит ряд высокоуровневых
примитивов с поддержкой корутин, которые рассматриваются в этом руководстве, включая `launch`, `async` и другие.

<!-- This is a guide on core features of `kotlinx.coroutines` with a series of examples, divided up into different topics. -->
Это руководство по основным функциям `kotlinx.coroutines` с серией примеров, разделенных на разные темы.

<!-- In order to use coroutines as well as follow the examples in this guide, you need to add a dependency on the `kotlinx-coroutines-core` module as explained
[in the project README](https://github.com/Kotlin/kotlinx.coroutines/blob/master/README.md#using-in-your-projects). -->
Чтобы использовать корутины, а также следовать примерам из этого руководства, вам необходимо добавить зависимость от модуля `kotlinx-coroutines-core`, как описано в [README проекта](https://github.com/Kotlin/kotlinx.coroutines/blob/master/README.md#using-in-your-projects).

<a name="table-of-contents"></a>

<!-- ## Table of contents -->
## Содержание

<!-- 
* [Coroutines basics](coroutines-basics.md)
* [Hands-on: Intro to coroutines and channels](https://play.kotlinlang.org/hands-on/Introduction%20to%20Coroutines%20and%20Channels)
* [Cancellation and timeouts](cancellation-and-timeouts.md)
* [Composing suspending functions](composing-suspending-functions.md)
* [Coroutine context and dispatchers](coroutine-context-and-dispatchers.md)
* [Asynchronous Flow](flow.md)
* [Channels](channels.md)
* [Coroutine exceptions handling](exception-handling.md)
* [Shared mutable state and concurrency](shared-mutable-state-and-concurrency.md)
* [Select expression (experimental)](select-expression.md)
* [Tutorial: Debug coroutines using IntelliJ IDEA](debug-coroutines-with-idea.md)
* [Tutorial: Debug Kotlin Flow using IntelliJ IDEA](debug-flow-with-idea.md)
-->

* [Основы корутин](coroutines-basics.html)
* [Практика: Введение в корутины и каналы](https://play.kotlinlang.org/hands-on/Introduction%20to%20Coroutines%20and%20Channels)
* [Отмена корутин и тайм-ауты](cancellation-and-timeouts.html)
* [Создание suspending-функций](composing-suspending-functions.html)
* [Контекст корутин и диспатчеры](coroutine-context-and-dispatchers.html)
* [Асинхронный Flow](flow.html)
* [Каналы](channels.html)
* [Обработка исключений в корутинах](exception-handling.html)
* [Общее изменяемое состояние и параллелизм](shared-mutable-state-and-concurrency.html)
* [Select-выражение (экспериментальное)](select-expression.html)
* [Туториал: Дебаггинг корутин с использованием IntelliJ IDEA](debug-coroutines-with-idea.html)
* [Туториал: Дебаггинг Flow с использованием IntelliJ IDEA](debug-flow-with-idea.html)

<a name="additional-references"></a>

<!-- ## Additional references -->
## Дополнительные ссылки

<!-- 
* [Guide to UI programming with coroutines](https://github.com/Kotlin/kotlinx.coroutines/blob/master/ui/coroutines-guide-ui.md)
* [Coroutines design document (KEEP)](https://github.com/Kotlin/KEEP/blob/master/proposals/coroutines.md)
* [Full kotlinx.coroutines API reference](https://kotlin.github.io/kotlinx.coroutines)
* [Best practices for coroutines in Android](https://developer.android.com/kotlin/coroutines/coroutines-best-practices)
* [Additional Android resources for Kotlin coroutines and flow](https://developer.android.com/kotlin/coroutines/additional-resources)
-->

* [Руководство по программированию UI с корутинами](https://github.com/Kotlin/kotlinx.coroutines/blob/master/ui/coroutines-guide-ui.md)
* [Документ по дизайну корутин (KEEP)](https://github.com/Kotlin/KEEP/blob/master/proposals/coroutines.md)
* [Полная справка по API kotlinx.coroutines](https://kotlin.github.io/kotlinx.coroutines)
* [Практика использования корутин в Android](https://developer.android.com/kotlin/coroutines/coroutines-best-practices)
* [Дополнительные ресурсы по Android с использованием корутин и flow](https://developer.android.com/kotlin/coroutines/additional-resources)
