---
type: doc
layout: reference
category: "Syntax"
title: "Типобезопасные строители"
---

<!--# Type-Safe Builders-->
# Типобезопасные строители

<!--The concept of [builders](http://www.groovy-lang.org/dsls.html#_nodebuilder) is rather popular in the *Groovy* community.
Builders allow for defining data in a semi-declarative way. Builders are good for [generating XML](http://www.groovy-lang.org/processing-xml.html#_creating_xml), 
[laying out UI components](http://www.groovy-lang.org/swing.html), 
[describing 3D scenes](http://www.artima.com/weblogs/viewpost.jsp?thread=296081) and more...-->
Идея строителей ([builders](http://www.groovy-lang.org/dsls.html#_nodebuilder)) довольна популярна в сообществе *Groovy*.
Строители позволяют объявлять данные в полудекларативном виде. Строители хороши для [генерации XML](http://www.groovy-lang.org/processing-xml.html#_creating_xml),
[вёрстки компонентов UI](http://www.groovy-lang.org/swing.html), [описания 3D сцен](http://www.artima.com/weblogs/viewpost.jsp?thread=296081) и многого другого...

<!--For many use cases, Kotlin allows to *type-check* builders, which makes them even more attractive than the 
dynamically-typed implementation made in Groovy itself.-->
В отличие от Groovy, Kotlin проверяет типы строителей, что делает их более приятными в использовании в большинстве юзкейсов.

<!--For the rest of the cases, Kotlin supports Dynamic types builders.-->
Для прочих случаев Kotlin поддерживает Динамически типизированные строители (Dynamic types builders).

<!--## A type-safe builder example-->
## Пример типобезопасного строителя
<!--Consider the following code:-->
Рассмотрим следующий код:

```kotlin
import com.example.html.* // смотрите объявления ниже

fun result(args: Array<String>) =
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

<!--This is completely legitimate Kotlin code.
You can play with this code online (modify it and run in the browser) [here](http://try.kotlinlang.org/#/Examples/Longer examples/HTML Builder/HTML Builder.kt).-->
Всё это полностью корректный Kotlin-код. 
[Здесь](https://try.kotlinlang.org/#/Examples/Longer%20examples/HTML%20Builder/HTML%20Builder.kt) 
вы можете отредактировать и запустить пример с этим кодом прямо у себя в браузере.

<!--## How it works-->
## Как это работает

<!--Let's walk through the mechanisms of implementing type-safe builders in Kotlin.
First of all we need to define the model we want to build, in this case we need to model HTML tags.
It is easily done with a bunch of classes.
For example, `HTML` is a class that describes the `<html>` tag, i.e. it defines children like `<head>` and `<body>`.
(See its declaration [below](#full-definition-of-the-comexamplehtml-package).) -->
Давайте рассмотрим механизм реализации типобезопасных строителей в Kotlin.
Прежде всего, нам нужно определить модель, которую мы собираемся строить. В данном случае это HTML-тэги.
Мы можем сделать это без труда с помощью нескольких классов.
К примеру, `HTML` — это класс, который описывает тэг `<html>`, т.е. он определяет потомков, таких как `<head>` и `<body>`.
(См. его объявление [ниже](#full-definition-of-the-comexamplehtml-package).)

<!--Now, let's recall why we can say something like this in the code:-->
Теперь давайте вернёмся к вопросу почему мы можем писать вот такой код:

```kotlin
html {
 // ...
}
```

<!--`html` is actually a function call that takes a [lambda expression](lambdas.html) as an argument.
This function is defined as follows:-->
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
The type of the function is `HTML.() -> Unit`, which is a _function type with receiver_.
This means that we need to pass an instance of type `HTML` (a _receiver_) to the function,
and we can call members of that instance inside the function.
The receiver can be accessed through the *this*{: .keyword } keyword:-->
Эта функция принимает один параметр-функцию под названием `init`.
Тип этой функции: `HTML.() -> Unit` — _функциональный тип с объектом-приёмником_.
Это значит, что нам нужно передать экземпляр класса `HTML` (_приёмник_) в функцию, 
и мы сможем обращаться к членам объекта в теле этой функции. Обращение происходит через
ключевое слово <b class="keyword">this</b>:

```kotlin
html {
    this.head { /* ... */ }
    this.body { /* ... */ }
}
```

<!--(`head` and `body` are member functions of `HTML`.)-->
(`head` и `body` — члены класса `HTML`)

<!--Now, *this*{: .keyword } can be omitted, as usual, and we get something that looks very much like a builder already:-->
Теперь <b class="keyword">this</b> может быть опущено, и мы получим что-то, что уже очень похоже на строителя:

```kotlin
html {
    head { /* ... */ }
    body { /* ... */ }
}
```

<!--So, what does this call do? Let's look at the body of `html` function as defined above.
It creates a new instance of `HTML`, then it initializes it by calling the function that is passed as an argument
(in our example this boils down to calling `head` and `body` on the `HTML` instance), and then it returns this instance. 
This is exactly what a builder should do.-->
Итак, что же делает этот вызов? Давайте посмотрим на тело функции `html`, объявленной выше.
Она создаёт новый экземпляр `HTML`, затем инициализирует его путём вызова функции, которая была передана в аргументе
(в нашем примере это сводится к вызову `head` и `body` у объекта `HTML`), и после этого возвращает его значение.
Это в точности то, что и должен делать строитель.

<!--The `head` and `body` functions in the `HTML` class are defined similarly to `html`. 
The only difference is that they add the built instances to the `children` collection of the enclosing `HTML` instance:-->
Функции `head` и `body` в классе `HTML` объявлены схоже с функцией `html`. 
Единственное отличие в том, что они добавляют отстроенные экземпляры в коллекцию `children` заключающего экземпляра `HTML`:

```kotlin
fun head(init: Head.() -> Unit) : Head {
    val head = Head()
    head.init()
    children.add(head)
    return head
}

fun body(init: Body.() -> Unit) : Body {
    val body = Body()
    body.init()
    children.add(body)
    return body
}
```

<!--Actually these two functions do just the same thing, so we can have a generic version, `initTag`:-->
На самом деле эти две функции делают одно и тоже, поэтому мы можем использовать обобщённую версию, `initTag`:

```kotlin
protected fun <T : Element> initTag(tag: T, init: T.() -> Unit): T {
    tag.init()
    children.add(tag)
    return tag
}
```

<!--So, now our functions are very simple:-->
Теперь наши функции выглядят очень просто:

```kotlin
fun head(init: Head.() -> Unit) = initTag(Head(), init)

fun body(init: Body.() -> Unit) = initTag(Body(), init)
```

<!--And we can use them to build `<head>` and `<body>` tags. -->
И мы можем использовать их для постройки тэгов `<html>` и `<body>`.


<!--One other thing to be discussed here is how we add text to tag bodies. In the example above we say something like-->
Ещё одна вещь, которую следует обсудить, это добавление текста в тело тэга. В примере выше мы используем такой синтаксис:

```kotlin
html {
    head {
        title {+"XML кодирование с Kotlin"}
    }
    // ...
}
```

<!--So basically, we just put a string inside a tag body, but there is this little `+` in front of it,
so it is a function call that invokes a prefix `unaryPlus()` operation.
That operation is actually defined by an extension function `unaryPlus()` that is a member of the `TagWithText` abstract class (a parent of `Title`):-->
Итак, мы просто добавляем строку в тело тэга, приписав `+` перед текстом, что ведёт к вызову префиксной операции `unaryPlus()`.
Эта операция определена с помощью [функции-расширения](extensions.html) `unaryPlus()`, 
которая является членом абстрактного класса `TagWithText` (родителя `Title`).

```kotlin
fun String.unaryPlus() {
    children.add(TextElement(this))
}
```

<!--So, what the prefix `+` does here is it wraps a string into an instance of `TextElement` and adds it to the `children` collection,
so that it becomes a proper part of the tag tree.-->
Иными словами, префикс `+` оборачивает строку в экземпляр `TextElement` и добавляет его в коллекцию `children`.

<!--All this is defined in a package `com.example.html` that is imported at the top of the builder example above.
In the last section you can read through the full definition of this package.-->
Всё это определено в пакете `com.example.html`, который импортирован в начале примера выше. 
В последней секции вы можете прочитать полное описание определений в этом пакете.

<!--## Scope control: @DslMarker (since 1.1)-->
## Контроль области видимости: @DslMarker (с версии 1.1)
<!--When using DSLs, one might have come across the problem that too many functions can be called in the context. 
We can call methods of every available implicit receiver inside a lambda and therefore get an inconsistent result, like the tag `head` inside another `head`: -->
При использовании [DSL](https://ru.wikipedia.org/wiki/%D0%9F%D1%80%D0%B5%D0%B4%D0%BC%D0%B5%D1%82%D0%BD%D0%BE-%D0%BE%D1%80%D0%B8%D0%B5%D0%BD%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%BD%D1%8B%D0%B9_%D1%8F%D0%B7%D1%8B%D0%BA) 
может возникнуть проблема, когда слишком много функций могут быть вызваны в определённом контексте.
Мы можем вызывать методы каждого неявного приёмника внутри лямбды, и из-за этого может возникать противоречивый результат, как, например, тэг `head` внутри другого тэга `head`:

```kotlin
html {
    head {
        head {} // должен быть запрещён
    }
    // ...
}
```

<!--In this example only members of the nearest implicit receiver `this@head` must be available; `head()` is a member of the outer receiver `this@html`, so it must be illegal to call it.-->
В этом примере должны быть доступны только члены ближайшего неявного приёмника `this@head`; `head()` является членом другого приёмника — `this@html`, поэтому его вызов в другом контексте должен быть запрещён.

<!--To address this problem, in Kotlin 1.1 a special mechanism to control receiver scope was introduced.

To make the compiler start controlling scopes we only have to annotate the types of all receivers used in the DSL with the same marker annotation.
For instance, for HTML Builders we declare an annotation `@HTMLTagMarker`:-->

Для решения этой проблемы в Kotlin 1.1 был введен специальный механизм для управления областью приёмника.

Чтобы заставить компилятор запускать контрольные области, нам нужно только аннотировать типы всех получателей, используемых в DSL, той же маркерной аннотацией.
Например, для HTML Builders мы объявляем аннотацию `@HTMLTagMarker`:
 
```kotlin
@DslMarker
annotation class HtmlTagMarker
```

<!--An annotation class is called a DSL marker if it is annotated with the `@DslMarker` annotation.-->
Аннотированный класс называется DSL-маркером, если он помечен аннотацией `@DslMarker`.

<!--In our DSL all the tag classes extend the same superclass `Tag`.
It's enough to annotate only the superclass with `@HtmlTagMarker` and after that the Kotlin compiler will treat all the inherited classes as annotated:-->
В нашем DSL все классы тэгов расширяют один и тот же суперкласс `Tag`.
Нам достаточно аннотировать `@HtmlTagMarker` только суперкласс, и после этого компилятор Kotlin обработает все унаследованные классы в соответствии с аннотацией:
```kotlin
@HtmlTagMarker
abstract class Tag(val name: String) { ... }
```

<!--We don't have to annotate the `HTML` or `Head` classes with `@HtmlTagMarker` because their superclass is already annotated:-->
Нам не нужно помечать классы `HTML` или `Head` аннотацией `@HtmlTagMarker`, потому что их суперкласс уже аннотирован:

```
class HTML() : Tag("html") { ... }
class Head() : Tag("head") { ... }
```

<!--After we've added this annotation, the Kotlin compiler knows which implicit receivers are part of the same DSL and allows to call members of the nearest receivers only: -->
После добавления этой аннотации, компилятор Kotlin знает, какие неявные приёмники являются частью того же DSL, и разрешает обращаться только к членам ближайших приёмников:

```kotlin
html {
    head {
        head { } // ошибка: член внешнего приёмника
    }
    // ...
}
```

<!--Note that it's still possible to call the members of the outer receiver, but to do that you have to specify this receiver explicitly:-->
Обратите внимание, что всё ещё возможно вызывать члены внешнего приёмника, но для этого вам нужно указать этот приёмник явно:

```kotlin
html {
    head {
        this@html.head { } // всё работает
    }
    // ...
}
```

<a name="full-definition-of-the-comexamplehtml-package"></a>
<!--## Full definition of the `com.example.html` package-->
## Полное описание пакета `com.example.html`

<!--This is how the package `com.example.html` is defined (only the elements used in the example above).
It builds an HTML tree. It makes heavy use of [extension functions](extensions.html) and
[lambdas with receiver](lambdas.html#function-literals-with-receiver).

Note that the `@DslMarker` annotation is available only since Kotlin 1.1.-->

Перед вами содержание пакета `com.example.html` (представлены только элементы, использованные в примере выше).
Он строит HTML дерево и активно использует [расширения](extensions.html) и [лямбды с приёмниками](lambdas.html#function-literals-with-receiver).

_Примечание: Аннотация @DslMarker доступна в Kotlin начиная с версии 1.1_

<a name='declarations'></a>

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
