---
type: doc
layout: reference
category: "Syntax"
title: "Пакеты и импорты"
url: https://kotlinlang.ru/docs/packages.html
---

<!-- При переводе статьи оригинальная версия была от 01 June 2021 -->

<!-- Packages and imports -->
# Пакеты и импорты

<!-- A source file may start with a package declaration: -->
Файл с исходным кодом может начинаться с объявления пакета.

```kotlin
package org.example

fun printMessage() { /*...*/ }
class Message { /*...*/ }

// ...
```

<!-- All the contents, such as classes and functions, of the source file are included in this package.
So, in the example above, the full name of `printMessage()` is `org.example.printMessage`,
and the full name of `Message` is `org.example.Message`. -->
Всё содержимое файла с исходниками (например, классы и функции) располагается в объявленном пакете.
Таким образом, в приведённом выше примере полное имя функции `printMessage()` будет `org.example.printMessage`,
а полное имя класса `Message` - `org.example.Message`.

<!-- If the package is not specified, the contents of such a file belong to the _default_ package with no name. -->
Если файл не содержит явного объявления пакета, то его содержимое находится в безымянном пакете *по умолчанию*.

<a name="default-imports"></a>

<!-- ## Default imports -->
## Импорт по умолчанию

<!-- A number of packages are imported into every Kotlin file by default: -->
По умолчанию в каждый файл Kotlin импортируется несколько пакетов:

* [kotlin.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/index.html)
* [kotlin.annotation.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/index.html)
* [kotlin.collections.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index.html)
* [kotlin.comparisons.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.comparisons/index.html)
* [kotlin.io.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/index.html)
* [kotlin.ranges.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/index.html)
* [kotlin.sequences.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/index.html)
* [kotlin.text.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/index.html)

<!-- Additional packages are imported depending on the target platform: -->
Дополнительные пакеты импортируются в зависимости от платформы:

* JVM:
  * java.lang.\*
  * [kotlin.jvm.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/index.html)

* JS:
  * [kotlin.js.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.js/index.html)

<a name="imports"></a>

<!-- ## Imports -->
## Импорт

<!-- Apart from the default imports, each file may contain its own `import` directives. -->
Помимо импорта по умолчанию каждый файл может содержать свои собственные объявления импорта.

<!-- You can import either a single name: -->
Вы можете импортировать одно имя:

```kotlin
import org.example.Message // теперь Message можно использовать без указания пакета
```

Можете импортировать всё доступное содержимое пространства имён (пакет, класс, объект и т.д.):

```kotlin
import org.example.* // всё в 'org.example' становится доступно без указания пакета
```

<!-- If there is a name clash, you can disambiguate by using `as` keyword to locally rename the clashing entity: -->
При совпадении имён мы можем разрешить коллизию используя ключевое слово `as` для локального переименования совпадающей сущности.

```kotlin
import org.example.Message // Message доступен
import org.test.Message as testMessage // testMessage заменяет имя 'org.test.Message'
```

<!-- The `import` keyword is not restricted to importing classes; you can also use it to import other declarations: -->
Ключевое слово `import` можно использовать не только с классами, но и с другими объявлениями:

<!-- * top-level functions and properties
  * functions and properties declared in [object declarations](object-declarations.md#object-declarations-overview)
  * [enum constants](enum-classes.md) -->

* функции и свойства верхнего уровня;
* функции и свойства, объявленные в [объявлениях объектов](object-declarations.html#object-declarations-overview);
* [перечислениях](enum-classes.html).

<a name="visibility-of-top-level-declarations"></a>

<!-- ## Visibility of top-level declarations -->
## Область видимости объявлений верхнего уровня

<!-- If a top-level declaration is marked `private`, it is private to the file it's declared in (see [Visibility modifiers](visibility-modifiers.md)). -->
Если объявление верхнего уровня отмечено как `private`, то оно является private в файле, в котором оно объявлено
(см. [Модификаторы области видимости](visibility-modifiers.html)).
