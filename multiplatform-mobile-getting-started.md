---
type: doc
layout: reference
title: "Начало работы с Kotlin Multiplatform"
url: https://kotlinlang.ru/docs/multiplatform-mobile-getting-started.html
---

<!-- Статья актуализирована по What is Kotlin Multiplatform от 18 November 2025 и Kotlin Multiplatform quickstart от 15 May 2026. -->

<!-- Get started with Kotlin Multiplatform -->
# Начало работы с Kotlin Multiplatform

Kotlin Multiplatform (KMP) - это технология JetBrains с открытым исходным кодом, которая позволяет разделять код между
Android, iOS, desktop, web и server, сохраняя преимущества нативной разработки.

С помощью Compose Multiplatform можно разделять и UI-код, если проекту нужен максимальный уровень переиспользования.
При этом вы можете выбрать подход постепенно: разделять отдельные модули, например networking или storage, вынести всю
бизнес-логику в общий код и оставить UI нативным или постепенно переносить UI на Compose Multiplatform.

<a name="start-from-scratch"></a>

## Начало с нуля

Чтобы быстро увидеть KMP в работе, начните с [quickstart](https://kotlinlang.org/docs/multiplatform/quickstart.html).
В нём вы настроите окружение, создадите проект и запустите пример на разных платформах.

Для мобильной разработки обычно выбирают один из двух путей:

* Если вы хотите разделять и бизнес-логику, и UI между платформами, используйте руководство по
  [созданию Compose Multiplatform-приложения](https://kotlinlang.org/docs/multiplatform/compose-multiplatform-create-first-app.html).
* Если вам нужна общая логика, но UI должен оставаться нативным, используйте руководство по
  [созданию приложения с общей логикой и нативным UI](https://kotlinlang.org/docs/multiplatform/multiplatform-create-first-app.html).

<a name="supported-platforms"></a>

## Поддерживаемые платформы

Kotlin Multiplatform поддерживает Android, iOS, desktop, web и server-сценарии. Для подробностей о целевых платформах и
их статусе см. [стабильность поддерживаемых платформ](https://kotlinlang.org/docs/components-stability.html#stability-levels-explained)
и [документацию по Kotlin Multiplatform](https://kotlinlang.org/docs/multiplatform/kmp-overview.html).

<a name="setup"></a>

## Настройка окружения

Для работы с KMP установите IntelliJ IDEA или Android Studio и
[Kotlin Multiplatform IDE plugin](https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform). Для iOS-целей
понадобится macOS с Xcode, потому что сборка iOS-фреймворков выполняется через инструменты Apple.

Подробные шаги см. в разделе [Настройка среды](multiplatform-mobile-setup.html).

<a name="learn-the-basics"></a>

## Изучение основ

После первого запуска проекта можно перейти к техническим разделам:

* [структура проекта](https://kotlinlang.org/docs/multiplatform/multiplatform-discover-project.html);
* [механизмы разделения кода](https://kotlinlang.org/docs/multiplatform/multiplatform-share-on-platforms.html);
* [зависимости в KMP-проекте](https://kotlinlang.org/docs/multiplatform/multiplatform-add-dependencies.html);
* [методы интеграции с iOS](https://kotlinlang.org/docs/multiplatform/multiplatform-ios-integration-overview.html);
* [сборка бинарных файлов](https://kotlinlang.org/docs/multiplatform/multiplatform-build-native-binaries.html).

<a name="get-help"></a>

## Получить помощь

* **Kotlin Slack**: получите [приглашение](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) и присоединитесь к
  каналу [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU).
* **Kotlin Multiplatform Tooling issue tracker**:
  [сообщите о новой проблеме](https://youtrack.jetbrains.com/newIssue?project=KT).
