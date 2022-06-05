---
type: doc
layout: reference
category: "Syntax"
title: "Расширения"
url: https://kotlinlang.ru/docs/extensions.html
---

<!-- При переводе статьи оригинальная версия была от 19 July 2021 -->

<!-- # Extensions -->
# Расширения (extensions)

<!-- Kotlin provides the ability to extend a class with new functionality
without having to inherit from the class or use design patterns such as _Decorator_.
This is done via special declarations called _extensions_. -->
Kotlin позволяет расширять класс путём добавления нового функционала без необходимости наследования от такого класса
и использования паттернов, таких как *Decorator*. Это реализовано с помощью специальных выражений, называемых *расширения*.

<!-- For example, you can write new functions for a class from a third-party library that you can't modify.
Such functions can be called in the usual way, as if they were methods of the original class.
This mechanism is called an _extension function_. There are also _extension properties_ that let you define
new properties for existing classes. -->
Например, вы можете написать новые функции для класса из сторонней библиотеки, которую вы не можете изменить.
Такие функции можно вызывать обычным способом, как если бы они были методами исходного класса.
Этот механизм называется *функцией расширения*. Существуют также *свойства расширения*,
которые позволяют определять новые свойства для существующих классов.

<a name="extension-functions"></a>
<!-- ## Extension functions -->
## Функции-расширения

<!-- To declare an extension function, prefix its name with a _receiver type_, which refers to the type being extended.
The following adds a `swap` function to `MutableList<Int>`: -->
Для того чтобы объявить функцию-расширение, укажите в качестве префикса *расширяемый тип*, то есть тип, который мы расширяем.
Следующий пример добавляет функцию `swap` к `MutableList<Int>`:

```kotlin
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' даёт ссылку на список
    this[index1] = this[index2]
    this[index2] = tmp
}
```

<!-- The `this` keyword inside an extension function corresponds to the receiver object (the one that is passed before the dot).
Now, you can call such a function on any `MutableList<Int>`: -->
Ключевое слово `this` внутри функции-расширения соотносится с объектом расширяемого типа (этот тип ставится перед точкой).
Теперь мы можем вызывать такую функцию в любом `MutableList<Int>`.

```kotlin
val list = mutableListOf(1, 2, 3)
list.swap(0, 2) // 'this' внутри 'swap()' будет содержать значение 'list'
```

<!-- This function makes sense for any `MutableList<T>`, and you can make it generic: -->
Следующая функция имеет смысл для любого `MutableList<T>`, и вы можете сделать её обобщённой:

```kotlin
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' относится к списку
    this[index1] = this[index2]
    this[index2] = tmp
}
```

<!-- You need to declare the generic type parameter before the function name to make it available in the receiver type expression.
For more information about generics, see [generic functions](generics.md). -->
Вам нужно объявлять обобщённый тип-параметр перед именем функции для того, чтобы он был доступен в получаемом типе-выражении.
См. [Обобщения](generics.html).

<a name="extensions-are-resolved-statically"></a>
<!-- ## Extensions are resolved _statically_ -->
## Расширения вычисляются статически

<!-- Extensions do not actually modify the classes they extend. By defining an extension, you are not inserting new members into
a class, only making new functions callable with the dot-notation on variables of this type. -->
Расширения на самом деле не проводят никаких модификаций с классами, которые они расширяют.
Объявляя расширение, вы создаёте новую функцию, а не новый член класса. Такие функции могут быть вызваны через точку, применимо к конкретному типу.

<!-- Extension functions are dispatched _statically_, which means they are not virtual by receiver type.
An extension function being called is determined by the type of the expression on which the function is invoked,
not by the type of the result from evaluating that expression at runtime. For example: -->
Расширения имеют *статическую* диспетчеризацию: это значит, что вызванная функция-расширение определяется типом её выражения, из которого она вызвана,
а не типом выражения, вычисленным в ходе выполнения программы, как при вызове виртуальных функций.

```kotlin
open class Shape
class Rectangle: Shape()

fun Shape.getName() = "Shape"
fun Rectangle.getName() = "Rectangle"

fun printClassName(s: Shape) {
    println(s.getName())
}

printClassName(Rectangle())
```

<!-- This example prints _Shape_, because the extension function called depends only on the declared type of the
parameter `s`, which is the `Shape` class. -->
Этот пример выведет нам *Shape* на экран потому, что вызванная функция-расширение зависит только от объявленного параметризованного типа `s`,
который является `Shape` классом.

