---
type: doc
layout: reference
title: "Kotlin для серверов"
url: https://kotlinlang.ru/docs/server-overview.html
---

<!-- # Kotlin for server side -->
# Kotlin для серверов

<!-- Kotlin is a great fit for developing server-side applications. It allows you to write concise and expressive code while
maintaining full compatibility with existing Java-based technology stacks, all with a smooth learning curve: -->
Kotlin отлично подходит для разработки серверных приложений. Kotlin позволяет вам писать лаконичный и выразительный код,
сохраняя при этом полную совместимость с существующими стеками технологий на базе Java, и все это с плавной кривой
обучения:

<!-- * **Expressiveness**: Kotlin's innovative language features, such as its support for [type-safe builders](type-safe-builders.md)
  and [delegated properties](delegated-properties.md), help build powerful and easy-to-use abstractions.
* **Scalability**: Kotlin's support for [coroutines](coroutines-overview.md) helps build server-side applications
  that scale to massive numbers of clients with modest hardware requirements.
* **Interoperability**: Kotlin is fully compatible with all Java-based frameworks, so you can use your
  familiar technology stack while reaping the benefits of a more modern language.
* **Migration**: Kotlin supports gradual migration of large codebases from Java to Kotlin. You can start
  writing new code in Kotlin while keeping older parts of your system in Java.
* **Tooling**: In addition to great IDE support in general, Kotlin offers framework-specific tooling (for example,
  for Spring) in the plugin for IntelliJ IDEA Ultimate.
* **Learning Curve**: For a Java developer, getting started with Kotlin is very easy. The automated Java-to-Kotlin converter included in the Kotlin plugin helps with the first steps. [Kotlin Koans](koans.md) can guide you through the key features of the language with a series of interactive exercises. -->

* **Выразительность**: инновационные возможности Kotlin, такие, как поддержка
[типобезопасных строителей](type-safe-builders.html) и [делегированных свойств](delegated-properties.html), помогают
создавать мощные и простые в использовании абстракции.
* **Масштабируемость**: поддержка [корутинов](coroutines-overview.html) помогает создавать серверные приложения,
масштабируемые для большого числа клиентов со скромными требованиями к оборудованию.
* **Совместимость**: Kotlin полностью совместим со всеми фреймворками на базе Java, поэтому вы можете использовать свой
привычный стек технологий, одновременно пользуясь преимуществами более современного языка.
* **Миграция**: Kotlin поддерживает постепенную миграцию больших кодовых баз с Java на Kotlin. Вы можете начать писать
новый код на Kotlin, сохраняя при этом старые части вашей системы на Java.
* **Инструментарий**: В дополнение к отличной поддержке IDE в целом, Kotlin предлагает инструментарий для конкретной
платформы (например, для Spring) в плагине для IntelliJ IDEA Ultimate.
* **Кривая обучения**: Для Java разработчика начать работу с Kotlin очень просто. Автоматический конвертер Java в
Kotlin, включенный в плагин Kotlin, помогает с первыми шагами. [Kotlin Koans](koans.html) поможет вам разобраться в
ключевых особенностях языка с помощью интерактивных упражнений.

<a name="frameworks-for-server-side-development-with-kotlin"></a>

<!-- ## Frameworks for server-side development with Kotlin -->
## Фреймворки для серверной разработки на Kotlin

