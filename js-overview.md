---
type: doc
layout: reference
title: "Kotlin/JavaScript"
url: https://kotlinlang.ru/docs/js-overview.html
---

<!-- При переводе статьи оригинальная версия была от 01 October 2025 -->

<!-- # Kotlin/JavaScript -->
# Kotlin/JavaScript

Kotlin/JavaScript (Kotlin/JS) позволяет транспилировать Kotlin-код, стандартную библиотеку Kotlin и совместимые
зависимости в JavaScript. Благодаря этому Kotlin-приложения могут работать в любой среде, где поддерживается JavaScript.

Используйте Kotlin/JS через
[Kotlin Multiplatform Gradle plugin](https://kotlinlang.org/docs/multiplatform.html) (`kotlin.multiplatform`), чтобы
настраивать Kotlin-проекты с JavaScript-целью и управлять ими из одного места. Плагин Kotlin Multiplatform Gradle даёт
доступ к возможностям вроде управления сборкой приложения и добавления JavaScript-зависимостей напрямую из npm.
Обзор доступных параметров см. в разделе
[Настройка Kotlin/JS-проекта](https://kotlinlang.org/docs/js-project-setup.html).

Текущая реализация Kotlin/JS ориентирована на стандарты [ES5](https://www.ecma-international.org/ecma-262/5.1/) и
[ES2015](https://262.ecma-international.org/6.0/).

<a name="use-cases-for-kotlin-js"></a>

## Сценарии использования Kotlin/JS

Ниже перечислены распространённые способы использовать Kotlin/JS.

* **Разделение общей логики между frontend и JVM backend.**

  Если backend написан на Kotlin или другом JVM-совместимом языке, вы можете разделять общий код между web-приложением
  и backend. Это могут быть data-transfer objects (DTO), правила валидации и аутентификации, абстракции для REST API
  endpoints и другой общий код.

* **Разделение общей логики между Android, iOS и web-клиентами.**

  Вы можете разделять бизнес-логику между web-интерфейсом и мобильными приложениями для Android и iOS, сохраняя
  нативные пользовательские интерфейсы. Это помогает не дублировать REST API-абстракции, аутентификацию пользователей,
  валидацию форм и доменные модели.

* **Создание frontend web-приложений на Kotlin/JS.**

  Используйте Kotlin для разработки традиционных web frontend и интеграции с существующими инструментами и
  библиотеками:

  * Если вы знакомы с Android-разработкой, можно создавать web-приложения с Compose-based фреймворками вроде
    [Kobweb](https://kobweb.varabyte.com/) или [Kilua](https://kilua.dev/).
  * Полностью типобезопасные React-приложения можно писать с помощью
    [Kotlin wrappers for common JavaScript libraries](https://github.com/JetBrains/kotlin-wrappers) от JetBrains.
    `kotlin-wrappers` предоставляют абстракции и интеграции для React и других JavaScript-фреймворков, а также
    поддерживают сопутствующие библиотеки вроде [React Redux](https://react-redux.js.org/),
    [React Router](https://reactrouter.com/) и [styled-components](https://styled-components.com/).
  * Можно использовать [фреймворки Kotlin/JS](#kotlin-js-frameworks), которые интегрируются с экосистемой Kotlin и
    поддерживают лаконичный выразительный код.

* **Создание multiplatform-приложений с поддержкой старых браузеров.**

  С Compose Multiplatform можно строить приложения на Kotlin и переиспользовать мобильные и desktop UI в web-проектах.
  Для этого основная цель — [Kotlin/Wasm](https://kotlinlang.org/docs/wasm-overview.html), но для поддержки старых
  браузеров можно дополнительно нацеливаться на Kotlin/JS.

* **Создание server-side и serverless-приложений на Kotlin/JS.**

  Node.js-цель в Kotlin/JS позволяет создавать приложения для серверных и serverless-сред на JavaScript runtime.
  Это даёт быстрый старт и низкое потребление памяти. Библиотека
  [`kotlinx-nodejs`](https://github.com/Kotlin/kotlinx-nodejs) предоставляет типобезопасный доступ к
  [Node.js API](https://nodejs.org/docs/latest/api/) из Kotlin.

В зависимости от сценария Kotlin/JS-проекты могут использовать совместимые библиотеки из экосистемы Kotlin и сторонние
библиотеки из экосистем JavaScript и TypeScript. Для использования сторонних библиотек из Kotlin-кода можно создать
собственные типобезопасные wrappers, использовать поддерживаемые сообществом wrappers или применить
[динамический тип](dynamic-type.html). `dynamic` позволяет обойти строгую типизацию и не создавать подробные wrappers,
но снижает типобезопасность.

Kotlin/JS совместим с наиболее распространёнными модульными системами:
[ESM](https://tc39.es/ecma262/#sec-modules), [CommonJS](https://nodejs.org/api/modules.html),
[UMD](https://github.com/umdjs/umd) и [AMD](https://github.com/amdjs/amdjs-api). Это позволяет
[создавать и потреблять модули](https://kotlinlang.org/docs/js-modules.html) и структурированно интегрироваться с
JavaScript-экосистемой.

<a name="share-your-use-cases"></a>

### Поделитесь своими сценариями

Список сценариев использования Kotlin/JS не исчерпывающий. Экспериментируйте с разными подходами и выбирайте то, что
лучше подходит вашему проекту.

Сценарии, опыт и вопросы можно обсуждать с сообществом Kotlin/JS в канале
[#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) в
[Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up).

<a name="get-started-with-kotlin-js"></a>

## Начало работы с Kotlin/JS

Изучите основы и первые шаги для работы с Kotlin/JS:

* Если вы только начинаете изучать Kotlin, начните с [базового синтаксиса](basic-syntax.html) и
  [Kotlin tour](https://kotlinlang.org/docs/kotlin-tour-welcome.html).
* Посмотрите список [примеров проектов Kotlin/JS](#sample-projects-for-kotlin-js) для вдохновения. В них есть полезные
  фрагменты кода и шаблоны, которые могут помочь начать собственный проект.
* Если вы только начинаете работать с Kotlin/JS, сначала пройдите
  [руководство по настройке](https://kotlinlang.org/docs/js-project-setup.html), а затем переходите к более сложным
  темам.

<a name="sample-projects-for-kotlin-js"></a>

## Примеры проектов Kotlin/JS

Ниже перечислены проекты, демонстрирующие разные сценарии Kotlin/JS, архитектуры и стратегии переиспользования кода.

| Проект | Описание |
|---|---|
| [Petclinic with common code between Spring and Angular](https://github.com/Kotlin/kmp-spring-petclinic) | Показывает, как избегать дублирования в enterprise-приложениях за счёт общих DTO, правил валидации и аутентификации, а также REST API-абстракций между [Spring Boot](https://spring.io/projects/spring-boot) backend и [Angular](https://angular.dev/) frontend. |
| [Fullstack Conference CMS](https://github.com/Kotlin/kmp-fullstack-conference-cms) | Демонстрирует несколько подходов к code sharing: от простого до полного разделения кода между [Ktor](https://ktor.io/), [Jetpack Compose](https://developer.android.com/jetpack/compose) и [Vue.js](https://vuejs.org/) приложениями. |
| [Todo App on a Compose-HTML-based Kobweb framework](https://github.com/Kotlin/kmp-web-wizard-kobweb-template) | Показывает, как создать todo-приложение с подходом, знакомым Android-разработчикам. Клиентский UI построен на [Kobweb](https://kobweb.varabyte.com/). |
| [Simple logic sharing between Android, iOS, and web](https://github.com/Kotlin/kmp-basic-sample) | Содержит шаблон проекта с общей Kotlin-логикой, которая используется в platform-native UI приложениях на Android ([Jetpack Compose](https://developer.android.com/jetpack/compose)), iOS ([SwiftUI](https://developer.apple.com/xcode/swiftui/)) и web ([React](https://react.dev/)). |
| [Full-stack collaborative to-do list](https://github.com/Kotlin/full-stack-spring-collaborative-todo-list-sample) | Показывает, как создать совместный todo-list с Kotlin Multiplatform, JS- и JVM-целями, [Ktor](https://ktor.io/) на backend и Kotlin/JS с React на frontend. |

<a name="kotlin-js-frameworks"></a>

## Фреймворки Kotlin/JS

Фреймворки Kotlin/JS упрощают web-разработку: предоставляют готовые компоненты, routing, state management и другие
инструменты для современных web-приложений.

Посмотрите [доступные фреймворки Kotlin/JS](https://kotlinlang.org/docs/js-frameworks.html), написанные разными
авторами.

<a name="join-the-kotlin-js-community"></a>

## Присоединяйтесь к сообществу Kotlin/JS

Присоединяйтесь к каналу [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) в официальном
[Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up), чтобы общаться с сообществом и командой
Kotlin/JS.

<a name="whats-next"></a>

## Что дальше

* [Настройка Kotlin/JS-проекта](https://kotlinlang.org/docs/js-project-setup.html).
* [Запуск Kotlin/JS-проектов](https://kotlinlang.org/docs/running-kotlin-js.html).
* [Отладка Kotlin/JS-кода](https://kotlinlang.org/docs/debugging-kotlin-js.html).
* [Запуск тестов в Kotlin/JS](https://kotlinlang.org/docs/js-running-tests.html).
