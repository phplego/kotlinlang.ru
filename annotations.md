---
type: doc
layout: reference
category: "Syntax"
title: "Аннотации"
url: https://kotlinlang.ru/docs/annotations.html
---

<!-- При переводе статьи оригинальная версия была от 16 November 2021 -->

<!-- # Annotations -->
# Аннотации

<!-- Annotations are means of attaching metadata to code. To declare an annotation, put the `annotation` modifier in front of a class: -->
Аннотации являются специальной формой синтаксических метаданных, добавленных в исходный код.
Для объявления аннотации используйте модификатор `annotation` перед именем класса.

```kotlin
annotation class Fancy
```

<!-- Additional attributes of the annotation can be specified by annotating the annotation class with meta-annotations: -->
Дополнительные атрибуты аннотаций могут быть определены путём аннотации класса-аннотации мета-аннотациями:

<!--  * [`@Target`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-target/index.html) specifies the possible kinds of
    elements which can be annotated with the annotation (such as classes, functions, properties, and expressions);
  * [`@Retention`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-retention/index.html) specifies whether the
    annotation is stored in the compiled class files and whether it's visible through reflection at runtime
    (by default, both are true);
  * [`@Repeatable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-repeatable/index.html) allows using the same annotation
    on a single element multiple times;
  * [`@MustBeDocumented`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-must-be-documented/index.html) specifies that the
    annotation is part of the public API and should be included in the class or method signature shown in the
    generated API documentation. -->

* [`@Target`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-target/index.html) определяет возможные
виды элементов, которые могут быть помечены аннотацией (такие как классы, функции, свойства и выражения);
* [`@Retention`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-retention/index.html) определяет, будет
ли аннотация храниться в скомпилированном классе и будет ли видима через рефлексию (по умолчанию оба утверждения верны);
* [`@Repeatable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-repeatable/index.html) позволяет
использовать одну и ту же аннотацию на одном элементе несколько раз;
* [`@MustBeDocumented`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-must-be-documented/index.html)
определяет то, что аннотация является частью публичного API и должна быть включена в сигнатуру класса или метода,
попадающую в сгенерированную документацию.

```kotlin
@Target(AnnotationTarget.CLASS, AnnotationTarget.FUNCTION,
        AnnotationTarget.TYPE_PARAMETER, AnnotationTarget.VALUE_PARAMETER, 
        AnnotationTarget.EXPRESSION)
@Retention(AnnotationRetention.SOURCE)
@MustBeDocumented
annotation class Fancy
```

<a name="usage"></a>

<!-- ## Usage -->
## Использование

```kotlin
@Fancy class Foo {
    @Fancy fun baz(@Fancy foo: Int): Int {
        return (@Fancy 1)
    }
}
```

<!-- If you need to annotate the primary constructor of a class, you need to add the `constructor` keyword
to the constructor declaration, and add the annotations before it: -->
Если вам нужно пометить аннотацией первичный конструктор класса, следует добавить ключевое слово `constructor`
при объявлении конструктора и вставить аннотацию перед ним.

```kotlin
class Foo @Inject constructor(dependency: MyDependency) { /* ... */ }
```

<!-- You can also annotate property accessors: -->
Вы также можете помечать аннотациями геттеры и сеттеры:

```kotlin
class Foo {
    var x: MyDependency? = null
        @Inject set
}
```

<a name="constructors"></a>

<!-- ## Constructors -->
## Конструкторы

<!-- Annotations can have constructors that take parameters. -->
Аннотации могут иметь конструкторы, принимающие параметры.

```kotlin
annotation class Special(val why: String)

@Special("пример") class Foo {}
```
<!-- Allowed parameter types are: -->
Разрешены параметры следующих типов:

<!--  * Types that correspond to Java primitive types (Int, Long etc.)
 * Strings
 * Classes (`Foo::class`)
 * Enums
 * Other annotations
 * Arrays of the types listed above -->

* типы, которые соответствуют примитивам Java (Int, Long и т.д.),
* строки,
* классы (`Foo::class`),
* перечисляемые типы,
* другие аннотации,
* массивы, содержащие значения приведённых выше типов.
  
