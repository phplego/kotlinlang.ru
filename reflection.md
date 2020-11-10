---
type: doc
layout: reference
category: "Syntax"
title: "Рефлексия"
url: https://kotlinlang.ru/docs/reference/reflection.html
---

<!--# Reflection-->
# Рефлексия

<!--Reflection is a set of language and library features that allows for introspecting the structure of your own program at runtime.
Kotlin makes functions and properties first-class citizens in the language, and introspecting them (i.e. learning a name or 
a type of a property or function at runtime) is closely intertwined with simply using a functional or reactive style.-->
Рефлексия — это набор возможностей языка и библиотек, который позволяет интроспектировать программу (обращаться к её структуре) 
во время её исполнения.
В Kotlin функции и свойства первичны, и поэтому их интроспекция (например, получение имени или типа во время исполнения) сильно
переплетена с использованием функциональной или реактивной парадигмы.

<!-- On the Java platform, the runtime component required for using the reflection features is distributed as a separate
JAR file (`kotlin-reflect.jar`). This is done to reduce the required size of the runtime library for applications
that do not use reflection features. If you do use reflection, please make sure that the .jar file is added to the
classpath of your project.-->
> На платформе Java библиотека для использования рефлексии находится в отдельном JAR-файле (`kotlin-reflect.jar`).
Это было сделано для уменьшения требуемого размера runtime-библиотеки для приложений, которые не используют рефлексию.
Если вы используете рефлексию, удостоверьтесь, что этот .jar файл добавлен в classpath вашего проекта.

<!--## Class References-->
## Ссылки на классы

<!--The most basic reflection feature is getting the runtime reference to a Kotlin class. To obtain the reference to a
statically known Kotlin class, you can use the _class literal_ syntax:-->
Самая базовая возможность рефлексии — это получение ссылки на Kotlin класс. Чтобы получить ссылку на статический Kotlin класс, 
используйте синтаксис _литерала класса_:

```kotlin
val c = MyClass::class
```

