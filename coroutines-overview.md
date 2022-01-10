---
type: doc
layout: reference
title: "Корутины"
url: https://kotlinlang.ru/docs/coroutines-overview.html
---
      
<!-- При переводе статьи оригинальная версия была от 09 July 2021 -->

<!-- # Coroutines -->
# Корутины

<!-- Asynchronous or non-blocking programming is an important part of the development landscape. When creating server-side,
desktop, or mobile applications, it's important to provide an experience that is not only fluid from the user's perspective,
but also scalable when needed. -->
Асинхронное или неблокирующее программирование является важной частью разработки. При создании серверных, десктопных или
мобильных приложений важно обеспечить интерфейс не только гибкий с точки зрения пользователя, но и масштабируемый при
необходимости.

<!-- Kotlin solves this problem in a flexible way by providing [coroutine](https://en.wikipedia.org/wiki/Coroutine) support
at the language level and delegating most of the functionality to libraries. -->
Kotlin решает эту проблему гибким способом, предоставляя поддержку [корутинов](https://en.wikipedia.org/wiki/Coroutine)
на уровне языка и делегирование большей части функциональности библиотекам.

<!-- In addition to opening the doors to asynchronous programming, coroutines also provide a wealth of other possibilities,
such as concurrency and actors. -->
В дополнение к возможности асинхронного программирования, корутины также предоставляют множество других возможностей,
таких как параллелизм и акторы (действующие субъекты, ориг.: *actors*).

<a name="how-to-start"></a>

<!-- ## How to start -->
## Как начать

<!-- New to Kotlin? Take a look at the [Getting started](getting-started.md) page. -->

<a name="documentation"></a>

<!-- ### Documentation -->
### Документация

<!-- - [Coroutines guide](coroutines-guide.md)
- [Basics](coroutines-basics.md)
- [Channels](channels.md)
- [Coroutine context and dispatchers](coroutine-context-and-dispatchers.md)
- [Shared mutable state and concurrency](shared-mutable-state-and-concurrency.md)
- [Asynchronous flow](flow.md) -->

* [Руководство по корутинам](coroutines-guide.html)
* [Основы](coroutines-basics.html)
* [Каналы](channels.html)
* [Контекст корутинов и диспетчеры](coroutine-context-and-dispatchers.html)
* [Разделяемое изменяемое состояние и параллелизм](shared-mutable-state-and-concurrency.html)
* [Асинхронный поток](flow.html)

<a name="tutorials"></a>

<!-- ### Tutorials -->
### Руководства

<!-- - [Asynchronous programming techniques](async-programming.md)
- [Introduction to coroutines and channels](https://play.kotlinlang.org/hands-on/Introduction%20to%20Coroutines%20and%20Channels/01_Introduction)
- [Debug coroutines using IntelliJ IDEA](debug-coroutines-with-idea.md)
- [Debug Kotlin Flow using IntelliJ IDEA – tutorial](debug-flow-with-idea.md) -->

* [Методы асинхронного программирования](async-programming.html)
* [Введение в корутины и каналы](https://play.kotlinlang.org/hands-on/Introduction%20to%20Coroutines%20and%20Channels/01_Introduction)
* [Отладка корутинов с помощью IntelliJ IDEA](debug-coroutines-with-idea.html)
* [Отладка Kotlin Flow с помощью IntelliJ IDEA – руководство](debug-flow-with-idea.html)

<a name="sample-projects"></a>

<!-- ## Sample projects -->
## Примеры проектов

<!-- - [kotlinx.coroutines examples and sources](https://github.com/Kotlin/kotlin-coroutines/tree/master/examples)
- [KotlinConf app](https://github.com/JetBrains/kotlinconf-app) -->

* [kotlinx.coroutines примеры и источники](https://github.com/Kotlin/kotlin-coroutines/tree/master/examples)
* [Приложение KotlinConf](https://github.com/JetBrains/kotlinconf-app)
