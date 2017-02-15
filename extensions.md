---
type: doc
layout: reference
category: "Syntax"
title: "Расширения (extensions)"
---

<!--# Extensions-->
# Расширения (extensions)

<!--Kotlin, similar to C# and Gosu, provides the ability to extend a class with new functionality without having to inherit from the class or use any type of design pattern such as Decorator.
This is done via special declarations called _extensions_. Kotlin supports _extension functions_ and _extension properties_.-->
<b>Kotlin</b>, так же как <b>C#</b> и <b>Gosu</b>, позволяет расширять класс, добавляя новый функционал, без наследования от него или использования такого паттерна, как Декоратор.
Это реализовано с помощью специальных выражений, называемых _расширения_. <b>Kotlin</b> поддерживает _функции-расширения_ и _свойства-расширения_.

<!--## Extension Functions-->
## Функции-расширения

<!--To declare an extension function, we need to prefix its name with a _receiver type_, i.e. the type being extended.
The following adds a `swap` function to `MutableList<Int>`:-->
Для того, чтобы объявить функцию-расширение, нам нужно указать в качестве приставки _возвращаемый тип_, то есть тип, который мы расширяем. Следующий пример добавляет функцию `swap` к `MutableList<Int>`:

``` kotlin
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' даёт ссылку на Int
    this[index1] = this[index2]
    this[index2] = tmp
}
```

<!--The *this*{: .keyword } keyword inside an extension function corresponds to the receiver object (the one that is passed before the dot). 
Now, we can call such a function on any `MutableList<Int>`:-->
Ключевое слово *this* внутри функции-расширения соотносится с получаемым объектом (который ставится перед точкой).
Теперь мы можем вызывать такую функцию в любом `MutableList<Int>`:

``` kotlin
val l = mutableListOf(1, 2, 3)
l.swap(0, 2) // 'this' внутри 'swap()' не будет содержать значение 'l'
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
См. [обощённые функции](http://kotlinlang.org/docs/reference/generics.html).

<!--## Extensions are resolved **statically**-->
## Статическое решение расширений

<!--Extensions do not actually modify classes they extend. By defining an extension, you do not insert new members into a class,
but merely make new functions callable with the dot-notation on variables of this type.-->
Расширения на самом деле не проводят никаких модификаций с классами, которые они расширяют. Определяя расширение, вы не добавляете никаких новых членов к классу, но только лишь создаёте новые функции, которые могут быть вызваны через точку, применимо к конкретному типу. 

<!--We would like to emphasize that extension functions are dispatched **statically**, i.e. they are not virtual by receiver type.
This means that the extension function being called is determined by the type of the expression on which the function is invoked,
not by the type of the result of evaluating that expression at runtime. For example:-->
Мы хотели бы подчеркнуть, что расширения запускаются **статически**, то есть фактически не являются получаемым типом. Это значит, что вызванная функция-расширение определена типом выражения, из которого она вызвана. А не типом результата, получаемым в ходе выполнения программы (в рантайме). К примеру:

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
Этот пример выведет нам "с" на экран, потому что вызванная функция-расширение зависит только от объявленного параметризованного типа `c`, который является `C` классом.

<!--If a class has a member function, and an extension function is defined which has the same receiver type, the same name
and is applicable to given arguments, the **member always wins**.
For example:-->
Если в классе есть член в виде обычной функции и функция-расширение, которая имеет такой же возвращаемый тип, такое же имя и применяется к таким же аргументам, **обычная функция выигрывает**.

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
Обратите внимание, что расширения могут быть объявлены с возможностью хапнуть null в качестве возврашаемого значения. Такие расширения могут быть вызваны переменными объекта, даже если их значение null. И проверяет `this == null` внутри тела функции. Это то, что позволяет вызывать toString() в <b>Koltin</b> без проверки на null: проверка происходит внутри функции-расширения.

``` kotlin
fun Any?.toString(): String {
    if (this == null) return "null"
    // после проверки на null, `this` автоматически кастуется к не-null типу, поэтому toString()
    // обращается (ориг.: resloves) к функции-члену класса Any
    return toString()
}
```

<!--## Extension Properties-->
## Свойства-расширения

<!--Similarly to functions, Kotlin supports extension properties:-->
Аналогично функциям, <b>Koltin</b> поддерживает расширения свойств:

``` kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1
```

<!--Note that, since extensions do not actually insert members into classes, there's no efficient way for an extension 
property to have a [backing field](properties.html#backing-fields). This is why **initializers are not allowed for 
extension properties**. Their behavior can only be defined by explicitly providing getters/setters.-->
Так как расширения на самом деле не добавляют никаких членов к классам, нет никакого эффективного способа для свойства иметь [_backing field_](http://kotlinlang.org/docs/reference/properties.html#backing-fields). Вот почему **запрещено использовать инициализаторы для свойств-расширений**. Их поведение может быть определено только явным образом, указывая геттеры/сеттеры.

<!--Example:-->
Пример:

``` kotlin
val Foo.bar = 1 // ошибка: запрещено инициализировать значения в свойствах-расширениях
```


<!--## Companion Object Extensions-->
## Расширения вспомогательных объектов (ориг.: _companion object extensions_)

<!--If a class has a [companion object](object-declarations.html#companion-objects) defined, you can also define extension
functions and properties for the companion object:-->
Если у класса есть [вспомогательный объект](http://kotlinlang.org/docs/reference/object-declarations.html#companion-objects), вы также можете определить функции и свойства для такого объекта:

``` kotlin
class MyClass {
    companion object { }  // будет назван "Companion"
}

