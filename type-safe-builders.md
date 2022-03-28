---
type: doc
layout: reference
category: "Syntax"
title: "Типобезопасные строители"
url: https://kotlinlang.ru/docs/type-safe-builders.html
---

<!-- При переводе статьи оригинальная версия была от 02 February 2022 -->

<!-- # Type-safe builders -->
# Типобезопасные строители

<!-- By using well-named functions as builders in combination with [function literals with receiver](lambdas.md#function-literals-with-receiver) 
it is possible to create type-safe, statically-typed builders in Kotlin. -->
Используя хорошо названные функции в качестве строителей в сочетании с [литералами функций с объектом-приёмником](lambdas.md#function-literals-with-receiver),
можно создавать типобезопасные статически типизированные строители.

<!-- Type-safe builders allow creating Kotlin-based domain-specific languages (DSLs) suitable for building complex hierarchical 
data structures in a semi-declarative way. Sample use cases for the builders are: -->
Типобезопасные строители позволяют на основе Kotlin создавать доменно-ориентированные языки (ориг.: *domain-specific languages*, DSL),
подходящие для построения сложных иерархических структур данных полу-декларативным способом. Примерами использования
строителей являются:

<!-- * Generating markup with Kotlin code, such as [HTML](https://github.com/Kotlin/kotlinx.html) or XML
* Configuring routes for a web server: [Ktor](https://ktor.io/docs/routing.html) -->

* Создание разметки с помощью кода Kotlin, например [HTML](https://github.com/Kotlin/kotlinx.html) or XML,
* Настройка маршрутов для веб-сервера: [Ktor](https://ktor.io/docs/routing.html).

<!-- Consider the following code: -->
Рассмотрим следующий код:

```kotlin
import com.example.html.* // смотрите объявления ниже

fun result() =
    html {
        head {
            title {+"XML кодирование с Kotlin"}
        }
        body {
            h1 {+"XML кодирование с Kotlin"}
            p  {+"этот формат может быть использован как альтернатва XML"}

            // элемент с атрибутом и текстовым содержанием
            a(href = "http://kotlinlang.ru") {+"Kotlin"}

            // смешанный контент
            p {
                +"Немного"
                b {+"смешанного"}
                +"текста. Посмотрите наш"
                a(href = "http://kotlinlang.org") {+"перевод"}
                +"документации Kotlin."
            }
            p {+"немного текста"}

            // контент генерируется в цикле
            p {
                for (arg in args)
                    +arg
            }
        }
    }
```

<!-- This is completely legitimate Kotlin code.
You can [play with this code online (modify it and run in the browser) here](https://play.kotlinlang.org/byExample/09_Kotlin_JS/06_HtmlBuilder). -->
Всё это полностью корректный Kotlin-код.
[Здесь](https://play.kotlinlang.org/byExample/09_Kotlin_JS/06_HtmlBuilder)
вы можете отредактировать и запустить пример с этим кодом прямо у себя в браузере.

<a name="how-it-works"></a>

<!-- ## How it works -->
## Как это работает

<!-- Assume that you need to implement a type-safe builder in Kotlin.
First of all, define the model you want to build. In this case you need to model HTML tags.
It is easily done with a bunch of classes.
For example, `HTML` is a class that describes the `<html>` tag defining children like `<head>` and `<body>`.
(See its declaration [below](#full-definition-of-the-com-example-html-package).) -->
Предположим, что вам нужно реализовать типобезопасный строитель в Kotlin.
Прежде всего, вам нужно определить модель, которую вы собираетесь строить. В данном случае это HTML-тэги.
Вы можете сделать это без труда с помощью нескольких классов.
К примеру, `HTML` — это класс, который описывает тэг `<html>`, т.е. он определяет потомков, таких как `<head>` и `<body>`.
(См. его объявление [ниже](#full-definition-of-the-com-example-html-package).)

<!-- Now, let's recall why you can say something like this in the code: -->
Теперь давайте вернёмся к вопросу почему вы можете писать вот такой код:

```kotlin
html {
 // ...
}
```

<!-- `html` is actually a function call that takes a [lambda expression](lambdas.md) as an argument.
This function is defined as follows: -->
На самом деле, `html` является вызовом функции, которая принимает [лямбда-выражение](lambdas.html) в качестве аргумента.
Вот как эта функция определена:

```kotlin
fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()
    html.init()
    return html
}
```

<!--This function takes one parameter named `init`, which is itself a function.
The type of the function is `HTML.() -> Unit`, which is a *function type with receiver*.
This means that you need to pass an instance of type `HTML` (a *receiver*) to the function,
and you can call members of that instance inside the function.-->
Эта функция принимает один параметр-функцию под названием `init`.
Тип этой функции: `HTML.() -> Unit` — *функциональный тип с объектом-приёмником*.
Это значит, что вам нужно передать экземпляр класса `HTML` (*приёмник*) в функцию,
и вы сможете обращаться к членам объекта в теле этой функции.

<!-- The receiver can be accessed through the `this` keyword: -->
Обращение происходит через ключевое слово `this`.

```kotlin
html {
    this.head { /* ... */ }
    this.body { /* ... */ }
}
```

<!--(`head` and `body` are member functions of `HTML`.)-->
(`head` и `body` — члены класса `HTML`)

<!-- Now, `this` can be omitted, as usual, and you get something that looks very much like a builder already: -->
`this`, как обычно, можно опустить, и вы получите нечто, что уже очень похожее на строителя.

```kotlin
html {
    head { /*...*/ }
    body { /*...*/ }
}
```

<!-- So, what does this call do? Let's look at the body of `html` function as defined above.
It creates a new instance of `HTML`, then it initializes it by calling the function that is passed as an argument
(in this example this boils down to calling `head` and `body` on the `HTML` instance), and then it returns this instance. 
This is exactly what a builder should do. -->
Итак, что же делает этот вызов? Давайте посмотрим на тело функции `html`, объявленной выше.
Она создаёт новый экземпляр `HTML`, затем инициализирует его путём вызова функции, которая была передана в аргументе
(в примере это сводится к вызову `head` и `body` у объекта `HTML`), и после этого возвращает его значение.
Это в точности то, что и должен делать строитель.

<!-- The `head` and `body` functions in the `HTML` class are defined similarly to `html`. 
The only difference is that they add the built instances to the `children` collection of the enclosing `HTML` instance: -->
Функции `head` и `body` в классе `HTML` объявлены схоже с функцией `html`.
Единственное отличие в том, что они добавляют отстроенные экземпляры в коллекцию `children` заключающего экземпляра `HTML`.

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
На самом деле эти две функции делают одно и тоже, поэтому вы можете использовать обобщённую версию, `initTag`.

```kotlin
protected fun <T : Element> initTag(tag: T, init: T.() -> Unit): T {
    tag.init()
    children.add(tag)
    return tag
}
```

<!-- So, now your functions are very simple: -->
Теперь ваши функции выглядят очень просто:

```kotlin
fun head(init: Head.() -> Unit) = initTag(Head(), init)

fun body(init: Body.() -> Unit) = initTag(Body(), init)
```

<!-- And you can use them to build `<head>` and `<body>` tags. -->
И вы можете использовать их для постройки тэгов `<html>` и `<body>`.

<!-- One other thing to be discussed here is how you add text to tag bodies. In the example above you say something like: -->
Ещё одна вещь, которую следует обсудить, это добавление текста в тело тэга. В примере выше используется такой синтаксис:

```kotlin
html {
    head {
        title {+"XML кодирование с Kotlin"}
    }
    // ...
}
```

<!-- So basically, you just put a string inside a tag body, but there is this little `+` in front of it,
so it is a function call that invokes a prefix `unaryPlus()` operation.
That operation is actually defined by an extension function `unaryPlus()` that is a member of the `TagWithText` abstract 
class (a parent of `Title`): -->
Итак, вы просто добавляете строку в тело тэга, приписав `+` перед текстом, что ведёт к вызову префиксной операции `unaryPlus()`.
Эта операция определена с помощью [функции-расширения](extensions.html) `unaryPlus()`,
которая является членом абстрактного класса `TagWithText` (родителя `Title`).

```kotlin
operator fun String.unaryPlus() {
    children.add(TextElement(this))
}
```

<!-- So, what the prefix `+` does here is wrapping a string into an instance of `TextElement` and adding it to the `children` collection,
so that it becomes a proper part of the tag tree. -->
Иными словами, префикс `+` оборачивает строку в экземпляр `TextElement` и добавляет его в коллекцию `children`.

<!-- All this is defined in a package `com.example.html` that is imported at the top of the builder example above.
In the last section you can read through the full definition of this package. -->
Всё это определено в пакете `com.example.html`, который импортирован в начале примера выше.
В последнем разделе вы можете прочитать полное описание определений в этом пакете.

<a name="scope-control-dslmarker"></a>

<!-- ## Scope control: `@DslMarker` -->
## Контроль области видимости: @DslMarker

<!-- When using DSLs, one might have come across the problem that too many functions can be called in the context. 
You can call methods of every available implicit receiver inside a lambda and therefore get an inconsistent result, 
like the tag `head` inside another `head`: -->
При использовании DSL может возникнуть проблема, когда слишком много функций могут быть вызваны в определённом контексте.
Вы можете вызывать методы каждого неявного приёмника внутри лямбды, и из-за этого может возникать противоречивый результат,
как, например, тэг `head` внутри другого тэга `head`.

```kotlin
html {
    head {
        head {} // такое не должно происходить
    }
    // ...
}
```

<!-- In this example only members of the nearest implicit receiver `this@head` must be available; `head()` is a member of the 
outer receiver `this@html`, so it must be illegal to call it. -->
В этом примере должны быть доступны только члены ближайшего неявного приёмника `this@head`;
`head()` является членом другого приёмника — `this@html`, поэтому его вызов в другом контексте должен быть запрещён.

<!-- To address this problem, there is a special mechanism to control receiver scope. -->
Для решения этой проблемы существует специальный механизм для управления областью видимости приёмника.

<!-- To make the compiler start controlling scopes you only have to annotate the types of all receivers used in the DSL with 
the same marker annotation.
For instance, for HTML Builders you declare an annotation `@HTMLTagMarker`: -->
Чтобы заставить компилятор запускать контрольные области, нам нужно только аннотировать типы всех получателей,
используемых в DSL, той же маркерной аннотацией. Например, для HTML Builders мы объявляем аннотацию `@HTMLTagMarker`.

```kotlin
@DslMarker
annotation class HtmlTagMarker
```

<!-- An annotation class is called a DSL marker if it is annotated with the `@DslMarker` annotation. -->
Аннотированный класс называется DSL-маркером, если он помечен аннотацией `@DslMarker`.

<!-- In our DSL all the tag classes extend the same superclass `Tag`.
It's enough to annotate only the superclass with `@HtmlTagMarker` and after that the Kotlin compiler will treat all the 
inherited classes as annotated: -->
В нашем DSL все классы тэгов расширяют один и тот же суперкласс `Tag`. Вам достаточно аннотировать `@HtmlTagMarker`
только суперкласс, и после этого компилятор Kotlin обработает все унаследованные классы в соответствии с аннотацией.

```kotlin
@HtmlTagMarker
abstract class Tag(val name: String) { /*...*/ }
```

<!-- You don't have to annotate the `HTML` or `Head` classes with `@HtmlTagMarker` because their superclass is already annotated: -->
Вам не нужно помечать классы `HTML` или `Head` аннотацией `@HtmlTagMarker`, потому что их суперкласс уже аннотирован.

```
class HTML() : Tag("html") { /*...*/ }
class Head() : Tag("head") { /*...*/ }
```

<!-- After you've added this annotation, the Kotlin compiler knows which implicit receivers are part of the same DSL and allows to call members of the nearest receivers only: -->
После добавления этой аннотации, компилятор Kotlin знает, какие неявные приёмники являются частью того же DSL,
и разрешает обращаться только к членам ближайших приёмников.

```kotlin
html {
    head {
        head { } // ошибка: член внешнего приёмника
    }
    // ...
}
```

<!-- Note that it's still possible to call the members of the outer receiver, but to do that you have to specify this receiver explicitly: -->
Обратите внимание, что всё ещё возможно вызывать члены внешнего приёмника, но для этого вам нужно указать этот приёмник явно.

```kotlin
html {
    head {
        this@html.head { } // всё работает
    }
    // ...
}
```

<a name="full-definition-of-the-com-example-html-package"></a>

<!-- ## Full definition of the `com.example.html` package -->
## Полное описание пакета com.example.html

<!-- This is how the package `com.example.html` is defined (only the elements used in the example above).
It builds an HTML tree. It makes heavy use of [extension functions](extensions.md) and
[lambdas with receiver](lambdas.md#function-literals-with-receiver). -->
Перед вами содержание пакета `com.example.html` (представлены только элементы, использованные в примере выше).
Он строит HTML дерево и активно использует [расширения](extensions.html) и
[лямбды с приёмниками](lambdas.html#function-literals-with-receiver).

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
