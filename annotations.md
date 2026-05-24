---
type: doc
layout: reference
category: "Syntax"
title: "Аннотации"
url: https://kotlinlang.ru/docs/annotations.html
---

<!-- При переводе статьи оригинальная версия была от 11 February 2026 -->

<!-- # Annotations -->
# Аннотации

<!-- Annotations are tags that you can use to attach metadata to elements in your code. Tools and frameworks process this
metadata during compilation and runtime, and perform different actions based on it. -->
Аннотации — это метки, которые позволяют прикреплять метаданные к элементам кода. Инструменты и фреймворки
обрабатывают эти метаданные во время компиляции и выполнения программы и выполняют разные действия на их основе.

<!-- You can annotate your code to simplify and automate common tasks, such as generating boilerplate code, enforcing
coding standards or writing documentation. -->
С помощью аннотаций можно упростить и автоматизировать распространённые задачи, например генерировать шаблонный код,
обеспечивать соблюдение стандартов кодирования или писать документацию.

<!-- > If you want to develop your own annotation processors, you can use the [Kotlin Symbol Processing (KSP)](https://kotlinlang.org/docs/ksp-overview.html) API. -->
> Если вы хотите разрабатывать собственные обработчики аннотаций, используйте API
> [Kotlin Symbol Processing (KSP)](https://kotlinlang.org/docs/ksp-overview.html).

<a name="declaration"></a>

<!-- ## Declaration -->
## Объявление

<!-- Annotations are a special type of class. To declare an annotation, use the `annotation` keyword before the class declaration: -->
Аннотации — это специальный тип классов. Чтобы объявить аннотацию, используйте ключевое слово `annotation` перед
объявлением класса:

```kotlin
annotation class Fancy
```

<!-- Additional attributes of the annotation can be specified by annotating the annotation class with meta-annotations: -->
Дополнительные атрибуты аннотаций могут быть определены путём аннотирования класса аннотации мета-аннотациями:

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
ли аннотация храниться в скомпилированном классе и будет ли видна через рефлексию (по умолчанию оба утверждения верны);
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
при объявлении конструктора и вставить аннотацию перед ним:

```kotlin
class Foo @Inject constructor(dependency: MyDependency) { ... }
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
Если вам нужно указать класс как аргумент аннотации, используйте Kotlin-класс
([KClass](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/index.html)). Компилятор Kotlin
автоматически сконвертирует его в Java-класс, так что код на Java сможет видеть аннотации и их аргументы.

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
В Java тип аннотации — это форма интерфейса, поэтому вы можете реализовать его и использовать экземпляр. В качестве
альтернативы этому механизму Kotlin позволяет вызывать конструктор класса аннотации в произвольном коде и аналогичным
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

<!-- Learn more about instantiation of annotation classes in [this KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/annotation-instantiation.md). -->
Узнайте больше о создании экземпляров классов аннотаций в
[этом KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/annotation-instantiation.md).

<a name="lambdas"></a>

<!-- ## Lambdas -->
## Лямбды

<!-- Annotations can also be used on lambdas. They will be applied to the `invoke()` method into which the body
of the lambda is generated. This is useful for frameworks like [Quasar](https://docs.paralleluniverse.co/quasar/),
which uses annotations for concurrency control. -->
Аннотации также можно использовать с лямбдами. Они будут применены к методу `invoke()`, в который генерируется тело
лямбды. Это полезно для фреймворков вроде [Quasar](https://docs.paralleluniverse.co/quasar/), которые используют
аннотации для контроля многопоточности.

```kotlin
annotation class Suspendable

val f = @Suspendable { Fiber.sleep(10) }
```

<a name="annotation-use-site-targets"></a>

<!-- ## Annotation use-site targets -->
## Аннотации с указаниями

<!-- When you're annotating a property or a primary constructor parameter, there are multiple Java elements that are
generated from the corresponding Kotlin element, and therefore multiple possible locations for the annotation in
the generated Java bytecode. To specify how exactly the annotation should be generated, use the following syntax: -->
Когда вы помечаете аннотацией свойство или параметр первичного конструктора, из соответствующего Kotlin-элемента
генерируются несколько Java-элементов, и поэтому в сгенерированном байт-коде для аннотации есть несколько возможных
мест. Чтобы указать, как именно должна быть сгенерирована аннотация, используйте следующий синтаксис:

```kotlin
class Example(@field:Ann val foo,    // аннотирует только Java-поле
              @get:Ann val bar,      // аннотирует только Java-геттер
              @param:Ann val quux)   // аннотирует только параметр Java-конструктора
```

<!-- The same syntax can be used to annotate the entire file. To do this, put an annotation with the target `file` at
the top level of a file, before the package directive or before all imports if the file is in the default package: -->
Тот же синтаксис может быть использован для аннотации целого файла. Для этого поместите аннотацию с указанием `file` на
верхнем уровне файла: перед указанием пакета или перед всеми импортами, если файл находится в пакете по умолчанию.

```kotlin
@file:JvmName("Foo")

package org.jetbrains.demo
```

<!-- If you have multiple annotations with the same target, you can avoid repeating the target by adding brackets after the
target and putting all the annotations inside the brackets (except for the `all` meta-target): -->
Если у вас есть несколько аннотаций с одним указанием, вы можете не повторять его: добавьте квадратные скобки после
указания и поместите все аннотации внутрь скобок (кроме мета-указания `all`).

```kotlin
class Example {
     @set:[Inject VisibleForTesting]
     var collaborator: Collaborator
}
```

<!-- The full list of supported use-site targets is: -->
Полный список поддерживаемых указаний:

<!--   * `file`
  * `field`
  * `property` (annotations with this target are not visible to Java)
  * `get` (property getter)
  * `set` (property setter)
  * `all` (an experimental meta-target for properties, see [below](#all-meta-target) for its purpose and usage)
  * `receiver` (receiver parameter of an extension function or property)

    To annotate the receiver parameter of an extension function, use the following syntax:

    ```kotlin
    fun @receiver:Fancy String.myExtension() { ... }
    ```

  * `param` (constructor parameter)
  * `setparam` (property setter parameter)
  * `delegate` (the field storing the delegate instance for a delegated property) -->

* `file`
* `field`
* `property` (такие аннотации не будут видны в Java)
* `get` (геттер свойства)
* `set` (сеттер свойства)
* `all` (экспериментальное мета-указание для свойств; назначение и использование описаны [ниже](#all-meta-target))
* `receiver` (параметр-приёмник [функции или свойства расширения](extensions.html))

    Чтобы пометить аннотацией параметр-приёмник функции расширения, используйте следующий синтаксис:

    ```kotlin
    fun @receiver:Fancy String.myExtension() { ... }
    ```

* `param` (параметр конструктора)
* `setparam` (параметр сеттера)
* `delegate` (поле, которое хранит экземпляр делегата для [делегированного свойства](delegated-properties.html))

<a name="defaults-when-no-use-site-targets-are-specified"></a>

<!-- ### Defaults when no use-site targets are specified -->
### Значения по умолчанию, когда указания не заданы

<!-- If you don't specify a use-site target, the target is chosen according to the `@Target` annotation of the annotation
being used.
If there are multiple applicable targets, the first applicable target from the following list is used: -->
Если вы не сделали указание, оно будет выбрано в соответствии с аннотацией `@Target` той аннотации, которую вы
используете. Если существует несколько применимых целей, будет выбрана первая подходящая цель из следующего списка:

* `param`
* `property`
* `field`

<!-- Let's use the [`@Email` annotation from Jakarta Bean Validation](https://jakarta.ee/specifications/bean-validation/3.0/apidocs/jakarta/validation/constraints/email): -->
Возьмём аннотацию
[`@Email` из Jakarta Bean Validation](https://jakarta.ee/specifications/bean-validation/3.0/apidocs/jakarta/validation/constraints/email):

```java
@Target(value={METHOD,FIELD,ANNOTATION_TYPE,CONSTRUCTOR,PARAMETER,TYPE_USE})
public @interface Email { }
```

<!-- With this annotation, consider the following example: -->
Рассмотрим следующий пример с этой аннотацией:

```kotlin
data class User(val username: String,
                // @Email эквивалентна @param:Email
                @Email val email: String) {
    // @Email эквивалентна @field:Email
    @Email val secondaryEmail: String? = null
}
```

<!-- Kotlin 2.2.0 introduced an experimental defaulting rule which should
make propagating annotations to parameters, fields, and properties more predictable. -->
В Kotlin 2.2.0 появилось экспериментальное правило выбора значения по умолчанию, которое должно сделать
распространение аннотаций на параметры, поля и свойства более предсказуемым.

<!-- With the new rule, if there are multiple applicable targets, one or more is chosen as follows: -->
С новым правилом, если существует несколько применимых целей, выбирается одна или несколько целей следующим образом:

<!-- * If the constructor parameter target (`param`) is applicable, it is used.
* If the property target (`property`) is applicable, it is used.
* If the field target (`field`) is applicable while `property` isn't, `field` is used. -->

* если применима цель параметра конструктора (`param`), она используется;
* если применима цель свойства (`property`), она используется;
* если применима цель поля (`field`), а `property` — нет, используется `field`.

<!-- Using the same example: -->
Для того же примера:

```kotlin
data class User(val username: String,
                // @Email теперь эквивалентна @param:Email @field:Email
                @Email val email: String) {
    // @Email по-прежнему эквивалентна @field:Email
    @Email val secondaryEmail: String? = null
}
```

<!-- If there are multiple targets, and none of `param`, `property`, or `field` are applicable, the annotation is invalid. -->
Если целей несколько и ни одна из `param`, `property` или `field` не применима, аннотация недопустима.

<!-- To enable the new defaulting rule, use the following line in your Gradle configuration: -->
Чтобы включить новое правило выбора значения по умолчанию, используйте следующую строку в конфигурации Gradle:

```kotlin
// build.gradle.kts
kotlin {
    compilerOptions {
        freeCompilerArgs.add("-Xannotation-default-target=param-property")
    }
}
```

<!-- Whenever you'd like to use the old behavior, you can: -->
Если вам нужно использовать старое поведение, вы можете:

<!-- * In a specific case, specify the necessary target explicitly, for example, using `@param:Annotation` instead of `@Annotation`.
* For a whole project, use this flag in your Gradle build file: -->

* в конкретном случае явно указать нужную цель, например использовать `@param:Annotation` вместо `@Annotation`;
* для всего проекта использовать этот флаг в файле сборки Gradle:

    ```kotlin
    // build.gradle.kts
    kotlin {
        compilerOptions {
            freeCompilerArgs.add("-Xannotation-default-target=first-only")
        }
    }
    ```

<a name="all-meta-target"></a>

<!-- ### `all` meta-target -->
### Мета-указание `all`

<!-- The `all` target makes it easier to apply the same annotation not only to the parameter and the property or field, but also to the corresponding getter and setter. -->
Указание `all` упрощает применение одной и той же аннотации не только к параметру и свойству или полю, но и к
соответствующему геттеру и сеттеру.

<!-- Specifically, the annotation marked with `all` is propagated, if applicable: -->
В частности, аннотация с `all` распространяется, если это применимо:

<!-- * To the constructor parameter (`param`) if the property is defined in the primary constructor.
* To the property itself (`property`).
* To the backing field (`field`) if the property has one.
* To the getter (`get`).
* To the setter parameter (`setparam`) if the property is defined as `var`.
* To the Java-only target `RECORD_COMPONENT` if the class has the `@JvmRecord` annotation. -->

* на параметр конструктора (`param`), если свойство определено в первичном конструкторе;
* на само свойство (`property`);
* на поле с резервным хранением (`field`), если у свойства оно есть;
* на геттер (`get`);
* на параметр сеттера (`setparam`), если свойство определено как `var`;
* на Java-only цель `RECORD_COMPONENT`, если у класса есть аннотация `@JvmRecord`.

<!-- Let's use the [`@Email` annotation from Jakarta Bean Validation](https://jakarta.ee/specifications/bean-validation/3.0/apidocs/jakarta/validation/constraints/email),
which is defined as follows: -->
Возьмём аннотацию
[`@Email` из Jakarta Bean Validation](https://jakarta.ee/specifications/bean-validation/3.0/apidocs/jakarta/validation/constraints/email),
которая определена следующим образом:

```java
@Target(value={METHOD,FIELD,ANNOTATION_TYPE,CONSTRUCTOR,PARAMETER,TYPE_USE})
public @interface Email { }
```

<!-- In the example below, this `@Email` annotation is applied to all relevant targets: -->
В примере ниже аннотация `@Email` применяется ко всем релевантным целям:

```kotlin
data class User(
    val username: String,
    // Применяет @Email к param, field и get
    @all:Email val email: String,
    // Применяет @Email к param, field, get и setparam
    @all:Email var name: String,
) {
    // Применяет @Email к field и getter (не к param, потому что свойство не в конструкторе)
    @all:Email val secondaryEmail: String? = null
}
```

<!-- You can use the `all` meta-target with any property, both inside and outside the primary constructor. -->
Мета-указание `all` можно использовать с любым свойством как внутри первичного конструктора, так и вне его.

<a name="limitations"></a>

<!-- #### Limitations -->
#### Ограничения

<!-- The `all` target comes with some limitations: -->
У указания `all` есть несколько ограничений:

<!-- * It does not propagate an annotation to types, potential extension receivers, or context receivers or parameters.
* It cannot be used with multiple annotations:
    ```kotlin
    @all:[A B] // forbidden, use @all:A @all:B
    val x: Int = 5
    ```
* It cannot be used with [delegated properties](delegated-properties.html). -->

* оно не распространяет аннотацию на типы, возможные приёмники расширений, контекстные приёмники или параметры;
* его нельзя использовать с несколькими аннотациями:

    ```kotlin
    @all:[A B] // запрещено, используйте @all:A @all:B
    val x: Int = 5
    ```

* его нельзя использовать с [делегированными свойствами](delegated-properties.html).

<a name="how-to-enable"></a>

<!-- #### How to enable -->
#### Как включить

<!-- To enable the `all` meta-target in your project, use the following compiler option in the command line: -->
Чтобы включить мета-указание `all` в вашем проекте, используйте следующую опцию компилятора в командной строке:

```Bash
-Xannotation-target-all
```

<!-- Or add it to the `compilerOptions {}` block of your Gradle build file: -->
Или добавьте её в блок `compilerOptions {}` файла сборки Gradle:

```kotlin
// build.gradle.kts
kotlin {
    compilerOptions {
        freeCompilerArgs.add("-Xannotation-target-all")
    }
}
```

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
    // применение аннотации @Rule к геттеру свойства
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

<!-- For other arguments that have an array type, you need to use the array literal syntax or
`arrayOf(...)`: -->
Для прочих аргументов, которые являются массивом, нужно использовать синтаксис литерала массива или `arrayOf(...)`.

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

<a name="ability-to-not-generate-jvm-1-8-annotation-targets"></a>

<!-- ### Ability to not generate JVM 1.8+ annotation targets -->
### Возможность не генерировать цели аннотаций JVM 1.8+

<!-- If a Kotlin annotation has `TYPE` among its Kotlin targets, the annotation maps to `java.lang.annotation.ElementType.TYPE_USE`
in its list of Java annotation targets. This is just like how the `TYPE_PARAMETER` Kotlin target maps to
the `java.lang.annotation.ElementType.TYPE_PARAMETER` Java target. This is an issue for Android clients with API levels
less than 26, which don't have these targets in the API. -->
Если среди Kotlin-целей аннотации Kotlin есть `TYPE`, аннотация сопоставляется с
`java.lang.annotation.ElementType.TYPE_USE` в списке её Java-целей. Аналогично Kotlin-цель `TYPE_PARAMETER`
сопоставляется с Java-целью `java.lang.annotation.ElementType.TYPE_PARAMETER`. Это проблема для Android-клиентов с API
level ниже 26, где этих целей нет в API.

<!-- To avoid generating the `TYPE_USE` and `TYPE_PARAMETER` annotation targets, use the new compiler argument `-Xno-new-java-annotation-targets`. -->
Чтобы не генерировать цели аннотаций `TYPE_USE` и `TYPE_PARAMETER`, используйте новый аргумент компилятора
`-Xno-new-java-annotation-targets`.

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
отметьте её объявление мета-аннотацией
[`@kotlin.annotation.Repeatable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-repeatable/). Это
сделает её повторяемой как в Kotlin, так и в Java. Повторяющиеся аннотации Java также поддерживаются со стороны Kotlin.

<!-- The main difference with the scheme used in Java is the absence of a _containing annotation_, which the Kotlin compiler
generates automatically with a predefined name. For an annotation in the example below, it will generate the containing
annotation `@Tag.Container`: -->
Основным отличием от схемы, используемой в Java, является отсутствие *содержащей аннотации*, которую компилятор Kotlin
генерирует автоматически с предопределённым именем. Для аннотации в приведённом ниже примере будет сгенерирована
содержащая аннотация `@Tag.Container`.

```kotlin
@Repeatable
annotation class Tag(val name: String)

// Компилятор генерирует содержащую аннотацию @Tag.Container
```

<!-- You can set a custom name for a containing annotation by applying the
[`@kotlin.jvm.JvmRepeatable`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.jvm/-jvm-repeatable/) meta-annotation
and passing an explicitly declared containing annotation class as an argument: -->
Вы можете задать имя для содержащей аннотации, применив мета-аннотацию
[`@kotlin.jvm.JvmRepeatable`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.jvm/-jvm-repeatable/) и передав явно
объявленный класс содержащей аннотации в качестве аргумента.

```kotlin
@JvmRepeatable(Tags::class)
annotation class Tag(val name: String)

annotation class Tags(val value: Array<Tag>)
```

<!-- To extract Kotlin or Java repeatable annotations via reflection, use the [`KAnnotatedElement.findAnnotations()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect.full/find-annotations.html)
function. -->
Чтобы извлечь повторяющиеся аннотации Kotlin или Java с помощью рефлексии, используйте функцию
[`KAnnotatedElement.findAnnotations()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect.full/find-annotations.html).

<!-- Learn more about Kotlin repeatable annotations in [this KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/repeatable-annotations.md). -->
Узнайте больше о повторяющихся аннотациях Kotlin в
[этом KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/repeatable-annotations.md).
