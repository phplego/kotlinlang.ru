---
type: doc
layout: reference
category: "Classes and Objects"
title: "Изолированные классы"
url: https://kotlinlang.ru/docs/sealed-classes.html
---

<!-- При переводе статьи оригинальная версия была от 10 January 2022 -->

<!-- # Sealed classes -->
# Изолированные классы

<!-- _Sealed_ classes and interfaces represent restricted class hierarchies that provide more control over inheritance.
All direct subclasses of a sealed class are known at compile time. No other subclasses may appear after
a module with the sealed class is compiled. For example, third-party clients can't extend your sealed class in their code.
Thus, each instance of a sealed class has a type from a limited set that is known when this class is compiled. -->
Изолированные классы и интерфейсы позволяют выразить ограниченные иерархии классов, которые обеспечивают больший
контроль над наследованием. Во время компиляции известны все прямые наследники изолированного класса. Никакие другие
наследники не могут появиться после компиляции модуля с изолированным классом. Например, сторонние клиенты не могут
расширить ваш изолированный класс в своем коде. Таким образом, каждый экземпляр изолированного класса имеет тип из
ограниченного набора, который известен при компиляции этого класса.

<!-- The same works for sealed interfaces and their implementations: once a module with a sealed interface is compiled,
no new implementations can appear. -->
То же самое справедливо для изолированных интерфейсов и их реализаций: новые реализации не могут появиться после
компиляции модуля с изолированным интерфейсом.

<!-- In some sense, sealed classes are similar to [`enum`](enum-classes.md) classes: the set of values
for an enum type is also restricted, but each enum constant exists only as a _single instance_, whereas a subclass
of a sealed class can have _multiple_ instances, each with its own state. -->
Изолированные классы похожи на [enum-классы](enum-classes.html): набор значений enum типа также ограничен, но каждая
enum-константа существует только *в единственном экземпляре*, в то время как наследник изолированного класса может иметь
*несколько* экземпляров, которые могут нести в себе какое-то состояние.

<!-- As an example, consider a library's API. It's likely to contain error classes to let the library users handle errors 
that it can throw. If the hierarchy of such error classes includes interfaces or abstract classes visible in the public API,
then nothing prevents implementing or extending them in the client code. However, the library doesn't know about errors
declared outside it, so it can't treat them consistently with its own classes. With a sealed hierarchy of error classes,
library authors can be sure that they know all possible error types and no other ones can appear later. -->
В качестве примера рассмотрим API библиотеки. Вероятно, он будет содержать классы ошибок, чтобы пользователи библиотеки
могли обрабатывать возникающие ошибки. Если иерархия таких классов ошибок включает интерфейсы или абстрактные классы,
видимые в общедоступном API, то ничто не препятствует их реализации или расширению в клиентском коде. Однако библиотека
не знает об ошибках, объявленных за её пределами, поэтому не может обрабатывать их согласованно с помощью собственных
классов. Благодаря изолированной иерархии классов ошибок авторы библиотек могут быть уверены, что им известны все
возможные типы ошибок, и никакие другие не могут появиться позже.

<!-- To declare a sealed class or interface, put the `sealed` modifier before its name: -->
Чтобы описать изолированный класс или интерфейс, укажите модификатор `sealed` перед его именем.

```kotlin
sealed interface Error

sealed class IOError(): Error

class FileReadError(val f: File): IOError()
class DatabaseError(val source: DataSource): IOError()

object RuntimeError : Error
```

