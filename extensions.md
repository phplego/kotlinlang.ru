---
type: doc
layout: reference
category: "Syntax"
title: "Расширения"
url: https://kotlinlang.ru/docs/reference/extensions.html
---

<!--# Extensions-->
# Расширения (extensions)

<!--Kotlin, similar to C# and Gosu, provides the ability to extend a class with new functionality without having to inherit from the class or use any type of design pattern such as Decorator.
This is done via special declarations called _extensions_. Kotlin supports _extension functions_ and _extension properties_.-->
Аналогично таким языкам программирования, как <b>C#</b> и <b>Gosu</b>, <b>Kotlin</b> позволяет расширять класс путём добавления нового функционала. Не наследуясь от такого класса и не используя паттерн "Декоратор".
Это реализовано с помощью специальных выражений, называемых _расширения_. <b>Kotlin</b> поддерживает _функции-расширения_ и _свойства-расширения_.

<!--## Extension Functions-->
## Функции-расширения

<!--To declare an extension function, we need to prefix its name with a _receiver type_, i.e. the type being extended.
The following adds a `swap` function to `MutableList<Int>`:-->
Для того, чтобы объявить функцию-расширение, нам нужно указать в качестве приставки _возвращаемый тип_, то есть тип, который мы расширяем. Следующий пример добавляет функцию `swap` к `MutableList<Int>`:

``` kotlin
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' даёт ссылку на лист
    this[index1] = this[index2]
    this[index2] = tmp
}
```

<!--The *this*{: .keyword } keyword inside an extension function corresponds to the receiver object (the one that is passed before the dot). 
Now, we can call such a function on any `MutableList<Int>`:-->
Ключевое слово *this* внутри функции-расширения соотносится с получаемым объектом (его тип ставится перед точкой).
Теперь мы можем вызывать такую функцию в любом `MutableList<Int>`:

``` kotlin
val l = mutableListOf(1, 2, 3)
l.swap(0, 2) // 'this' внутри 'swap()' будет содержать значение 'l'
```

<!--Of course, this function makes sense for any `MutableList<T>`, and we can make it generic:-->
Разумеется, эта функция имеет смысл для любого `MutableList<T>`, и мы можем сделать её обобщённой: 

``` kotlin
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' относится к листу
    this[index1] = this[index2]
    this[index2] = tmp
}
```

<!--We declare the generic type parameter before the function name for it to be available in the receiver type expression. 
See [Generic functions](generics.html).-->
Мы объявляем обобщённый тип-параметр перед именем функции для того, чтобы он был доступен в получаемом типе-выражении.
См. [Обобщения](/docs/reference/generics.html).

<!--## Extensions are resolved **statically**-->
## Расширения вычисляются статически

<!--Extensions do not actually modify classes they extend. By defining an extension, you do not insert new members into a class,
but merely make new functions callable with the dot-notation on variables of this type.-->
Расширения на самом деле не проводят никаких модификаций с классами, которые они расширяют. Объявляя расширение, вы создаёте новую функцию, а не новый член класса. Такие функции могут быть вызваны через точку, применимо к конкретному типу. 

<!--We would like to emphasize that extension functions are dispatched **statically**, i.e. they are not virtual by receiver type.
This means that the extension function being called is determined by the type of the expression on which the function is invoked,
not by the type of the result of evaluating that expression at runtime. For example:-->
<!--Мы хотели бы подчеркнуть, что расширения запускаются **статически**, то есть фактически не являются получаемым типом. Это значит, что вызванная функция-расширение определена типом выражения, из которого она вызвана. А не типом результата, получаемым в ходе выполнения программы (в рантайме). К примеру:-->

Мы хотели бы подчеркнуть, что расширения имеют статическую диспетчеризацию: это значит, что вызванная функция-расширение определяется типом её выражения во время компиляции, а не типом выражения, вычисленным в ходе выполнения программы, как при вызове виртуальных функций. К примеру:

``` kotlin
open class C

class D: C()

fun C.foo() = "c"

fun D.foo() = "d"

fun printFoo(c: C) {
    println(c.foo())
}

printFoo(D())
```

<!--This example will print "c", because the extension function being called depends only on the declared type of the
parameter `c`, which is the `C` class.-->
Этот пример выведет нам "с" на экран потому, что вызванная функция-расширение зависит только от объявленного параметризованного типа `c`, который является `C` классом.

