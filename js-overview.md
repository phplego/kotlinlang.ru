---
type: doc
layout: reference
title: "Kotlin для JavaScript"
url: https://kotlinlang.ru/docs/js-overview.html
---
      
<!-- При переводе статьи оригинальная версия была от 07 December 2021 -->

<!-- # Kotlin for JavaScript -->
# Kotlin для JavaScript

<!-- Kotlin/JS provides the ability to transpile your Kotlin code, the Kotlin standard library, and any compatible dependencies
to JavaScript. The current implementation of Kotlin/JS targets [ES5](https://www.ecma-international.org/ecma-262/5.1/). -->
Kotlin/JS предоставляет возможность переноса вашего кода Kotlin, стандартной библиотеки Kotlin и любых совместимых
зависимостей в JavaScript. Текущая реализация Kotlin/JS ориентирована на [ES5](https://www.ecma-international.org/ecma-262/5.1/).

<!-- The recommended way to use Kotlin/JS is via the `kotlin.js` and `kotlin.multiplatform` Gradle plugins. They let you easily set up and control Kotlin projects targeting JavaScript in one place. This includes essential functionality
such as controlling the bundling of your application, adding JavaScript dependencies directly from npm, and more. To get
an overview of the available options, check out the [Kotlin/JS project setup](js-project-setup.md) documentation. -->
Рекомендуемый способ использования Kotlin/JS - через `kotlin.js` и плагины Gradle `kotlin.multiplatform`. Они позволяют
в одном месте легко настраивать и управлять проектами Kotlin, ориентированными на JavaScript. Вы можете использовать все
основные функции: управление билдингом вашего приложения, добавление зависимостей JavaScript непосредственно из npm и
многое другое. Чтобы ознакомиться с доступными опциями, ознакомьтесь с
[документацией по настройке проекта Kotlin/JS](js-project-setup.html).

<a name="use-cases-for-kotlin-js"></a>

<!-- ## Use cases for Kotlin/JS -->
## Примеры использования Kotlin/JS

<!-- There are numerous ways to use Kotlin/JS. Here is a non-exhaustive list of
scenarios in which you can use Kotlin/JS: -->
Существует множество способов использования Kotlin/JS. Вот неполный список некоторых из них:

<!-- * **Write frontend web applications using Kotlin/JS**
    * Kotlin/JS allows you to **leverage powerful browser and web APIs** in a type-safe fashion. Create, modify, and interact
      with the elements in the Document Object Model (DOM), use Kotlin code to control the rendering of `canvas` or WebGL components,
      and enjoy access to many more features that modern browsers support.
    * Write **full, type-safe React applications with Kotlin/JS** using the [`kotlin-wrappers`](https://github.com/JetBrains/kotlin-wrappers)
      provided by JetBrains, which provide convenient abstractions and deep integrations for React and other popular JavaScript frameworks.
      `kotlin-wrappers` also provides support for a select number of adjacent technologies, like
      `react-redux`, `react-router`, and `styled-components`. Interoperability with the JavaScript ecosystem means that
      you can also use third-party React components and component libraries.
    * Use the **[Kotlin/JS frameworks](#kotlin-js-frameworks)**, which take full advantage of Kotlin concepts and its expressive power
      and conciseness.

* **Write server-side and serverless applications using Kotlin/JS**
    * The Node.js target provided by Kotlin/JS enables you to create applications that **run on a server** or are
      **executed on serverless infrastructure**. This gives you all the advantages of executing in a
      JavaScript runtime, such as **faster startup** and a **reduced memory footprint**. With [`kotlinx-nodejs`](https://github.com/Kotlin/kotlinx-nodejs),
      you have typesafe access to the [Node.js API](https://nodejs.org/docs/latest/api/) directly from your Kotlin code.

*  **Use Kotlin's [multiplatform](multiplatform.md) projects to share code with other Kotlin targets**
    * All the functionality of Kotlin/JS can also be accessed when using the Kotlin `multiplatform` Gradle plugin.
    * If your backend is written in Kotlin, you can **share common code** such as data models or validation logic
    with a frontend written in Kotlin/JS, which allows you to **write and maintain full-stack web applications**.
    * You can also **share business logic between your web interface and mobile apps** for Android and iOS, and avoid
    duplicating commonly used functionality, like providing abstractions around REST API endpoints, user authentication,
    or your domain models.

* **Create libraries for use with JavaScript and TypeScript**
    * You don't have to write your whole application in Kotlin/JS – instead, you can **generate libraries from your
      Kotlin code** that can be consumed as modules from any code base written in JavaScript or TypeScript, regardless of the
      other frameworks or technologies you use. This approach of **creating hybrid applications** allows you to leverage the
      competencies that you and your team might already have around web development while helping you **reduce the amount
      of duplicated work**, making it easier to keep your web target consistent with other targets of your application. -->

* **Написание фронтенда веб-приложений с использованием Kotlin/JS**
  * Kotlin/JS позволяет использовать **мощные браузерные и веб-API** типобезопасным способом. Создавайте, изменяйте и
  взаимодействуйте с элементами в Document Object Model (DOM), используйте Kotlin-код для управления отображением
  компонентов `canvas` или WebGL и наслаждайтесь многими другими функциями, поддерживаемыми современными браузерами.
  * Пишите **полноценные, типобезопасные React-приложения с Kotlin/JS**, используя
  [`kotlin-wrappers`](https://github.com/JetBrains/kotlin-wrappers), предоставляемую JetBrains, которая обеспечивает
  удобные абстракции и глубокую интеграцию React и других популярных фреймворков JavaScript. `kotlin-wrappers` также
  обеспечивает поддержку ряда смежных технологий, таких как `react-redux`, `react-router` и `styled-components`.
  Совместимость с экосистемой JavaScript означает, что вы также можете использовать сторонние React-компоненты и
  библиотеки компонентов.
  * Используйте **[фреймворки Kotlin/JS](#kotlin-js-frameworks)**, которые в полной мере используют концепции Kotlin,
  его выразительную мощь и лаконичность.

* **Написание серверных и бессерверных приложений с использованием Kotlin/JS**
  * Возможность использования Node.js, предоставляемая Kotlin/JS, позволяет создавать приложения, которые
  **выполняются на сервере** или **выполняются в бессерверной инфраструктуре**. Это дает вам все преимущества
  JavaScript, такие как **быстрый запуск** и **малый объем памяти**. С помощью
  [`kotlinx-nodejs`](https://github.com/Kotlin/kotlinx-nodejs) у вас есть типобезопасный доступ к
  [Node.js API](https://nodejs.org/docs/latest/api/) непосредственно из вашего Kotlin-кода.

* **[Мультиплатформенные](multiplatform.html) проекты Kotlin для импользования одного кода на нескольких платформах**
  * Все функциональные возможности Kotlin/JS также доступны при использовании плагина Gradle `multiplatform`.
  * Если ваш бэкенд написан на Kotlin, вы можете **использовать общий код**, такой как модели данных или логика валидации.
  С бэкендом, написанным на Kotlin/JS, вы можете **разрабатывать и поддерживать фулстек веб-приложения**.
  * Вы также можете **использовать одну бизнес-логику в веб-интерфейсе и мобильном приложении** для
  Android и iOS и избегать дублирования часто используемых функций, таких как предоставление абстракций вокруг REST API
  endpoint'ов, аутентификации пользователей или моделей вашего домена.

* **Создание библиотеки для использования с JavaScript и TypeScript**
  * Вам не нужно писать все свое приложение на Kotlin/JS – вместо этого вы можете **создавать библиотеки из своего
  Kotlin-кода**, которые можно использовать в качестве модулей из любой базы кода, написанной на JavaScript или
  TypeScript, независимо от других фреймворков или технологий, которые вы используете. Такой подход к **созданию
  гибридных приложений** позволяет вам использовать компетенции, которыми вы и ваша команда, возможно, уже обладаете в
  области веб-разработки, помогая при этом **сократить объем дублируемой работы**.

<!-- Of course, this is not a complete list of all the ways you can use Kotlin/JS to your advantage, but merely some cherry-picked
use cases. We invite you to experiment with different combinations and find out what works best for your project. -->
Конечно, это не полный список всего того, где вы можете использовать Kotlin/JS, а лишь некоторые тщательно отобранные
варианты использования. Мы предлагаем вам поэкспериментировать с различными комбинациями и выяснить, что лучше всего
подходит для вашего проекта.

<!-- Whatever your specific use case, Kotlin/JS projects can use compatible **libraries from the Kotlin ecosystem**,
as well as third-party **libraries from the JavaScript and TypeScript ecosystems**. To use the latter from Kotlin code,
you can either provide your own typesafe wrappers, use community-maintained wrappers, or let [Dukat](js-external-declarations-with-dukat.md)
automatically generate Kotlin declarations for you. Using the Kotlin/JS-exclusive [dynamic type](dynamic-type.md) allows
you to loosen the constraints of Kotlin's type system and skip creating detailed library wrappers, though this comes at the expense of type safety. -->
Независимо от того, как вы используете Kotlin/JS, ваши проекты могут использовать совместимые **библиотеки из экосистемы
Kotlin**, а также сторонние **библиотеки из экосистем JavaScript и TypeScript**. Чтобы использовать последнее из
Kotlin-кода, вы можете либо предоставить свои типобезопасные собственные обертки, использовать обертки, поддерживаемые
сообществом, либо позволить [Dukat](js-external-declarations-with-dukat.html) автоматически генерировать объявления
Kotlin. Использование Kotlin/JS-exclusive динамических типов позволяет ослабить ограничения системы типов Kotlin и
отказаться от создания подробных библиотечных оболочек, хотя это происходит за счет безопасности типов.

<!-- Kotlin/JS is also compatible with the most common module systems: UMD, CommonJS, and AMD. The ability to [produce and consume modules](js-modules.md)
means that you can interact with the JavaScript ecosystem in a structured manner. -->
Kotlin/JS также совместим с наиболее распространенными модульными системами: UMD, CommonJS и AMD. Возможность
[производить и потреблять модули](js-modules.html) означает, что вы можете взаимодействовать с экосистемой JavaScript
структурированным образом.

<a name="kotlin-js-frameworks"></a>

<!-- ## Kotlin/JS frameworks -->
## Kotlin/JS фреймворки

<!-- Modern web development benefits significantly from frameworks that simplify building web applications.
Here are a few examples of popular web frameworks for Kotlin/JS written by different authors: -->
Современная веб-разработка значительно выигрывает от фреймворков, которые упрощают создание веб-приложений.
Вот несколько примеров популярных веб-фреймворков для Kotlin/JS, написанных разными авторами:

<a name="kvision"></a>

### KVision

<!-- _KVision_ is an object-oriented web framework that makes it possible to write applications in Kotlin/JS with ready-to-use components
that can be used as building blocks for your application’s user interface. You can use both reactive and imperative programming
models to build your frontend, use connectors for Ktor, Spring Boot, and other frameworks to integrate it with your server-side
applications, and share code using [Kotlin Multiplatform](multiplatform.md). -->
*KVision* - это объектно-ориентированный веб-фреймворк, который позволяет писать приложения на Kotlin/JS с ready-to-use
компонентами, которые можно использовать в качестве строительных блоков для пользовательского интерфейса вашего
приложения. Вы можете использовать как реактивные, так и императивные модели программирования для создания своего
интерфейса, использовать соединители для Ktor, Spring Boot и других фреймворков для интеграции с вашими серверными
приложениями и обмениваться кодом с [Kotlin Multiplatform](multiplatform.html).

<!-- Visit [https://kvision.io](https://kvision.io) for documentation, tutorials, and examples. -->
Чтобы изучить документацию, учебные пособия и примеры, посетите [https://kvision.io](https://kvision.io).

<!-- For updates and discussions about the framework, join the [#kvision](https://kotlinlang.slack.com/messages/kvision) and
[#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) channels in the [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up). -->
Для обсуждения и получения обновлений фреймворка присоединяйтесь к каналам
[#kvision](https://kotlinlang.slack.com/messages/kvision) и [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69)
в [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up).

<a name="fritz2"></a>

### fritz2

<!-- _fritz2_ is a standalone framework for building reactive web user interfaces. It provides its own type-safe DSL for building
and rendering HTML elements, and it makes use of Kotlin’s coroutines and flows to express components and their data bindings.
It provides state management, validation, routing, and more out of the box, and integrates with Kotlin Multiplatform projects. -->
*fritz2* - это независимый фреймворк для создания реактивных веб-пользовательских интерфейсов. Он предоставляет
собственный типобезопасный DSL для создания и визуализации HTML-элементов, а также использует корутины и потоки Kotlin
для представления компонентов и их привязок к данным. Он "из коробки" обеспечивает управление состоянием, валидацию,
маршрутизацию и многое другое и интегрируется с Kotlin Multiplatform проектами.

<!-- Visit [https://www.fritz2.dev](https://www.fritz2.dev) for documentation, tutorials, and examples. -->
Чтобы изучить документацию, учебные пособия и примеры, посетите [https://www.fritz2.dev](https://www.fritz2.dev).

<!-- For updates and discussions about the framework, join the [#fritz2](https://kotlinlang.slack.com/messages/fritz2) and
[#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) channels in the [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up). -->
Для обсуждения и получения обновлений фреймворка присоединяйтесь к каналам
[#fritz2](https://kotlinlang.slack.com/messages/fritz2) и [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69)
в [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up).

<a name="doodle"></a>

### Doodle

<!-- _Doodle_ is a vector-based UI framework for Kotlin/JS. Doodle applications use the browser’s graphics capabilities to draw
user interfaces instead of relying on DOM, CSS, or Javascript. By using this approach, Doodle gives you precise control
over the rendering of arbitrary  elements, vector shapes, gradients, and custom visualizations. -->
*Doodle* - это векторный UI фреймворк для Kotlin/JS. Приложения Doodle используют графические возможности браузера для
рисования пользовательских интерфейсов вместо того, чтобы полагаться на DOM, CSS или Javascript. Используя этот подход,
Doodle дает вам точный контроль над визуализацией произвольных UI элементов, векторных фигур, градиентов и
пользовательских визуализаций.

<!-- Visit [https://nacular.github.io/doodle/](https://nacular.github.io/doodle/) for documentation, tutorials, and examples. -->
Чтобы изучить документацию, учебные пособия и примеры, посетите [https://nacular.github.io/doodle/](https://nacular.github.io/doodle/).

<!-- For updates and discussions about the framework, join the [#doodle](https://kotlinlang.slack.com/messages/doodle) and
[#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) channels in the [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up). -->
Для обсуждения и получения обновлений фреймворка присоединяйтесь к каналам
[#doodle](https://kotlinlang.slack.com/messages/doodle) и [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69)
в [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up).

<a name="compose-for-web"></a>

### Compose for Web

<!-- _Compose for Web_, a part of Compose Multiplatform, brings [Google's Jetpack Compose UI toolkit](https://developer.android.com/jetpack/compose)
to your browser. It allows you to build reactive web user interfaces using the concepts introduced by Jetpack Compose.
It provides a DOM API to describe your website, as well as an experimental set of multiplatform layout primitives.
Compose for Web also gives you the option to share parts of your UI code and logic across Android, desktop, and the web. -->
*Compose for Web*, часть Compose Multiplatform, добавляет в ваш браузер
[UI инструментарий Google Jetpack Compose](https://developer.android.com/jetpack/compose). Это позволяет создавать
реактивные веб-пользовательские интерфейсы, используя концепции, представленные Jetpack Compose. Он предоставляет DOM
API для описания вашего веб-сайта, а также экспериментальный набор примитивов мультиплатформенной компоновки. Compose
for Web также дает вам возможность делиться частями UI кода и логики на Android, десктопе и в web.

<!-- You can find more information about Compose Multiplatform on its [landing page](https://www.jetbrains.com/lp/compose-mpp/). -->
[Здесь](https://www.jetbrains.com/lp/compose-mpp/) вы можете найти более подробную информацию о Compose Multiplatform.

<!-- Join the [#compose-web](https://kotlinlang.slack.com/archives/C01F2HV7868) channel on the [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)
to discuss Compose for Web, or [#compose](https://kotlinlang.slack.com/archives/CJLTWPH7S) for general Compose Multiplatform discussions. -->
Присоединяйтесь к каналу [#compose-web](https://kotlinlang.slack.com/archives/C01F2HV7868) в
[Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up), чтобы обсудить Compose for Web, или
[#compose](https://kotlinlang.slack.com/archives/CJLTWPH7S) для обсуждения Compose Multiplatform.

<a name="kotlin-js-today-and-tomorrow"></a>

<!-- ## Kotlin/JS, Today and Tomorrow -->
## Kotlin/JS, Today and Tomorrow

<!-- In [this video](https://www.youtube.com/watch?v=fZUL8_kgHXg), Kotlin Developer Advocate Sebastian Aigner explains the
main Kotlin/JS benefits, shares some tips and use cases, and talks about the plans and upcoming features for Kotlin/JS. -->
В [этом видео](https://www.youtube.com/watch?v=fZUL8_kgHXg) Kotlin-разработчик Себастьян Айгнер объясняет основные
преимущества Kotlin/JS, делится некоторыми советами и примерами использования, а также рассказывает о планах и
планируемых функциях для Kotlin/JS.

<iframe width="560" height="315" src="https://www.youtube.com/embed/fZUL8_kgHXg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<a name="get-started-with-kotlin-js"></a>

<!-- ## Get started with Kotlin/JS -->
## Начните работу с Kotlin/JS

<!-- If you're new to Kotlin, a good first step is to familiarize yourself with the [basic syntax](basic-syntax.md) of the language. -->
Если вы только начали изучать Kotlin, хорошим первым шагом будет ознакомление с [базовым синтаксисом](basic-syntax.html)
языка.

<!-- To start using Kotlin for JavaScript, please refer to [Set up a Kotlin/JS project](js-project-setup.md). You can also
pick a [hands-on](#hands-on-labs-for-kotlin-js) lab to work through or check out the list of [Kotlin/JS sample projects](#sample-projects-for-kotlin-js)
for inspiration. They contain useful snippets and patterns and can serve as nice jump-off points for your own projects. -->
Чтобы начать использовать Kotlin для JavaScript, пожалуйста, ознакомьтесь с разделом
[Настройка проекта Kotlin/JS](js-project-setup.md). Вы также можете выбрать
[практические занятия](#hands-on-labs-for-kotlin-js) для прорабатывания или ознакомиться со списком
[примеров проектов Kotlin/JS](#sample-projects-for-kotlin-js) для вдохновения. Они содержат полезные сниппеты и паттерны
и могут послужить хорошей отправной точкой для ваших собственных проектов.

<a name="hands-on-labs-for-kotlin-js"></a>

<!-- ## Hands-on labs for Kotlin/JS -->
## Практические занятия Kotlin/JS

<!-- * [Building Web Applications with React and Kotlin/JS](https://play.kotlinlang.org/hands-on/Building%20Web%20Applications%20with%20React%20and%20Kotlin%20JS/01_Introduction)
guides you through the process of building a simple web application using the React framework, shows how a type-safe Kotlin
DSL for HTML makes it easy to build reactive DOM elements, and illustrates how to use third-party React components and
obtain information from APIs, all while writing the whole application logic in pure Kotlin/JS.

* [Building a Full Stack Web App with Kotlin Multiplatform](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)
teaches the concepts behind building an application that targets Kotlin/JVM and Kotlin/JS by building a client-server
application that makes use of shared code, serialization, and other multiplatform paradigms. It also provides a brief
introduction to working with Ktor both as a server- and client-side framework. -->

* [Создание веб-приложений с помощью React и Kotlin/JS](https://play.kotlinlang.org/hands-on/Building%20Web%20Applications%20with%20React%20and%20Kotlin%20JS/01_Introduction)
проведет вас через процесс создания простого веб-приложения с использованием React, покажет, как типобезопасный Kotlin
DSL для HTML упрощает создание реактивных элементов DOM и иллюстрирует, как использовать сторонние компоненты React и
получать информацию из API, и все это при написании всей логики приложения в чистом Kotlin/JS;
* [Создание фулстек веб-приложения с помощью Kotlin Multiplatform](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)
обучает концепциям создания приложения, ориентированного на Kotlin/JVM и Kotlin/JS, путем создания клиент-серверного
приложения, использующего общий код, сериализацию и другие мультиплатформенные парадигмы. В нем также содержится краткое
введение в работу с Ktor как с серверным, так и с клиентским фреймворком.

<a name="sample-projects-for-kotlin-js"></a>

<!-- ## Sample projects for Kotlin/JS -->
## Примеры проектов Kotlin/JS

<!-- * [Full-stack Spring collaborative to-do list](https://github.com/Kotlin/full-stack-spring-collaborative-todo-list-sample)
shows how to create a to-do list for collaborative work using `kotlin-multiplatform` with JS and JVM targets, Spring
for the backend, Kotlin/JS with React for the frontend, and RSocket.
* [Kotlin/JS and React Redux to-do list](https://github.com/Kotlin/react-redux-js-ir-todo-list-sample) implements
the React Redux to-do list using JS libraries (`react`, `react-dom`, `react-router`, `redux`, and `react-redux`)
from npm and Webpack to bundle, minify, and run the project.
* [Full-stack demo application](https://github.com/Kotlin/full-stack-web-jetbrains-night-sample) guides you through
the process of building an app with a feed containing user-generated posts and comments. All data is stubbed by
the fakeJSON and JSON Placeholder services. -->

* [Фулстек список дел для совместной работы Spring](https://github.com/Kotlin/full-stack-spring-collaborative-todo-list-sample)
показывает, как создать список дел для совместной работы с использованием `kotlin-multiplatform`, таргетированный на JS
и JVM, Spring для бэкэнда, Kotlin/JS с React для фронтеда и RSocket;
* [Список дел Kotlin/JS и React Redux](https://github.com/Kotlin/react-redux-js-ir-todo-list-sample) реализует список
дел React Redux с использованием библиотек JS (`react`, `react-dom`, `react-router`, `redux` и `react-redux`) из npm и
Webpack для объединения, минимизации и запуска проекта;
* [Фулстек демонстрационное приложение](https://github.com/Kotlin/full-stack-web-jetbrains-night-sample) проведет вас
через процесс создания приложения с лентой, содержащей созданные пользователями сообщения и комментарии. Все данные
берутся из сервисов-заполнителей fakeJSON и JSON Placeholder.

<a name="new-kotlin-js-ir-compiler"></a>

<!-- ## New Kotlin/JS  compiler -->
## Новый IR-компилятор Kotlin/JS

<!-- The [new Kotlin/JS IR compiler](js-ir-compiler.md) (currently with [Beta](components-stability.md) stability) comes with
a number of improvements over the current default compiler. For example, it reduces the size of generated executables
via dead code elimination and provides smoother interoperability with the JavaScript ecosystem and its tooling.
By generating TypeScript declaration files (d.ts) from Kotlin code, the new compiler makes it easier to create “hybrid”
applications that mix TypeScript and Kotlin code and to leverage code-sharing functionality using Kotlin Multiplatform. -->
[Новый IR-компилятор Kotlin/JS](js-ir-compiler.html) (в настоящее время находится в
[бета-версии](components-stability.html)) содержит ряд улучшений по сравнению с текущим компилятором по умолчанию.
Например, он уменьшает размер создаваемых исполняемых файлов за счет устранения мертвого кода и обеспечивает более
плавную совместимость с экосистемой JavaScript и ее инструментами. Генерируя файлы объявлений TypeScript (d.ts) из
Kotlin-кода, новый компилятор упрощает создание “гибридных” приложений, в которых сочетаются TypeScript-код и Kotlin-код,
и позволяет использовать функции Kotlin Multiplatform.

<!-- To learn more about the available features in the new Kotlin/JS IR compiler and how to try it for your project, visit the
[Kotlin/JS IR compiler documentation page](js-ir-compiler.md) and the [migration guide](js-ir-migration.md). -->
Чтобы узнать больше о доступных функциях нового IR-компилятора Kotlin/JS и о том, как его использовать в вашем проекте,
посетите [страницу документации IR-компилятора Kotlin/JS](js-ir-compiler.html) и
[руководство по переносу проектов](js-ir-migration.html).

<a name="join-the-kotlin-js-community"></a>

<!-- ## Join the Kotlin/JS community -->
## Присоединяйтесь к сообществу Kotlin/JS

<!-- You can also join the [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) channel in the official [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)
to chat with the community and the team. -->
Вы также можете присоединиться к каналу [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) на официальном
сайте [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up), чтобы пообщаться с сообществом и командой.