<!-- * [Spring](https://spring.io) makes use of Kotlin's language features to offer [more concise APIs](https://spring.io/blog/2017/01/04/introducing-kotlin-support-in-spring-framework-5-0),
  starting with version 5.0. The [online project generator](https://start.spring.io/#!language=kotlin) allows you to quickly generate a new project in Kotlin.

* [Vert.x](https://vertx.io), a framework for building reactive Web applications on the JVM, offers [dedicated support](https://github.com/vert-x3/vertx-lang-kotlin)
  for Kotlin, including [full documentation](https://vertx.io/docs/vertx-core/kotlin/).

* [Ktor](https://github.com/kotlin/ktor) is a framework built by JetBrains for creating Web applications in Kotlin, making use of coroutines for high scalability and offering an easy-to-use and idiomatic API.

* [kotlinx.html](https://github.com/kotlin/kotlinx.html) is a DSL that can be used to build HTML in Web applications.
  It serves as an alternative to traditional templating systems such as JSP and FreeMarker.

* [Micronaut](https://micronaut.io/) is a modern JVM-based full-stack framework for building modular, easily testable microservices and serverless applications. It comes with a lot of useful built-in features.

* [http4k](https://http4k.org/) is the functional toolkit with a tiny footprint for Kotlin HTTP applications, written in pure Kotlin. The library is based on the "Your Server as a Function" paper from Twitter and represents modeling both HTTP servers and clients as simple Kotlin functions that can be composed together.

* [Javalin](https://javalin.io) is a very lightweight web framework for Kotlin and Java which supports WebSockets, HTTP2, and async requests.

* The available options for persistence include direct JDBC access, JPA, and using NoSQL databases through their Java drivers.
  For JPA, the [kotlin-jpa compiler plugin](no-arg-plugin.md#jpa-support) adapts
  Kotlin-compiled classes to the requirements of the framework. -->

* [Spring](https://spring.io) использует возможности Kotlin, чтобы предлагать
[более лаконичные API](https://spring.io/blog/2017/01/04/introducing-kotlin-support-in-spring-framework-5-0), начиная с
версии 5.0. [Онлайн-генератор проектов](https://start.spring.io/#!language=kotlin) позволяет быстро сгенерировать новый
проект в Kotlin.
* [Vert.x](https://vertx.io), фреймворк для создания интерактивных веб-приложений на JVM, предлагает
[специальную поддержку](https://github.com/vert-x3/vertx-lang-kotlin) Kotlin, включая
[полную документацию](https://vertx.io/docs/vertx-core/kotlin/).
* [Ktor](https://github.com/kotlin/ktor) - это фреймворк, созданный JetBrains для создания веб-приложений в Kotlin,
использующий корутины для высокой масштабируемости и предлагающий простой в использовании идиоматический API.
* [kotlinx.html](https://github.com/kotlin/kotlinx.html) - это DSL, который можно использовать для HTML разметки в
веб-приложениях. Он служит альтернативой традиционным системам шаблонов, таким как JSP и FreeMarker.
* [Micronaut](https://micronaut.io/) - это современный фулстек фреймворк на базе JVM созданный для создания модульных,
легко тестируемых микросервисов и бессерверных приложений. В него встроены множество полезных функций.
* [http4k](https://http4k.org/) - это функциональный инструментарий с небольшими возможностями для HTTP-приложений
Kotlin, написанный на чистом Kotlin. Библиотека основана на статье "Your Server as a Function" из Twitter и представляет
моделирование как HTTP-серверов, так и клиентов в виде простых функций Kotlin, которые могут быть собраны вместе.
* [Javalin](https://javalin.io) - это очень легкий веб-фреймворк для Kotlin и Java, который поддерживает WebSockets,
HTTP2 и асинхронные запросы.
* Доступные варианты стабильности включают в себя прямой доступ к JDBC, JPA и использование баз данных NoSQL через их
Java драйверы. Для JPA [плагин компилятора kotlin-jpa](no-arg-plugin.html#jpa-support) адаптирует классы,
скомпилированные на Kotlin, к требованиям фреймворка.

<a name="deploying-kotlin-server-side-applications"></a>

<!-- ## Deploying Kotlin server-side applications -->
## Внедрение серверных приложений Kotlin

<!-- Kotlin applications can be deployed into any host that supports Java Web applications, including Amazon Web Services,
Google Cloud Platform, and more. -->
Приложения Kotlin могут быть развернуты на любом хосте, поддерживающем веб-приложения Java, включая Amazon Web Services,
Google Cloud Platform и многое другое.

<!-- To deploy Kotlin applications on [Heroku](https://www.heroku.com), you can follow the [official Heroku tutorial](https://devcenter.heroku.com/articles/getting-started-with-kotlin). -->
Чтобы развернуть приложения Kotlin на [Heroku](https://www.heroku.com), вы можете следовать
[официальному руководству Heroku](https://devcenter.heroku.com/articles/getting-started-with-kotlin).

<!-- AWS Labs provides a [sample project](https://github.com/awslabs/serverless-photo-recognition) showing the use of Kotlin
for writing [AWS Lambda](https://aws.amazon.com/lambda/) functions. -->
AWS Labs предлагает [пример проекта](https://github.com/awslabs/serverless-photo-recognition), демонстрирующего
использование Kotlin для написания [AWS лямбда-функций](https://aws.amazon.com/lambda/).

<!-- Google Cloud Platform offers a series of tutorials for deploying Kotlin applications to GCP, both for [Ktor and App Engine](https://cloud.google.com/community/tutorials/kotlin-ktor-app-engine-java8) and [Spring and App engine](https://cloud.google.com/community/tutorials/kotlin-springboot-app-engine-java8). In addition,
there is an [interactive code lab](https://codelabs.developers.google.com/codelabs/cloud-spring-cloud-gcp-kotlin) for deploying a Kotlin Spring application. -->
Google Cloud Platform предлагает серию учебных пособий по развертыванию приложений Kotlin в GCP для
[Ktor и App Engine](https://cloud.google.com/community/tutorials/kotlin-ktor-app-engine-java8), а также для
[Spring и App engine](https://cloud.google.com/community/tutorials/kotlin-springboot-app-engine-java8). Кроме того,
существует [интерактивная лаборатория кода](https://codelabs.developers.google.com/codelabs/cloud-spring-cloud-gcp-kotlin)
для развертывания Kotlin Spring приложения.

<a name="products-that-use-kotlin-on-the-server-side"></a>

<!-- ## Products that use Kotlin on the server side -->
## Продукты, использующие Kotlin на сервере

<!-- [Corda](https://www.corda.net/) is an open-source distributed ledger platform that is supported by major
banks and built entirely in Kotlin. -->
[Corda](https://www.corda.net/) - это open-source платформа распределенных реестров, которая поддерживается крупными
банками и полностью построена Kotlin.

<!-- [JetBrains Account](https://account.jetbrains.com/), the system responsible for the entire license sales and validation
process at JetBrains, is written in 100% Kotlin and has been running in production since 2015 with no major issues. -->
[JetBrains Account](https://account.jetbrains.com/), система, отвечающая за весь процесс продажи и проверки лицензий в
JetBrains, вся на 100% написана на Kotlin и работает с 2015 года без каких-либо серьезных проблем.

<a name="next-steps"></a>

<!-- ## Next steps -->
## Дальнейшие действия

<!-- * For a more in-depth introduction to the language, check out the Kotlin documentation on this site and [Kotlin Koans](koans.md).
* Watch a webinar ["Micronaut for microservices with Kotlin"](https://micronaut.io/2020/12/03/webinar-micronaut-for-microservices-with-kotlin/) and
  explore a detailed [guide](https://guides.micronaut.io/latest/micronaut-kotlin-extension-fns.html)
  showing how you can use [Kotlin extension functions](extensions.md#extension-functions) in the Micronaut framework.
* http4k provides the [CLI](https://toolbox.http4k.org) to generate fully formed projects, and a [starter](https://start.http4k.org) repo to generate an entire CD pipeline using GitHub, Travis, and Heroku with a single bash command.
* Want to migrate from Java to Kotlin? Learn how to perform [typical tasks with strings in Java and Kotlin](java-to-kotlin-idioms-strings.md). -->

* Для более подробного ознакомления с языком ознакомьтесь с документацией Kotlin на этом сайте и на
[Kotlin Koans](koans.md);
* Посмотрите вебинар ["Micronaut для микросервисов с Kotlin"](https://micronaut.io/2020/12/03/webinar-micronaut-for-microservices-with-kotlin/)
и ознакомьтесь с подробным [руководством](https://guides.micronaut.io/latest/micronaut-kotlin-extension-fns.html),
демонстрирующем, как вы можете использовать [функции-расширения Kotlin](extensions.html#extension-functions)
в фреймворке Micronaut;
* http4k предлагает [CLI](https://toolbox.http4k.org) для создания полностью сформированных проектов и
[стартовый](https://start.http4k.org) репозиторий для создания всего конвейера CD с использованием GitHub, Travis и
Heroku с помощью одной bash-команды;
* Хотите перейти с Java на Kotlin? Узнайте, как выполнять [типичные задачи со строками в Java и Kotlin](java-to-kotlin-idioms-strings.html).