<!-- A sealed class is [abstract](classes.md#abstract-classes) by itself, it cannot be instantiated directly and can have `abstract` members. -->
Сам по себе изолированный класс является [абстрактным](classes.html#abstract-classes), он не может быть создан напрямую
и может иметь абстрактные компоненты.

<!-- Constructors of sealed classes can have one of two [visibilities](visibility-modifiers.md): `protected` (by default) or
`private`: -->

Конструкторы изолированных классов могут иметь одну из двух [видимостей](visibility-modifiers.html): `protected` (по
умолчанию) или `private`.

```kotlin
sealed class IOError {
    constructor() { /*...*/ } // protected по умолчанию
    private constructor(description: String): this() { /*...*/ } // private допускается
    // public constructor(code: Int): this() {} // Ошибка: public и internal не допускаются
}
```

<a name="location-of-direct-subclasses"></a>
<!-- ## Location of direct subclasses -->
## Расположение прямых наследников

<!-- Direct subclasses of sealed classes and interfaces must be declared in the same package. They may be top-level or nested
inside any number of other named classes, named interfaces, or named objects. Subclasses can have any [visibility](visibility-modifiers.md)
as long as they are compatible with normal inheritance rules in Kotlin. -->
Прямые наследники изолированных классов и интерфейсов должны быть объявлены в том же пакете. Они могут быть верхнего
уровня или вложены в любое количество других проименованных классов, проименованных интерфейсов или проименованных
объектов. Наследники могут иметь любую [видимость](visibility-modifiers.html), если они совместимы с обычными правилами
наследования в Kotlin.

<!-- Subclasses of sealed classes must have a proper qualified name. They can't be local nor anonymous objects. -->
Наследники изолированных классов должны иметь правильные имена. Они не могут быть локальными или анонимными объектами.

<!-- > `enum` classes can't extend a sealed class (as well as any other class), but they can implement sealed interfaces. -->
> `Enum`-классы не могут расширять изолированный класс (как и любой другой класс), но они могут реализовывать
изолированные интерфейсы.

<!-- These restrictions don't apply to indirect subclasses. If a direct subclass of a sealed class is not marked as sealed,
it can be extended in any way that its modifiers allow: -->
Эти ограничения не применяются к непрямым наследникам. Если прямой наследник изолированного класса не помечен как
изолированный, он может быть расширен любыми способами, разрешенными его модификаторами.

```kotlin
sealed interface Error // имеет реализации только в том же пакете и модуле

sealed class IOError(): Error // расширяется только в том же пакете и модуле
open class CustomError(): Error // может быть расширен везде, где виден
```

<a name="inheritance-in-multiplatform-projects"></a>
<!-- ### Inheritance in multiplatform projects -->
## Наследование в мультиплатформенных проектах

<!-- There is one more inheritance restriction in [multiplatform projects](mpp-intro.md): direct subclasses of sealed classes must
reside in the same source set. It applies to sealed classes without the [`expect` and `actual` modifiers](mpp-connect-to-apis.md). -->
В [мультиплатформенных проектах](mpp-intro.html) есть еще одно ограничение наследования: прямые наследники изолированных
классов должны находиться в одном модуле. Это применимо к изолированным классам без
[модификаторов `expect` и `actual`](mpp-connect-to-apis.html).

<!-- If a sealed class is declared as `expect` in a common source set and have `actual` implementations in platform source sets,
both `expect` and `actual` versions can have subclasses in their source sets. Moreover, if you use a [hierarchical structure](mpp-share-on-platforms.md#share-code-on-similar-platforms),
you can create subclasses in any source set between the `expect` and `actual` declarations. -->
Если изолированный класс объявлен как `expected` в общем модуле и имеет `actual` реализации в платформенном модуле, как
ожидаемая, так и актуальные версии могут иметь наследников в своих модулях. Более того, если вы используете
[иерархическую структуру](mpp-share-on-platforms.html#share-code-on-similar-platforms), вы можете создавать наследников
в любом исходном наборе между `expect` и `actual` объявлениями.

<!-- [Learn more about the hierarchical structure of multiplatform projects](mpp-share-on-platforms.md#share-code-on-similar-platforms). -->
Узнайте больше о [иерархической структуре мультиплатформенных проектов](mpp-share-on-platforms.html#share-code-on-similar-platforms).

<a name="sealed-classes-and-when-expression"></a>
<!-- ## Sealed classes and when expression -->
## Изолированные классы и выражение when

<!-- The key benefit of using sealed classes comes into play when you use them in a [`when`](control-flow.md#when-expression)
expression.
If it's possible to verify that the statement covers all cases, you don't need to add an `else` clause to the statement.
However, this works only if you use `when` as an expression (using the result) and not as a statement: -->
Ключевое преимущество от использования изолированных классов проявляется тогда, когда вы используете их в
[выражении `when`](control-flow.html#when-expression). Если возможно проверить, что выражение покрывает все случаи,
то вам не нужно добавлять `else`. Однако, это работает только в том случае, если вы используете `when` как выражение
(используя результат), а не как оператор.

```kotlin
fun log(e: Error) = when(e) {
    is FileReadError -> { println("Error while reading file ${e.file}") }
    is DatabaseError -> { println("Error while reading from database ${e.source}") }
    RuntimeError ->  { println("Runtime error") }
    // оператор `else` не требуется, потому что мы покрыли все возможные случаи
}
```

<!-- > `when` expressions on [`expect`](mpp-connect-to-apis.md) sealed classes in the common code of multiplatform projects still 
> require an `else` branch. This happens because subclasses of `actual` platform implementations aren't known in the 
> common code. -->

> Выражение `when` в [`expect`](mpp-connect-to-apis.html) изолированных классах в общем коде многоплатформенных проектов
> по-прежнему требует ветки `else`. Это происходит потому, что наследники актуальных реализаций платформы не известны
> в общем коде.