<!-- If a class has a member function, and an extension function is defined which has the same receiver type,
the same name, and is applicable to given arguments, the _member always wins_.
For example: -->
Если в классе есть и функция-член, и функция-расширение с тем же возвращаемым типом, таким же именем и применяется с такими же аргументами,
то *функция-член имеет более высокий приоритет*.

```kotlin
class Example {
    fun printFunctionType() { println("Class method") }
}

fun Example.printFunctionType() { println("Extension function") }

Example().printFunctionType()
```

<!-- This code prints _Class method_. -->
Этот код выведет *Class method*.

<!-- However, it's perfectly OK for extension functions to overload member functions that have the same name but a different signature: -->
Однако для функций-расширений совершенно нормально перегружать функции-члены, которые имеют такое же имя, но другую сигнатуру.

```kotlin
class Example {
    fun printFunctionType() { println("Class method") }
}

fun Example.printFunctionType(i: Int) { println("Extension function #$i") }

Example().printFunctionType(1)
```

Обращение к `Example().printFunctionType(1)` выведет на экран надпись *Extension function #1*.

<a name="nullable-receiver"></a>
<!-- ## Nullable receiver -->
## Расширение null-допустимых типов

<!-- Note that extensions can be defined with a nullable receiver type. These extensions can be called on an object variable
even if its value is null, and they can check for `this == null` inside the body. -->
Обратите внимание, что расширения могут быть объявлены для null-допустимых типов.
Такие расширения могут ссылаться на переменные объекта, даже если значение переменной равно null
и есть возможность провести проверку `this == null` внутри тела функции.

<!-- This way, you can call `toString()` in Kotlin without checking for `null`, as the check happens inside the extension function: -->
Благодаря этому метод `toString()` в Kotlin вызывается без проверки на `null`: она проходит внутри функции-расширения.

```kotlin
fun Any?.toString(): String {
    if (this == null) return "null"
    // после проверки на null, `this` автоматически приводится к не-null типу,
    // поэтому toString() обращается (ориг.: resolves) к функции-члену класса Any
    return toString()
}
```

<a name="extension-properties"></a>
<!-- ## Extension properties -->
## Свойства-расширения

<!--Kotlin supports extension properties much like it supports functions:-->
Аналогично функциям, Kotlin поддерживает расширения свойств.

```kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1
```