<!-- Annotation parameters cannot have nullable types, because the JVM does not support storing `null` as a value
of an annotation attribute. -->
Параметры аннотаций не могут иметь обнуляемые типы, потому что JVM не поддерживает хранение `null` в качестве значения
атрибута аннотации.

<!-- If an annotation is used as a parameter of another annotation, its name is not prefixed with the `@` character: -->
Если аннотация используется в качестве параметра к другой аннотации, её имя не нужно начинать со знака `@`.

```kotlin
annotation class ReplaceWith(val expression: String)

annotation class Deprecated(
        val message: String,
        val replaceWith: ReplaceWith = ReplaceWith(""))

@Deprecated("Эта функция устарела, вместо неё используйте ===", ReplaceWith("this === other"))
```

<!-- If you need to specify a class as an argument of an annotation, use a Kotlin class
([KClass](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/index.html)). The Kotlin compiler will
automatically convert it to a Java class, so that the Java code can access the annotations and arguments
normally. -->
Если вам нужно определить класс как аргумент аннотации, используйте Kotlin класс
([KClass](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/index.html)). Компилятор Kotlin
автоматически сконвертирует его в Java класс, так что код на Java сможет видеть аннотации и их аргументы.

```kotlin

import kotlin.reflect.KClass

annotation class Ann(val arg1: KClass<*>, val arg2: KClass<out Any>)

@Ann(String::class, Int::class) class MyClass
```

<a name="instantiation"></a>

<!-- ## Instantiation -->
## Создание экземпляра

<!-- In Java, an annotation type is a form of an interface, so you can implement it and use an instance.
As an alternative to this mechanism, Kotlin lets you call a constructor of an annotation class in arbitrary code 
and similarly use the resulting instance. -->
В Java тип аннотации - это форма интерфейса, поэтому вы можете реализовать его и использовать экземпляр. В качестве
альтернативы этому механизму Kotlin позволяет вызывать конструктор класса аннотаций в произвольном коде и аналогичным
образом использовать полученный экземпляр.

```kotlin
annotation class InfoMarker(val info: String)

fun processInfo(marker: InfoMarker): Unit = TODO()

fun main(args: Array<String>) {
    if (args.isNotEmpty())
        processInfo(getAnnotationReflective(args))
    else
        processInfo(InfoMarker("default"))
}
```

<!-- > Instantiation of annotation classes for Kotlin/Native is not yet available. -->
> Создание экземпляров классов аннотаций для Kotlin/Native пока недоступно.

<!-- Learn more about instantiation of annotation classes in [this KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/annotation-instantiation.md). -->
Узнайте больше о создании экземпляров классов аннотаций в [этом KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/annotation-instantiation.md).

<a name="lambdas"></a>

<!-- ## Lambdas -->
## Лямбды

