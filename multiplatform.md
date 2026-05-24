---
type: doc
layout: reference
title: "Kotlin Multiplatform"
url: https://kotlinlang.ru/docs/multiplatform.html
---

<!-- При переводе статьи оригинальная версия была от 18 November 2025 -->

# Что такое Kotlin Multiplatform

Kotlin Multiplatform (KMP) — это open-source технология JetBrains, которая позволяет переиспользовать код для Android,
iOS, desktop, web и server-side разработки, сохраняя преимущества нативной разработки.

С [Compose Multiplatform](https://kotlinlang.org/docs/multiplatform/compose-multiplatform-and-jetpack-compose.html)
можно также переиспользовать UI-код на нескольких платформах и добиться максимального повторного использования кода.
Статус поддержки платформ описан в разделе
[Stability of supported platforms](https://kotlinlang.org/docs/multiplatform/supported-platforms.html).

## Почему компании выбирают KMP

### Экономия затрат и более быстрая поставка

Kotlin Multiplatform помогает упростить и технические, и организационные процессы:

* Вы можете сократить дублирование и расходы на сопровождение, переиспользуя логику и UI-код на разных платформах.
  Это также позволяет выпускать функции одновременно для нескольких платформ.
* Командная работа становится проще, потому что единая логика доступна в общем коде. Это упрощает передачу знаний между
  участниками команды и снижает дублирование усилий между платформенными командами.

Помимо более быстрого вывода продукта на рынок, по результатам KMP Survey Q2 2024 55% пользователей сообщили об
улучшении совместной работы после внедрения KMP, а 65% команд — об улучшении производительности и качества.

KMP используется в production в организациях разного масштаба: от стартапов до глобальных компаний. Google, Duolingo,
Forbes, Philips, McDonald's, Bolt, H&M, Baidu, Kuaishou, Bilibili и другие команды выбирают KMP за гибкость, нативную
производительность, возможность создавать нативный пользовательский опыт, экономичность и поддержку постепенного
внедрения. Подробнее см. на странице
[кейсов внедрения Kotlin](https://kotlinlang.org/case-studies/?type=multiplatform).

### Гибкость переиспользования кода

Вы сами выбираете, какой код делать общим: можно начать с отдельных модулей, например networking или storage, и
постепенно расширять общий код. Можно переиспользовать всю бизнес-логику, оставив UI нативным, или постепенно переносить
UI на Compose Multiplatform.

### Нативный опыт на iOS

UI можно полностью писать на SwiftUI или UIKit, создавать единый опыт для Android и iOS с помощью Compose Multiplatform
или комбинировать нативный и общий UI-код в нужных частях приложения.

Какой бы подход вы ни выбрали, приложение может ощущаться нативным на каждой платформе.

### Нативная производительность

Kotlin Multiplatform использует [Kotlin/Native](native-overview.html), чтобы создавать нативные бинарные файлы и
напрямую обращаться к API платформы там, где виртуальные машины нежелательны или невозможны, например на iOS.

Это помогает получать близкую к нативной производительность, продолжая писать платформенно-независимый код.

### Удобные инструменты

IntelliJ IDEA и Android Studio предоставляют умную поддержку KMP с
[Kotlin Multiplatform IDE plugin](https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform), общие UI previews,
[hot reload для Compose Multiplatform](https://kotlinlang.org/docs/multiplatform/compose-hot-reload.html), навигацию
между языками, рефакторинги и отладку Kotlin- и Swift-кода.

### Разработка с поддержкой AI

[Junie](https://www.jetbrains.com/junie/) — AI coding agent от JetBrains — может брать на себя задачи по KMP и помогать
команде двигаться быстрее.

## Изучите сценарии Kotlin Multiplatform

Посмотрите, как компании и разработчики уже используют преимущества общего Kotlin-кода:

* Узнайте, как компании внедряют KMP в своих кодовых базах, на
  [странице кейсов](https://kotlinlang.org/case-studies/?type=multiplatform).
* Изучите подборку
  [примеров приложений](https://kotlinlang.org/docs/multiplatform/multiplatform-samples.html) и GitHub-тему
  [kotlin-multiplatform-sample](https://github.com/topics/kotlin-multiplatform-sample).
* Найдите подходящие мультиплатформенные библиотеки среди тысяч пакетов на
  [klibs.io](https://klibs.io/).

## Изучите основы

Чтобы быстро увидеть KMP в действии, пройдите
[quickstart](https://kotlinlang.org/docs/multiplatform/quickstart.html): вы настроите окружение и запустите пример
приложения на разных платформах.

Выберите сценарий:

* Чтобы создать приложение с общей бизнес-логикой и общим UI-кодом, пройдите
  [руководство по shared logic and UI](https://kotlinlang.org/docs/multiplatform/compose-multiplatform-create-first-app.html).
* Чтобы превратить Android-приложение в мультиплатформенное, изучите
  [руководство по миграции](https://kotlinlang.org/docs/multiplatform/multiplatform-integrate-in-existing-app.html).
* Чтобы переиспользовать часть кода без общего UI, пройдите
  [руководство по shared logic](https://kotlinlang.org/docs/multiplatform/multiplatform-create-first-app.html).

Углубитесь в технические детали:

* Начните с
  [базовой структуры проекта](https://kotlinlang.org/docs/multiplatform/multiplatform-discover-project.html).
* Изучите доступные
  [механизмы переиспользования кода](https://kotlinlang.org/docs/multiplatform/multiplatform-share-on-platforms.html).
* Разберитесь, как в KMP-проекте работают
  [зависимости](multiplatform-add-dependencies.html).
* Рассмотрите разные
  [способы интеграции с iOS](https://kotlinlang.org/docs/multiplatform/multiplatform-ios-integration-overview.html).
* Узнайте, как KMP
  [компилирует код](https://kotlinlang.org/docs/multiplatform/multiplatform-configure-compilations.html) и
  [собирает бинарные файлы](https://kotlinlang.org/docs/multiplatform/multiplatform-build-native-binaries.html) для
  разных targets.
* Прочитайте о публикации
  [мультиплатформенного приложения](https://kotlinlang.org/docs/multiplatform/multiplatform-publish-apps.html) или
  [мультиплатформенной библиотеки](https://kotlinlang.org/docs/multiplatform/multiplatform-publish-lib-setup.html).

## Внедрите Kotlin Multiplatform в масштабе

Внедрение кроссплатформенного фреймворка в команде может быть непростой задачей. Чтобы узнать о преимуществах и
решениях потенциальных проблем, посмотрите обзорные материалы:

* [Что такое кроссплатформенная мобильная разработка?](https://kotlinlang.org/docs/multiplatform/cross-platform-mobile-development.html)
  Обзор подходов и реализаций для кроссплатформенных приложений.
* [Как представить multiplatform mobile development своей команде](https://kotlinlang.org/docs/multiplatform/multiplatform-introduce-your-team.html)
  Стратегии внедрения кроссплатформенной разработки в команде.
* [Десять причин выбрать Kotlin Multiplatform](https://kotlinlang.org/docs/multiplatform/multiplatform-reasons-to-try.html)
  Причины рассмотреть KMP как решение для кроссплатформенной разработки.

## Что дальше

* [Начните работу с Kotlin Multiplatform](https://kotlinlang.org/docs/multiplatform/get-started.html).
* [Изучите стабильность поддерживаемых платформ](https://kotlinlang.org/docs/multiplatform/supported-platforms.html).
