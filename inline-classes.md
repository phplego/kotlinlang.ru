---
type: doc
layout: reference
title: "Встроенные классы"
url: https://kotlinlang.ru/docs/inline-classes.html
---

<!-- При переводе статьи оригинальная версия была от 04 May 2026 -->

<!-- Inline value classes -->
# Встроенные классы значений (inline value classes)

<!-- Sometimes it is useful to wrap a value in a class to create a more domain-specific type. However, it introduces runtime
overhead due to additional heap allocations. Moreover, if the wrapped type is primitive, the performance hit is significant,
because primitive types are usually heavily optimized by the runtime, while their wrappers don't get any special treatment. -->
Иногда полезно обернуть значение в класс, чтобы создать более предметно-ориентированный тип. Однако это приводит к
накладным расходам во время выполнения из-за дополнительных выделений в куче. Более того, если обернутый тип является
примитивным, влияние на производительность значительно: примитивные типы обычно сильно оптимизируются средой выполнения,
а их обертки не получают специальной обработки.

<!-- To solve such issues, Kotlin introduces a special kind of class called an _inline class_.
Inline classes are a subset of [value-based classes](https://github.com/Kotlin/KEEP/blob/master/notes/value-classes.md). They don't have an identity and can only hold values. -->
Чтобы решить такие проблемы, Kotlin вводит специальный вид класса, который называется _inline class_ (встроенный класс).
Встроенные классы являются подмножеством [классов, основанных на значениях](https://github.com/Kotlin/KEEP/blob/master/notes/value-classes.md).
У них нет идентичности, и они могут только хранить значения.

<!-- To declare an inline class, use the `value` modifier before the name of the class: -->
Чтобы объявить встроенный класс, используйте модификатор `value` перед именем класса:

```kotlin
value class Password(private val s: String)
```

<!-- To declare an inline class for the JVM backend, use the `value` modifier along with the `@JvmInline` annotation before the class declaration: -->
Чтобы объявить встроенный класс для JVM-бэкенда, используйте модификатор `value` вместе с аннотацией `@JvmInline`
перед объявлением класса:

```kotlin
// Для JVM-бэкендов
@JvmInline
value class Password(private val s: String)
```

<!-- An inline class must have a single property initialized in the primary constructor. At runtime, instances of the inline
class will be represented using this single property (see details about runtime representation [below](#representation)): -->
Встроенный класс должен иметь одно свойство, инициализированное в основном конструкторе. Во время выполнения экземпляры
встроенного класса будут представлены с помощью этого единственного свойства (подробнее о представлении во время
выполнения [ниже](#representation)):

```kotlin
// Фактически, создание экземпляра класса 'Password' не происходит
// Во время выполнения 'securePassword' содержит только 'String'
val securePassword = Password("Don't try this in production")
```

<!-- This is the main feature of inline classes, which inspired the name *inline*: data of the class is *inlined* into its
usages (similar to how content of [inline functions](inline-functions.md) is inlined to call sites). -->
Это основная особенность встроенных классов, которая и дала название *inline*: данные класса *встраиваются* в места его
использования, подобно тому, как содержимое [встроенных функций](inline-functions.html) встраивается в места вызова.

<a name="members"></a>
<!-- ## Members -->
## Члены

<!-- Inline classes support some functionality of regular classes. In particular, they are allowed to declare properties and
functions, have an `init` block and [secondary constructors](classes.md#secondary-constructors): -->
Встроенные классы поддерживают некоторые возможности обычных классов. В частности, им разрешено объявлять свойства и
функции, иметь блок `init` и [вторичные конструкторы](classes.html#secondary-constructors):

```kotlin
@JvmInline
value class Person(private val fullName: String) {
    init {
        require(fullName.isNotEmpty()) {
            "Full name shouldn't be empty"
        }
    }

    constructor(firstName: String, lastName: String) : this("$firstName $lastName") {
        require(lastName.isNotBlank()) {
            "Last name shouldn't be empty"
        }
    }

    val length: Int
        get() = fullName.length

    fun greet() {
        println("Hello, $fullName")
    }
}

fun main() {
    val name1 = Person("Kotlin", "Mascot")
    val name2 = Person("Kodee")
    name1.greet() // функция `greet()` вызывается как статический метод
    println(name2.length) // геттер свойства вызывается как статический метод
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.9"}

<!-- Inline class properties cannot have [backing fields](properties.md#backing-fields). They can only have simple computable
properties (no `lateinit`/delegated properties). -->
Свойства встроенного класса не могут иметь [теневые поля](properties.html#backing-fields). Они могут быть только простыми
вычисляемыми свойствами (без `lateinit`/делегированных свойств).

<a name="inheritance"></a>
<!-- ## Inheritance -->
## Наследование

<!-- Inline classes are allowed to inherit from interfaces: -->
Встроенным классам разрешено наследоваться от интерфейсов:

```kotlin
interface Printable {
    fun prettyPrint(): String
}

@JvmInline
value class Name(val s: String) : Printable {
    override fun prettyPrint(): String = "Let's $s!"
}

fun main() {
    val name = Name("Kotlin")
    println(name.prettyPrint()) // Все еще вызывается как статический метод
}
```

<!-- It is forbidden for inline classes to participate in a class hierarchy. This means that inline classes cannot extend
other classes and are always `final`. -->
Встроенным классам запрещено участвовать в иерархии классов. Это означает, что встроенные классы не могут расширять
другие классы и всегда являются `final`.

<a name="representation"></a>
<!-- ## Representation -->
## Представление

<!-- In generated code, the Kotlin compiler keeps a *wrapper* for each inline class. Inline class instances can be represented
at runtime either as wrappers or as the underlying type. This is similar to how `Int` can be
[represented](numbers.md#boxing-and-caching-numbers-on-the-jvm) either as a primitive `int` or as the wrapper `Integer`. -->
В сгенерированном коде компилятор Kotlin сохраняет *обертку* (wrapper) для каждого встроенного класса. Экземпляры
встроенного класса во время выполнения могут быть представлены либо как обертки, либо как базовый тип. Это похоже на то,
как `Int` может быть [представлен](basic-types.html#numbers-representation-on-the-jvm) либо примитивом `int`, либо
оберткой `Integer`.

<!-- The Kotlin compiler will prefer using underlying types instead of wrappers to produce the most performant and optimized code.
However, sometimes it is necessary to keep wrappers around. As a rule of thumb, inline classes are boxed whenever they
are used as another type. -->
Компилятор Kotlin предпочитает использовать базовые типы вместо оберток, чтобы создавать максимально производительный и
оптимизированный код. Однако иногда обертки нужно сохранять. Общее правило: встроенные классы упаковываются (boxed),
когда используются как другой тип.

```kotlin
interface I

@JvmInline
value class Foo(val i: Int) : I

fun asInline(f: Foo) {}
fun <T> asGeneric(x: T) {}
fun asInterface(i: I) {}
fun asNullable(i: Foo?) {}

fun <T> id(x: T): T = x

fun main() {
    val f = Foo(42)

    asInline(f)    // без упаковки (unboxed): используется как сам Foo
    asGeneric(f)   // с упаковкой (boxed): используется как обобщенный тип T
    asInterface(f) // с упаковкой (boxed): используется как тип I
    asNullable(f)  // с упаковкой (boxed): используется как Foo?, который отличается от Foo

    // Ниже 'f' сначала упаковывается при передаче в 'id',
    // а затем распаковывается при возврате из 'id'.
    // В итоге 'c' содержит представление без упаковки (просто '42'), как и 'f'
    val c = id(f)
}
```

<!-- Because inline classes may be represented both as the underlying value and as a wrapper, [referential equality](equality.md#referential-equality)
is pointless for them and is therefore prohibited. -->
Поскольку встроенные классы могут быть представлены и как базовое значение, и как обертка, [равенство по ссылке](equality.html#referential-equality)
для них бессмысленно и поэтому запрещено.

<!-- Inline classes can also have a generic type parameter as the underlying type. In this case, the compiler maps it to `Any?`
or, generally, to the upper bound of the type parameter. -->
У встроенных классов также может быть обобщенный параметр типа в качестве базового типа. В этом случае компилятор
сопоставляет его с `Any?` или, в общем случае, с верхней границей параметра типа.

```kotlin
@JvmInline
value class UserId<T>(val value: T)

fun compute(s: UserId<String>) {} // компилятор генерирует fun compute-<hashcode>(s: Any?)
```

<a name="mangling"></a>
<!-- ### Mangling -->
### Манглирование имен

<!-- Since inline classes are compiled to their underlying type, it may lead to various obscure errors, for example unexpected platform signature clashes: -->
Поскольку встроенные классы компилируются в их базовый тип, это может приводить к различным неочевидным ошибкам,
например к неожиданным конфликтам платформенных сигнатур:

```kotlin
@JvmInline
value class UInt(val x: Int)

// Представлен как 'public final void compute(int x)' на JVM
fun compute(x: Int) { }

// Тоже представлен как 'public final void compute(int x)' на JVM!
fun compute(x: UInt) { }
```

<!-- To mitigate such issues, functions using inline classes are _mangled_ by adding some stable hashcode to the function name.
Therefore, `fun compute(x: UInt)` will be represented as `public final void compute-<hashcode>(int x)`, which solves the clash problem. -->
Чтобы смягчить такие проблемы, функции, использующие встроенные классы, подвергаются *манглированию* (mangling): к имени
функции добавляется стабильный хэш-код. Поэтому `fun compute(x: UInt)` будет представлена как
`public final void compute-<hashcode>(int x)`, что решает проблему конфликта.

<a name="calling-from-java-code"></a>
<!-- ### Calling from Java code -->
### Вызов из Java-кода

<!-- You can call functions that accept inline classes from Java code. To do so, you should manually disable mangling:
add the `@JvmName` annotation before the function declaration: -->
Из Java-кода можно вызывать функции, которые принимают встроенные классы. Для этого нужно вручную отключить
манглирование: добавьте аннотацию `@JvmName` перед объявлением функции:

```kotlin
@JvmInline
value class UInt(val x: Int)

fun compute(x: Int) { }

@JvmName("computeUInt")
fun compute(x: UInt) { }
```

<!-- By default, Kotlin compiles inline classes using **unboxed representations**, which makes them difficult to access from Java.
To learn how to compile inline classes into **boxed representations** that are accessible from Java, see the guide to
[Calling Kotlin from Java](java-to-kotlin-interop.md#inline-value-classes). -->
По умолчанию Kotlin компилирует встроенные классы с использованием **представлений без упаковки** (unboxed
representations), из-за чего к ним сложно обращаться из Java. Чтобы узнать, как скомпилировать встроенные классы в
доступные из Java **упакованные представления** (boxed representations), смотрите руководство
[Вызов Kotlin из Java](https://kotlinlang.org/docs/java-to-kotlin-interop.html#inline-value-classes).

<a name="inline-classes-vs-type-aliases"></a>
<!-- ## Inline classes vs type aliases -->
## Встроенные классы и псевдонимы типов

<!-- At first sight, inline classes seem very similar to [type aliases](type-aliases.md). Indeed, both seem to introduce
a new type and both will be represented as the underlying type at runtime. -->
На первый взгляд встроенные классы очень похожи на [псевдонимы типов](type-aliases.html). Действительно, и те и другие
как будто вводят новый тип, и оба во время выполнения будут представлены базовым типом.

<!-- However, the crucial difference is that type aliases are *assignment-compatible* with their underlying type (and with
other type aliases with the same underlying type), while inline classes are not. -->
Однако ключевое различие заключается в том, что псевдонимы типов *совместимы по присваиванию* со своим базовым типом
(и с другими псевдонимами типов с тем же базовым типом), а встроенные классы - нет.

<!-- In other words, inline classes introduce a truly _new_ type, contrary to type aliases which only introduce an alternative name
(alias) for an existing type: -->
Другими словами, встроенные классы вводят действительно _новый_ тип, в отличие от псевдонимов типов, которые вводят
только альтернативное имя (псевдоним) для существующего типа:

```kotlin
typealias NameTypeAlias = String

@JvmInline
value class NameInlineClass(val s: String)

fun acceptString(s: String) {}
fun acceptNameTypeAlias(n: NameTypeAlias) {}
fun acceptNameInlineClass(p: NameInlineClass) {}

fun main() {
    val nameAlias: NameTypeAlias = ""
    val nameInlineClass: NameInlineClass = NameInlineClass("")
    val string: String = ""

    acceptString(nameAlias) // OK: передается псевдоним вместо базового типа
    acceptString(nameInlineClass) // Ошибка: нельзя передать встроенный класс вместо базового типа

    // И наоборот:
    acceptNameTypeAlias(string) // OK: передается базовый тип вместо псевдонима
    acceptNameInlineClass(string) // Ошибка: нельзя передать базовый тип вместо встроенного класса
}
```

<a name="inline-classes-and-delegation"></a>
<!-- ## Inline classes and delegation -->
## Встроенные классы и делегирование

<!-- Implementation by delegation to inlined value of inlined class is allowed with interfaces: -->
Для интерфейсов разрешена реализация через делегирование встроенному значению встроенного класса:

```kotlin
interface MyInterface {
    fun bar()
    fun foo() = "foo"
}

@JvmInline
value class MyInterfaceWrapper(val myInterface: MyInterface) : MyInterface by myInterface

fun main() {
    val my = MyInterfaceWrapper(object : MyInterface {
        override fun bar() {
            // тело
        }
    })
    println(my.foo()) // выводит "foo"
}
```