<!-- > Since extensions do not actually insert members into classes, there's no efficient way for an extension
> property to have a [backing field](properties.md#backing-fields). This is why _initializers are not allowed for
> extension properties_. Their behavior can only be defined by explicitly providing getters/setters. -->
> Поскольку расширения фактически не добавляют никаких членов к классам, свойство-расширение не может иметь [теневого поля](properties.html#backing-fields).
> Вот почему *запрещено использовать инициализаторы для свойств-расширений*.
> Их поведение может быть определено только явным образом, с указанием геттеров/сеттеров.

<!-- Example: -->
Пример:

```kotlin
val House.number = 1 // ошибка: запрещено инициализировать значения
                     // в свойствах-расширениях
```

<a name="companion-object-extensions"></a>
<!-- ## Companion object extensions -->
## Расширения для вспомогательных объектов (ориг.: _companion object extensions_)

<!-- If a class has a [companion object](object-declarations.md#companion-objects) defined, you can also define extension
functions and properties for the companion object. Just like regular members of the companion object,
they can be called using only the class name as the qualifier: -->
Если у класса есть [вспомогательный объект](object-declarations.html#companion-objects), вы также можете определить функции и свойства расширения для такого объекта.
Как и обычные члены вспомогательного объекта, их можно вызывать, используя в качестве определителя только имя класса.

```kotlin
class MyClass {
    companion object { } // называется "Companion"
}

fun MyClass.Companion.printCompanion() { println("companion") }
```

<a name="scope-of-extensions"></a>
<!-- ## Scope of extensions -->
## Область видимости расширений

<!-- In most cases, you define extensions on the top level, directly under packages: -->
В большинстве случаев вы определяете расширения на верхнем уровне, непосредственно в разделе пакетов.

```kotlin
package org.example.declarations

fun List<String>.getLongestString() { /*...*/}
```

<!-- To use an extension outside its declaring package, import it at the call site: -->
Для того, чтобы использовать такое расширение вне пакета, в котором оно было объявлено, импортируйте его на месте вызова.

```kotlin
package org.example.usage

import org.example.declarations.getLongestString

fun main() {
    val list = listOf("red", "green", "blue")
    list.getLongestString()
}
```

<!-- See [Imports](packages.md#imports) for more information. -->
См. [Импорт](packages.html#imports) для более подробной информации.

<a name="declaring-extensions-as-members"></a>
<!-- ## Declaring extensions as members -->
## Объявление расширений в качестве членов класса

<!-- You can declare extensions for one class inside another class. Inside such an extension, there are multiple _implicit receivers_ -
objects whose members can be accessed without a qualifier. An instance of a class in which the extension is declared is called a
_dispatch receiver_, and an instance of the receiver type of the extension method is called an _extension receiver_. -->
Внутри класса вы можете объявить расширение для другого класса. Внутри такого объявления существует несколько *неявных объектов-приёмников*
(ориг.: *implicit receivers*), доступ к членам которых может быть произведён без квалификатора. Экземпляр класса, в котором расширение
объявлено, называется *диспетчером приёмников* (ориг.: *dispatch receiver*), а экземпляр класса, для которого вызывается расширение,
называется *приёмником расширения* (ориг.: *extension receiver*).

```kotlin
class Host(val hostname: String) {
    fun printHostname() { print(hostname) }
}

class Connection(val host: Host, val port: Int) {
    fun printPort() { print(port) }

    fun Host.printConnectionString() {
        printHostname() // вызывает Host.printHostname()
        print(":")
        printPort()     // вызывает Connection.printPort()
    }

    fun connect() {
        /*...*/
        host.printConnectionString() // вызов функции-расширения
    }
}

fun main() {
    Connection(Host("kotl.in"), 443).connect()
    // Host("kotl.in").printConnectionString() // ошибка, функция расширения недоступна вне подключения
}
```

<!-- In the event of a name conflict between the members of a dispatch receiver and an extension receiver, the extension receiver takes
precedence. To refer to the member of the dispatch receiver, you can use the [qualified `this` syntax](this-expressions.md#qualified-this). -->
В случае конфликта имён между членами классов диспетчера приёмников и приёмников расширения, приоритет имеет приёмник расширения.
Чтобы обратиться к члену класса диспетчера приёмников, можно использовать синтаксис [`this` с квалификатором](this-expressions.html#qualified-this).

```kotlin
class Connection {
    fun Host.getConnectionString() {
        toString()                 // вызывает D.toString()
        this@Connection.toString() // вызывает C.toString()
    }
}
```

<!-- Extensions declared as members can be declared as `open` and overridden in subclasses. This means that the dispatch of such
functions is virtual with regard to the dispatch receiver type, but static with regard to the extension receiver type. -->
Расширения, объявленные как члены класса, могут иметь модификатор видимости `open` и быть переопределены в унаследованных классах.
Это означает, что диспечеризация таких функций является виртуальной по отношению к типу диспетчера приёмников,
но статической по отношению к типам приёмников расширения.

```kotlin
open class Base { }

class Derived : Base() { }

open class BaseCaller {
    open fun Base.printFunctionInfo() {
        println("Base extension function in BaseCaller")
    }

    open fun Derived.printFunctionInfo() {
        println("Derived extension function in BaseCaller")
    }

    fun call(b: Base) {
        b.printFunctionInfo() // вызов функции расширения
    }
}

class DerivedCaller: BaseCaller() {
    override fun Base.printFunctionInfo() {
        println("Base extension function in DerivedCaller")
    }

    override fun Derived.printFunctionInfo() {
        println("Derived extension function in DerivedCaller")
    }
}

fun main() {
    BaseCaller().call(Base())        // "Base extension function in BaseCaller"
    DerivedCaller().call(Base())     // "Base extension function in DerivedCaller" - приемник отправки является виртуальным
    DerivedCaller().call(Derived())  // "Base extension function in DerivedCaller" - приемник расширения является статическим
}
```

<a name="note-on-visibility"></a>
<!-- ## Note on visibility -->
## Примечание о видимости

<!-- Extensions utilize the same [visibility modifiers](visibility-modifiers.md) as regular functions declared in the same scope would.
For example: -->
Расширения используют те же [модификаторы видимости](visibility-modifiers.html) как и обычные функции, объявленные в той же области видимости.
Например:

<!-- * An extension declared at the top level of a file has access to the other `private` top-level declarations in the same file.
* If an extension is declared outside its receiver type, it cannot access the receiver's `private` or `protected` members. -->
* Расширение, объявленное на верхнем уровне файла, имеет доступ к другим `private` объявлениям верхнего уровня в том же файле;
* Если расширение объявлено вне своего типа приёмника, оно не может получить доступ к `private` или `protected` членам приёмника.