<!--If a class has a member function, and an extension function is defined which has the same receiver type, the same name
and is applicable to given arguments, the **member always wins**.
For example:-->
Если в классе есть и член в виде обычной функции, и функция-расширение с тем же возвращаемым типом, таким же именем и применяется с такими же аргументами, то **обычная функция имеет более высокий приоритет**. К примеру:

``` kotlin
class C {
    fun foo() { println("member") }
}

fun C.foo() { println("extension") }
```

<!--If we call `c.foo()` of any `c` of type `C`, it will print "member", not "extension".-->
Если мы вызовем `c.foo()` любого объекта `c` с типом `C`, на экран выведется "member", а не "extension".

<!--However, it's perfectly OK for extension functions to overload member functions which have the same name but a different signature:-->
Однако, для функций-расширений совершенно нормально перегружать функции-члены, которые имеют такое же имя, но другую сигнатуру:

``` kotlin
class C {
    fun foo() { println("member") }
}

fun C.foo(i: Int) { println("extension") }
```

<!--The call to `C().foo(1)` will print "extension".-->
Обращение к `C().foo(1)` выведет на экран надпись "extension".


<!--## Nullable Receiver-->
## Возвращаемое null значение

<!--Note that extensions can be defined with a nullable receiver type. Such extensions can be called on an object variable
even if its value is null, and can check for `this == null` inside the body. This is what allows you
to call toString() in Kotlin without checking for null: the check happens inside the extension function.-->
Обратите внимание, что расширения могут быть объявлены с возможностью получения null в качестве возврашаемого значения. Такие расширения могут ссылаться на переменные объекта, даже если их значение null. В таком случае есть возможность провести проверку `this == null` внутри тела функции. Благодаря этому метод `toString()` в языке <b>Koltin</b> вызывается без проверки на null: она проходит внутри функции-расширения.

``` kotlin
fun Any?.toString(): String {
    if (this == null) return "null"
    // после проверки на null, `this` автоматически кастуется к не-null типу, поэтому toString()
    // обращается (ориг.: resolves) к функции-члену класса Any
    return toString()
}
```

<!--## Extension Properties-->
## Свойства-расширения

<!--Similarly to functions, Kotlin supports extension properties:-->
Аналогично функциям, <b>Kotlin</b> поддерживает расширения свойств:

``` kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1
```

