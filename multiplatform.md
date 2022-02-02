---
type: doc
layout: reference
title: "Мультиплатформенные проекты"
url: https://kotlinlang.ru/docs/multiplatform.html
---

<!-- При переводе статьи оригинальная версия была от 13 September 2021 -->

<!-- # Multiplatform programming -->
# Мультиплатформенное программирование

<!-- > Multiplatform projects are in [Alpha](components-stability.md). Language features and tooling may change in future Kotlin versions. -->
> Мультиплатформенные проекты находятся в [стадии альфа-тестирования](components-stability.html). Особенности языка и
> инструментарий могут быть изменены в будущих версиях Kotlin.

<!-- Support for multiplatform programming is one of Kotlin’s key benefits. It reduces time spent writing and maintaining the
same code for [different platforms](mpp-supported-platforms.md) while retaining the flexibility and benefits of native programming. -->
Поддержка мультиплатформенного программирования является одним из ключевых преимуществ Kotlin. Она сокращает время,
затрачиваемое на написание и поддержку одного и того же кода для [разных платформ](mpp-supported-platforms.html),
сохраняя при этом гибкость и преимущества нативного программирования.

<!-- This is how Kotlin Multiplatform works. -->
Вот как работает мультиплатформенность в Kotlin:

<img src="https://kotlinlang.org/docs/images/kotlin-multiplatform.png" alt="Мультиплатформенность Kotlin" title="Мультиплатформенность Kotlin">

