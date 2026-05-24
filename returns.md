---
type: doc
layout: reference
category: "Syntax"
title: "Операторы перехода"
url: https://kotlinlang.ru/docs/returns.html
---

<!-- При переводе статьи оригинальная версия была от 25 June 2025 -->

<!-- # Returns and jumps -->
# Операторы перехода

<!-- Kotlin has three structural jump expressions: -->
В Kotlin определено три структурных оператора перехода:

<!-- * `return` by default returns from the nearest enclosing function or [anonymous function](lambdas.md#anonymous-functions).
* `break` terminates the nearest enclosing loop.
* `continue` proceeds to the next step of the nearest enclosing loop. -->
* `return` по умолчанию производит возврат из ближайшей содержащей его функции или [анонимной функции](lambdas.html#anonymous-functions);
* `break` завершает выполнение ближайшего окружающего его цикла;
* `continue` продолжает выполнение цикла со следующего его шага, без обработки оставшегося кода текущей итерации.

<!-- All of these expressions can be used as part of larger expressions: -->
Все эти выражения можно использовать как часть более крупных выражений:

```kotlin
val s = person.name ?: return
```

<!-- The type of these expressions is the [Nothing type](exceptions.md#the-nothing-type). -->
Эти выражения имеют [тип Nothing](exceptions.html#the-nothing-type).

<a name="break-and-continue-labels"></a>
<!-- ## Break and continue labels -->
## Метки операторов break и continue

<!-- Any expression in Kotlin may be marked with a _label_.
Labels have the form of an identifier followed by the `@` sign, such as `abc@` or `fooBar@`.
To label an expression, just add a label in front of it. -->
Любое выражение в Kotlin может быть помечено меткой *label*.
Метки имеют форму идентификатора, за которым следует знак `@`, например `abc@` или `fooBar@`.
Для того чтобы пометить выражение, мы просто ставим метку перед ним.

```kotlin
loop@ for (i in 1..100) {
    // ...
}
```

<!-- Now, you can qualify a `break` or a `continue` with a label: -->
Теперь можно уточнить операторы `break` или `continue` с помощью меток:

```kotlin
loop@ for (i in 1..100) {
    for (j in 1..100) {
        if (...) break@loop
    }
}
```

<!-- A `break` qualified with a label jumps to the execution point right after the loop marked with that label.
A `continue` proceeds to the next iteration of that loop. -->
Оператор `break`, уточненный меткой, переводит выполнение кода в точку сразу после цикла, отмеченного этой меткой.
Оператор `continue` переходит к следующей итерации этого цикла.

<!-- > In some cases, you can apply `break` and `continue` *non-locally* without explicitly defining labels.
> Such non-local usages are valid in lambda expressions used in enclosing [inline functions](inline-functions.md#break-and-continue).
>
{style="note"} -->
> В некоторых случаях операторы `break` и `continue` можно применять *нелокально*, без явного определения меток.
> Такое нелокальное использование допустимо в лямбда-выражениях внутри окружающих [инлайн-функций](inline-functions.html#non-local-jump-expressions).
{:.note}

<a name="return-to-labels"></a>
<!-- ## Return to labels -->
## Возврат к меткам

<!-- In Kotlin, functions can be nested using function literals, local functions, and object expressions.
A qualified `return` allows you to return from an outer function. -->
В Kotlin функции могут быть вложенными: для этого используются литералы функций, локальные функции и выражения объектов.
Квалифицированный `return` позволяет вернуться из внешней функции.

<!-- The most important use case is returning from a lambda expression. To return from a lambda expression,
label it and qualify the `return`: -->
Самый важный сценарий - возврат из лямбда-выражения. Чтобы вернуться из лямбда-выражения, пометьте его меткой и
укажите эту метку у `return`:

```kotlin
//sampleStart
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach lit@{
        if (it == 3) return@lit // локальный возврат к вызывающему коду лямбды - циклу forEach
        print(it)
    }
    print(" выполнено с явной меткой")
}
//sampleEnd

fun main() {
    foo()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- Now, it returns only from the lambda expression. Often it is more convenient to use _implicit labels_, because such a label
has the same name as the function to which the lambda is passed. -->
Теперь он возвращает только из лямбда-выражения. Зачастую намного более удобно использовать *неявные метки*, потому что такие метки имеют такое же имя,
как и функция, к которой относится лямбда.

```kotlin
//sampleStart
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return@forEach // локальный возврат к вызывающему коду лямбды - циклу forEach
        print(it)
    }
    print(" выполнено с неявной меткой")
}
//sampleEnd

fun main() {
    foo()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- Alternatively, you can replace the lambda expression with an [anonymous function](lambdas.md#anonymous-functions).
A `return` statement in an anonymous function will return from the anonymous function itself. -->
В качестве альтернативы лямбда-выражению можно использовать [анонимную функцию](lambdas.html#anonymous-functions).
Оператор `return` возвращает из самой анонимной функции.

```kotlin
//sampleStart
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach(fun(value: Int) {
        if (value == 3) return  // локальный возврат к вызывающему коду анонимной функции - циклу forEach
        print(value)
    })
    print(" выполнено с анонимной функцией")
}
//sampleEnd

fun main() {
    foo()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- Note that the use of local returns in the previous three examples is similar to the use of `continue` in regular loops. -->
Обратите внимание, что использование локальных возвратов в предыдущих трех примерах похоже на использование `continue` в обычных циклах.

<!-- There is no direct equivalent for `break`, but it can be simulated by adding an outer `run` lambda and non-locally
returning from it: -->
Прямого эквивалента для `break` не существует, но его можно смоделировать: добавить внешнюю лямбду `run` и нелокально
вернуться из нее.

```kotlin
//sampleStart
fun foo() {
    run loop@{
        listOf(1, 2, 3, 4, 5).forEach {
            if (it == 3) return@loop // нелокальный возврат из лямбды, переданной в run
            print(it)
        }
    }
    print(" выполнено с вложенным циклом")
}
//sampleEnd

fun main() {
    foo()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

<!-- The non-local return here is possible since the nested `forEach()` lambda acts as an [inline function](inline-functions.md). -->
Нелокальный возврат здесь возможен, потому что вложенный вызов `forEach()` относится к [инлайн-функции](inline-functions.html).

<!-- When returning a value, the parser gives preference to the qualified return: -->
При возвращении значения парсер отдаёт предпочтение квалифицированному `return`:

```kotlin
return@a 1
```

<!-- This means "return `1` at label `@a`" rather than "return a labeled expression `(@a 1)`". -->
Это означает "вернуть `1` по метке `@a`", а не "вернуть помеченное выражение `(@a 1)`".

<!-- > In some cases, you can return from a lambda expression without using labels. Such *non-local* returns are located in a
> lambda but exit the enclosing [inline function](inline-functions.md#returns).
>
{style="note"} -->
> В некоторых случаях из лямбда-выражения можно вернуть значение без меток. Такие *нелокальные* возвраты находятся
> в лямбде, но завершают окружающую [инлайн-функцию](inline-functions.html#non-local-jump-expressions).
{:.note}