<!--Note that, since extensions do not actually insert members into classes, there's no efficient way for an extension 
property to have a [backing field](properties.html#backing-fields). This is why **initializers are not allowed for 
extension properties**. Their behavior can only be defined by explicitly providing getters/setters.-->
Так как расширения на самом деле не добавляют никаких членов к классам, свойство-расширение не может иметь [_backing field_](properties.html#backing-fields). Вот почему **запрещено использовать инициализаторы для свойств-расширений**. Их поведение может быть определено только явным образом, с указанием геттеров/сеттеров.

<!--Example:-->
Пример:

``` kotlin
val Foo.bar = 1 // ошибка: запрещено инициализировать значения в свойствах-расширениях
```


<!--## Companion Object Extensions-->
## Расширения для вспомогательных объектов (ориг.: _companion object extensions_)

<!--If a class has a [companion object](object-declarations.html#companion-objects) defined, you can also define extension
functions and properties for the companion object:-->
Если у класса есть [вспомогательный объект](object-declarations.html#companion-objects), вы также можете определить функции и свойства для такого объекта:

``` kotlin
class MyClass {
    companion object { }  // называется "сompanion"
}

fun MyClass.Companion.foo() {
    // ...
}
```

<!--Just like regular members of the companion object, they can be called using only the class name as the qualifier:-->
Как и для остальных членов вспомогательного объекта, для вызова функции расширения достаточно указания имени класса:

``` kotlin
MyClass.foo()
```


<!-- ## Scope of Extensions -->
## Область видимости расширений

<!-- Most of the time we define extensions on the top level, i.e. directly under packages: -->
 Чаще всего мы объявляем расширения на самом верхнем уровне, то есть сразу под пакетами:

``` kotlin
package foo.bar
 
fun Baz.goo() { ... } 
``` 

<!-- To use such an extension outside its declaring package, we need to import it at the call site: -->
Для того, чтобы использовать такое расширение вне пакета, в котором оно было объявлено, нам надо импортировать его на стороне вызова:

``` kotlin
package com.example.usage

import foo.bar.goo // импортировать все расширения за именем "goo"
                   // или
import foo.bar.*   // импортировать все из "foo.bar"

fun usage(baz: Baz) {
    baz.goo()
)

```

_См. [Импорт](packages.html#imports) для более подробной информации._

<!-- ## Declaring Extensions as Members -->
## Объявление расширений в качестве членов класса

<!-- Inside a class, you can declare extensions for another class. Inside such an extension, there are multiple _implicit receivers_ - -->
<!-- objects members of which can be accessed without a qualifier. The instance of the class in which the extension is declared is called -->
<!-- _dispatch receiver_, and the instance of the receiver type of the extension method is called _extension receiver_. -->
Внутри класса вы можете объявить расширение для другого класса. Внутри такого объявления существует несколько _неявных приёмников_ (ориг.:_implicit receivers_), доступ к членам которых может быть произведён без классификатора. Экземпляр такого класса, к которому относится вызываемое расширение, называется _отсылкой_ (ориг.: _dispatch receiver_), а экземпляр класса, в котором вызывается расширение называется _принимающим расширением_ (ориг.: _extension receiver_).

``` kotlin
class D {
    fun bar() { ... }
}

class C {
    fun baz() { ... }

    fun D.foo() {
        bar()   // вызывает D.bar
        baz()   // вызывает C.baz
    }

    fun caller(d: D) {
        d.foo()   // вызов функции-расширения
    }
}
```

<!-- In case of a name conflict between the members of the dispatch receiver and the extension receiver, the extension receiver takes -->
<!-- precedence. To refer to the member of the dispatch receiver you can use the [qualified `this` syntax](this-expressions.html#qualified). -->
В случае конфликта имён между членами класса, к которому отсылается расширение, и членами класса, в котором оно вызывается, в приоритете будут имена класса, принимающего расширение. Чтобы обратиться к члену класса, к которому отсылается расширение, можно использовать [синтаксис this с определителем](http://kotlinlang.ru/docs/reference/this-expressions.html#qualified).

``` kotlin
class C {
    fun D.foo() {
        toString()         // вызывает D.toString()
        this@C.toString()  // вызывает C.toString()
    }
```

<!-- Extensions declared as members can be declared as `open` and overridden in subclasses. This means that the dispatch of such -->
<!-- functions is virtual with regard to the dispatch receiver type, but static with regard to the extension receiver type. -->
Расширения, объявленные как члены класса, могут иметь модификатор видимости `open` и быть переопределены в унаследованных классах. Это означает, что виртуально такая отсылка происходит с учётом типа, к которому она отсылает, но статически - с учётом типа, возвращаемого таким расширением.

``` kotlin
open class D {
}

class D1 : D() {
}

open class C {
    open fun D.foo() {
        println("D.foo in C")
    }

    open fun D1.foo() {
        println("D1.foo in C")
    }

    fun caller(d: D) {
        d.foo()   // вызов функции-расширения
    }
}

class C1 : C() {
    override fun D.foo() {
        println("D.foo in C1")
    }

    override fun D1.foo() {
        println("D1.foo in C1")
    }
}

C().caller(D())   // prints "D.foo in C"
C1().caller(D())  // prints "D.foo in C1" - получатель отсылки вычислен виртуально
C().caller(D1())  // prints "D.foo in C" - получатель расширения вычислен статически
```

 
<!-- ## Motivation -->
## Мотивация

<!-- In Java, we are used to classes named "\*Utils": `FileUtils`, `StringUtils` and so on. The famous `java.util.Collections` belongs to the same breed. -->
<!-- And the unpleasant part about these Utils-classes is that the code that uses them looks like this: -->
В <b>Java</b> мы привыкли к классам с названием "\*Utils": `FileUtils`, `StringUtils` и т.п. Довольно известным следствием этого является `java.util.Collections`. Но вот использование таких утилитных классов в своём коде - не самое приятное мероприятие:

``` java
// Java
Collections.swap(list, Collections.binarySearch(list, Collections.max(otherList)), Collections.max(list))
```

<!-- Those class names are always getting in the way. We can use static imports and get this: -->
Имена таких классов постоянно используются при вызове. Мы можем их статически импортировать и получить что-то типа:

``` java
// Java
swap(list, binarySearch(list, max(otherList)), max(list))
```

<!-- This is a little better, but we have no or little help from the powerful code completion of the IDE. It would be so much better if we could say -->
Уже лучше, но такой мощный инструмент IDE, как автодополнение, не предоставляет нам сколь-нибудь серьёзную помощь в данном случае. Намного лучше, если бы у нас было:

``` java
// Java
list.swap(list.binarySearch(otherList.max()), list.max())
```

<!-- But we don't want to implement all the possible methods inside the class `List`, right? This is where extensions help us. -->
Но мы же не хотим реализовывать все методы класса `List`, так? Вот для чего и нужны расширения.
