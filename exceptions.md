---
type: doc
layout: reference
category: "Syntax"
title: "Исключения"
url: https://kotlinlang.ru/docs/exceptions.html
---

<!-- При переводе статьи оригинальная версия была от 05 March 2022 -->

<!-- # Exceptions -->
# Исключения

<a name="exception-classes"></a>

<!-- ## Exception classes -->
## Классы исключений

<!-- All exception classes in Kotlin inherit the `Throwable` class.
Every exception has a message, a stack trace, and an optional cause. -->
Все исключения в Kotlin являются наследниками класса `Throwable`.
У каждого исключения есть сообщение, трассировка стека и (опционально) причина, по которой
это исключение вероятно было вызвано.

<!-- To throw an exception object, use the `throw` expression: -->
Для того чтобы возбудить исключение явным образом, используйте оператор `throw`.

```kotlin
throw Exception("Hi There!")
```

<!-- To catch an exception, use the `try`...`catch` expression: -->
Чтобы перехватить исключение, используйте выражение `try`...`catch`.

```kotlin
try {
    // some code
} catch (e: SomeException) {
    // handler
} finally {
    // optional finally block
}
```

<!-- There may be zero or more `catch` blocks, and the `finally` block may be omitted.
However, at least one `catch` or `finally` block is required. -->
В коде может быть любое количество блоков `catch` (такие блоки могут и вовсе отсутствовать). Блоки `finally`
могут быть опущены. Однако, должен быть использован как минимум один блок `catch` или `finally`.

<a name="try-is-an-expression"></a>

<!-- ### Try is an expression -->
### Try - это выражение

<!-- `try` is an expression, which means it can have a return value: -->

`try` является выражением, что означает, что оно может иметь возвращаемое значение.

```kotlin
val a: Int? = try { input.toInt() } catch (e: NumberFormatException) { null }
```

<!-- The returned value of a `try` expression is either the last expression in the `try` block or the
last expression in the `catch` block (or blocks).
The contents of the `finally` block don't affect the result of the expression. -->
Возвращаемым значением будет либо последнее выражение в блоке `try`, либо последнее выражение
в блоке `catch` (или блоках). Содержимое `finally` блока никак не повлияет на результат `try`-выражения.

<a name="checked-exceptions"></a>

<!-- ## Checked exceptions -->
## Проверяемые исключения

<!-- Kotlin does not have checked exceptions. There are many reasons for this, but we will provide a simple example that illustrates why it is the case. -->
В Kotlin нет проверяемых исключений. Для этого существует целый ряд причин, но мы рассмотрим простой пример, который иллюстрирует причину этого.

<!-- The following is an example interface from the JDK implemented by the `StringBuilder` class: -->
Приведённый ниже фрагмент кода является примером простого интерфейса в JDK, который реализован в классе `StringBuilder`.

```java
Appendable append(CharSequence csq) throws IOException;
```

<!-- This signature says that every time I append a string to something (a `StringBuilder`, some kind of a log, a console, etc.),
I have to catch the `IOExceptions`. Why? Because the implementation might be performing IO operations (`Writer` also implements `Appendable`).
The result is code like this all over the place: -->
Сигнатура говорит, что каждый раз, когда я присоединяю строку к чему-то (к `StringBuilder`, какому-нибудь логу, сообщению в консоль и т.п),
мне необходимо отлавливать исключения типа `IOExceptions`. Почему? Потому, что данная операция может вызывать IO (Input-Output: Ввод-Вывод) (`Writer` также
реализует интерфейс `Appendable`).
Данный факт постоянно приводит к написанию подобного кода:

```kotlin
try {
    log.append(message)
} catch (IOException e) {
    // Должно быть безопасно
}
```