<!--The reference is a value of type [KClass](/api/latest/jvm/stdlib/kotlin.reflect/-k-class/index.html).-->
Ссылка на класс имеет тип [KClass](http://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/index.html).

<!--Note that a Kotlin class reference is not the same as a Java class reference. To obtain a Java class reference,
use the `.java` property on a `KClass` instance.-->
Обратите внимание, что ссылка на Kotlin класс это не то же самое, что и ссылка на Java класс. Для получения ссылки на Java класс,
используйте свойство `.java` экземпляра `KClass`.

<!--## Bound Class References (since 1.1)-->
## Ссылки на привязанные классы
_Примечание: доступно с версии Kotlin 1.1_

<!--You can get the reference to a class of a specific object with the same `::class` syntax by using the object as a receiver:-->
Вы можете получить ссылку на класс определённого объекта с помощью уже известного вам синтаксиса, вызвав `::class` у нужного объекта:

```kotlin
val widget: Widget = ...
assert(widget is GoodWidget) { "Bad widget: ${widget::class.qualifiedName}" }
```

<!--You obtain the reference to an exact class of an object, for instance `GoodWidget` or `BadWidget`, despite the type of the receiver expression (`Widget`).  -->
Вы получите ссылку на точный класс объекта, например `GoodWidget` или `BadWidget`, несмотря на тип объекта, участвующего в выражении (`Widget`).

<a name="function-references"></a>

<!--## Function References-->
## Ссылки на функции

<!--When we have a named function declared like this:-->
Когда у нас есть именованная функция, объявленная следующим образом:

```kotlin
fun isOdd(x: Int) = x % 2 != 0
```

<!--We can easily call it directly (`isOdd(5)`), but we can also pass it as a value, e.g. to another function.
To do this, we use the `::` operator:-->
Мы можем как вызвать её напрямую (`isOdd(5)`), так и передать её как значение, например в другую функцию.
Чтобы сделать это, используйте оператор `::`:

```kotlin
val numbers = listOf(1, 2, 3)
println(numbers.filter(::isOdd)) // выведет [1, 3]
```

<!--Here `::isOdd` is a value of function type `(Int) -> Boolean`.-->
Здесь, `::isOdd` — значение функционального типа `(Int) -> Boolean`.

<!--`::` can be used with overloaded functions when the expected type is known from the context.
For example:-->
Оператор `::` может быть использован с перегруженными функциями, когда тип используемой функции известен из контекста.
Например:

```kotlin
fun isOdd(x: Int) = x % 2 != 0
fun isOdd(s: String) = s == "brillig" || s == "slithy" || s == "tove"

val numbers = listOf(1, 2, 3)
println(numbers.filter(::isOdd)) // ссылается на isOdd(x: Int)
```

<!--Alternatively, you can provide the necessary context by storing the method reference in a variable with an explicitly specified type:-->
Также вместо этого вы можете указать нужный контекст путём сохранения ссылки на функцию в переменной, тип которой задан явно:

```kotlin
val predicate: (String) -> Boolean = ::isOdd   // ссылается на isOdd(x: String)
```

<!--If we need to use a member of a class, or an extension function, it needs to be qualified.
e.g. `String::toCharArray` gives us an extension function for type `String`: `String.() -> CharArray`.-->
Если вы хотите использовать член класса или [функцию-расширение](extensions.html), вам нужно обозначить это явным образом.
Например, `String::toCharArray` даёт нам функцию-расширение для типа `String`: `String.() -> CharArray`

<!--### Example: Function Composition-->
### Пример: композиция функций

<!--Consider the following function:-->
Рассмотрим следующую функцию:

```kotlin
fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C {
    return { x -> f(g(x)) }
}
```

<!--It returns a composition of two functions passed to it: `compose(f, g) = f(g(*))`.
Now, you can apply it to callable references:-->
Она возвращает композицию двух функций, переданных ей: `compose(f, g) = f(g(*))`. 
Теперь вы можете применять её к ссылкам на функции:

```kotlin
fun length(s: String) = s.length

val oddLength = compose(::isOdd, ::length)
val strings = listOf("a", "ab", "abc")

println(strings.filter(oddLength)) // выведет "[a, abc]"
```

<!--## Property References-->
## Ссылки на свойства

<!--To access properties as first-class objects in Kotlin, we can also use the `::` operator:-->
Для доступа к свойствам как первичным объектам в Kotlin мы по-прежнему можем использовать оператор `::`:

```kotlin
var x = 1

fun main(args: Array<String>) {
    println(::x.get()) // выведет "1"
    ::x.set(2)
    println(x)         // выведет "2"
}
```

<!--The expression `::x` evaluates to a property object of type `KProperty<Int>`, which allows us to read its
value using `get()` or retrieve the property name using the `name` property. For more information, please refer to
the [docs on the `KProperty` class](/api/latest/jvm/stdlib/kotlin.reflect/-k-property/index.html).-->
Выражение `::x` возвращает объект свойства типа `KProperty<Int>`, который позволяет нам читать его значение с помощью `get()`
или получать имя свойства при помощи обращения к свойству `name`. Для получения более подробной информации 
обратитесь к [документации класса `KProperty`](http://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-property/index.html).

<!--For a mutable property, e.g. `var y = 1`, `::y` returns a value of type [`KMutableProperty<Int>`](/api/latest/jvm/stdlib/kotlin.reflect/-k-mutable-property/index.html),
which has a `set()` method.-->
Для изменяемых свойств, например `var y = 1`, `::y` возвращает значение типа 
[`KMutableProperty<Int>`](http://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-mutable-property/index.html).

<!--A property reference can be used where a function with no parameters is expected:-->
Ссылка на свойство может быть использована там, где ожидается функция без параметров:
 
```kotlin
val strs = listOf("a", "bc", "def")
println(strs.map(String::length)) // выведет [1, 2, 3]
```

<!--To access a property that is a member of a class, we qualify it:-->
Для доступа к свойству, которое является членом класса, мы указываем класс:

```kotlin
class A(val p: Int)

fun main(args: Array<String>) {
    val prop = A::p
    println(prop.get(A(1))) // выведет "1"
}
```

<!--For an extension property:-->
Для [функции-расширения](extensions.html):


```kotlin
val String.lastChar: Char
    get() = this[length - 1]

fun main(args: Array<String>) {
    println(String::lastChar.get("abc")) // выведет "c"
}
```

<!--### Interoperability With Java Reflection-->
### Взаимодействие с рефлексией Java 
<!--On the Java platform, standard library contains extensions for reflection classes that provide a mapping to and from Java
  reflection objects (see package `kotlin.reflect.jvm`).
For example, to find a backing field or a Java method that serves as a getter for a Kotlin property, you can say something like this:-->
На платформе Java стандартная библиотека Kotlin содержит [расширения](extensions.html), 
которые сопоставляют расширяемые ими объекты рефлексии Kotlin с объектами рефлексии Java (см. пакет `kotlin.reflect.jvm`).
К примеру, для нахождения поля или метода, который служит геттером для Kotlin-свойства, вы можете написать что-то вроде этого:

```kotlin
import kotlin.reflect.jvm.*
 
class A(val p: Int)
 
fun main(args: Array<String>) {
    println(A::p.javaGetter) // выведет "public final int A.getP()"
    println(A::p.javaField)  // выведет "private final int A.p"
}
```

<!--To get the Kotlin class corresponding to a Java class, use the `.kotlin` extension property:-->
Для получения класса Kotlin, соответствующего классу Java, используйте свойство-расширение `.kotlin`:

```kotlin
fun getKClass(o: Any): KClass<Any> = o.javaClass.kotlin
```

<!--## Constructor References-->
## Ссылки на конструктор

<!--Constructors can be referenced just like methods and properties. They can be used wherever an object of function type 
is expected that takes the same parameters as the constructor and returns an object of the appropriate type. 
Constructors are referenced by using the `::` operator and adding the class name. Consider the following function 
that expects a function parameter with no parameters and return type `Foo`:-->
К конструкторам можно обратиться так же, как и к методам или свойствам. Они могут быть использованы везде, где ожидается 
объект функционального типа. Обращение к конструкторам происходит с помощью оператора `::` и имени класса. 
Рассмотрим функцию, которая принимает функциональный параметр без параметров и возвращает `Foo`:

```kotlin
class Foo

fun function(factory : () -> Foo) {
    val x : Foo = factory()
}
```

<!--Using `::Foo`, the zero-argument constructor of the class Foo, we can simply call it like this:-->
Используя `::Foo`, конструктор класса Foo без аргументов, мы можем просто вызывать функцию таким образом:

```kotlin
function(::Foo)
```

<!--## Bound Function and Property References (since 1.1)-->
## Привязанные функции 
<!--You can refer to an instance method of a particular object.-->
Вы можете сослаться на метод экземпляра конкретного объекта.

```kotlin 
val numberRegex = "\\d+".toRegex()
println(numberRegex.matches("29")) // выведет "true"
 
val isNumber = numberRegex::matches
println(isNumber("29")) // выведет "true"
```

<!--Instead of calling the method `matches` directly we are storing a reference to it.
Such reference is bound to its receiver.
It can be called directly (like in the example above) or used whenever an expression of function type is expected:-->
Вместо вызова метода `matches` напрямую, мы храним ссылку на него. Такие ссылки привязаны к объектам, к которым относятся:

```kotlin
val strings = listOf("abc", "124", "a70")
println(strings.filter(numberRegex::matches)) // выведет "[124]"
```

<!--Compare the types of bound and the corresponding unbound references.
Bound callable reference has its receiver "attached" to it, so the type of the receiver is no longer a parameter:-->
Сравним типы привязанных и соответствующих непривязанных ссылок. Объект-приёмник "прикреплён" к привязанной ссылке, поэтому тип
приёмника больше не является параметром:

```kotlin
val isNumber: (CharSequence) -> Boolean = numberRegex::matches

val matches: (Regex, CharSequence) -> Boolean = Regex::matches
```

<!--Property reference can be bound as well:-->
Ссылка на свойство может быть также привязанной:

```kotlin
val prop = "abc"::length
println(prop.get())   // выведет "3"
```
