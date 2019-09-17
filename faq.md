---
type: doc
layout: reference
category: FAQ
title: ЧаВо
---

# Частые вопросы

### Что такое Kotlin?

Kotlin - OSS-статически типизированный язык программирования с поддержкой JVM, Android, JavaScript и Native.
Язык разработан [JetBrains](http://www.jetbrains.com).
Проект начался в 2010 и с самого начала был открытым.
Версия 1.0 была выпущена в феврале 2016 г.

### Какова текущая версия Kotlin?

Последняя выпущенная версия - 1.3.40, опубликована в 19 июня 2019 г.

### Свободен ли Kotlin?

Да. Kotlin был, есть и будет свободным. Он разрабатывается под лицензией Apache 2.0,
а исходный код доступен [на GitHub](https://github.com/jetbrains/kotlin).

### Является ли Kotlin обьектно-ориентированным или функциональным?

Kotlin является и тем, и другим.
Вы можете как угодно использовать и комбинировать эти стили.
При полной поддержке таких возможностей, как функции высшего порядка, функциональные типы
и лямбда-выражения,
Kotlin - отличный выбор, если вы используете или изучаете функциональное программирование.

### Какие преимущества есть у Kotlin по сравнению с Java?

Kotlin более лаконичен. Код на Kotlin примерно на 40% короче.
Также Kotlin является типобезопасным. Благодаря null-безопасности приложения менее уязвимы к NullPointerException.
В списке других возможностей: умное приведение типов, функции высшего порядка,
функции-расширения и лямбда-выражения с получателями. Они позволяют писать выразительный код и
поддерживать создание DSL.

### Совместим ли Kotlin с Java?

Да, Kotlin на 100% совместим с Java. Возможности вашего существующего кода на Java правильно взаимодействовать с Kotlin
уделено много внимания. Вы можете запросто вызывать код на Kotlin из Java и наоборот.
Благодаря этому, внедрение Kotlin в проект намного проще и безопасней.
Также стоит отметить автоматический конвертер кода из Java в Kotlin.
Он встроен в IDE и позволяет упростить переход существующего кода на Kotlin.

### Для чего я могу использовать Kotlin?

Kotlin может быть использован для разработки для множества платформ, будь то сервер, клиент, веб или Android.
C релизом Kotlin/Native (который на текущий момент в разработке) появится поддержка таких платформ,
как встроенные системы, macOS и iOS. Люди используют Kotlin для мобильных
и серверных приложений, клиентских приложений с JavaScript или JavaFX, в data science и так далее.

### Можно ли использовать Kotlin для разработки для Android?

Можно. Kotlin поддерживается как основной язык для Android, наравне с Java. Сотни приложений для Android уже используют
Kotlin, к примеру Basecamp, Pinterest и так далее. Чтобы узнать больше,
смотрите [статью про разработку для Android](https://kotlinlang.org/docs/reference/android-overview.html).

### Можно ли использовать Kotlin для разработки серверных приложений?

Можно. Kotlin на 100% совместим с JVM, а значит вы можете использовать любые фреймворки для Java, такие как Spring Boot,
vert.x или JSF. Также есть фреймворки для Kotlin, как например [Ktor](http://github.com/kotlin/ktor).
Смотрите [статью о разработке серверных приложений](https://kotlinlang.org/docs/reference/server-overview.html).

### Можно ли использовать Kotlin для веб-разработки?

Да. Кроме использования в бэкэнде, вы также можете использовать Kotlin/JS
для клиентской стороны. Kotlin может использовать определения из
[DefinitelyTyped](http://definitelytyped.org) для статической типизации для популярных
библиотек на JavaScript. Также Kotlin совместим с модульными системи, такими как AMD и CommonJS.
Смотрите [статью о разработке для клиентской стороны](https://kotlinlang.org/docs/reference/js-overview.html).

### Можно ли использовать Kotlin для разработки Desktop-приложений?

Да. Вы можете использовать любой UI-фреймворк, например JavaFx, Swing и т.д.
В добавок, существуют фреймворки для Kotlin, такие как
[TornadoFX](https://github.com/edvin/tornadofx).

### Можно ли использовать Kotlin для нативной разработки?

Kotlin/Native в текущий момент [в разработке](https://blog.jetbrains.com/kotlin/tag/native/). Kotlin/Native позволит
компилировать исходный код в нативный, который может работать без виртуальной машины. На данный момент готова
технологическая демо-версия, но она еще не готова к продакшену и не поддерживает некоторые платформы,
которые запланированы к версии 1.0. Смотрите
[пост, анонсирующий Kotlin/Native](https://blog.jetbrains.com/kotlin/2017/04/kotlinnative-tech-preview-kotlin-without-a-vm/).

### Какие IDE поддерживают Kotlin?

Kotlin поддерживается всеми крупными IDE для Java, включая [IntelliJ IDEA](https://kotlinlang.org/docs/tutorials/getting-started.html),
[Android Studio](https://kotlinlang.org/docs/tutorials/kotlin-android.html), [Eclipse](https://kotlinlang.org/docs/tutorials/getting-started-eclipse.html) и
[NetBeans](http://plugins.netbeans.org/plugin/68590/kotlin).
Также доступен [отдельный компилятор](https://kotlinlang.org/docs/tutorials/command-line.html),
полностью поддерживающий все возможности компиляции и запуска приложений.

### Какие инструменты для сборки поддерживают Kotlin?

На стороне JVM - [Gradle](https://kotlinlang.org/docs/reference/using-gradle.html), [Maven](https://kotlinlang.org/docs/reference/using-maven.html),
[Ant](https://kotlinlang.org/docs/reference/using-ant.html) и [Kobalt](http://beust.com/kobalt/home/index.html). Также есть множество инструментов
со стороны JavaScript.

### Во что может компилироваться и транслироваться Kotlin?

В Java-байткод и ES5.1, поддерживаемый модульными системами AMD and CommonJS. Скоро появится возможность компилировать
в код для конкретной платформы с LLVM.

### Kotlin поддерживает лишь Java 6?

Не только. Вы можете выбирать между байткодом 6 или 8 версии. Чем новее версия Java, тем лучше и эффективнее байткод.

### Сложен ли Kotlin?

Kotlin похож на Java, C#, JavaScript, Scala и Groovy. Мы пытались сделать Kotlin простым для изучения,
чтобы на него можно было перейти всего за неколько дней.
Более детальное изучение языка займёт немного больше времени, но в общем это несложный язык.

### Какие компании используют Kotlin?

Kotlin используют множество компаний. Из числа тех, что обьявили об использовании языка: [Square](https://medium.com/square-corner-blog/square-open-source-loves-kotlin-c57c21710a17), [Pinterest](https://www.youtube.com/watch?v=mDpnc45WwlI), [Basecamp](https://m.signalvnoise.com/how-we-made-basecamp-3s-android-app-100-kotlin-35e4e1c0ef12) и [Corda](https://docs.corda.net/releases/release-M9.2/further-notes-on-kotlin.html).

### Кто разрабатывает Kotlin?

Kotlin разрабатывается в основном группой инженеров JetBrains (более чем 40 человек).
Главный разработчик - [Андрей Бреслав](https://twitter.com/abreslav). В дополнение к команде, на GitHub есть примерно 100
активных вкладчиков.

### Где можно узнать больше про язык?

Лучше всего начать прямо на этом сайте или на https://kotlinlang.org. На kotlinlang.org можно скачать компилятор или
[попробовать его онлайн](https://try.kotlinlang.org) Доступны [справочник](https://kotlinlang.org/docs/reference/index.html)
и [туториалы](https://kotlinlang.org/docs/tutorials/index.html).

### Существуют ли книги про Kotlin?

Список книг есть [здесь](https://kotlinlang.org/docs/books.html).
Из самых популярных: [Kotlin в Действии](https://www.manning.com/books/kotlin-in-action), написанная членами команды
разработчиков Дмитрием Джемеровым и Светланой Исаковой, а также
[Kotlin for Android Developers](https://leanpub.com/kotlin-for-android-developers).

### Есть ли онлайн-курсы для изучения Kotlin?

[Pluralsight Kotlin Course](https://www.pluralsight.com/courses/kotlin-getting-started) от Кевина Джонса,
[курс O’Reilly](http://shop.oreilly.com/product/0636920052982.do) от Хади Харири и [курс Udemy](http://petersommerhoff.com/dev/kotlin/kotlin-beginner-tutorial/) от Петра Соммерхоффа.

Также есть множество записей [Kotlin talks](http://kotlinlang.org/community/talks.html), которые можно найти на YouTube
и Vimeo.

### Есть ли сообщество у Kotlin?

Да. У Kotlin очень отзывчивое сообщество на [форумах Kotlin](http://discuss.kotlinlang.org),
[StackOverflow](http://stackoverflow.com/questions/tagged/kotlin) и, наиболее активно, [Kotlin Slack](http://slack.kotlinlang.org)
(примерно 7000 участников в мае 2017).

### Проходят ли какие-то мероприятия по Kotlin?

Да. Сейчас много пользовательских групп и митапов, которые сосредоточены на Kotlin. Список данных мероприятий можно найти [на этом сайте](https://kotlinlang.org/community/user-groups.html). Также во всем мире проводятся специальные мероприятия - [Kotlin Nights](https://kotlinlang.org/community/kotlin-nights.html)

### Проходят ли конференции, посвящённые Kotlin?

Да. Первая официальная конференция - [KotlinConf](https://kotlinconf.com) - прошла в Сан-Франциско со 2 по 3 ноября 2017 года.
Kotlin также обсуждается во время различных конференций по всему миру. Вы можете найти список [ближайших конференций на этом сайте](https://kotlinlang.org/community/talks.html?time=upcoming).

### Есть ли Kotlin в социальных сетях?

Наиболее активный аккаунт [на Twitter](https://twitter.com/kotlin).
Также есть группа [Google+](https://plus.google.com/communities/104597899765146112928).

### Есть ли ещё какие-либо онлайн-ресурсы по Kotlin?

В [сообществе Kotlin](https://kotlinlang.org/community/) вы можете найти множество ресурсов, включая [Дайджесты Kotlin](https://kotlin.link), организуемые сообществом,
[новостную рассылку](http://www.kotlinweekly.net), [подкаст](https://talkingkotlin.com) и так далее.   

### Где можно найти HD-логотип Kotlin?

Логотипы можно найти [здесь](https://resources.jetbrains.com/storage/products/kotlin/docs/kotlin_logos.zip).
Следуйте указаниям `guidelines.pdf` в архиве.
