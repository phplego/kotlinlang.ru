---
type: doc
layout: reference
title: "Встроенные классы"
url: https://kotlinlang.ru/docs/basic-syntax.html
---

<!-- При переводе статьи оригинальная версия была от 05 May 2021 -->

<!-- Inline classes -->
# Встроенные (inline) классы

<!-- Sometimes it is necessary for business logic to create a wrapper around some type. However, it introduces runtime 
overhead due to additional heap allocations. Moreover, if the wrapped type is primitive, the performance hit is terrible, 
because primitive types are usually heavily optimized by the runtime, while their wrappers don't get any special treatment. -->
Иногда для бизнес-логики необходимо создать обертку вокруг какого-либо типа. Однако это приводит к дополнительным
затратам во время выполнения из-за дополнительных выделений кучи. Более того, если обернутый тип является примитивным,
снижение производительности ужасно, потому что примитивные типы обычно сильно оптимизируются средой выполнения, в то
время как их обертки не получают никакой специальной обработки.

<!-- To solve such issues, Kotlin introduces a special kind of class called an _inline class_. 
Inline classes are a subset of [value-based classes](https://github.com/Kotlin/KEEP/blob/master/notes/value-classes.md). They don't have an identity and can only hold values. -->
Чтобы решить эту проблему, в Kotlin есть специальный класс, который называется встроенным. Встроенные классы являются
подмножеством [классов, основанных на значениях](https://github.com/Kotlin/KEEP/blob/master/notes/value-classes.md). У
них нет идентичности и они могут только хранить значения.

<!-- To declare an inline class, use the `value` modifier before the name of the class: -->
Чтобы объявить встроенный класс, используйте модификатор `value` перед именем класса.

```kotlin
value class Password(private val s: String)
```

<!-- To declare an inline class for the JVM backend, use the `value` modifier along with the `@JvmInline` annotation before the class declaration: -->
Чтобы объявить встроенный класс для JVM backend, используйте модификатор `value` вместе с аннотацией
`@JvmInline` перед объявлением класса.

```kotlin
// Для JVM backends
@JvmInline
value class Password(private val s: String)
```

<!-- > The `inline` modifier for inline classes is deprecated. -->
> Модификатор `inline` для встроенных классов не рекомендуется.

<!-- An inline class must have a single property initialized in the primary constructor. At runtime, instances of the inline 
class will be represented using this single property (see details about runtime representation [below](#representation)): -->
Встроенный класс должен иметь одно свойство, инициализированное в основном конструкторе. Во время выполнения экземпляры
встроенного класса будут представлены с помощью этого единственного свойства (подробнее о представлении во время
выполнения [ниже](#representation)).

```kotlin
// Фактически, создание экземпляра класса 'Password' не происходит
// Во время выполнения 'securePassword' содержит только 'String'
val securePassword = Password("Don't try this in production") 
```

<!-- This is the main feature of inline classes, which inspired the name *inline*: data of the class is *inlined* into its 
usages (similar to how content of [inline functions](inline-functions.md) is inlined to call sites). -->
Это главная особенность встроенных классов, которая иллюстрирует их название - *встроенные*: данные класса *встроены* в
его использование (аналогично тому, как содержимое [встроенных функций](inline-functions.html) встроено в местах вызовов).

<a name="members"></a>
<!-- ## Members -->
## Члены

<!-- Inline classes support some functionality of regular classes. In particular, they are allowed to declare properties and 
functions, and have the `init` block: -->
Встроенные классы поддерживают некоторый функционал обычных классов. В частности, им разрешено объявлять свойства и
функции и иметь блок инициализации `init`.

```kotlin
@JvmInline
value class Name(val s: String) {
    init {
        require(s.length > 0) { }
    }

    val length: Int
        get() = s.length

    fun greet() {
        println("Hello, $s")
    }
}

fun main() {
    val name = Name("Kotlin")
    name.greet() // метод `greet` вызывается как статический метод
    println(name.length) // геттер вызывается как статический метод
}
```

<!-- Inline class properties cannot have [backing fields](properties.md#backing-fields). They can only have simple computable 
properties (no `lateinit`/delegated properties). -->
Свойства встроенного класса не могут иметь [теневые поля](properties.html#backing-fields). Они могут иметь только
простые вычислимые свойства (без `lateinit`/делегированных свойств).

<a name="inheritance"></a>
<!-- ## Inheritance -->
## Наследование

<!-- Inline classes are allowed to inherit from interfaces: -->
Встроенные классы можно наследовать от интерфейсов.

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
other classes and must be `final`. -->
Встроенным классам запрещено участвовать в иерархии классов. Это означает, что встроенные классы не могут расширять
другие классы и должны быть `final`.

<a name="representation"></a>
<!-- ## Representation -->
## Представление

<!-- In generated code, the Kotlin compiler keeps a *wrapper* for each inline class. Inline class instances can be represented 
at runtime either as wrappers or as the underlying type. This is similar to how `Int` can be 
[represented](basic-types.md#numbers-representation-on-the-jvm) either as a primitive `int` or as the wrapper `Integer`. -->
В сгенерированном коде компилятор Kotlin сохраняет *обертку* для каждого встроенного класса. Экземпляры встроенного
класса могут быть представлены во время выполнения либо как обертки, либо как базовый тип. Это похоже на то, как `Int`
может быть [представлен](basic-types.html#numbers-representation-on-the-jvm) либо в виде примитива `int`, либо в виде
обертки `Integer`.

<!-- The Kotlin compiler will prefer using underlying types instead of wrappers to produce the most performant and optimized code. 
However, sometimes it is necessary to keep wrappers around. As a rule of thumb, inline classes are boxed whenever they 
are used as another type. -->
Компилятор Kotlin предпочтет использовать базовые типы вместо оберток для создания максимально производительного и
оптимизированного кода. Однако иногда обертки необходимы. Как правило, встроенные классы оборачиваются всякий раз, когда
они используются в качестве другого типа.

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

    asInline(f)    // не обернутый: используется сам Foo
    asGeneric(f)   // обернутый: используется в качестве универсального типа T
    asInterface(f) // обернутый: используется в качестве типа I
    asNullable(f)  // обернутый: используется в качестве Foo?, который отличается от Foo

    // Ниже 'f' сначала оборачивается (при передаче в 'id') а затем - нет (при возврате из 'id')
    // В итоге 'c' содержит не обернутое представление (просто '42'), как 'f'
    val c = id(f)  
}
```

<!-- Because inline classes may be represented both as the underlying value and as a wrapper, [referential equality](equality.md#referential-equality) 
is pointless for them and is therefore prohibited. -->
Поскольку встроенные классы могут быть представлены и как базовые типы, и как обертки, [равенство по ссылке](equality.html#referential-equality)
для них бессмысленно и поэтому запрещено.

<a name="mangling"></a>
<!-- ### Mangling -->
### Искажение

<!-- Since inline classes are compiled to their underlying type, it may lead to various obscure errors, for example unexpected platform signature clashes: -->
Поскольку встроенные классы компилируются в соответствии с их базовым типом, это может привести к различным неясным
ошибкам, например, неожиданным конфликтам сигнатур платформы.

```kotlin
@JvmInline
value class UInt(val x: Int)

// Предоставлен как 'public final void compute(int x)' в JVM
fun compute(x: Int) { }

// Так же представлен как 'public final void compute(int x)' в JVM!
fun compute(x: UInt) { }
```

<!-- To mitigate such issues, functions using inline classes are *mangled* by adding some stable hashcode to the function name. 
Therefore, `fun compute(x: UInt)` will be represented as `public final void compute-<hashcode>(int x)`, which solves the clash problem. -->
Чтобы устранить такие проблемы, функции, использующие встроенные классы, *искажаются* путем добавления некоторого
стабильного хэш-кода к имени функции. Поэтому `fun compute(x: UInt)` будет представлена как
`public final void compute-<hashcode>(int x)`, что решает проблему конфликтов.

<!-- > The mangling scheme has been changed in Kotlin 1.4.30. 
> Use the `-Xuse-14-inline-classes-mangling-scheme` compiler flag to force the compiler to use the old 1.4.0 mangling scheme and preserve binary compatibility. -->
> Схема искажения была изменена в Котлине 1.4.30. Используйте флаг компилятора `-Xuse-14-inline-classes-mangling-scheme`,
> чтобы заставить компилятор использовать старую схему искажения 1.4.0 и сохранить двоичную совместимость.

<a name="calling-from-java-code"></a>
<!-- ### Calling from Java code -->
### Вызов из Java кода

<!-- You can call functions that accept inline classes from Java code. To do so, you should manually disable mangling:
add the `@JvmName` annotation before the function declaration: -->
Вы можете вызывать функции, которые принимают встроенные классы из кода Java. Для этого вам следует вручную отключить
искажение: добавьте аннотацию `@JvmName` перед объявлением функции.

```kotlin
@JvmInline
value class UInt(val x: Int)

fun compute(x: Int) { }

@JvmName("computeUInt")
fun compute(x: UInt) { }
```

<a name="inline-classes-vs-type-aliases"></a>
<!-- ## Inline classes vs type aliases -->
## Встроенные классы vs псевдонимы типов

<!-- At first sight, inline classes seem very similar to [type aliases](type-aliases.md). Indeed, both seem to introduce 
a new type and both will be represented as the underlying type at runtime. -->
На первый взгляд встроенные классы кажутся очень похожими на [псевдонимы типов](type-aliases.html). Действительно,
кажется, что оба вводят новый тип, и оба будут представлены как базовый тип во время выполнения.

<!-- However, the crucial difference is that type aliases are *assignment-compatible* with their underlying type (and with 
other type aliases with the same underlying type), while inline classes are not. -->
Однако решающее различие заключается в том, что псевдонимы типов *совместимы с присвоением* с их базовым типом (и с
другими псевдонимами типов с тем же базовым типом), в то время как встроенные классы - нет.

<!-- In other words, inline classes introduce a truly _new_ type, contrary to type aliases which only introduce an alternative name 
(alias) for an existing type: -->
Другими словами, встроенные классы вводят действительно новый тип, в отличие от псевдонимов типов, которые вводят только
альтернативное имя (псевдоним) для существующего типа.

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

    acceptString(nameAlias) // Всё хорошо: передача псевдонима вместо базового типа
    acceptString(nameInlineClass) // Всё плохо: не удается передать встроенный класс вместо базового типа

    // И наоборот:
    acceptNameTypeAlias(string) // Всё хорошо: передача базового типа вместо псевдонима
    acceptNameInlineClass(string) // Всё плохо: не удается передать базовый тип вместо встроенного класса
}
```
