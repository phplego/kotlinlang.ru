---
type: doc
layout: reference
title: "Kotlin Native"
url: https://kotlinlang.ru/docs/native-overview.html
---
      
<!-- При переводе статьи оригинальная версия была от 25 February 2022 -->

# Kotlin Native

<!-- Kotlin/Native is a technology for compiling Kotlin code to native binaries which can run without a virtual machine.
Kotlin/Native includes an [LLVM](https://llvm.org/)-based backend for the Kotlin compiler and a native implementation of the Kotlin standard
library. -->
Kotlin/Native - это технология компиляции Kotlin-кода в нативные двоичные файлы, которые могут запускаться без
виртуальной машины. Kotlin/Native включает в себя серверную часть на основе [LLVM](https://llvm.org/) для компилятора
Kotlin и нативную реализацию стандартной библиотеки Kotlin.

<a name="why-kotlin-native"></a>

<!-- ## Why Kotlin/Native? -->
## Почему Kotlin/Native?

<!-- Kotlin/Native is primarily designed to allow compilation for platforms on which _virtual machines_ are not
desirable or possible, such as embedded devices or iOS.
It is ideal for situations when a developer needs to produce a
self-contained program that does not require an additional runtime or virtual machine. -->
Kotlin/Native в первую очередь предназначен для обеспечения компиляции для платформ, на которых *виртуальные машины*
нежелательны или невозможны, таких как встроенные устройства или iOS. Он идеально подходит для ситуаций, когда
разработчику необходимо создать самодостаточную программу, которая не требует дополнительной среды выполнения или
виртуальной машины.

<a name="target-platforms"></a>

<!-- ## Target platforms -->
## Платформы, для которых используется Kotlin/Native

<!-- Kotlin/Native supports the following platforms: -->
Kotlin/Native поддерживает следующие платформы:

* macOS
* iOS, tvOS, watchOS
* Linux
* Windows (MinGW)
* Android NDK

<!-- [The full list of supported targets is available here](multiplatform-dsl-reference.md). -->
[Полный список поддерживаемых платформ вы можете найти здесь](multiplatform-dsl-reference.html).

<a name="interoperability"></a>

<!-- ## Interoperability -->
## Совместимость

<!-- Kotlin/Native supports two-way interoperability with native programming languages for different operating systems.
The compiler creates: -->
Kotlin/Native поддерживает двустороннюю совместимость с родными языками программирования для различных операционных
систем. Компилятор создает:

<!-- * an executable for many [platforms](#target-platforms)
* a static library or [dynamic](native-dynamic-libraries.md) library with C headers for C/C++ projects
* an [Apple framework](apple-framework.md) for Swift and Objective-C projects -->

* исполняемый файл для многих [платформ](#target-platforms),
* статическую библиотеку или [динамическую](native-dynamic-libraries.html) библиотеку с C-заголовками для проектов на C/C++,
* Apple-фреймворк для проектов на Swift и Objective-C.

<!-- Kotlin/Native supports interoperability to use existing libraries
directly from Kotlin/Native: -->
Благодаря совместимости, вы можете использования существующие библиотеки непосредственно из Kotlin/Native:

<!-- * static or dynamic [C libraries](native-c-interop.md)
* C, [Swift, and Objective-C](native-objc-interop.md) frameworks -->.

* статичные или динамичные [библиотеки C](native-c-interop.html),
* фреймворки C, [Swift, and Objective-C](native-objc-interop.html).

<!-- It is easy to include compiled Kotlin code in
existing projects written in C, C++, Swift, Objective-C, and other languages.
It is also easy to use existing native code,
static or dynamic [C libraries](native-c-interop.md),
Swift/Objective-C [frameworks](native-objc-interop.md),
graphical engines, and anything else directly from Kotlin/Native. -->
Вы можете легко включить скомпилированный Kotlin-код в существующие проекты, написанные на C, C++, Swift, Objective-C и
других языках. Также легко вы можете использовать существующий нативный код, статические или динамические
[библиотеки C](native-c-interop.html), [фреймворки](native-objc-interop.html) Swift/Objective-C, графические движки и
все остальное непосредственно из Kotlin/Native.

<!-- Kotlin/Native [libraries](native-platform-libs.md) help share Kotlin
code between projects.
POSIX, gzip, OpenGL, Metal, Foundation, and many other popular libraries and Apple frameworks
are pre-imported and included as Kotlin/Native libraries in the compiler package. -->
[Библиотеки](native-platform-libs.html) Kotlin/Native помогают использовать один Kotlin-кодом на нескольких проектах.
POSIX, gzip, OpenGL, Metal, Foundation и многие другие популярные библиотеки и фреймворки Apple уже импортированы и
включены в пакет компилятора в качестве библиотек Kotlin/Native.

<a name="sharing-code-between-platforms"></a>

<!-- ## Sharing code between platforms -->
## Использование кода на нескольких платформах

<!-- [Multiplatform projects](multiplatform.md) allow sharing common Kotlin code between multiple platforms, including Android, iOS, JVM, JavaScript, and native.
Multiplatform libraries provide required APIs for common Kotlin code and help develop shared parts of a project in
Kotlin in one place and share it with some or all target platforms. -->
[Мультиплатформенные проекты](multiplatform.html) позволяют обмениваться общим Kotlin-кодом между несколькими платформами,
включая Android, iOS, JVM, JavaScript и native. Мультиплатформенные библиотеки предоставляют необходимые API для общего
кода Kotlin и помогают разрабатывать общие части проекта в Kotlin в одном месте и делиться ими с некоторыми или всеми
платформами.

<!-- You can use [Kotlin Multiplatform Mobile](https://kotlinlang.org/lp/mobile/) to create multiplatform mobile applications with code shared between Android and iOS. -->
Вы можете использовать [Kotlin Multiplatform Mobile](https://kotlinlang.org/lp/mobile/) для создания
мультиплатформенных мобильных приложений с одним кодом для Android и iOS.

<a name="how-to-get-started"></a>

<!-- ## How to get started -->
## С чего начать

<a name="tutorials-and-documentation"></a>

<!-- ### Tutorials and documentation -->
### Руководства и документация

<!-- New to Kotlin? Take a look at [Getting started with Kotlin](getting-started.md). -->
Недавно начали изучать Kotlin? Загляните в раздел [Начало работы с Kotlin](getting-started.html).

<!-- Recommended documentation: -->
Рекомендуемая документация:

<!-- * [Kotlin Multiplatform Mobile documentation](multiplatform-mobile-getting-started.md)
* [Multiplatform documentation](multiplatform-get-started.md)
* [C interop](native-c-interop.md)
* [Swift/Objective-C interop](native-objc-interop.md) -->

* [Документация Kotlin Multiplatform Mobile](multiplatform-mobile-getting-started.html)
* [Документация по мультиплатформенности](multiplatform-get-started.html)
* [C interop](native-c-interop.html)
* [Swift/Objective-C interop](native-objc-interop.html)

<!-- Recommended tutorials: -->
Рекомендуемые руководства:

<!-- * [Get started with Kotlin/Native](native-get-started.md)
* [Create your first cross-platform mobile application](multiplatform-mobile-create-first-app.md)
* [Types mapping between C and Kotlin/Native](mapping-primitive-data-types-from-c.md)
* [Kotlin/Native as a Dynamic Library](native-dynamic-libraries.md)
* [Kotlin/Native as an Apple Framework](apple-framework.md) -->

* [Начало работы с Kotlin/Native](native-get-started.html)
* [Создание первого кроссплатформенного мобильного приложения](multiplatform-mobile-create-first-app.html)
* [Сравнение типов C и Kotlin/Native](mapping-primitive-data-types-from-c.html)
* [Kotlin/Native как динамическая библиотека](native-dynamic-libraries.html)
* [Kotlin/Native как Apple фреймворк](apple-framework.html)

<a name="sample-projects"></a>

<!-- ## Sample projects -->
## Примеры проктов

<!-- * [Kotlin Multiplatform Mobile samples](multiplatform-mobile-samples.md)
* [Kotlin/Native sources and examples](https://github.com/JetBrains/kotlin/tree/master/kotlin-native/samples)
* [KotlinConf app](https://github.com/JetBrains/kotlinconf-app)
* [KotlinConf Spinner app](https://github.com/jetbrains/kotlinconf-spinner)
* [Kotlin/Native sources and examples (.tgz)](https://download.jetbrains.com/kotlin/native/kotlin-native-samples-1.0.1.tar.gz)
* [Kotlin/Native sources and examples (.zip)](https://download.jetbrains.com/kotlin/native/kotlin-native-samples-1.0.1.zip) -->

* [Примеры Kotlin Multiplatform Mobile](multiplatform-mobile-samples.html)
* [Kotlin/Native исходный код и примеры](https://github.com/JetBrains/kotlin/tree/master/kotlin-native/samples)
* [Приложение KotlinConf](https://github.com/JetBrains/kotlinconf-app)
* [Приложение KotlinConf Spinner](https://github.com/jetbrains/kotlinconf-spinner)
* [Kotlin/Native исходный код и примеры (.tgz)](https://download.jetbrains.com/kotlin/native/kotlin-native-samples-1.0.1.tar.gz)
* [Kotlin/Native исходный код и примеры (.zip)](https://download.jetbrains.com/kotlin/native/kotlin-native-samples-1.0.1.zip)
