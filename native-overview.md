---
type: doc
layout: reference
title: "Kotlin Native"
url: https://kotlinlang.ru/docs/native-overview.html
---

<!-- При переводе статьи оригинальная версия была от 26 November 2025 -->

# Kotlin/Native

Kotlin/Native - это технология компиляции Kotlin-кода в нативные двоичные файлы, которые могут запускаться без
виртуальной машины. Kotlin/Native включает в себя бэкенд компилятора Kotlin на основе [LLVM](https://llvm.org/) и
нативную реализацию стандартной библиотеки Kotlin.

<a name="why-kotlin-native"></a>

## Почему Kotlin/Native?

Kotlin/Native в первую очередь предназначен для компиляции под платформы, на которых *виртуальные машины* нежелательны
или невозможны, например для встраиваемых устройств или iOS. Он хорошо подходит для ситуаций, когда нужно создать
самодостаточную программу, не требующую дополнительной среды выполнения или виртуальной машины.

Скомпилированный Kotlin-код легко включать в существующие проекты на C, C++, Swift, Objective-C и других языках. Также
из Kotlin/Native можно напрямую использовать существующий нативный код, статические или динамические библиотеки C,
Swift/Objective-C-фреймворки, графические движки и любые другие нативные компоненты.

<a name="target-platforms"></a>

## Целевые платформы

Kotlin/Native поддерживает следующие платформы:

* Linux
* Windows (через [MinGW](https://www.mingw-w64.org/))
* [Android NDK](https://developer.android.com/ndk)
* Apple-платформы для macOS, iOS, tvOS и watchOS

Для компиляции под Apple-платформы нужно установить [Xcode](https://apps.apple.com/us/app/xcode/id497799835) и его
инструменты командной строки.

[Полный список поддерживаемых целей и хостов](https://kotlinlang.org/docs/native-target-support.html).

<a name="interoperability"></a>

## Совместимость

Kotlin/Native поддерживает двустороннюю совместимость с нативными языками программирования для разных операционных
систем. Компилятор может создавать исполняемые файлы для многих платформ, статические или динамические библиотеки C и
Swift/Objective-C-фреймворки.

<a name="interoperability-with-c"></a>

### Совместимость с C

Kotlin/Native предоставляет [совместимость с C](https://kotlinlang.org/docs/native-c-interop.html). Существующие
библиотеки C можно использовать напрямую из Kotlin-кода.

Чтобы узнать больше, пройдите следующие руководства:

* [Создание динамической библиотеки с C-заголовками для проектов C/C++](https://kotlinlang.org/docs/native-dynamic-libraries.html)
* [Сопоставление типов C и Kotlin](https://kotlinlang.org/docs/mapping-primitive-data-types-from-c.html)
* [Создание нативного HTTP-клиента с C interop и libcurl](https://kotlinlang.org/docs/native-app-with-c-and-libcurl.html)

<a name="interoperability-with-swift-objective-c"></a>

### Совместимость со Swift/Objective-C

Kotlin/Native предоставляет
[совместимость со Swift через Objective-C](https://kotlinlang.org/docs/native-objc-interop.html). Kotlin-код можно
использовать напрямую из приложений на Swift/Objective-C для macOS и iOS.

Чтобы узнать больше, пройдите руководство
[Kotlin/Native как Apple-фреймворк](https://kotlinlang.org/docs/apple-framework.html).

<a name="sharing-code-between-platforms"></a>

## Использование кода на нескольких платформах

Kotlin/Native включает набор готовых
[платформенных библиотек](https://kotlinlang.org/docs/native-platform-libs.html), которые помогают использовать
Kotlin-код в разных проектах. POSIX, gzip, OpenGL, Metal, Foundation и многие другие популярные библиотеки и
Apple-фреймворки уже импортированы и включены в пакет компилятора как библиотеки Kotlin/Native.

Kotlin/Native является частью технологии [Kotlin Multiplatform](multiplatform.html), которая помогает использовать общий
код на нескольких платформах, включая Android, iOS, JVM, web и native. Мультиплатформенные библиотеки предоставляют
необходимые API для общего Kotlin-кода и позволяют писать общие части проектов на Kotlin в одном месте.

<a name="memory-manager"></a>

## Менеджер памяти

Kotlin/Native использует автоматический [менеджер памяти](https://kotlinlang.org/docs/native-memory-manager.html),
похожий на JVM и Go. У него есть собственный трассирующий сборщик мусора, который также интегрирован с ARC в
Swift/Objective-C.

Потребление памяти контролируется пользовательским аллокатором. Он оптимизирует использование памяти и помогает
предотвращать резкие всплески выделения памяти.
