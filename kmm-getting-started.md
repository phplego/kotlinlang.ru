---
type: doc
layout: reference
title: "Начало работы с Kotlin Multiplatform Mobile"
url: https://kotlinlang.ru/docs/kmm-getting-started.html
---

<!-- При переводе статьи оригинальная версия была от 30 December 2021 -->

# Начало работы с Kotlin Multiplatform Mobile

<!-- > Kotlin Multiplatform Mobile is in [Alpha](components-stability.md). Language features and tooling may change in future
> Kotlin versions.
> 
> Beta is expected in spring 2022. Check out [Kotlin Multiplatform Mobile Beta Roadmap Video Highlights](https://blog.jetbrains.com/kotlin/2021/10/kmm-beta-roadmap-video-highlights/)
> to learn about upcoming features. You can also see how [different companies](https://kotlinlang.org/lp/mobile/case-studies/)
> already use Kotlin for cross-platform app development. -->
> Kotlin Multiplatform Mobile находится в [стадии альфа-тестирования](components-stability.html). Особенности языка и
> инструментарий могут быть изменены в будущих версиях Kotlin.
>
> Бета версия ожидается весной 2022 г. Посмотрите ["Ключевые моменты видео Kotlin Multiplatform Mobile Beta Roadmap"](https://blog.jetbrains.com/kotlin/2021/10/kmm-beta-roadmap-video-highlights/),
> чтобы узнать о планируемом функционале. Также вы можете изучить, как [разные компании](https://kotlinlang.org/lp/mobile/case-studies/)
> уже используют Kotlin для разработки кроссплатформенного программного обеспечения.

<!-- Kotlin Multiplatform Mobile (KMM) is an SDK designed to simplify the development of cross-platform mobile applications.
You can share common code between iOS and Android apps and write platform-specific code only where it's necessary.
For example, to implement a native UI or when working with platform-specific APIs. -->
Kotlin Multiplatform Mobile (KMM) - это средства разработки ПО, предназначенные для упрощения разработки
кроссплатформенных приложений для мобильных устройств. Вы можете использовать общий код в программах для iOS и Android и
писать код, специфичный для каждой из платформ, только там, где это необходимо. Например, чтобы реализовать нативный UI
или когда работаете с API, которые ограничены конкретной платформой.

<!-- Watch the introductory [video](https://www.youtube.com/watch?v=mdN6P6RI__k), in which Kotlin Product Marketing Manager Ekaterina 
Petrova explains what Kotlin Multiplatform Mobile is and how to use it in your project. With Ekaterina,
you'll set up an environment and prepare to create your first cross-platform mobile application: -->
Посмотрите вводное [видео](https://www.youtube.com/watch?v=mdN6P6RI__k), в котором менеджер по продуктовому маркетингу
Kotlin Екатерина Петрова объясняет, что такое Kotlin Multiplatform Mobile и как использовать его в вашем проекте. Вместе
с ней вы настроете среду разработки и подготовитесь к созданию вашего первого кроссплатформенных мобильного приложения.

<iframe width="560" height="315" src="https://www.youtube.com/embed/mdN6P6RI__k" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<!-- You can also check out other videos about [Kotlin Multiplatform Multiverse](https://www.youtube.com/playlist?list=PLlFc5cFwUnmy_oVc9YQzjasSNoAk4hk_C) on YouTube. -->
Также вы можете посмотреть другие видео о [Kotlin Multiplatform Multiverse](https://www.youtube.com/playlist?list=PLlFc5cFwUnmy_oVc9YQzjasSNoAk4hk_C)
на YouTube.

<!-- ## Supported platforms -->
## Поддерживаемые платформы

<!-- * Android applications and libraries
* [Android NDK](https://developer.android.com/ndk) (ARM64 and ARM32)
* Apple iOS devices (ARM64 and ARM32) and simulators
* Apple watchOS devices (ARM64 and ARM32) and simulators -->

* Приложения и библиотеки для Android,
* [Android NDK](https://developer.android.com/ndk) (ARM64 и ARM32),
* Устройства Apple iOS (ARM64 и ARM32) и симуляторы,
* Устройства Apple watchOS (ARM64 и ARM32) и симуляторы.

<!-- [Kotlin Multiplatform](multiplatform.md) technology also supports [other platforms](mpp-supported-platforms.md), including
JavaScript, Linux, Windows, and WebAssembly. -->
Технология [Kotlin Multiplatform](multiplatform.html) также поддерживает [другие платформы](mpp-supported-platforms.html),
включая JavaScript, Linux, Windows, и WebAssembly.

<!-- ## Start from scratch -->
## Начало с нуля

<!-- * [Set up the environment for cross-platform mobile development](kmm-setup.md)
* [Create your first app that works both on Android and iOS with IDE](kmm-create-first-app.md)
* [Check out the list of sample projects](kmm-samples.md)
* [Introduce cross-platform mobile development to your team](kmm-introduce-your-team.md) -->

* [Настройте окружение для кроссплатформенной мобильной разработки](kmm-setup.html),
* [Создайте в IDE ваше первое приложение, работующее и на Android, и на iOS](kmm-create-first-app.html),
* [Изучите примеры проектов](kmm-samples.html),
* [Познакомьте свою команду с кроссплатформенной мобильной разработкой](kmm-introduce-your-team.html).

<!-- ## Make your Android application work on iOS -->
## Настройте ваше Android приложение для работы на iOS

<!-- If you already have an Android mobile application and want to make it cross-platform, here are some resources to help
you get started: -->
Если у вас уже есть мобильное Android приложение и вы хотите сделать его кроссплатформенным, то вот что вам может помочь
начать:

<!-- * [Set up the environment for cross-platform development](kmm-setup.md)
* [Make a sample Android application work well on iOS](kmm-integrate-in-existing-app.md) -->

* [Настройте окружение для кроссплатформенной мобильной разработки](kmm-setup.html),
* [Сделайте так, чтобы пример Android приложения, хорошо работал под iOS](kmm-integrate-in-existing-app.html).

<!-- ## Get help -->
## Получить помощь

<!-- * **Kotlin Slack**: Get an [invite](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) and join the [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU) channel
* **StackOverflow**: Subscribe to the [“kotlin-multiplatform” tag](https://stackoverflow.com/questions/tagged/kotlin-multiplatform)
* **Kotlin issue tracker**: [Report a new issue](https://youtrack.jetbrains.com/newIssue?project=KT) -->

* **Kotlin Slack**: получите [приглашение](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) и присоединитесь к
каналу [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU);
* **StackOverflow**: подпишитесь на [метку «kotlin-multiplatform»](https://stackoverflow.com/questions/tagged/kotlin-multiplatform);
* **Kotlin issue tracker**: [сообщите о новой проблеме](https://youtrack.jetbrains.com/newIssue?project=KT).