fun MyClass.Companion.foo() {
    // ...
}
```

<!--Just like regular members of the companion object, they can be called using only the class name as the qualifier:-->
Так же, как и обычные члены вспомогательного объекта, они могут быть вызваны с помощью имени класса в качестве точки доступа:

``` kotlin
MyClass.foo()
```


## Scope of Extensions

Most of the time we define extensions on the top level, i.e. directly under packages:
 
``` kotlin
package foo.bar
 
fun Baz.goo() { ... } 
``` 

To use such an extension outside its declaring package, we need to import it at the call site:

``` kotlin
package com.example.usage

import foo.bar.goo // importing all extensions by name "goo"
                   // or
import foo.bar.*   // importing everything from "foo.bar"

fun usage(baz: Baz) {
    baz.goo()
)

```

See [Imports](packages.html#imports) for more information.

## Declaring Extensions as Members

Inside a class, you can declare extensions for another class. Inside such an extension, there are multiple _implicit receivers_ -
objects members of which can be accessed without a qualifier. The instance of the class in which the extension is declared is called
_dispatch receiver_, and the instance of the receiver type of the extension method is called _extension receiver_.

``` kotlin
class D {
    fun bar() { ... }
}

class C {
    fun baz() { ... }

    fun D.foo() {
        bar()   // calls D.bar
        baz()   // calls C.baz
    }

    fun caller(d: D) {
        d.foo()   // call the extension function
    }
}
```

In case of a name conflict between the members of the dispatch receiver and the extension receiver, the extension receiver takes
precedence. To refer to the member of the dispatch receiver you can use the [qualified `this` syntax](this-expressions.html#qualified).

``` kotlin
class C {
    fun D.foo() {
        toString()         // calls D.toString()
        this@C.toString()  // calls C.toString()
    }
```

Extensions declared as members can be declared as `open` and overridden in subclasses. This means that the dispatch of such
functions is virtual with regard to the dispatch receiver type, but static with regard to the extension receiver type.

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
        d.foo()   // call the extension function
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
C1().caller(D())  // prints "D.foo in C1" - dispatch receiver is resolved virtually
C().caller(D1())  // prints "D.foo in C" - extension receiver is resolved statically
```

 
## Motivation

In Java, we are used to classes named "\*Utils": `FileUtils`, `StringUtils` and so on. The famous `java.util.Collections` belongs to the same breed.
And the unpleasant part about these Utils-classes is that the code that uses them looks like this:

``` java
// Java
Collections.swap(list, Collections.binarySearch(list, Collections.max(otherList)), Collections.max(list))
```

Those class names are always getting in the way. We can use static imports and get this:

``` java
// Java
swap(list, binarySearch(list, max(otherList)), max(list))
```

This is a little better, but we have no or little help from the powerful code completion of the IDE. It would be so much better if we could say

``` java
// Java
list.swap(list.binarySearch(otherList.max()), list.max())
```

But we don't want to implement all the possible methods inside the class `List`, right? This is where extensions help us.
