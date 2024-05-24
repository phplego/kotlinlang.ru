---
type: doc
layout: reference
title: "Kotlin Multiplatform"
url: https://kotlinlang.ru/docs/multiplatform.html
---

<!-- При переводе статьи оригинальная версия была от 28 February 2022 -->

<!-- # Kotlin Multiplatform -->
# Kotlin Multiplatform

<!-- > Multiplatform projects are in [Alpha](components-stability.md). Language features and tooling may change in future Kotlin versions. -->
> Мультиплатформенные проекты находятся в [стадии альфа-тестирования](components-stability.html). Особенности языка и
> инструментарий могут быть изменены в будущих версиях Kotlin.

<!-- Support for multiplatform programming is one of Kotlin’s key benefits. It reduces time spent writing and maintaining the
same code for [different platforms](multiplatform-dsl-reference.md#targets) while retaining the flexibility and benefits of native programming. -->
Поддержка мультиплатформенного программирования является одним из ключевых преимуществ Kotlin. Она сокращает время,
затрачиваемое на написание и поддержку одного и того же кода для [разных платформ](multiplatform-supported-platforms.html),
сохраняя при этом гибкость и преимущества нативного программирования.

<a name="kotlin-multiplatform-use-cases"></a>

<!-- ## Kotlin Multiplatform use cases -->
## Примеры использования Kotlin Multiplatform

<a name="android-and-ios-applications"></a>

<!-- ### Android and iOS applications -->
### Android и iOS приложения

<!-- Sharing code between mobile platforms is one of the major Kotlin Multiplatform use cases. With Kotlin Multiplatform Mobile,
you can build cross-platform mobile applications and share common code between Android and iOS, such as business logic, connectivity,
and more. -->
Использование одного кода на всех мобильных платформах является одним из основных вариантов использования Kotlin
Multiplatform. С помощью Kotlin Multiplatform Mobile вы можете создавать мультиплатформенные мобильные приложения,
совместно использующие один код и в Android, и в iOS, например бизнес-логику, возможности подключения и многое другое.

<!-- Check out the [Get started with Kotlin Multiplatform Mobile](https://kotlinlang.org/lp/mobile/) section and
[Kotlin Multiplatform Hands-on: Networking and Data Storage](https://play.kotlinlang.org/hands-on/Networking%20and%20Data%20Storage%20with%20Kotlin%20Multiplatfrom%20Mobile/01_Introduction),
where you will create an application for Android and iOS that includes a module with shared code for both platforms. -->
Ознакомьтесь с разделом [Начало работы с Kotlin Multiplatform Mobile](https://kotlinlang.org/lp/mobile/) и
[Kotlin Multiplatform Hands-on: Сети и хранение данных](https://play.kotlinlang.org/hands-on/Networking%20and%20Data%20Storage%20with%20Kotlin%20Multiplatfrom%20Mobile/01_Introduction),
где вы создадите приложение для Android и iOS, включающее модуль с общим кодом для обеих платформ.

<a name="full-stack-web-applications"></a>

<!-- ### Full-stack web applications -->
### Фулстек веб-приложения

<!-- Another scenario when code sharing may bring benefits is a connected application where the logic can be
reused on both the server and the client side running in the browser. This is covered by Kotlin
Multiplatform as well. -->
Другой сценарий, когда совместное использование кода может принести пользу, — это приложение, в котором логика может
быть повторно использована как на стороне сервера, так и на стороне клиента, запущенного в браузере. С этим Kotlin
Multiplatform тоже прекрасно справляется.

<!-- See [Build a Full Stack Web App with Kotlin Multiplatform](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)
hands-on, where you will create a connected application consisting of a server part, using Kotlin/JVM and a web client,
using Kotlin/JS. -->
См. руководство [Создание фулстек веб-приложения с помощью Kotlin Multiplatform](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction),
где вы создадите приложение, состоящее из серверной части с использованием Kotlin/JVM и веб-клиента с использованием
Kotlin/JS.

<a name="multiplatform-libraries"></a>

<!-- ### Multiplatform libraries -->
### Мультиплатформенные библиотеки

<!-- Kotlin Multiplatform is also useful for library authors. You can create a multiplatform library with common code and its
platform-specific implementations for JVM, JS, and Native platforms. Once published, a multiplatform library can be used
in other cross-platform projects as a dependency. -->
Kotlin Multiplatform также отлично подойдет для авторов библиотек. Вы можете создать мультиплатформенную библиотеку с
общим кодом и ее платформенными реализациями для JVM, JS и собственных платформ. После публикации мультиплатформенная
библиотека может использоваться в других кроссплатформенных проектах в качестве зависимости.

<!-- See the [Create and publish a multiplatform library](multiplatform-library.md) tutorial, where you will create
a multiplatform library, test it, and publish it to Maven. -->
См. руководство [Создание и публикация мультиплатформенной библиотеки](multiplatform-library.html), в котором вы
создадите мультиплатформенную библиотеку, протестируете ее и опубликуете в Maven.

<a name="common-code-for-mobile-and-web-applications"></a>

<!-- ### Common code for mobile and web applications -->
### Общий код для мобильных и веб-приложений

<!-- One more popular case for using Kotlin Multiplatform is sharing the same code across Android, iOS, and web apps. It
reduces the amount of business logic coded by frontend developers and helps implement products more efficiently,
decreasing the coding and testing efforts. -->
Еще одним популярным примером использования Kotlin Multiplatform является совместное использование одного и того же кода
в Android, iOS и веб-приложениях. Это сокращает объем бизнес-логики, написанной фронтенд-разработчиками, и помогает
более эффективно внедрять продукты, сокращая затраты на разработку и тестирование.

<!-- See the [RSS Reader](https://github.com/Kotlin/kmm-production-sample/tree/c6a0d9182802490d17729ae634fb59268f68a447) sample
project — a cross-platform application for iOS and Android with desktop and web clients implemented as experimental features. -->
Смотрите пример проекта [RSS Reader](https://github.com/Kotlin/kmm-production-sample/tree/c6a0d9182802490d17729ae634fb59268f68a447 ) —
кроссплатформенное приложение для iOS и Android с десктоп- и веб-клиентами, реализованными в качестве экспериментальных
функций.

<a name="how-kotlin-multiplatform-works"></a>

<!-- ## How Kotlin Multiplatform works -->
## Как работает Kotlin Multiplatform

<img src="https://kotlinlang.org/docs/images/kotlin-multiplatform.png" alt="Kotlin Multiplatform" title="Kotlin Multiplatform">

<!-- * **Common Kotlin** includes the language, core libraries, and basic tools. Code written in common Kotlin works
everywhere on all platforms.
* With Kotlin Multiplatform libraries, you can reuse the multiplatform logic in common and platform-specific code.
Common code can rely on a set of libraries that cover everyday tasks such as [HTTP](https://ktor.io/clients/http-client/multiplatform.html), [serialization](https://github.com/Kotlin/kotlinx.serialization), and [managing
coroutines](https://github.com/Kotlin/kotlinx.coroutines).
* To interop with platforms, use platform-specific versions of Kotlin. **Platform-specific versions of Kotlin**
(Kotlin/JVM, Kotlin/JS, Kotlin/Native) include extensions to the Kotlin language, and platform-specific libraries and tools.
* Through these platforms you can access the **platform native code** (JVM, JS, and Native) and leverage all native
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

<a name="code-sharing-between-platforms"></a>

<!-- ### Code sharing between platforms -->
### Использование кода на разных платформах

<!-- With Kotlin Multiplatform, spend less time on writing and maintaining the same code for [different platforms](multiplatform-dsl-reference.md#targets)
– just share it using the mechanisms Kotlin provides: -->
Благодаря Kotlin Multiplatform тратьте меньше времени на написание и поддержку одного и того же кода для [разных платформ](multiplatform-dsl-reference.html#targets) –
просто поделитесь им, используя механизмы, предоставляемые Kotlin:

<!-- * [Share code among all platforms used in your project](multiplatform-share-on-platforms.md#share-code-on-all-platforms). Use it for sharing the common
business logic that applies to all platforms.

* [Share code among some platforms](multiplatform-share-on-platforms.md#share-code-on-similar-platforms) included in your project but not all. Do this
when you can reuse much of the code in similar platforms:

    ![Code shared for iOS targets](kotlin-multiplatofrm-hierarchical-structure.png){width=700}

* If you need to access platform-specific APIs from the shared code, use the Kotlin mechanism of [expected and actual
declarations](multiplatform-connect-to-apis.md). -->

* [Использование одного кода на всех платформах, которые задействованы в вашем проекте](multiplatform-share-on-platforms.html#share-code-on-all-platforms).
Используйте это для общей бизнес-логики, применимой ко всем платформам;

* [Использование одного кода только на некоторых платформах](mpp-share-on-platforms.html#share-code-on-similar-platforms),
которые включены в ваш проект, а не на всех. Поступайте так, когда вы можете повторно использовать большую часть кода на
аналогичных платформах.

    <img src="https://kotlinlang.org/docs/images/kotlin-multiplatofrm-hierarchical-structure.png" alt="Общий код для iOS" title="Общий код для iOS">

* Если вам нужно получить доступ к API-интерфейсам, зависящим от платформы, из общего кода, используйте механизм Kotlin
[ожидаемых и фактических объявлений](multiplatform-connect-to-apis.html).

<a name="get-started"></a>

<!-- ## Get started -->
## Начало работы

<!-- * Look through [our examples and tutorials](multiplatform-share-on-platforms.md) if you want to create applications or libraries targeting JVM, JavaScript, and other platforms
* Start with the [Get started with Kotlin Multiplatform Mobile](multiplatform-mobile-getting-started.md) if you want to create iOS and Android applications with shared code -->

* Ознакомьтесь с [нашими примерами и руководствами](multiplatform-share-on-platforms.html), если вы хотите создавать
приложения или библиотеки, ориентированные на JVM, JavaScript и другие платформы;
* Начните с [Начало работы с Kotlin Multiplatform Mobile](multiplatform-mobile-getting-started.html), если вы хотите
создавать приложения для iOS и Android с общим кодом.

<!-- > New to Kotlin? Take a look at [Getting started with Kotlin](getting-started.md). -->
> Недавно начали изучать Kotlin? Посетите [Начало работы с Kotlin](getting-started.html).

<a name="sample-projects"></a>

<!-- ### Sample projects -->
### Примеры проектов

<!-- Look through cross-platform application samples to understand how Kotlin Multiplatform works: -->
Просмотрите примеры кроссплатформенных приложений, чтобы понять, как работает Kotlin Multiplatform:

<!-- * [Kotlin Multiplatform Mobile samples](multiplatform-mobile-samples.md)
* [KotlinConf app](https://github.com/JetBrains/kotlinconf-app)
* [KotlinConf Spinner app](https://github.com/jetbrains/kotlinconf-spinner)
* [Build a Full Stack Web App with Kotlin Multiplatform hands-on](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction) -->

* [Примеры Kotlin Multiplatform Mobile (KMM)](kmm-samples.html)
* [Приложение KotlinConf](https://github.com/JetBrains/kotlinconf-app)
* [Приложение KotlinConf Spinner](https://github.com/jetbrains/kotlinconf-spinner)
* [Создайте фулстек веб-приложение с помощью Kotlin Multiplatform hands-on](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)

