---
type: doc
layout: reference
title: "Начало работы с Kotlin Multiplatform"
url: https://kotlinlang.ru/docs/multiplatform-get-started.html
---

<!-- При переводе статьи оригинальная версия была от 02 February 2022 -->

<!-- # Get started with Kotlin Multiplatform -->
# Начало работы с Kotlin Multiplatform

<!-- > Multiplatform projects are in [Alpha](components-stability.md). Language features and tooling may change in future Kotlin versions. -->
> Мультиплатформенные проекты находятся в [стадии альфа-тестирования](components-stability.html). Особенности языка и
> инструментарий могут быть изменены в будущих версиях Kotlin.

<!-- Support for multiplatform programming is one of Kotlin’s key benefits. It reduces time spent writing and maintaining the 
same code for [different platforms](mpp-supported-platforms.md) while retaining the flexibility and benefits of native programming. -->
Поддержка мультиплатформенного программирования является одним из ключевых преимуществ Kotlin. Она сокращает время,
затрачиваемое на написание и поддержку одного и того же кода для [разных платформ](mpp-supported-platforms.html),
сохраняя при этом гибкость и плюсы нативного программирования.

<!-- Learn more about [Kotlin Multiplatform benefits](multiplatform.md). -->
Узнайте больше про [преимущества использования Kotlin Multiplatform](multiplatform.html).

<a name="start-from-scratch"></a>

<!-- ## Start from scratch -->
## Начало с нуля

<!-- * [Create and publish a multiplatform library](multiplatform-library.md) teaches how to create a multiplatform 
library available for JVM, JS, and Native and which can be used from any other common code (for example, shared with 
Android and iOS). It also shows how to write tests which will be executed on all platforms and use an efficient implementation
 provided by a specific platform.

* [Build a Full Stack Web App with Kotlin Multiplatform](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction) 
  teaches the concepts behind building an application that targets Kotlin/JVM and Kotlin/JS by building a client-server 
  application that makes use of shared code, serialization, and other multiplatform paradigms. It also provides a brief
  introduction to working with Ktor both as a server- and client-side framework.

* [Create your first Kotlin Multiplatform Mobile application](kmm-create-first-app.md) shows how to create a mobile
  application that works on Android and iOS with the help of the [KMM plugin for Android Studio](https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform-mobile).
  Create, run, and test your first multiplatform mobile application. -->

* ["Создание и публикация мультиплатформенной библиотеки"](multiplatform-library.html) научит процессу создания
мультиплатформенной библиотеки для JVM, JS и Native, которую можно использовать из любого другого общего кода
(например в Android и iOS). Здесь также описано, как писать тесты, которые будут выполняться на всех платформах и использовать
эффективную реализацию, предоставляемую конкретной платформой;
* ["Создание фулстек веб-приложения с помощью Kotlin Multiplatform"](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)
обучает концепциям построения приложения для Kotlin/JVM и Kotlin/JS на архитектуре клиент-сервер, использующего общий
код, сериализацию и другие мультиплатформенные парадигмы. Также предоставляется краткое введение в работу с Ktor как на
стороне сервера, так и на стороне клиента;
* ["Создание вашего первого Kotlin Multiplatform Mobile приложения"](kmm-create-first-app.html): узнайте, как создать
мобильное приложение, работающее на Android и iOS, с помощью
[KMM плагина для Android Studio](https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform-mobile). Создайте,
запустите и протестируйте свое первое мультиплатформенное мобильное приложение.

<a name="dive-deep-into-kotlin-multiplatform"></a>

<!-- ## Dive deep into Kotlin Multiplatform -->
## Глубокое погружение в Kotlin Multiplatform

<!-- Once you have gained some experience with Kotlin Multiplatform and want to know how to solve particular cross-platform development tasks: -->
Как только вы получите первый опыт работы с Kotlin Multiplatform и захотите узнать, как решать конкретные задачи
кроссплатформенной разработки:

<!-- * [Share code on platforms](mpp-share-on-platforms.md) in your Kotlin Multiplatform project.

* [Connect to platform-specific APIs](mpp-connect-to-apis.md) using the Kotlin mechanism of expected and actual declarations.

* [Set up targets manually](mpp-set-up-targets.md) for your Kotlin Multiplatform project.

* [Add dependencies](mpp-add-dependencies.md) on the standard, test, or another kotlinx library.

* [Configure compilations](mpp-configure-compilations.md) for production and test purposes in your project.

* [Run tests](mpp-run-tests.md) for JVM, JavaScript, Android, Linux, Windows, macOS, iOS, watchOS, and tvOS simulators.

* [Publish a multiplatform library](mpp-publish-lib.md) to the Maven repository.

* [Build native binaries](mpp-build-native-binaries.md) as executables or shared libraries, like universal frameworks or XCFrameworks. -->

* [Используйте один код на платформах](mpp-share-on-platforms.md) в своем проекте Kotlin Multiplatform;
* [Подключайтесь к API конкретной платформы](mpp-connect-to-apis.md), используя механизм ожидаемых и фактических
объявлений Kotlin;
* [Настройте целевые показатели вручную](mpp-set-up-targets.md) для вашего проекта Kotlin Multiplatform;
* [Добавьте зависимости](mpp-add-dependencies.md) от стандартной, тестовой или другой библиотеки kotlinx;
* [Настройте компиляции](mpp-configure-compilations.md) для продакшена и тестовых целей в вашем проекте;
* [Запустите тесты](mpp-run-tests.md) для симуляторов JVM, JavaScript, Android, Linux, Windows, macOS, iOS, watchOS и
tvOS;
* [Опубликуйте мультиплатформенную библиотеку](mpp-publish-lib.md) в репозитории Maven;
* [Создавайте нативные двоичные файлы](mpp-build-native-binaries.md) в виде исполняемых файлов или общих библиотек,
таких как universal framework или XCFrameworks.

<a name="get-help"></a>

<!-- ## Get help -->
## Получить помощь

<!-- * **Kotlin Slack**: Get an [invite](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) and join the [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU) channel
* **StackOverflow**: Subscribe to the [“kotlin-multiplatform” tag](https://stackoverflow.com/questions/tagged/kotlin-multiplatform)
* **Kotlin issue tracker**: [Report a new issue](https://youtrack.jetbrains.com/newIssue?project=KT) -->

* **Kotlin Slack**: получите [приглашение](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) и присоединитесь к
каналу [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU);
* **StackOverflow**: подпишитесь на [метку «kotlin-multiplatform»](https://stackoverflow.com/questions/tagged/kotlin-multiplatform);
* **Kotlin issue tracker**: [сообщите о новой проблеме](https://youtrack.jetbrains.com/newIssue?project=KT).
