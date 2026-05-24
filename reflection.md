---
type: doc
layout: reference
category: "Syntax"
title: "Рефлексия"
url: https://kotlinlang.ru/docs/reflection.html
---

<!-- При переводе статьи оригинальная версия была от 13 November 2025 -->

<!-- # Reflection -->
# Рефлексия

<!-- _Reflection_ is a set of language and library features that allows you to introspect the structure of your program at runtime.
Functions and properties are first-class citizens in Kotlin, and the ability to introspect them (for example, learning the name or
the type of a property or function at runtime) is essential when using a functional or reactive style. -->
*Рефлексия* — это набор возможностей языка и библиотек, который позволяет интроспектировать структуру программы во время
ее исполнения. В Kotlin функции и свойства являются полноправными сущностями, поэтому возможность интроспектировать их
(например, узнавать имя или тип свойства либо функции во время выполнения) важна при использовании функционального или
реактивного стиля.

<!-- > Kotlin/JS provides limited support for reflection features. [Learn more about reflection in Kotlin/JS](js-reflection.md).
>
{style="note"} -->
> Kotlin/JS предоставляет ограниченную поддержку возможностей рефлексии.
> [Подробнее о рефлексии в Kotlin/JS](https://kotlinlang.org/docs/js-reflection.html).
{:.note}

<a name="jvm-dependency"></a>

<!-- ## JVM dependency -->
## Зависимость для JVM

<!-- On the JVM platform, the Kotlin compiler distribution includes the runtime component required for using the reflection features as a separate
artifact, `kotlin-reflect.jar`. This is done to reduce the required size of the runtime
library for applications that do not use reflection features. -->
На платформе JVM дистрибутив компилятора Kotlin включает компонент среды выполнения, необходимый для использования
рефлексии, как отдельный артефакт `kotlin-reflect.jar`. Это сделано, чтобы уменьшить размер runtime-библиотеки для
приложений, которые не используют рефлексию.

<!-- To use reflection in a Gradle or Maven project, add the dependency on `kotlin-reflect`: -->
Чтобы использовать рефлексию в проекте Gradle или Maven, добавьте зависимость `kotlin-reflect`:

<!-- * In Gradle: -->
* В Gradle:

```kotlin
dependencies {
    implementation(kotlin("reflect"))
}
```

```groovy
dependencies {
    implementation "org.jetbrains.kotlin:kotlin-reflect:%kotlinVersion%"
}
```

<!-- * In Maven: -->
* В Maven:

```xml
<dependencies>
    <dependency>
        <groupId>org.jetbrains.kotlin</groupId>
        <artifactId>kotlin-reflect</artifactId>
    </dependency>
</dependencies>
```

<!-- If you don't use Gradle or Maven, make sure you have `kotlin-reflect.jar` in the classpath of your project.
In other supported cases (IntelliJ IDEA projects that use the command-line compiler),
it is added by default. In the command-line compiler, you can use the `-no-reflect` compiler option to exclude
`kotlin-reflect.jar` from the classpath. -->
Если вы не используете Gradle или Maven, убедитесь, что `kotlin-reflect.jar` добавлен в classpath вашего проекта.
В других поддерживаемых случаях, например в проектах IntelliJ IDEA, которые используют компилятор командной строки,
он добавляется по умолчанию. В компиляторе командной строки можно использовать опцию `-no-reflect`, чтобы исключить
`kotlin-reflect.jar` из classpath.

<a name="class-references"></a>

<!-- ## Class references -->
## Ссылки на классы

<!-- The most basic reflection feature is getting the runtime reference to a Kotlin class. To obtain the reference to a
statically known Kotlin class, you can use the _class literal_ syntax: -->
Самая базовая возможность рефлексии — получение ссылки времени выполнения на Kotlin-класс. Чтобы получить ссылку на
статически известный Kotlin-класс, используйте синтаксис *литерала класса*:

```kotlin
val c = MyClass::class
```

<!-- The reference is a [KClass](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/index.html) type value. -->
Ссылка имеет тип [KClass](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/index.html).

<!-- >On JVM: a Kotlin class reference is not the same as a Java class reference. To obtain a Java class reference,
>use the `.java` property on a `KClass` instance.
>
{style="note"} -->
> На JVM ссылка на Kotlin-класс не совпадает со ссылкой на Java-класс. Чтобы получить ссылку на Java-класс, используйте
> свойство `.java` у экземпляра `KClass`.
{:.note}

<a name="bound-class-references"></a>

<!-- ### Bound class references -->
### Ссылки на привязанные классы

<!-- You can get the reference to the class of a specific object with the same `::class` syntax by using the object as a receiver: -->
Вы можете получить ссылку на класс конкретного объекта с помощью того же синтаксиса `::class`, используя объект как
получатель:

```kotlin
val widget: Widget = ...
assert(widget is GoodWidget) { "Bad widget: ${widget::class.qualifiedName}" }
```

<!-- You will obtain the reference to the exact class of an object, for example, `GoodWidget` or `BadWidget`,
regardless of the type of the receiver expression (`Widget`). -->
Вы получите ссылку на точный класс объекта, например `GoodWidget` или `BadWidget`, независимо от типа выражения-получателя
(`Widget`).

<a name="callable-references"></a>

<!-- ## Callable references -->
## Вызываемые ссылки

<!-- References to functions, properties, and constructors can
also be called or used as instances of [function types](lambdas.md#function-types). -->
Ссылки на функции, свойства и конструкторы также можно вызывать или использовать как экземпляры
[функциональных типов](lambdas.html#function-types).

<!-- The common supertype for all callable references is [`KCallable<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-callable/index.html),
where `R` is the return value type. It is the property type for properties, and the constructed type for constructors. -->
Общий супертип для всех вызываемых ссылок —
[`KCallable<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-callable/index.html), где `R` — тип
возвращаемого значения. Для свойств это тип свойства, а для конструкторов — тип создаваемого объекта.

<a name="function-references"></a>

<!-- ### Function references -->
### Ссылки на функции

<!-- When you have a named function declared as below, you can call it directly (`isOdd(5)`): -->
Если у вас есть именованная функция, объявленная как ниже, вы можете вызвать ее напрямую (`isOdd(5)`):

```kotlin
fun isOdd(x: Int) = x % 2 != 0
```

<!-- Alternatively, you can use the function as a function type value, that is, pass it
to another function. To do so, use the `::` operator: -->
Также функцию можно использовать как значение функционального типа, то есть передать ее в другую функцию. Для этого
используйте оператор `::`:

```kotlin
fun isOdd(x: Int) = x % 2 != 0

fun main() {
//sampleStart
    val numbers = listOf(1, 2, 3)
    println(numbers.filter(::isOdd))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- Here `::isOdd` is a value of function type `(Int) -> Boolean`. -->
Здесь `::isOdd` — значение функционального типа `(Int) -> Boolean`.

<!-- Function references belong to one of the [`KFunction<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-function/index.html)
subtypes, depending on the parameter count. For instance, `KFunction3<T1, T2, T3, R>`. -->
Ссылки на функции принадлежат к одному из подтипов
[`KFunction<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-function/index.html), в зависимости
от количества параметров. Например, `KFunction3<T1, T2, T3, R>`.

<!-- `::` can be used with overloaded functions when the expected type is known from the context.
For example: -->
Оператор `::` можно использовать с перегруженными функциями, когда ожидаемый тип известен из контекста. Например:

```kotlin
fun main() {
//sampleStart
    fun isOdd(x: Int) = x % 2 != 0
    fun isOdd(s: String) = s == "brillig" || s == "slithy" || s == "tove"

    val numbers = listOf(1, 2, 3)
    println(numbers.filter(::isOdd)) // ссылается на isOdd(x: Int)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- Alternatively, you can provide the necessary context by storing the method reference in a variable with an explicitly specified type: -->
Также можно предоставить нужный контекст, сохранив ссылку на метод в переменной с явно указанным типом:

```kotlin
val predicate: (String) -> Boolean = ::isOdd   // ссылается на isOdd(x: String)
```

<!-- If you need to use a member of a class or an extension function, it needs to be qualified: `String::toCharArray`. -->
Если вам нужно использовать член класса или [функцию-расширение](extensions.html), ссылку нужно квалифицировать:
`String::toCharArray`.

<!-- Even if you initialize a variable with a reference to an extension function, the inferred function type will
have no receiver, but it will have an additional parameter accepting a receiver object. To have a function type
with a receiver instead, specify the type explicitly: -->
Даже если вы инициализируете переменную ссылкой на функцию-расширение, у выведенного функционального типа не будет
получателя, но будет дополнительный параметр, принимающий объект-получатель. Чтобы получить функциональный тип
с получателем, укажите тип явно:

```kotlin
val isEmptyStringList: List<String>.() -> Boolean = List<String>::isEmpty
```

<a name="example-function-composition"></a>

<!-- #### Example: function composition -->
#### Пример: композиция функций

<!-- Consider the following function: -->
Рассмотрим следующую функцию:

```kotlin
fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C {
    return { x -> f(g(x)) }
}
```

<!--It returns a composition of two functions passed to it: `compose(f, g) = f(g(*))`.
You can apply this function to callable references:-->
Она возвращает композицию двух переданных функций: `compose(f, g) = f(g(*))`. Эту функцию можно применять к вызываемым
ссылкам:

```kotlin
fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C {
    return { x -> f(g(x)) }
}

fun isOdd(x: Int) = x % 2 != 0

fun main() {
//sampleStart
    fun length(s: String) = s.length

    val oddLength = compose(::isOdd, ::length)
    val strings = listOf("a", "ab", "abc")

    println(strings.filter(oddLength))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<a name="property-references"></a>

<!-- ### Property references -->
### Ссылки на свойства

<!-- To access properties as first-class objects in Kotlin, use the `::` operator: -->
Чтобы обращаться к свойствам как к полноправным объектам в Kotlin, используйте оператор `::`:

```kotlin
val x = 1

fun main() {
    println(::x.get())
    println(::x.name)
}
```

<!-- The expression `::x` evaluates to a `KProperty0<Int>` type property object. You can read its
value using `get()` or retrieve the property name using the `name` property. For more information, see
the [docs on the `KProperty` class](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-property/index.html). -->
Выражение `::x` возвращает объект свойства типа `KProperty0<Int>`. Его значение можно прочитать с помощью `get()`, а имя
свойства — получить через свойство `name`. Подробнее см. в
[документации класса `KProperty`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-property/index.html).

<!-- For a mutable property such as `var y = 1`, `::y` returns a value with the [`KMutableProperty0<Int>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-mutable-property/index.html) type
which has a `set()` method: -->
Для изменяемого свойства, например `var y = 1`, `::y` возвращает значение типа
[`KMutableProperty0<Int>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-mutable-property/index.html),
у которого есть метод `set()`:

```kotlin
var y = 1

fun main() {
    ::y.set(2)
    println(y)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- A property reference can be used where a function with a single generic parameter is expected: -->
Ссылку на свойство можно использовать там, где ожидается функция с одним обобщенным параметром:

```kotlin
fun main() {
//sampleStart
    val strs = listOf("a", "bc", "def")
    println(strs.map(String::length))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- To access a property that is a member of a class, qualify it as follows: -->
Чтобы обратиться к свойству-члену класса, квалифицируйте его так:

```kotlin
fun main() {
//sampleStart
    class A(val p: Int)
    val prop = A::p
    println(prop.get(A(1)))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- For an extension property: -->
Для свойства-расширения:

```kotlin
val String.lastChar: Char
    get() = this[length - 1]

fun main() {
    println(String::lastChar.get("abc"))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<a name="interoperability-with-java-reflection"></a>

<!-- ### Interoperability with Java reflection -->
### Взаимодействие с рефлексией Java

<!-- On the JVM platform, the standard library contains extensions for reflection classes that provide a mapping to and from Java
reflection objects (see package `kotlin.reflect.jvm`).
For example, to find a backing field or a Java method that serves as a getter for a Kotlin property, you can write something like this: -->
На платформе JVM стандартная библиотека содержит расширения для классов рефлексии, которые обеспечивают сопоставление
с объектами рефлексии Java и обратно (см. пакет `kotlin.reflect.jvm`). Например, чтобы найти backing field или Java-метод,
который служит геттером для Kotlin-свойства, можно написать так:

```kotlin
import kotlin.reflect.jvm.*

class A(val p: Int)

fun main() {
    println(A::p.javaGetter) // выведет "public final int A.getP()"
    println(A::p.javaField)  // выведет "private final int A.p"
}
```

<!-- To get the Kotlin class that corresponds to a Java class, use the `.kotlin` extension property: -->
Чтобы получить Kotlin-класс, соответствующий Java-классу, используйте свойство-расширение `.kotlin`:

```kotlin
fun getKClass(o: Any): KClass<Any> = o.javaClass.kotlin
```

<a name="constructor-references"></a>

<!-- ### Constructor references -->
### Ссылки на конструкторы

<!-- Constructors can be referenced just like methods and properties. You can use them wherever the program expects a function type object
that takes the same parameters as the constructor and returns an object of the appropriate type.
Constructors are referenced by using the `::` operator and adding the class name. Consider the following function
that expects a function parameter with no parameters and return type `Foo`: -->
На конструкторы можно ссылаться так же, как на методы и свойства. Их можно использовать везде, где программа ожидает
объект функционального типа, который принимает те же параметры, что и конструктор, и возвращает объект соответствующего
типа. Ссылка на конструктор создается с помощью оператора `::` и имени класса. Рассмотрим функцию, которая ожидает
функциональный параметр без параметров и с возвращаемым типом `Foo`:

```kotlin
class Foo

fun function(factory: () -> Foo) {
    val x: Foo = factory()
}
```

<!-- Using `::Foo`, the zero-argument constructor of the class `Foo`, you can call it like this: -->
Используя `::Foo`, конструктор класса `Foo` без аргументов, можно вызвать эту функцию так:

```kotlin
function(::Foo)
```

<!-- Callable references to constructors are typed as one of the
[`KFunction<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-function/index.html) subtypes
depending on the parameter count. -->
Вызываемые ссылки на конструкторы имеют тип одного из подтипов
[`KFunction<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-function/index.html) в зависимости
от количества параметров.

<a name="bound-function-and-property-references"></a>

<!-- ### Bound function and property references -->
### Привязанные ссылки на функции и свойства

<!-- You can refer to an instance method of a particular object: -->
Вы можете сослаться на метод экземпляра конкретного объекта:

```kotlin
fun main() {
//sampleStart
    val numberRegex = "\\d+".toRegex()
    println(numberRegex.matches("29"))

    val isNumber = numberRegex::matches
    println(isNumber("29"))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- Instead of calling the method `matches` directly, the example uses a reference to it.
Such a reference is bound to its receiver.
It can be called directly (like in the example above) or used whenever a function type expression is expected: -->
Вместо прямого вызова метода `matches` в примере используется ссылка на него. Такая ссылка привязана к своему получателю.
Ее можно вызвать напрямую, как в примере выше, или использовать везде, где ожидается выражение функционального типа:

```kotlin
fun main() {
//sampleStart
    val numberRegex = "\\d+".toRegex()
    val strings = listOf("abc", "124", "a70")
    println(strings.filter(numberRegex::matches))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- Compare the types of the bound and the unbound references.
The bound callable reference has its receiver "attached" to it, so the type of the receiver is no longer a parameter: -->
Сравните типы привязанных и непривязанных ссылок. У привязанной вызываемой ссылки получатель уже "прикреплен", поэтому
тип получателя больше не является параметром:

```kotlin
val isNumber: (CharSequence) -> Boolean = numberRegex::matches

val matches: (Regex, CharSequence) -> Boolean = Regex::matches
```

<!-- A property reference can be bound as well: -->
Ссылка на свойство тоже может быть привязанной:

```kotlin
fun main() {
//sampleStart
    val prop = "abc"::length
    println(prop.get())
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- You don't need to specify `this` as the receiver: `this::foo` and `::foo` are equivalent. -->
Указывать `this` как получатель не нужно: `this::foo` и `::foo` эквивалентны.

<a name="bound-constructor-references"></a>

<!-- ### Bound constructor references -->
### Привязанные ссылки на конструкторы

<!-- A bound callable reference to a constructor of an [inner class](nested-classes.md#inner-classes) can
be obtained by providing an instance of the outer class: -->
Привязанную вызываемую ссылку на конструктор [внутреннего класса](nested-classes.html#inner-classes) можно получить,
предоставив экземпляр внешнего класса:

```kotlin
class Outer {
    inner class Inner
}

val o = Outer()
val boundInnerCtor = o::Inner
```