<!-- And that’s not good. Just take a look at [Effective Java, 3rd Edition](https://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 77: *Don't ignore exceptions*. -->
И это плохо. См. [Effective Java](https://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 77: *Don't ignore exceptions* (не игнорируйте исключения).

<!-- Bruce Eckel says this about checked exceptions: -->

Брюс Эккель как-то сказал о проверяемых исключения:

<!-- > Examination of small programs leads to the conclusion that requiring exception specifications
>could both enhance developer productivity and enhance code quality, but experience with large software projects suggests
>a different result – decreased productivity and little or no increase in code quality. -->

> Анализ небольших программ показал, что обязательная обработка исключений может повысить производительность разработчика и улучшить качество кода.
> Однако, изучение крупных проектов по разработке программного обеспечения позволяет сделать противоположный вывод:
> происходит понижение продуктивности и сравнительно небольшое улучшение кода (а иногда и без всякого улучшения).

<!-- And here are some additional thoughts on the matter: -->
Вот несколько других рассуждений по этому поводу:

* [Java's checked exceptions were a mistake](https://radio-weblogs.com/0122027/stories/2003/04/01/JavasCheckedExceptionsWereAMistake.html) (Rod Waldhoff)
* [The Trouble with Checked Exceptions](https://www.artima.com/intv/handcuffs.html) (Anders Hejlsberg)

<!-- If you want to alert callers about possible exceptions when calling Kotlin code from Java, Swift, or Objective-C,
you can use the `@Throws` annotation. Read more about using this annotation [for Java](java-to-kotlin-interop.md#checked-exceptions)
and [for Swift and Objective-C](native-objc-interop.md#errors-and-exceptions). -->
Если вы хотите предупредить вызывающие объекты о возможных исключениях при вызове Kotlin кода из Java, Swift или Objective-C,
вы можете использовать аннотацию `@Throws`. Узнайте больше об использовании этой аннотации [для Java](java-to-kotlin-interop.md#checked-exceptions),
а также [для Swift и Objective-C](native-objc-interop.md#errors-and-exceptions).

<a name="the-nothing-type"></a>

<!-- ## The Nothing type -->
## Тип Nothing

<!-- `throw` is an expression in Kotlin, so you can use it, for example, as part of an Elvis expression: -->
`throw` в Kotlin является выражением, поэтому есть возможность использовать его, например, в качестве части Elvis-выражения:

```kotlin
val s = person.name ?: throw IllegalArgumentException("Name required")
```

<!-- The `throw` expression has the type `Nothing`.
This type has no values and is used to mark code locations that can never be reached.
In your own code, you can use `Nothing` to mark a function that never returns: -->
Типом выражения `throw` является специальный тип под названием `Nothing`.
У этого типа нет никаких значений, он используется для того, чтобы обозначить те участки кода, которые могут быть не достигнуты никогда.
В своём коде вы можете использовать `Nothing` для того, чтобы отметить функцию, чей результат никогда не будет возвращён.

```kotlin
fun fail(message: String): Nothing {
    throw IllegalArgumentException(message)
}
```

<!-- When you call this function, the compiler will know that the execution doesn't continue beyond the call: -->
При вызове такой функции компилятор будет в курсе, что исполнения кода далее не последует:

```kotlin
val s = person.name ?: fail("Name required")
println(s) // известно, что переменная 's' проинициализирована к этому моменту
```

<!-- You may also encounter this type when dealing with type inference. The nullable variant of this type,
`Nothing?`, has exactly one possible value, which is `null`. If you use `null` to initialize
a value of an inferred type and there's no other information that can be used to determine a more
specific type, the compiler will infer the `Nothing?` type: -->
Вы также можете столкнуться с этим типом при работе с выводом типов.
Nullable-вариант этого типа `Nothing?` имеет ровно одно возможное значение - `null`.
Если вы используете `null` для инициализации значения предполагаемого типа, и нет никакой другой информации,
которую можно использовать для определения более конкретного типа, компилятор определит тип `Nothing?`.

```kotlin
val x = null           // у 'x' тип `Nothing?`
val l = listOf(null)   // у 'l' тип `List<Nothing?>
```

<a name="java-interoperability"></a>

<!-- ## Java interoperability -->
## Совместимость с Java

<!-- Please see the section on exceptions in the [Java interoperability page](java-interop.md) for information about Java interoperability. -->

См. раздел, посвящённый исключениям, [Страница совместимости Java](java-interop.html) для получения информации о совместимости с Java.
