---
type: doc
layout: reference
category: "Classes and Objects"
title: "Модификаторы доступа"
url: https://kotlinlang.ru/docs/visibility-modifiers.html
---

<!-- При переводе статьи оригинальная версия была от 14 September 2021 -->

<!-- # Visibility modifiers -->
# Модификаторы доступа

<!-- Classes, objects, interfaces, constructors, and functions, as well as properties and their setters, can have *visibility modifiers*.
Getters always have the same visibility as their properties. --> 
Классы, объекты, интерфейсы, конструкторы, функции, свойства и их сеттеры могут иметь *модификаторы доступа*.
Геттеры всегда имеют тут же видимость, что и свойства, к которым они относятся.

<!-- There are four visibility modifiers in Kotlin: `private`, `protected`, `internal`, and `public`.
The default visibility is `public`. -->
В Kotlin предусмотрено четыре модификатора доступа: `private`, `protected`, `internal` и `public`.
Если явно не использовать никакого модификатора, то по умолчанию применяется `public`.

<!-- On this page, you'll learn how the modifiers apply to different types of declaring scopes. -->
На этой странице вы узнаете, как использовать модификаторы для всех областей видимости.

<a name="packages"></a>
<!-- ## Packages -->
## Пакеты

<!--Functions, properties, classes, objects, and interfaces can be declared at the "top-level" directly inside a package:-->
Функции, свойства, классы, объекты и интерфейсы могут быть объявлены на самом "высоком уровне" прямо внутри пакета.

```kotlin
// имя файла: example.kt
package foo

fun baz() { /*...*/ }
class Bar { /*...*/ }
```

<!-- * If you don’t use a visibility modifier, `public` is used by default, which means that your declarations will be
  visible everywhere.
* If you mark a declaration as `private`, it will only be visible inside the file that contains the declaration.
* If you mark it as `internal`, it will be visible everywhere in the same [module](#modules).
* The `protected` modifier is not available for top-level declarations. -->
* Если модификатор доступа не указан, будет использован `public`. Это значит, что весь код данного объявления будет виден из космоса;
* Если вы пометите объявление словом `private`, оно будет иметь видимость только внутри файла, где было объявлено;
* Если вы используете `internal`, видимость будет распространяться на весь [модуль](visibility-modifiers.html#modules);
* `protected` запрещено использовать в объявлениях "высокого уровня".

<!-- >To use a visible top-level declaration from another package, you should [import](packages.md#imports) it. -->
> Чтобы использовать видимое объявление верхнего уровня из другого пакета, вы должны [импортировать](packages.html#imports) его.

<!-- Examples: -->
Примеры:

```kotlin
// имя файла: example.kt
package foo

private fun foo() { /*...*/ } // имеет видимость внутри example.kt

public var bar: Int = 5       // свойство видно со дна Марианской впадины
    private set               // сеттер видно только внутри example.kt

internal val baz = 6          // имеет видимость внутри модуля
```

<a name="class-members"></a>
<!-- ## Class members -->
## Члены класса

<!-- For members declared inside a class: -->
Для членов, объявленных в классе:

<!-- * `private` means that the member is visible inside this class only (including all its members).
* `protected` means that the member has the same visibility as one marked as `private`, but that it is also visible in subclasses.
* `internal` means that any client *inside this module* who sees the declaring class sees its `internal` members.
* `public` means that any client who sees the declaring class sees its `public` members. -->
* `private` означает видимость только внутри этого класса (включая его члены);
* `protected` — то же самое, что и `private` + видимость в субклассах;
* `internal` — любой клиент *внутри модуля*, который видит объявленный класс, видит и его `internal` члены;
* `public` — любой клиент, который видит объявленный класс, видит его `public` члены.

<!-- > In Kotlin, an outer class does not see private members of its inner classes. -->
> В Kotlin внешний класс не видит `private` члены своих вложенных классов.

<!-- If you override a `protected` or an `internal` member and do not specify the visibility explicitly, the overriding member
will also have the same visibility as the original. -->
Если вы переопределите `protected` или `internal` член и явно не укажете его видимость,
переопределённый элемент будет иметь тот же модификатор, что и исходный.

<!-- Examples: -->
Примеры:

```kotlin
open class Outer {
    private val a = 1
    protected open val b = 2
    internal open val c = 3
    val d = 4 // public по умолчанию

    protected class Nested {
        public val e: Int = 5
    }
}

class Subclass : Outer() {
    // a не видно
    // b, c и d видно
    // класс Nested и e видно

    override val b = 5   // b - protected
    override val c = 7   // c - internal
}

class Unrelated(o: Outer) {
    // o.a и o.b не видно
    // o.c и o.d видно (тот же модуль)
    // Outer.Nested не видно, и Nested::e также не видно
}
```

<a name="constructors"></a>
<!-- ### Constructors -->
### Конструкторы

<!-- Use the following syntax to specify the visibility of the primary constructor of a class: -->
Для указания видимости основного конструктора класса используется следующий синтаксис:

<!-- > You need to add an explicit `constructor` keyword. -->
> Вам необходимо добавить ключевое слово `constructor`.

```kotlin
class C private constructor(a: Int) { ... }
```

<!-- Here the constructor is private. By default, all constructors are `public`, which effectively
amounts to them being visible everywhere the class is visible (this means that a constructor of an `internal` class is only
visible within the same module). -->
В этом примере конструктор помечен `private`. По умолчанию все конструкторы имеют модификатор доступа `public`,
то есть видны везде, где виден сам класс (а вот конструктор `internal` класса видно только в том же модуле).

<a name="local-declarations"></a>
<!-- ### Local declarations -->
### Локальные объявления

<!-- Local variables, functions, and classes can't have visibility modifiers. -->
Локальные переменные, функции и классы не могут иметь модификаторов доступа. <!-- rcd27: неожиданно... --> <!-- StarHamster: реально -->

<a name="modules"></a>
<!-- ## Modules -->
## Модули

<!-- The `internal` visibility modifier means that the member is visible within the same module. More specifically,
a module is a set of Kotlin files compiled together, for example: -->
Модификатор доступа `internal` означает, что этот член видно в рамках его модуля.
Модуль - это набор скомпилированных вместе Kotlin файлов, например:

<!-- * An IntelliJ IDEA module.
* A Maven project.
* A Gradle source set (with the exception that the `test` source set can access the internal declarations of `main`).
* A set of files compiled with one invocation of the `<kotlinc>` Ant task. -->
* модуль IntelliJ IDEA;
* Maven проект;
* исходный набор Gradle (за исключением того, что исходный набор `test` может получить доступ к внутренним объявлениям `main`);
* набор скомпилированных вместе файлов с одним способом вызова `<kotlinc>` задачи в Ant.