<!-- Annotations can also be used on lambdas. They will be applied to the `invoke()` method into which the body
of the lambda is generated. This is useful for frameworks like [Quasar](https://docs.paralleluniverse.co/quasar/),
which uses annotations for concurrency control. -->
Аннотации также можно использовать с лямбдами. Они будут применены к `invoke()` методу, в который генерируется тело
лямбды. Это полезно для фреймворков вроде [Quasar](https://docs.paralleluniverse.co/quasar/), которые используют
аннотации для контроля многопоточности.

```kotlin
annotation class Suspendable

val f = @Suspendable { Fiber.sleep(10) }
```

<a name="annotation-use-site-targets"></a>

<!-- ## Annotation use-site targets -->
## Аннотации с указаниями

<!-- When you're annotating a property or a primary constructor parameter, there are multiple Java elements which are
generated from the corresponding Kotlin element, and therefore multiple possible locations for the annotation in
the generated Java bytecode. To specify how exactly the annotation should be generated, use the following syntax: -->
Когда вы помечаете свойство или первичный конструктор аннотацией, из соответствующего Kotlin-элемента генерируются
несколько Java-элементов, и поэтому в сгенерированном байт-коде элемент появляется в нескольких местах. Чтобы указать, в
каком именно месте аннотация должна быть сгенерирована, используйте следующий синтаксис:

```kotlin
class Example(@field:Ann val foo,  // аннотация для Java-поля
              @get:Ann val bar,    // аннотация для Java-геттера
              @param:Ann val quux) // аннотация для параметра конструктора Java 
```

<!-- The same syntax can be used to annotate the entire file. To do this, put an annotation with the target `file` at
the top level of a file, before the package directive or before all imports if the file is in the default package: -->
Тот же синтаксис может быть использован для аннотации целого файла. Для этого отметьте аннотацию словом `file` и
вставьте её в начале файла: перед указанием пакета или перед импортами, если файл находится в пакете по умолчанию.

```kotlin
@file:JvmName("Foo")

package org.jetbrains.demo
```

<!-- If you have multiple annotations with the same target, you can avoid repeating the target by adding brackets after the
target and putting all the annotations inside the brackets: -->
Если вы помечаете несколькими аннотациями одно указание, вы можете избежать повторения путём указания всех аннотаций в
квадратных скобках.

```kotlin
class Example {
     @set:[Inject VisibleForTesting]
     var collaborator: Collaborator
}
```

<!-- The full list of supported use-site targets is: -->
Полный список поддерживаемых указаний:

<!--   * `file`
  * `property` (annotations with this target are not visible to Java)
  * `field`
  * `get` (property getter)
  * `set` (property setter)
  * `receiver` (receiver parameter of an extension function or property)
  * `param` (constructor parameter)
  * `setparam` (property setter parameter)
  * `delegate` (the field storing the delegate instance for a delegated property) -->

* `file`
* `property` (такие аннотации не будут видны в Java)
* `field`
* `get` (геттер)
* `set` (сеттер)
* `receiver` (параметр-приёмник [расширения](extensions.html))
* `param` (параметр конструктора)
* `setparam` (параметр сеттера)
* `delegate` (поле, которое хранит экземпляр делегата для [делегированного свойства](delegated-properties.html))

<!-- To annotate the receiver parameter of an extension function, use the following syntax: -->
Чтобы пометить аннотацией параметр-приёмник [расширения](extensions.html), используйте следующий синтаксис:

```kotlin
fun @receiver:Fancy String.myExtension() { /* ... */ }
```

<!-- If you don't specify a use-site target, the target is chosen according to the `@Target` annotation of the annotation
being used. If there are multiple applicable targets, the first applicable target from the following list is used: -->
Если вы не сделали указание, аннотация будет применена к элементу, выбранному в соответствии с аннотацией `@Target` той
аннотации, которую вы используете. Если существует несколько элементов, к которым возможно применение аннотации, будет
выбран первый подходящий элемент из следующего списка:

* `param`
* `property`
* `field`

<a name="java-annotations"></a>

<!-- ## Java annotations -->
## Java-аннотации

<!-- Java annotations are 100% compatible with Kotlin: -->
Java-аннотации на 100% совместимы в Kotlin:

```kotlin
import org.junit.Test
import org.junit.Assert.*
import org.junit.Rule
import org.junit.rules.*

class Tests {
    // применение аннотации @Rule к геттеру
    @get:Rule val tempFolder = TemporaryFolder()

    @Test fun simple() {
        val f = tempFolder.newFile()
        assertEquals(42, getTheAnswer())
    }
}
```

<!-- Since the order of parameters for an annotation written in Java is not defined, you can't use a regular function
call syntax for passing the arguments. Instead, you need to use the named argument syntax: -->
Так как порядок параметров для Java-аннотаций не задан, вы не можете использовать обычный синтаксис вызова функции для
передачи аргументов. Вместо этого вам нужно использовать именованные аргументы.

```java
// Java
public @interface Ann {
    int intValue();
    String stringValue();
}
```

```kotlin
// Kotlin
@Ann(intValue = 1, stringValue = "abc") class C
```

<!-- Just like in Java, a special case is the `value` parameter; its value can be specified without an explicit name: -->
Также, как и в Java, параметр `value` — особый случай; его значение может быть определено без явного указания имени.

```java
// Java
public @interface AnnWithValue {
    String value();
}
```

```kotlin
// Kotlin
@AnnWithValue("abc") class C
```

<a name="arrays-as-annotation-parameters"></a>

<!-- ### Arrays as annotation parameters -->
### Массивы в качестве параметров аннотаций

<!-- If the `value` argument in Java has an array type, it becomes a `vararg` parameter in Kotlin: -->
Если аргумент `value` в Java является массивом, в Kotlin он становится `vararg` параметром.

```java
// Java
public @interface AnnWithArrayValue {
    String[] value();
}
```

```kotlin
// Kotlin
@AnnWithArrayValue("abc", "foo", "bar") class C
```

<!--For other arguments that have an array type, you need to use the array literal syntax or 
`arrayOf(...)`:-->
Для прочих аргументов, которые являются массивом, вам нужно использовать синтаксис литерала массива или явно
использовать `arrayOf`.

```java
// Java
public @interface AnnWithArrayMethod {
    String[] names();
}
```

```kotlin
@AnnWithArrayMethod(names = ["abc", "foo", "bar"]) 
class C
```

<a name="accessing-properties-of-an-annotation-instance"></a>

<!-- ### Accessing properties of an annotation instance -->
### Доступ к свойствам экземпляра аннотации

<!-- Values of an annotation instance are exposed as properties to Kotlin code: -->
Значения экземпляра аннотации становятся свойствами в Kotlin-коде.

```java
// Java
public @interface Ann {
    int value();
}
```

```kotlin
// Kotlin
fun foo(ann: Ann) {
    val i = ann.value
}
```

<a name="repeatable-annotations"></a>

<!-- ## Repeatable annotations -->
## Повторяющиеся аннотации

<!-- Just like [in Java](https://docs.oracle.com/javase/tutorial/java/annotations/repeating.html), Kotlin has repeatable annotations,
which can be applied to a single code element multiple times. To make your annotation repeatable, mark its declaration
with the [`@kotlin.annotation.Repeatable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-repeatable/)
meta-annotation. This will make it repeatable both in Kotlin and Java. Java repeatable annotations are also supported
from the Kotlin side. -->
Как и [в Java](https://docs.oracle.com/javase/tutorial/java/annotations/repeating.html), в Kotlin есть повторяющиеся
аннотации, которые можно применять к одному элементу кода несколько раз. Чтобы сделать вашу аннотацию повторяемой,
отметьте ее объявление мета-аннотацией
[`@kotlin.annotation.Repeatable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-repeatable/). Это
сделает её повторяемой как в Kotlin, так и в Java. Повторяющиеся аннотации Java также поддерживаются со стороны Kotlin.

<!-- The main difference with the scheme used in Java is the absence of a _containing annotation_, which the Kotlin compiler
generates automatically with a predefined name. For an annotation in the example below, it will generate the containing
annotation `@Tag.Container`: -->
Основным отличием от схемы, используемой в Java, является отсутствие *содержащей аннотации*, которую компилятор Kotlin
генерирует автоматически с предопределенным именем. Для аннотации в приведенном ниже примере будет сгенерирована
содержащая аннотация `@Tag.Container`.

```kotlin
@Repeatable
annotation class Tag(val name: String)

// Компилятор генерирует содержащую аннотацию @Tag.Container
```

<!-- You can set a custom name for a containing annotation by applying the
[`@kotlin.jvm.JvmRepeatable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvmrepeatable/) meta-annotation
and passing an explicitly declared containing annotation class as an argument: -->
Вы можете задать имя для содержащей аннотации, применив мета-аннотацию
[`@kotlin.jvm.JvmRepeatable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvmrepeatable/) и передав явно
объявленный класс содержащей аннотации в качестве аргумента.

```kotlin
@JvmRepeatable(Tags::class)
annotation class Tag(val name: String)

annotation class Tags(val value: Array<Tag>)
```

<!-- To extract Kotlin or Java repeatable annotations via reflection, use the [`KAnnotatedElement.findAnnotations()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect.full/find-annotations.html)
function. -->
Чтобы извлечь повторяющиеся аннотации Kotlin или Java с помощью отражения, используйте функцию
[`KAnnotatedElement.findAnnotations()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect.full/find-annotations.html).

<!-- Learn more about Kotlin repeatable annotations in [this KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/repeatable-annotations.md). -->
Узнайте больше о повторяющихся аннотациях Kotlin в
[этом KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/repeatable-annotations.md).
