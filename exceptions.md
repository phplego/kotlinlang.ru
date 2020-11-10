---
type: doc
layout: reference
category: "Syntax"
title: "Исключения"
url: https://kotlinlang.ru/docs/reference/exceptions.html
---

<!-- # Exceptions -->
# Исключения

<!-- ## Exception Classes -->
## Классы исключений

<!-- All exception classes in Kotlin are descendants of the class `Throwable`. -->
<!-- Every exception has a message, stack trace and an optional cause. -->

Все исключения в <b>Kotlin</b> являются наследниками класса `Throwable`.
У каждого исключения есть сообщение, трассировка стека, а также причина, по которой
это исключение вероятно было вызвано.

<!-- To throw an exception object, use the *throw*{: .keyword }-expression -->

Для того, чтобы возбудить исключение явным образом, используйте оператор *throw*

```kotlin
throw MyException("Hi There!")
```

<!-- To catch an exception, use the *try*{: .keyword }-expression -->

Оператор *try* позволяет перехватывать исключения

```kotlin
try {
    // some code
}
catch (e: SomeException) {
    // handler
}
finally {
    // optional finally block
}
```

<!-- There may be zero or more *catch*{: .keyword } blocks. *finally*{: .keyword } blocks may be omitted. -->
<!-- However at least one *catch*{: .keyword } or *finally*{: .keyword } block should be present. -->

В коде может быть любое количество блоков *catch* (такие блоки могут и вовсе отсутствовать). Блоки *finally*
могут быть опущены. Однако, должен быть использован как минимум один блок *catch* или *finally*.

<!-- ### Try is an expression -->

### _Try_ - это выражение

<!-- *try*{: .keyword } is an expression, i.e. it may have a return value. -->

Ключевое слово *try* является выражением, то есть оно может иметь возвращаемое значение.

```kotlin
val a: Int? = try { parseInt(input) } catch (e: NumberFormatException) { null }
```

<!-- The returned value of a *try*{: .keyword }-expression is either the last expression in the *try*{: .keyword } block or the -->
<!-- last expression in the *catch*{: .keyword } block (or blocks). -->
<!-- Contents of the *finally*{: .keyword } block do not affect the result of the expression. -->

Возвращаемым значением будет либо последнее выражение в блоке *try*, либо последнее выражение
 в блоке *catch* (или блоках). Содержимое *finally* блока никак не повлияет на результат *try*-выражения.

<!-- ## Checked Exceptions -->

## Проверяемые исключения

<!-- Kotlin does not have checked exceptions. There are many reasons for this, but we will provide a simple example. -->

В языке <b>Kotlin</b> нет проверяемых исключений. Для этого существует целый ряд причин, но мы рассмотрим простой пример.

<!-- The following is an example interface of the JDK implemented by `StringBuilder` class -->

Приведённый ниже фрагмент кода является примером простого интерфейса в JDK, который реализован в классе `StringBulder`

``` java
Appendable append(CharSequence csq) throws IOException;
```

<!-- What does this signature say? It says that every time I append a string to something (a `StringBuilder`, some kind of a log, a console, etc.) -->
<!-- I have to catch those `IOExceptions`. Why? Because it might be performing IO (`Writer` also implements `Appendable`)... -->
<!-- So it results into this kind of code all over the place: -->

О чём говорит нам сигнатура? О том, что каждый раз, когда я присоединяю строку к чему-то (к `StringBuilder`, какому-нибудь логу, сообщению в консоль и т.п)
, мне необходимо отлавливать исключения типа `IOExceptions`. Почему? Потому, что данная операция может вызывать IO (Input-Output: Ввод-Вывод) (`Writer` также
реализует интерфейс `Appendable`)...
Данный факт постоянно приводит к написанию подобного кода:

```kotlin
try {
    log.append(message)
}
catch (IOException e) {
    // Должно быть безопасно
}
```

<!-- And this is no good, see [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 65: *Don't ignore exceptions*. -->

И это плохо. См. [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 65: *Don't ignore exceptions* (не игнорируйте исключения).

<!-- Bruce Eckel says in [Does Java need Checked Exceptions?](http://www.mindview.net/Etc/Discussions/CheckedExceptions): -->

Брюс Эккель как-то сказал в своей статье "Нужны ли в Java проверяемые исключения?"([Does Java need Checked Exceptions?](http://www.mindview.net/Etc/Discussions/CheckedExceptions)):

<!-- > Examination of small programs leads to the conclusion that requiring exception specifications could both enhance developer productivity and enhance code quality, -->
 <!-- but experience with large software projects suggests a different result – decreased productivity and little or no increase in code quality. -->
 
> Анализ небольших программ показал, что обязательная обработка исключений может повысить производительность разработчика и улучшить качество кода.
Однако, изучение крупных проектов по разработке программного обеспечения позволяет сделать противоположный  вывод: происходит понижение продуктивности и сравнительно небольшое
улучшение кода (а иногда и без всякого улучшения).

<!-- Other citations of this sort: -->

Другие цитаты подобного рода:

* [Java's checked exceptions were a mistake](http://radio-weblogs.com/0122027/stories/2003/04/01/JavasCheckedExceptionsWereAMistake.html) (Rod Waldhoff)
* [The Trouble with Checked Exceptions](http://www.artima.com/intv/handcuffs.html) (Anders Hejlsberg)

<!-- ## The Nothing type -->

## Тип `Nothing`

<!-- `throw` is an expression in Kotlin, so you can use it, for example, as part of an Elvis expression: -->

Вы можете использовать выражение `throw`  в качестве части элвис-выражения:

```kotlin
val s = person.name ?: throw IllegalArgumentException("Name required")
```

<!-- The type of the `throw` expression is the special type `Nothing`. -->
<!-- The type has no values and is used to mark code locations that can never be reached. -->
<!-- In your own code, you can use `Nothing` to mark a function that never returns: -->

Типом выражения `throw` является специальный тип под названием `Nothing`.
У этого типа нет никаких значений, он используется для того, чтобы обозначить те участки кода, которые могут быть не достигнуты никогда.
В своём коде вы можете использовать `Nothing` для того, чтобы отметить функцию, чей результат никогда не будет возвращён:

```kotlin
fun fail(message: String): Nothing {
    throw IllegalArgumentException(message)
}
```

<!-- When you call this function, the compiler will know that the execution doesn't continue beyond the call: -->

При вызове такой функции компилятор будет в курсе, что исполнения кода далее не последует:

```kotlin
val s = person.name ?: fail("Name required")
println(s)     // известно, что переменная 's' проинициализирована к этому моменту
```

<!-- ## Java Interoperability -->

## Совместимость с <b>Java</b>

<!-- Please see the section on exceptions in the [Java Interoperability section](java-interop.html) for information about Java interoperability. -->

См. раздел, посвещённый исключениям, в "Совместимости с Java" [Java Interoperability section](java-interop.html).
