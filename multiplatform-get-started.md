---
type: doc
layout: reference
title: "Начало работы с Kotlin Multiplatform"
url: https://kotlinlang.ru/docs/multiplatform-get-started.html
---

<!-- Статья актуализирована по Kotlin Multiplatform get started, проверенному 24 May 2026: https://kotlinlang.org/docs/multiplatform/get-started.html -->

<!-- # Get started with Kotlin Multiplatform -->
# Начало работы с Kotlin Multiplatform

Делитесь UI с Compose Multiplatform или оставляйте его нативным. Начинайте новый проект с нуля или переносите
существующее Android-приложение. Kotlin Multiplatform позволяет выбрать подход под вашу команду и продукт.

<a name="quickstart"></a>

## Быстрый старт

* [Kotlin Multiplatform quickstart](https://kotlinlang.org/docs/multiplatform/quickstart.html): настройте окружение и
  запустите первый KMP-проект.
* [Что такое Kotlin Multiplatform](https://kotlinlang.org/docs/multiplatform/kmp-overview.html): узнайте о возможностях
  и преимуществах KMP.

<a name="start-from-scratch"></a>

## Популярные сценарии

* [Разделять и логику, и UI](https://kotlinlang.org/docs/multiplatform/compose-multiplatform-create-first-app.html):
  создайте приложение с общим UI для Android, iOS, desktop и web с помощью Compose Multiplatform.
* [Разделять логику, но оставить UI нативным](https://kotlinlang.org/docs/multiplatform/multiplatform-create-first-app.html):
  создайте мобильное приложение в IDE, которое работает на Android и iOS.
* [Разделить только часть логики](https://kotlinlang.org/docs/multiplatform/multiplatform-ktor-sqldelight.html):
  вынесите общий слой доступа к данным между Android и iOS.
* [Сделать существующее приложение мультиплатформенным](https://kotlinlang.org/docs/multiplatform/multiplatform-integrate-in-existing-app.html):
  перенесите Android-приложение на Kotlin Multiplatform для Android и iOS.

<a name="technology"></a>

## Технология Kotlin Multiplatform

* [Почему стоит выбрать Kotlin Multiplatform](https://kotlinlang.org/docs/multiplatform/kmp-overview.html): что такое
  KMP, какие у него преимущества и сценарии применения.
* [Ключевые понятия структуры проекта](https://kotlinlang.org/docs/multiplatform/multiplatform-discover-project.html):
  common- и platform-specific-код, targets и source sets.
* [Ожидаемые и фактические объявления](https://kotlinlang.org/docs/multiplatform/multiplatform-expect-actual.html):
  доступ к platform-specific API через `expect`/`actual`.
* [Интеграция с iOS](https://kotlinlang.org/docs/multiplatform/multiplatform-ios-integration-overview.html):
  подключение общего KMP-модуля к iOS-приложению.

<a name="tools"></a>

## Инструменты

* [Kotlin Multiplatform IDE plugin](https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform): поддержка KMP в IDE.
* [Compose Hot Reload](https://www.jetbrains.com/help/kotlin-multiplatform-dev/compose-hot-reload.html): быстрый цикл
  правок UI в Compose Multiplatform.
* [Gradle DSL reference](https://kotlinlang.org/docs/multiplatform/multiplatform-dsl-reference.html): справочник по
  Gradle DSL для KMP-проектов.
* [klibs.io](https://klibs.io/): поиск мультиплатформенных библиотек.
* [New project wizard](https://kmp.jetbrains.com/): создание KMP-проекта через web-мастер.
* [Amper](https://kotlinlang.org/docs/multiplatform/amper.html): конфигурация мультиплатформенных проектов.

<a name="tutorials-and-samples"></a>

## Руководства и примеры

* [Тестирование KMP-приложения](https://kotlinlang.org/docs/multiplatform/multiplatform-run-tests.html).
* [Подборка примеров](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-samples.html).
* [Проекты с темой `kotlin-multiplatform-sample`](https://github.com/topics/kotlin-multiplatform-sample) на GitHub.

<a name="get-help"></a>

## Получить помощь

* **Kotlin Slack**: получите [приглашение](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) и присоединитесь к
  каналу [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU).
* **Kotlin Multiplatform Tooling issue tracker**:
  [сообщите о новой проблеме](https://youtrack.jetbrains.com/newIssue?project=KT).