<!-- *   **Common Kotlin** includes the language, core libraries, and basic tools. Code written in common Kotlin works 
everywhere on all platforms.
*   With Kotlin Multiplatform libraries, you can reuse the multiplatform logic in common and platform-specific code. 
Common code can rely on a set of libraries that cover everyday tasks such as [HTTP](https://ktor.io/clients/http-client/multiplatform.html), [serialization](https://github.com/Kotlin/kotlinx.serialization), and [managing 
coroutines](https://github.com/Kotlin/kotlinx.coroutines).
*   To interop with platforms, use platform-specific versions of Kotlin. **Platform-specific versions of Kotlin** 
(Kotlin/JVM, Kotlin/JS, Kotlin/Native) include extensions to the Kotlin language, and platform-specific libraries and tools. 
*   Through these platforms you can access the **platform native code** (JVM, JS, and Native) and leverage all native
 capabilities. -->

* **Обычный Kotlin** включает в себя язык, основные библиотеки и базовые инструменты. Код, написанный на обычном Kotlin,
работает везде на всех платформах;
* С помощью мультиплатформенных библиотек Kotlin вы можете повторно использовать мультиплатформенную логику в обычном и
специфичном для платформы коде. Обычный код может полагаться на набор библиотек, которые охватывают повседневные задачи,
такие как [HTTP](https://ktor.io/clients/http-client/multiplatform.html),
[сериализация](https://github.com/Kotlin/kotlinx.serialization) и
[управление корутинами](https://github.com/Kotlin/kotlinx.coroutines);
* Для взаимодействия с платформами используйте версии Kotlin для конкретной платформы. **Версии Kotlin для конкретной
платформы** (Kotlin/JVM, Kotlin/JS, Kotlin/Native) включают расширения для языка Kotlin, а также библиотеки и
инструменты для конкретной платформы;
* С помощью этих платформ вы можете получить доступ к **встроенному коду платформы** (JVM, JS и Native) и использовать
все встроенные возможности.

<!-- With Kotlin Multiplatform, spend less time on writing and maintaining the same code for [different platforms](mpp-supported-platforms.md)
– just share it using the mechanisms Kotlin provides: -->
Благодаря мультиплатформенности Kotlin вы можете тратить меньше времени на написание и поддержку одного и того же кода
для [разных платформ](mpp-supported-platforms.html) – просто делитесь им, используя механизмы, предоставляемые Kotlin:

<!-- * [Share code among all platforms used in your project](mpp-share-on-platforms.md#share-code-on-all-platforms). Use it for sharing the common 
business logic that applies to all platforms. 
     
    ![Code shared for all platforms](flat-structure.png)
    
* [Share code among some platforms](mpp-share-on-platforms.md#share-code-on-similar-platforms) included in your project but not all. Do this 
when you can reuse much of the code in similar platforms.  
    
    ![Hierarchical structure](hierarchical-structure.png)

    ![Code shared for iOS targets](iosmain-hierarchy.png){width=400} -->

* [Использование одного кода на всех платформах, которые задействованы в вашем проекте](mpp-share-on-platforms.html#share-code-on-all-platforms).
Используйте это для общей бизнес-логики, применимой ко всем платформам;
    <img src="https://kotlinlang.org/docs/images/flat-structure.png" alt="Общий код для всех платформ" title="Общий код для всех платформ">

* [Использование одного кода только на некоторых платформах](mpp-share-on-platforms.html#share-code-on-similar-platforms),
которые включены в ваш проект, а не на всех. Поступайте так, когда вы можете повторно использовать большую часть кода на аналогичных платформах.
    <img src="https://kotlinlang.org/docs/images/hierarchical-structure.png" alt="Иерархическая структура" title="Иерархическая структура">
    <img src="https://kotlinlang.org/docs/images/iosmain-hierarchy.png" alt="Общий код для целей iOS" title="Общий код для целей iOS">

<!-- If you need to access platform-specific APIs from the shared code, use the Kotlin mechanism of [expected and actual 
declarations](mpp-connect-to-apis.md). -->
Если вам нужно получить доступ к API, которые ограничены конкретной платформой, используйте механизм
[ожидаемых и фактических объявлений](mpp-connect-to-apis.html).

<!-- With this mechanism, a common source set defines an _expected declaration_, and platform source sets must provide the 
_actual declaration_ that corresponds to the expected declaration. This works for most Kotlin declarations, such as 
functions, classes, interfaces, enumerations, properties, and annotations. -->
С помощью этого механизма общий набор источников определяет *ожидаемое объявление*, а наборы источников платформы должны
предоставлять *фактическое объявление*, соответствующее ожидаемому объявлению. Это работает для большинства объявлений
Kotlin, таких как функции, классы, интерфейсы, перечисления, свойства и аннотации.

<img src="https://kotlinlang.org/docs/images/expect-actual.png" alt="Ожидаемые и фактические объявления" title="Ожидаемые и фактические объявления">

```kotlin
//Common
expect fun randomUUID(): String
```

```kotlin
//Android
import java.util.*
actual fun randomUUID() = UUID.randomUUID().toString()
```

```kotlin
//iOS
import platform.Foundation.NSUUID
actual fun randomUUID(): String = NSUUID().UUIDString()
```

<a name="use-cases"></a>

<!-- ## Use cases -->
## Примеры использования

<a name="android-ios"></a>

### Android — iOS

<!-- Sharing code between mobile platforms is one of the major Kotlin Multiplatform use cases. With Kotlin Multiplatform Mobile (KMM), 
you can build multiplatform mobile applications sharing code, such as business logic, connectivity, 
and more, between Android and iOS. -->
Совместное использование кода между мобильными платформами является одним из основных вариантов использования
мультиплатформенности Kotlin. С помощью Kotlin Multiplatform Mobile (KMS) вы можете создавать мультиплатформенные
мобильные приложения, совместно использующие один код и в Android, и в iOS, например бизнес-логику, возможности
подключения и многое другое.

<!-- See [KMM features, case studies and examples](https://kotlinlang.org/lp/mobile/) -->
Смотрите [особенности KMM, примеры](https://kotlinlang.org/lp/mobile/).

<a name="client-server"></a>

<!-- ### Client — Server -->
### Клиент — Сервер

<!-- Another scenario when code sharing may bring benefits is a connected application where the logic can be 
reused on both the server and the client side running in the browser. This is covered by Kotlin 
Multiplatform as well. -->
Другой сценарий, когда совместное использование кода может принести пользу, - это подключенное приложение, в котором
логика может быть повторно использована как на стороне сервера, так и на стороне клиента, запущенного в браузере. Это
также распространяется на мультиплатформенность Kotlin.

<!-- The [Ktor framework](https://ktor.io/) is suitable for building asynchronous servers and clients in connected systems. -->
[Ktor framework](https://ktor.io/) подходит для создания асинхронных серверов и клиентов в связанных системах.

<a name="what-s-next"></a>

<!-- ## What's next? -->
## Что дальше?

<!-- New to Kotlin? Visit [Getting started with Kotlin](getting-started.md). -->
Недавно начали изучать Kotlin? Посетите [Начало работы с Kotlin](getting-started.html).

<a name="documentation"></a>

<!-- ### Documentation -->
### Документация

<!-- * [Get started with Kotlin Multiplatform Mobile (KMM)](kmm-getting-started.md)
* [Create a multiplatform project](mpp-create-lib.md)
* [Share code on multiple platforms](mpp-share-on-platforms.md)
* [Connect to platform-specific APIs](mpp-connect-to-apis.md) -->

* [Начало работы с Kotlin Multiplatform Mobile (KMM)](kmm-getting-started.html)
* [Создание мультиплатформенного проект](mpp-create-lib.html)
* [Совместное использование кода на нескольких платформах](mpp-share-on-platforms.html)
* [Подключение к специфичным для конкретной платформы API](mpp-connect-to-apis.html)

<a name="tutorials"></a>

<!-- ### Tutorials -->
### Руководства

<!-- * [Create your first KMM application](kmm-create-first-app.md) shows how to create a mobile application that works on Android and iOS with the help of the [KMM plugin for Android Studio](https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform-mobile).
Create, run, and test your first multiplatform mobile application.

* [Creating a multiplatform Kotlin library](multiplatform-library.md) teaches how to create a multiplatform 
library available for JVM, JS, and Native and which can be used from any other common code (for example, shared with 
Android and iOS). It also shows how to write tests which will be executed on all platforms and use an efficient implementation
 provided by a specific platform.

* [Building a full stack web app with Kotlin Multiplatform](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction) 
  teaches the concepts behind building an application that targets Kotlin/JVM and Kotlin/JS by building a client-server 
  application that makes use of shared code, serialization, and other multiplatform paradigms. It also provides a brief
  introduction to working with Ktor both as a server- and client-side framework. -->

* ["Создайте свое первое KMM приложение"](kmm-create-first-app.html): узнайте, как создать мобильное приложение,
работающее на Android и iOS, с помощью
[KMM плагина для Android Studio](https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform-mobile). Создайте,
запустите и протестируйте свое первое мультиплатформенное мобильное приложение;
* ["Создание мультиплатформенной Kotlin библиотеки"](multiplatform-library.html) научит процессу создания
мультиплатформенной библиотеки для JVM, JS и Native, которую можно использовать из любого другого общего кода
(например в Android и iOS). Здесь также описано, как писать тесты, которые будут выполняться на всех платформах и использовать
эффективную реализацию, предоставляемую конкретной платформой.
* ["Создание фулстек веб-приложения с помощью Kotlin Multiplatform"](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)
обучает концепциям построения приложения для Kotlin/JVM и Kotlin/JS посредством создания клиент-серверной приложения,
использующего общий код, сериализацию и другие мультиплатформенные парадигмы. Также предоставляется краткое введение в
работу с Ktor как на стороне сервера, так и клиента.

<a name="sample-projects"></a>

<!-- ## Sample projects -->
## Примеры проектов

<!-- - [Kotlin Multiplatform Mobile (KMM) samples](kmm-samples.md)
- [KotlinConf app](https://github.com/JetBrains/kotlinconf-app) 
- [KotlinConf Spinner app](https://github.com/jetbrains/kotlinconf-spinner) -->

* [Примеры Kotlin Multiplatform Mobile (KMM)](kmm-samples.html)
* [Приложение KotlinConf](https://github.com/JetBrains/kotlinconf-app)
* [Приложение KotlinConf Spinner](https://github.com/jetbrains/kotlinconf-spinner)
