---
type: doc
layout: reference
category: "Syntax"
title: "Типобезопасные строители"
url: https://kotlinlang.ru/docs/type-safe-builders.html
---

<!-- При переводе статьи оригинальная версия была от 02 February 2026 -->

<!-- # Type-safe builders -->
# Типобезопасные строители

<!-- By using well-named functions as builders in combination with [function literals with receiver](lambdas.md#function-literals-with-receiver)
it is possible to create type-safe, statically-typed builders in Kotlin. -->
Используя хорошо названные функции в качестве строителей в сочетании с [литералами функций с объектом-приёмником](lambdas.html#function-literals-with-receiver),
можно создавать типобезопасные статически типизированные строители на Kotlin.

<!-- Type-safe builders allow creating Kotlin-based domain-specific languages (DSLs) suitable for building complex hierarchical
data structures in a semi-declarative way. Sample use cases for the builders are: -->
Типобезопасные строители позволяют создавать на основе Kotlin предметно-ориентированные языки (DSL),
которые подходят для построения сложных иерархических структур данных полу-декларативным способом.
Примеры использования таких строителей:

<!-- * Generating markup with Kotlin code, such as [HTML](https://github.com/Kotlin/kotlinx.html) or XML
* Configuring routes for a web server: [Ktor](https://ktor.io/docs/routing.html) -->

* создание разметки с помощью кода Kotlin, например [HTML](https://github.com/Kotlin/kotlinx.html) или XML;
* настройка маршрутов для веб-сервера: [Ktor](https://ktor.io/docs/routing.html).

<!-- Consider the following code: -->
Рассмотрим следующий код:

```kotlin
package html

fun main() {
    //sampleStart
    val result = html {
        head {
            title { +"HTML-кодирование с Kotlin" }
        }
        body {
            h1 { +"HTML-кодирование с Kotlin" }
            p {
                +"этот формат можно использовать как"
                +"альтернативную разметку для HTML"
            }

            // Элемент с атрибутами и текстовым содержимым
            a(href = "http://kotlinlang.org") { +"Kotlin" }

            // Смешанное содержимое
            p {
                +"Это немного"
                b { +"смешанного" }
                +"текста. Подробнее см. в"
                a(href = "http://kotlinlang.org") {
                    +"Kotlin"
                }
                +"проекте"
            }
            p {
                +"немного текста"
                ul {
                    for (i in 1..5)
                        li { +"${i}*2 = ${i*2}" }
                }
            }
        }
    }
    //sampleEnd
    println(result)
}

interface Element {
    fun render(builder: StringBuilder, indent: String)
}

class TextElement(val text: String) : Element {
    override fun render(builder: StringBuilder, indent: String) {
        builder.append("$indent$text\n")
    }
}

@DslMarker
annotation class HtmlTagMarker

@HtmlTagMarker
abstract class Tag(val name: String) : Element {
    val children = arrayListOf<Element>()
    val attributes = hashMapOf<String, String>()

    protected fun <T : Element> initTag(tag: T, init: T.() -> Unit): T {
        tag.init()
        children.add(tag)
        return tag
    }

    override fun render(builder: StringBuilder, indent: String) {
        builder.append("$indent<$name${renderAttributes()}>\n")
        for (c in children) {
            c.render(builder, indent + "  ")
        }
        builder.append("$indent</$name>\n")
    }

    private fun renderAttributes(): String {
        val builder = StringBuilder()
        for ((attr, value) in attributes) {
            builder.append(" $attr=\"$value\"")
        }
        return builder.toString()
    }

    override fun toString(): String {
        val builder = StringBuilder()
        render(builder, "")
        return builder.toString()
    }
}

abstract class TagWithText(name: String) : Tag(name) {
    operator fun String.unaryPlus() {
        children.add(TextElement(this))
    }
}

class HTML() : TagWithText("html") {
    fun head(init: Head.() -> Unit) = initTag(Head(), init)
    fun body(init: Body.() -> Unit) = initTag(Body(), init)
}

class Head() : TagWithText("head") {
    fun title(init: Title.() -> Unit) = initTag(Title(), init)
}

class Title() : TagWithText("title")

abstract class BodyTag(name: String) : TagWithText(name) {
    fun b(init: B.() -> Unit) = initTag(B(), init)
    fun p(init: P.() -> Unit) = initTag(P(), init)
    fun h1(init: H1.() -> Unit) = initTag(H1(), init)
    fun ul(init: UL.() -> Unit) = initTag(UL(), init)
    fun a(href: String, init: A.() -> Unit) {
        val a = initTag(A(), init)
        a.href = href
    }
}

class Body() : BodyTag("body")
class UL() : BodyTag("ul") {
    fun li(init: LI.() -> Unit) = initTag(LI(), init)
}

class B() : BodyTag("b")
class LI() : BodyTag("li")
class P() : BodyTag("p")
class H1() : BodyTag("h1")

class A : BodyTag("a") {
    var href: String
        get() = attributes["href"]!!
        set(value) {
            attributes["href"] = value
        }
}

fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()
    html.init()
    return html
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-type-safe-builders"}

```
<html>
  <head>
    <title>
      HTML-кодирование с Kotlin
    </title>
  </head>
  <body>
    <h1>
      HTML-кодирование с Kotlin
    </h1>
    <p>
      этот формат можно использовать как
      альтернативную разметку для HTML
    </p>
    <a href="http://kotlinlang.org">
      Kotlin
    </a>
    <p>
      Это немного
      <b>
        смешанного
      </b>
      текста. Подробнее см. в
      <a href="http://kotlinlang.org">
        Kotlin
      </a>
      проекте
    </p>
    <p>
      немного текста
      <ul>
        <li>
          1*2 = 2
        </li>
        <li>
          2*2 = 4
        </li>
        <li>
          3*2 = 6
        </li>
        <li>
          4*2 = 8
        </li>
        <li>
          5*2 = 10
        </li>
      </ul>
    </p>
  </body>
</html>
```
{collapsible="true" collapsed-title="Пример вывода"}

<a name="how-it-works"></a>

<!-- ## How it works -->
## Как это работает

<!-- Assume that you need to implement a type-safe builder in Kotlin.
First of all, define the model you want to build. In this case you need to model HTML tags.
It is easily done with a bunch of classes.
For example, `HTML` is a class that describes the `<html>` tag defining children like `<head>` and `<body>`.
(See its declaration [below](#full-definition-of-the-com-example-html-package).) -->
Предположим, что вам нужно реализовать типобезопасный строитель на Kotlin.
Прежде всего определите модель, которую вы собираетесь строить. В данном случае нужно смоделировать HTML-теги.
Это легко сделать с помощью набора классов.
Например, `HTML` — это класс, который описывает тег `<html>` и определяет его дочерние элементы, такие как `<head>` и `<body>`.
(См. его объявление [ниже](#full-definition-of-the-com-example-html-package).)

<!-- Now, let's recall why you can say something like this in the code: -->
Теперь давайте вспомним, почему в коде можно написать что-то вроде этого:

```kotlin
html {
 // ...
}
```

<!-- `html` is actually a function call that takes a [lambda expression](lambdas.md) as an argument.
This function is defined as follows: -->
На самом деле `html` — это вызов функции, которая принимает [лямбда-выражение](lambdas.html) в качестве аргумента.
Эта функция определена так:

```kotlin
fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()
    html.init()
    return html
}
```

<!-- This function takes one parameter named `init`, which is itself a function.
The type of the function is `HTML.() -> Unit`, which is a *function type with receiver*.
This means that you need to pass an instance of type `HTML` (a *receiver*) to the function,
and you can call members of that instance inside the function. -->
Эта функция принимает один параметр `init`, который сам является функцией.
Тип этой функции — `HTML.() -> Unit`, то есть *функциональный тип с объектом-приёмником*.
Это значит, что в функцию нужно передать экземпляр типа `HTML` (*приёмник*), а внутри функции можно вызывать члены этого экземпляра.

<!-- The receiver can be accessed through the `this` keyword: -->
К приёмнику можно обращаться с помощью ключевого слова `this`:

```kotlin
html {
    this.head { ... }
    this.body { ... }
}
```

<!-- (`head` and `body` are member functions of `HTML`.) -->
(`head` и `body` — функции-члены класса `HTML`.)

<!-- Now, `this` can be omitted, as usual, and you get something that looks very much like a builder already: -->
Теперь `this`, как обычно, можно опустить, и получится код, который уже очень похож на строитель:

```kotlin
html {
    head { ... }
    body { ... }
}
```

<!-- So, what does this call do? Let's look at the body of `html` function as defined above.
It creates a new instance of `HTML`, then it initializes it by calling the function that is passed as an argument
(in this example this boils down to calling `head` and `body` on the `HTML` instance), and then it returns this instance.
This is exactly what a builder should do. -->
Что делает этот вызов? Посмотрим на тело функции `html`, определённой выше.
Она создаёт новый экземпляр `HTML`, затем инициализирует его, вызывая функцию, переданную в качестве аргумента
(в этом примере всё сводится к вызовам `head` и `body` у экземпляра `HTML`), а после этого возвращает этот экземпляр.
Именно это и должен делать строитель.

<!-- The `head` and `body` functions in the `HTML` class are defined similarly to `html`.
The only difference is that they add the built instances to the `children` collection of the enclosing `HTML` instance: -->
Функции `head` и `body` в классе `HTML` определены аналогично `html`.
Единственное отличие в том, что они добавляют построенные экземпляры в коллекцию `children` внешнего экземпляра `HTML`:

```kotlin
fun head(init: Head.() -> Unit): Head {
    val head = Head()
    head.init()
    children.add(head)
    return head
}

fun body(init: Body.() -> Unit): Body {
    val body = Body()
    body.init()
    children.add(body)
    return body
}
```

<!-- Actually these two functions do just the same thing, so you can have a generic version, `initTag`: -->
На самом деле эти две функции делают одно и то же, поэтому можно использовать обобщённую версию `initTag`:

```kotlin
protected fun <T : Element> initTag(tag: T, init: T.() -> Unit): T {
    tag.init()
    children.add(tag)
    return tag
}
```

<!-- So, now your functions are very simple: -->
Теперь функции выглядят очень просто:

```kotlin
fun head(init: Head.() -> Unit) = initTag(Head(), init)

fun body(init: Body.() -> Unit) = initTag(Body(), init)
```

<!-- And you can use them to build `<head>` and `<body>` tags. -->
Их можно использовать для построения тегов `<head>` и `<body>`.

<!-- One other thing to be discussed here is how you add text to tag bodies. In the example above you say something like: -->
Ещё стоит обсудить, как добавлять текст в тело тега. В примере выше используется такой код:

```kotlin
html {
    head {
        title {+"XML encoding with Kotlin"}
    }
    // ...
}
```

<!-- So basically, you just put a string inside a tag body, but there is this little `+` in front of it,
so it is a function call that invokes a prefix `unaryPlus()` operation.
That operation is actually defined by an extension function `unaryPlus()` that is a member of the `TagWithText` abstract
class (a parent of `Title`): -->
По сути, вы помещаете строку внутрь тела тега, но перед ней стоит маленький `+`.
Это вызов функции, который запускает префиксную операцию `unaryPlus()`.
Эта операция определена функцией-расширением `unaryPlus()`, которая является членом абстрактного класса `TagWithText`
(родителя `Title`):

```kotlin
operator fun String.unaryPlus() {
    children.add(TextElement(this))
}
```

<!-- So, what the prefix `+` does here is wrapping a string into an instance of `TextElement` and adding it to the `children` collection,
so that it becomes a proper part of the tag tree. -->
Таким образом, префиксный `+` оборачивает строку в экземпляр `TextElement` и добавляет его в коллекцию `children`,
чтобы строка стала полноценной частью дерева тегов.

<!-- All this is defined in a package `com.example.html` that is imported at the top of the builder example above.
In the last section you can read through the full definition of this package. -->
Всё это определено в пакете `com.example.html`, который импортирован в начале примера строителя выше.
Полное определение этого пакета приведено в последнем разделе.

<a name="scope-control-dslmarker"></a>

<!-- ## Scope control: @DslMarker -->
## Контроль области видимости: @DslMarker

<!-- When using DSLs, one might have come across the problem that too many functions can be called in the context.
You can call methods of every available [implicit receiver](lambdas.md#function-literals-with-receiver) inside a lambda and therefore get an inconsistent result,
like the tag `head` inside another `head`: -->
При использовании DSL можно столкнуться с проблемой: в контексте доступно слишком много функций.
Внутри лямбды можно вызывать методы каждого доступного [неявного приёмника](lambdas.html#function-literals-with-receiver),
и из-за этого можно получить неконсистентный результат, например тег `head` внутри другого тега `head`:

```kotlin
html {
    head {
        head {} // должно быть запрещено
    }
    // ...
}
```

<!-- In this example only members of the nearest implicit receiver `this@head` must be available; `head()` is a member of the
outer receiver `this@html`, so it must be illegal to call it. -->
В этом примере должны быть доступны только члены ближайшего неявного приёмника `this@head`;
`head()` является членом внешнего приёмника `this@html`, поэтому такой вызов должен быть запрещён.

<!-- To address this problem, there is a special mechanism to control receiver scope. -->
Для решения этой проблемы существует специальный механизм контроля области видимости приёмников.

<!-- To make the compiler start controlling scopes you only have to annotate the types of all receivers used in the DSL with
the same marker annotation.
For instance, for HTML Builders you declare an annotation `@HtmlTagMarker`: -->
Чтобы компилятор начал контролировать области видимости, достаточно аннотировать типы всех приёмников,
используемых в DSL, одной и той же маркерной аннотацией.
Например, для HTML-строителей можно объявить аннотацию `@HtmlTagMarker`:

```kotlin
@DslMarker
@Target(AnnotationTarget.CLASS)
annotation class HtmlTagMarker
```

<!-- An annotation class is called a DSL marker if it is annotated with the `@DslMarker` annotation. -->
Класс аннотации называется DSL-маркером, если он аннотирован `@DslMarker`.

<!-- The `@Target` annotation restricts where `@HtmlTagMarker` can be applied.
DSL markers only affect scope control when applied to:

* Type declarations (`CLASS`): classes or interfaces used as DSL receivers.
* Type usages (`TYPE`): receiver types in function type signatures.
* Type aliases (`TYPEALIAS`): type aliases that expand to DSL receiver types.

Applying a DSL marker to other targets (such as functions or properties) has no effect on scope control. -->
Аннотация `@Target` ограничивает места, где можно применять `@HtmlTagMarker`.
DSL-маркеры влияют на контроль области видимости только при применении к:

* объявлениям типов (`CLASS`): классам или интерфейсам, используемым как DSL-приёмники;
* использованиям типов (`TYPE`): типам приёмников в сигнатурах функциональных типов;
* псевдонимам типов (`TYPEALIAS`): псевдонимам типов, которые разворачиваются в типы DSL-приёмников.

Применение DSL-маркера к другим целям, например функциям или свойствам, не влияет на контроль области видимости.

<!-- > For more details on how DSL marker works, see the corresponding [KEEP document](https://github.com/Kotlin/KEEP/blob/main/notes/0005-dsl-marker.md).
>
{style="note"} -->
> Подробнее о том, как работает DSL-маркер, см. в соответствующем [KEEP-документе](https://github.com/Kotlin/KEEP/blob/main/notes/0005-dsl-marker.md).
>
{style="note"}

<!-- In our DSL all the tag classes extend the same superclass `Tag`.
It's enough to annotate only the superclass with `@HtmlTagMarker` and after that the Kotlin compiler will treat all the
inherited classes as annotated: -->
В нашем DSL все классы тегов расширяют один и тот же суперкласс `Tag`.
Достаточно аннотировать `@HtmlTagMarker` только этот суперкласс, и после этого компилятор Kotlin будет считать
все унаследованные классы аннотированными:

```kotlin
@HtmlTagMarker
abstract class Tag(val name: String) { ... }
```

<!-- You don't have to annotate the `HTML` or `Head` classes with `@HtmlTagMarker` because their superclass is already annotated: -->
Не нужно аннотировать классы `HTML` или `Head` с помощью `@HtmlTagMarker`, потому что их суперкласс уже аннотирован:

```kotlin
class HTML() : Tag("html") { ... }

class Head() : Tag("head") { ... }
```

<!-- After you've added this annotation, the Kotlin compiler knows which implicit receivers are part of the same DSL and allows to call members of the nearest receivers only: -->
После добавления этой аннотации компилятор Kotlin знает, какие неявные приёмники являются частью одного DSL,
и разрешает вызывать только члены ближайших приёмников:

```kotlin
html {
    head {
        head { } // ошибка: член внешнего приёмника
    }
    // ...
}
```

<!-- Note that it's still possible to call the members of the outer receiver, but to do that you have to specify this receiver explicitly: -->
Обратите внимание, что члены внешнего приёмника всё ещё можно вызывать, но для этого нужно указать этот приёмник явно:

```kotlin
html {
    head {
        this@html.head { } // возможно
    }
    // ...
}
```

<!-- You can also apply the `@DslMarker` annotation directly to [function types](lambdas.md#function-types).
This requires including `AnnotationTarget.TYPE` in the annotation targets: -->
Аннотацию `@DslMarker` также можно применять напрямую к [функциональным типам](lambdas.html#function-types).
Для этого нужно добавить `AnnotationTarget.TYPE` в цели аннотации:

```kotlin
@DslMarker
@Target(AnnotationTarget.CLASS, AnnotationTarget.TYPE)
annotation class HtmlTagMarker
```

<!-- As a result, the `@DslMarker` annotation can be applied to function types, most commonly to lambdas with receivers. For example: -->
В результате аннотацию `@DslMarker` можно применять к функциональным типам, чаще всего к лямбдам с приёмниками.
Например:

```kotlin
fun html(init: @HtmlTagMarker HTML.() -> Unit): HTML { ... }

fun HTML.head(init: @HtmlTagMarker Head.() -> Unit): Head { ... }

fun Head.title(init: @HtmlTagMarker Title.() -> Unit): Title { ... }
```

<!-- When you call these functions, the `@DslMarker` annotation restricts access to outer receivers in the body of a lambda marked with it unless you specify them explicitly: -->
При вызове таких функций аннотация `@DslMarker` ограничивает доступ к внешним приёмникам в теле помеченной ей лямбды,
если эти приёмники не указаны явно:

```kotlin
html {
    head {
        title {
            // Доступ к title, head и другим функциям внешних приёмников здесь ограничен.
        }
    }
}
```

<!-- Only the nearest receiver's members and extensions are accessible within a lambda, preventing unintended interactions between nested scopes. -->
Внутри лямбды доступны только члены и расширения ближайшего приёмника, что предотвращает нежелательное взаимодействие
между вложенными областями видимости.

<!-- When both a member of an implicit receiver and a declaration from a [context parameter](context-parameters.md) are in a scope with the same name,
the compiler reports a warning because the implicit receiver is shadowed by the context parameter.
To resolve this, use a `this` qualifier to explicitly call the receiver, or use `contextOf<T>()` to call the context declaration: -->
Когда член неявного приёмника и объявление из [контекстного параметра](https://kotlinlang.org/docs/context-parameters.html)
с одним и тем же именем находятся в одной области видимости, компилятор сообщает предупреждение, потому что неявный приёмник
скрыт контекстным параметром.
Чтобы решить проблему, используйте квалификатор `this` для явного вызова приёмника или `contextOf<T>()` для вызова
контекстного объявления:

```kotlin
interface HtmlTag {
    fun setAttribute(name: String, value: String)
}

// Объявляет функцию верхнего уровня с таким же именем,
// которая доступна через контекстный параметр
context(tag: HtmlTag)
fun setAttribute(name: String, value: String) { tag.setAttribute(name, value) }

fun test(head: HtmlTag, extraInfo: HtmlTag) {
    with(head) {
        // Вводит контекстное значение того же типа во внутренней области видимости
        context(extraInfo) {
            // Сообщает предупреждение:
            // Использует неявный приёмник, скрытый контекстным параметром
            setAttribute("user", "1234")

            // Явно вызывает член приёмника
            this.setAttribute("user", "1234")

            // Явно вызывает контекстное объявление
            contextOf<HtmlTag>().setAttribute("user", "1234")
        }
    }
}
```

<a name="full-definition-of-the-com-example-html-package"></a>

<!-- ### Full definition of the com.example.html package -->
### Полное определение пакета com.example.html

<!-- This is how the package `com.example.html` is defined (only the elements used in the example above).
It builds an HTML tree. It makes heavy use of [extension functions](extensions.md) and
[lambdas with receiver](lambdas.md#function-literals-with-receiver). -->
Ниже приведено определение пакета `com.example.html` (только элементы, использованные в примере выше).
Он строит HTML-дерево и активно использует [функции-расширения](extensions.html) и
[лямбды с приёмником](lambdas.html#function-literals-with-receiver).

```kotlin
package com.example.html

interface Element {
    fun render(builder: StringBuilder, indent: String)
}

class TextElement(val text: String) : Element {
    override fun render(builder: StringBuilder, indent: String) {
        builder.append("$indent$text\n")
    }
}

@DslMarker
@Target(AnnotationTarget.CLASS, AnnotationTarget.TYPE)
annotation class HtmlTagMarker

@HtmlTagMarker
abstract class Tag(val name: String) : Element {
    val children = arrayListOf<Element>()
    val attributes = hashMapOf<String, String>()

    protected fun <T : Element> initTag(tag: T, init: T.() -> Unit): T {
        tag.init()
        children.add(tag)
        return tag
    }

    override fun render(builder: StringBuilder, indent: String) {
        builder.append("$indent<$name${renderAttributes()}>\n")
        for (c in children) {
            c.render(builder, indent + "  ")
        }
        builder.append("$indent</$name>\n")
    }

    private fun renderAttributes(): String {
        val builder = StringBuilder()
        for ((attr, value) in attributes) {
            builder.append(" $attr=\"$value\"")
        }
        return builder.toString()
    }

    override fun toString(): String {
        val builder = StringBuilder()
        render(builder, "")
        return builder.toString()
    }
}

abstract class TagWithText(name: String) : Tag(name) {
    operator fun String.unaryPlus() {
        children.add(TextElement(this))
    }
}

class HTML : TagWithText("html") {
    fun head(init: Head.() -> Unit) = initTag(Head(), init)

    fun body(init: Body.() -> Unit) = initTag(Body(), init)
}

class Head : TagWithText("head") {
    fun title(init: Title.() -> Unit) = initTag(Title(), init)
}

class Title : TagWithText("title")

abstract class BodyTag(name: String) : TagWithText(name) {
    fun b(init: B.() -> Unit) = initTag(B(), init)
    fun p(init: P.() -> Unit) = initTag(P(), init)
    fun h1(init: H1.() -> Unit) = initTag(H1(), init)
    fun a(href: String, init: A.() -> Unit) {
        val a = initTag(A(), init)
        a.href = href
    }
}

class Body : BodyTag("body")
class B : BodyTag("b")
class P : BodyTag("p")
class H1 : BodyTag("h1")

class A : BodyTag("a") {
    var href: String
        get() = attributes["href"]!!
        set(value) {
            attributes["href"] = value
        }
}

fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()
    html.init()
    return html
}
```

<!-- See also: [This expressions](this-expressions.md), [Using builders with builder type inference](using-builders-with-builder-inference.md). -->
См. также: [выражения `this`](this-expressions.html), [использование строителей с выводом типов строителей](using-builders-with-builder-inference.html).
