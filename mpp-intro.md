---
type: doc
layout: reference
title: "Kotlin Multiplatform"
url: https://kotlinlang.ru/docs/mpp-intro.html
---

<!-- При переводе статьи оригинальная версия была от 13 September 2021 -->

# Kotlin Multiplatform

<!-- > Multiplatform projects are in [Alpha](components-stability.md). Language features and tooling may change in future Kotlin versions. -->
> Мультиплатформенные проекты находятся в [стадии альфа-тестирования](components-stability.md). Особенности языка и
> инструментарий могут быть изменены в будущих версиях Kotlin.

<!-- Support for multiplatform programming is one of Kotlin’s key benefits. It reduces time spent writing and maintaining the
 same code for [different platforms](mpp-supported-platforms.md) while retaining the flexibility and benefits of native programming. 
 Learn more about [Kotlin Multiplatform benefits](multiplatform.md). -->
Поддержка мультиплатформенного программирования является одним из ключевых преимуществ Kotlin. Она сокращает время,
затрачиваемое на написание и поддержку одного и того же кода для [разных платформ](mpp-supported-platforms.html),
сохраняя при этом гибкость и преимущества нативного программирования. Узнайте больше про
[преимущества использования Kotlin Multiplatform](multiplatform.html).

<!-- With Kotlin Multiplatform, share the code using the mechanisms Kotlin provides: -->
С Kotlin Multiplatform вы можете переиспользовать один код несколько раз с помощью механизмов, которые предоставляет
Kotlin:

<!-- *   [Share code among all platforms used in your project](mpp-share-on-platforms.md#share-code-on-all-platforms). Use it for sharing the common 
business logic that applies to all platforms. 
     
    ![Code shared for all platforms](flat-structure.png)
    
*   [Share code among some platforms](mpp-share-on-platforms.md#share-code-on-similar-platforms) included in your project but not all. You can 
reuse much of the code in similar platforms using a hierarchical structure. You can use [target shortcuts](mpp-share-on-platforms.md#use-target-shortcuts) 
for common combinations of targets or [create the hierarchical structure manually](mpp-share-on-platforms.md#configure-the-hierarchical-structure-manually).
    
    ![Hierarchical structure](hierarchical-structure.png) -->

* [Использование одного кода на всех платформах, которые задействованы в вашем проекте](mpp-share-on-platforms.html#share-code-on-all-platforms).
Используйте это для общей бизнес-логики, применимой ко всем платформам;
    <img src="https://kotlinlang.org/docs/images/flat-structure.png" alt="Общий код для всех платформ" title="Общий код для всех платформ">

* [Использование одного кода только на некоторых платформах](mpp-share-on-platforms.html#share-code-on-similar-platforms),
которые включены в ваш проект, а не на всех. Вы можете повторно использовать большую часть кода на идентичных
платформах, используя иерархическую структуру. Можно объединить такие платформы [целевым ярлыком](mpp-share-on-platforms.html#use-target-shortcuts)
или [самостоятельно создать иерархию](mpp-share-on-platforms.html#configure-the-hierarchical-structure-manually).
    <img src="https://kotlinlang.org/docs/images/hierarchical-structure.png" alt="Иерархическая структура" title="Иерархическая структура">

<!-- If you need to access platform-specific APIs from the shared code, use the Kotlin mechanism of [expected and actual 
declarations](mpp-connect-to-apis.md). -->
Если вам нужно получить доступ к API, которые ограничены конкретной платформой, используйте механизм
[ожидаемых и фактических объявлений](mpp-connect-to-apis.html).

<a name="tutorials"></a>

<!-- ## Tutorials -->
## Руководства

<!-- * [Creating a multiplatform Kotlin library](multiplatform-library.md) teaches how to create a multiplatform 
library available for JVM, JS, and Native and which can be used from any other common code (for example, shared with 
Android and iOS). It also shows how to write tests which will be executed on all platforms and use an efficient implementation
 provided by a specific platform.
 
* [Building a Full Stack Web App with Kotlin Multiplatform](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction) 
  teaches the concepts behind building an application that targets Kotlin/JVM and Kotlin/JS by building a client-server 
  application that makes use of shared code, serialization, and other multiplatform paradigms. It also provides a brief
  introduction to working with Ktor both as a server- and client-side framework. -->

* ["Создание мультиплатформенной Kotlin библиотеки"](multiplatform-library.html) научит процессу создания
мультиплатформенной библиотеки для JVM, JS и Native, которую можно использовать из любого другого общего кода
(например в Android и iOS). Здесь также описано, как писать тесты, которые будут выполняться на всех платформах и использовать
эффективную реализацию, предоставляемую конкретной платформой.
* ["Создание фулстек веб-приложения с помощью Kotlin Multiplatform"](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)
обучает концепциям построения приложения для Kotlin/JVM и Kotlin/JS посредством создания клиент-серверной приложения,
использующего общий код, сериализацию и другие мультиплатформенные парадигмы. Также предоставляется краткое введение в
работу с Ktor как на стороне сервера, так и клиента.
