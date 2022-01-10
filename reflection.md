---
type: doc
layout: reference
category: "Syntax"
title: "Рефлексия"
url: https://kotlinlang.ru/docs/reflection.html
---

<!-- При переводе статьи оригинальная версия была от 07 December 2021 -->

<!-- # Reflection -->
# Рефлексия

<!-- _Reflection_ is a set of language and library features that allows you to introspect the structure of your program at runtime.
Functions and properties are first-class citizens in Kotlin, and the ability to introspect them (for example, learning the name or
the type of a property or function at runtime) is essential when using a functional or reactive style. -->
*Рефлексия* — это набор возможностей языка и библиотек, который позволяет интроспектировать программу (обращаться к её
структуре) во время её исполнения. В Kotlin функции и свойства первичны, и поэтому их интроспекция (например, получение
имени или типа во время исполнения) сильно переплетена с использованием функциональной или реактивной парадигмы.

<!-- > Kotlin/JS provides limited support for reflection features. [Learn more about reflection in Kotlin/JS](js-reflection.md). -->
> Kotlin/JS предоставляет ограниченную поддержку рефлексии. [Узнайте больше об рефлексии в Kotlin/JS](js-reflection.html).

<a name="jvm-dependency"></a>

<!-- ## JVM dependency -->
## JVM pависимость

<!-- On the JVM platform, the Kotlin compiler distribution includes the runtime component required for using the reflection features as a separate
artifact, `kotlin-reflect.jar`. This is done to reduce the required size of the runtime
library for applications that do not use reflection features. -->
На платформе JVM дистрибутив компилятора Kotlin включает в себя компонент среды выполнения, необходимый для
использования рефлексии в качестве отдельного артефакта, `kotlin-reflect.jar`. Это сделано для уменьшения требуемого
размера runtime-библиотеки для приложений, которые не используют рефлексию.

<!-- To use reflection in a Gradle or Maven project, add the dependency on `kotlin-reflect`: -->
Чтобы использовать рефлексию в проекте Gradle или Maven, добавьте зависимость `kotlin-reflect`:

<!-- * In Gradle: -->
* В Gradle:

    ```kotlin
    // Kotlin
    dependencies {
        implementation("org.jetbrains.kotlin:kotlin-reflect:%kotlinVersion%")
    }
    ```

    ```groovy
    // Groovy
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
In other supported cases (IntelliJ IDEA projects that use the command-line compiler or Ant),
it is added by default. In the command-line compiler and Ant, you can use the `-no-reflect` compiler option to exclude
`kotlin-reflect.jar` from the classpath. -->
Если вы не используете Gradle или Maven, убедитесь, что `kotlin-reflect.jar` добавлен в classpath вашего проекта. В
других поддерживаемых случаях (проекты IntelliJ IDEA, использующие компилятор командной строки или Ant), он добавляется
по умолчанию. В компиляторе командной строки и Ant вы можете использовать опцию компилятора `-no-reflect`, чтобы
исключить `kotlin-reflect.jar` из classpath.

<a name="class-references"></a>

<!-- ## Class references -->
## Ссылки на классы

<!-- The most basic reflection feature is getting the runtime reference to a Kotlin class. To obtain the reference to a
statically known Kotlin class, you can use the _class literal_ syntax: -->
Самая базовая возможность рефлексии — это получение ссылки на Kotlin класс. Чтобы получить ссылку на статический Kotlin
класс, используйте синтаксис *литерала класса*.

```kotlin
val c = MyClass::class
```

<!-- The reference is a [KClass](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/index.html) type value. -->
Ссылка на класс имеет тип [KClass](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/index.html).

<!-- >On JVM: a Kotlin class reference is not the same as a Java class reference. To obtain a Java class reference,
>use the `.java` property on a `KClass` instance. -->
> Ссылка на Kotlin класс это не то же самое, что и ссылка на Java класс. Для получения ссылки на Java класс, используйте
> свойство `.java` экземпляра `KClass`.

<a name="bound-class-references"></a>

<!-- ### Bound class references -->
### Ссылки на привязанные классы

<!-- You can get the reference to the class of a specific object with the same `::class` syntax by using the object as a receiver: -->
Вы можете получить ссылку на класс определённого объекта с помощью уже известного вам синтаксиса, вызвав `::class` у
нужного объекта.

```kotlin
val widget: Widget = ...
assert(widget is GoodWidget) { "Bad widget: ${widget::class.qualifiedName}" }
```

<!-- You will obtain the reference to the exact class of an object, for example, `GoodWidget` or `BadWidget`,
regardless of the type of the receiver expression (`Widget`). -->
Вы получите ссылку на точный класс объекта, например `GoodWidget` или `BadWidget`, несмотря на тип объекта, участвующего
в выражении (`Widget`).

<a name="callable-references"></a>

<!-- ## Callable references -->
## Вызываемые ссылки

<!-- References to functions, properties, and constructors can
also be called or used as instances of [function types](lambdas.md#function-types). -->
Ссылки на функции, свойства и конструкторы также могут вызываться или использоваться в качестве экземпляров
[функциональных типов](lambdas.html#function-types).

<!-- The common supertype for all callable references is [`KCallable<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-callable/index.html),
where `R` is the return value type. It is the property type for properties, and the constructed type for constructors. -->
Общим супертипом для всех вызываемых ссылок является
[`KCallable<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-callable/index.html), где `R` - тип
возвращаемого значения. Это тип свойства для свойств и сконструированный тип для конструкторов.

<a name="function-references"></a>

<!-- ### Function references -->
### Ссылки на функции

<!-- When you have a named function declared as below, you can call it directly (`isOdd(5)`): -->
Когда у нас есть именованная функция, как объявленная ниже, вы можете вызвать её напрямую (`isOdd(5)`).

```kotlin
fun isOdd(x: Int) = x % 2 != 0
```

<!-- Alternatively, you can use the function as a function type value, that is, pass it
to another function. To do so, use the `::` operator: -->
В качестве альтернативы вы можете передать её как значение, например в другую функцию.
Чтобы сделать это, используйте оператор `::`.

```kotlin
val numbers = listOf(1, 2, 3)
println(numbers.filter(::isOdd)) // выведет [1, 3]
```

<!-- Here `::isOdd` is a value of function type `(Int) -> Boolean`. -->
Здесь, `::isOdd` — значение функционального типа `(Int) -> Boolean`.

<!-- Function references belong to one of the [`KFunction<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-function/index.html)
subtypes, depending on the parameter count. For instance, `KFunction3<T1, T2, T3, R>`. -->
Ссылки на функции принадлежат к одному из подтипов
[`KFunction<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-function/index.html), в зависимости
от количества параметров. Например, `KFunction3<T1, T2, T3, R>`.

<!-- `::` can be used with overloaded functions when the expected type is known from the context.
For example: -->
Оператор `::` может быть использован с перегруженными функциями, когда тип используемой функции известен из контекста.
Например:

```kotlin
fun isOdd(x: Int) = x % 2 != 0
fun isOdd(s: String) = s == "brillig" || s == "slithy" || s == "tove"

val numbers = listOf(1, 2, 3)
println(numbers.filter(::isOdd)) // ссылается на isOdd(x: Int)
```

<!-- Alternatively, you can provide the necessary context by storing the method reference in a variable with an explicitly specified type: -->
Также вместо этого вы можете указать нужный контекст путём сохранения ссылки на функцию в переменной, тип которой задан
явно.

```kotlin
val predicate: (String) -> Boolean = ::isOdd // ссылается на isOdd(x: String)
```

<!-- If you need to use a member of a class or an extension function, it needs to be qualified: `String::toCharArray`. -->
Если вы хотите использовать член класса или [функцию-расширение](extensions.html), вам нужно обозначить это явным образом:
`String::toCharArray`.

<!-- Even if you initialize a variable with a reference to an extension function, the inferred function type will
have no receiver, but it will have an additional parameter accepting a receiver object. To have a function type
with a receiver instead, specify the type explicitly: -->
Даже если вы инициализируете переменную ссылкой на функцию-расширение, у предполагаемого типа функции не будет
получателя, но у него будет дополнительный параметр, принимающий объект-получатель. Чтобы вместо этого иметь тип функции
с приемником, укажите тип явно.

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
Она возвращает композицию двух функций, переданных ей: `compose(f, g) = f(g(*))`. Вы можете применять её к ссылкам на
функции.

```kotlin
fun length(s: String) = s.length

val oddLength = compose(::isOdd, ::length)
val strings = listOf("a", "ab", "abc")

println(strings.filter(oddLength)) // выведет "[a, abc]"
```

<a name="property-references"></a>

<!-- ### Property references -->
### Ссылки на свойства

<!-- To access properties as first-class objects in Kotlin, use the `::` operator: -->
Для доступа к свойствам как первичным объектам в Kotlin используйте оператор `::`.

```kotlin
val x = 1

fun main() {
    println(::x.get())
    println(::x.name)
}
```

<!-- The expression `::x` evaluates to a `KProperty<Int>` type property object. You can read its
value using `get()` or retrieve the property name using the `name` property. For more information, see
the [docs on the `KProperty` class](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-property/index.html). -->
Выражение `::x` возвращает объект свойства типа `KProperty<Int>`, который позволяет вам читать его значение с помощью
`get()` или получать имя свойства при помощи обращения к свойству `name`. Для получения более подробной информации
обратитесь к [документации класса `KProperty`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-property/index.html).

<!-- For a mutable property such as `var y = 1`, `::y` returns a value with the [`KMutableProperty<Int>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-mutable-property/index.html) type
which has a `set()` method: -->
Для изменяемых свойств, например `var y = 1`, `::y` возвращает значение типа
[`KMutableProperty<Int>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-mutable-property/index.html),
который имеет метод `set()`.

```kotlin
var y = 1

fun main() {
    ::y.set(2)
    println(y) // выведет 2
}
```

<!-- A property reference can be used where a function with a single generic parameter is expected: -->
Ссылка на свойство может быть использована там, где ожидается функция с одним обобщённым параметром.

```kotlin
val strs = listOf("a", "bc", "def")
println(strs.map(String::length)) // выведет [1, 2, 3]
```

<!-- To access a property that is a member of a class, qualify it as follows: -->
Для доступа к свойству, которое является членом класса, укажите класс.

```kotlin
class A(val p: Int)

fun main() {
    val prop = A::p
    println(prop.get(A(1))) // выведет "1"
}
```

<!-- For an extension property: -->
Для [функции-расширения](extensions.html):

```kotlin
val String.lastChar: Char
    get() = this[length - 1]

fun main() {
    println(String::lastChar.get("abc")) // выведет "c"
}
```

<a name="interoperability-with-java-reflection"></a>

<!-- ### Interoperability with Java reflection -->
### Взаимодействие с рефлексией Java

<!-- On the JVM platform, the standard library contains extensions for reflection classes that provide a mapping to and from Java
reflection objects (see package `kotlin.reflect.jvm`).
For example, to find a backing field or a Java method that serves as a getter for a Kotlin property, you can write something like this: -->
На платформе JVM стандартная библиотека Kotlin содержит [расширения](extensions.html), которые сопоставляют расширяемые
ими объекты рефлексии Kotlin с объектами рефлексии Java (см. пакет `kotlin.reflect.jvm`). К примеру, для нахождения поля
или Java метода, который служит геттером для Kotlin-свойства, вы можете написать что-то вроде этого:

```kotlin
import kotlin.reflect.jvm.*
 
class A(val p: Int)
 
fun main() {
    println(A::p.javaGetter) // выведет "public final int A.getP()"
    println(A::p.javaField)  // выведет "private final int A.p"
}
```

<!-- To get the Kotlin class that corresponds to a Java class, use the `.kotlin` extension property: -->
Для получения класса Kotlin, соответствующего классу Java, используйте свойство-расширение `.kotlin`.

```kotlin
fun getKClass(o: Any): KClass<Any> = o.javaClass.kotlin
```

<a name="constructor-references"></a>

<!-- ### Constructor references -->
### Ссылки на конструктор

<!-- Constructors can be referenced just like methods and properties. You can use them wherever the program expects a function type object
that takes the same parameters as the constructor and returns an object of the appropriate type.
Constructors are referenced by using the `::` operator and adding the class name. Consider the following function
that expects a function parameter with no parameters and return type `Foo`: -->
К конструкторам можно обратиться так же, как и к методам или свойствам. Они могут быть использованы везде, где ожидается
объект функционального типа, который принимает те же параметры, что и конструктор, и возвращает объект соответствующего
типа. Обращение к конструкторам происходит с помощью оператора `::` и имени класса. Рассмотрим функцию, которая
принимает функциональный параметр без параметров и возвращает `Foo`:

```kotlin
class Foo

fun function(factory: () -> Foo) {
    val x: Foo = factory()
}
```

<!-- Using `::Foo`, the zero-argument constructor of the class `Foo`, you can call it like this: -->
Используя `::Foo`, конструктор класса Foo без аргументов, вы можете просто вызывать функцию таким образом:

```kotlin
function(::Foo)
```

<!-- Callable references to constructors are typed as one of the
[`KFunction<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-function/index.html) subtypes
depending on the parameter count. -->
Вызываемые ссылки на конструкторы вводятся как один из подтипов
[`KFunction<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-function/index.html) в зависимости
от количества параметров.

<a name="bound-function-and-property-references"></a>

<!-- ### Bound function and property references -->
## Привязанные функции и свойства

<!-- You can refer to an instance method of a particular object: -->
Вы можете сослаться на метод экземпляра конкретного объекта.

```kotlin
val numberRegex = "\\d+".toRegex()
println(numberRegex.matches("29")) // выведет "true"

val isNumber = numberRegex::matches
println(isNumber("29")) // выведет "true"
```

<!-- Instead of calling the method `matches` directly, the example uses a reference to it.
Such a reference is bound to its receiver.
It can be called directly (like in the example above) or used whenever a function type expression is expected: -->
Вместо вызова метода `matches` напрямую, в примере используется ссылка на него. Такие ссылки привязаны к объектам, к
которым относятся. Их можно вызывать напрямую (как в примере выше) или использовать всякий раз, когда ожидается
выражение функционального типа.

```kotlin
val numberRegex = "\\d+".toRegex()
val strings = listOf("abc", "124", "a70")
println(strings.filter(numberRegex::matches)) // выведет "[124]"
```

<!-- Compare the types of the bound and the unbound references.
The bound callable reference has its receiver "attached" to it, so the type of the receiver is no longer a parameter: -->
Сравним типы привязанных и соответствующих непривязанных ссылок. Объект-приёмник "прикреплён" к привязанной ссылке,
поэтому тип приёмника больше не является параметром.

```kotlin
val isNumber: (CharSequence) -> Boolean = numberRegex::matches

val matches: (Regex, CharSequence) -> Boolean = Regex::matches
```

<!-- A property reference can be bound as well: -->
Ссылка на свойство может быть также привязанной.

```kotlin
val prop = "abc"::length
println(prop.get())   // выведет "3"
```

<!-- You don't need to specify `this` as the receiver: `this::foo` and `::foo` are equivalent. -->
Вам не нужно указывать `this` в качестве приёмника: `this::foo` и `::foo` эквивалентны.

<a name="bound-constructor-references"></a>

<!-- ### Bound constructor references -->
### Ссылки на привязанные конструкторы

<!-- A bound callable reference to a constructor of an [inner class](nested-classes.md#inner-classes) can
be obtained by providing an instance of the outer class: -->
Привязанная ссылка на конструктор [внутреннего класса](nested-classes.html#inner-classes) может быть получена путем
предоставления экземпляра внешнего класса.

```kotlin
class Outer {
    inner class Inner
}

val o = Outer()
val boundInnerCtor = o::Inner
```
