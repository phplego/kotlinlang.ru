---
type: doc
layout: reference
category: "Syntax"
title: "Операторы перехода"
url: https://kotlinlang.ru/docs/returns.html
---

<!-- При переводе статьи оригинальная версия была от 06 October 2021 -->

<!-- # Returns and jumps -->
# Операторы перехода

<!-- Kotlin has three structural jump expressions: -->
В Kotlin определено три оператора перехода:

<!-- * `return` by default returns from the nearest enclosing function or [anonymous function](lambdas.md#anonymous-functions).
* `break` terminates the nearest enclosing loop.
* `continue` proceeds to the next step of the nearest enclosing loop. -->
* `return` по умолчанию производит возврат из ближайшей окружающей его функции или [анонимной функции](lambdas.md#anonymous-functions);
* `break` завершает выполнение ближайшего окружающего его цикла;
* `continue` продолжает выполнение цикла со следующего его шага, без обработки оставшегося кода текущей итерации.

<!-- All of these expressions can be used as part of larger expressions: -->
Все эти выражения можно использовать как часть более крупных выражений:

```kotlin
val s = person.name ?: return
```

<!-- The type of these expressions is the [Nothing type](exceptions.md#the-nothing-type). -->
Эти выражения имеют тип [Nothing](exceptions.md#the-nothing-type).

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

<!-- Now, we can qualify a `break` or a `continue` with a label: -->
Теперь мы можем уточнить значения операторов `break` или `continue` с помощью меток.

```kotlin
loop@ for (i in 1..100) {
    for (j in 1..100) {
        if (...) break@loop
    }
}
```

<!-- A `break` qualified with a label jumps to the execution point right after the loop marked with that label.
A `continue` proceeds to the next iteration of that loop. -->
Оператор `break`, отмеченный `@loop`, переводит выполнение кода в точку сразу после цикла, отмеченного этой меткой.
Оператор `continue` продолжает цикл со следующей его итерации.

<a name="return-to-labels"></a>
<!-- ## Return to labels -->
## Возврат к меткам

<!-- In Kotlin, functions can be nested using function literals, local functions, and object expressions.
Qualified `return`s allow us to return from an outer function.
The most important use case is returning from a lambda expression. Recall that when we write the following,
the `return`-expression returns from the nearest enclosing function - `foo`: -->
В Kotlin функции могут быть вложены друг в друга с помощью литералов функций, локальных функций и анонимных объектов.
Подходящий `return` позволит вернуться из внешней функции.
Одним из самых важных применений этой синтаксической конструкции является возврат из лямбда-выражения.
Напомним, что в таких случаях, как в примере ниже, `return` возвращает из ближайшей заключающей функции - `foo`:

```kotlin
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return // нелокальный возврат, непосредственно к объекту вызывающему функцию foo()
        print(it)
    }
    println("эта строка не достижима")
}
```

<!-- Note that such non-local returns are supported only for lambda expressions passed to [inline functions](inline-functions.md).
To return from a lambda expression, label it and qualify the `return`: -->
Обратите внимание, что такой нелокальный возврат поддерживается только лямбда-выражениями, переданными [инлайн-функциям](inline-functions.html).
Чтобы вернуться из лямбда-выражения, к оператору стоит поставить метку и тем самым сделать уточнение для `return`.

```kotlin
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach lit@{
        if (it == 3) return@lit // локальный возврат внутри лямбды, то есть к циклу forEach
        print(it)
    }
    print(" выполнится с использованием явной метки(lit@)")
}
```

<!-- Now, it returns only from the lambda expression. Often it is more convenient to use _implicit labels_, because such a label
has the same name as the function to which the lambda is passed. -->
Теперь он возвращает только из лямбда-выражения. Зачастую намного более удобно использовать *неявные метки*, потому что такие метки имеют такое же имя,
как и функция, к которой относится лямбда.

```kotlin
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return@forEach // локальный возврат внутри лямбды, то есть к циклу forEach
        print(it)
    }
    print(" выполнится с использованием неявной метки(forEach@)")
}
```

<!-- Alternatively, you can replace the lambda expression with an [anonymous function](lambdas.md#anonymous-functions).
A `return` statement in an anonymous function will return from the anonymous function itself. -->
Возможно также использование [анонимной функции](lambdas.html#anonymous-functions) в качестве альтернативы лямбда-выражениям.
Оператор `return` возвращает из самой анонимной функции.

```kotlin
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach(fun(value: Int) {
        if (value == 3) return  // локальный возврат внутри анонимной функции, то есть к циклу forEach
        print(value)
    })
    print(" выполнится с использованием анонимной функции")
}
```

<!-- Note that the use of local returns in the previous three examples is similar to the use of `continue` in regular loops. -->
Обратите внимание, что использование локальных возвратов в предыдущих трех примерах аналогично использованию `continue` в обычных циклах.

<!-- There is no direct equivalent for `break`, but it can be simulated by adding another nesting lambda and non-locally returning from it: -->
Прямого эквивалента для `break` не существует, но его можно смоделировать - добавить еще одну вложенную лямбду и нелокально вернуться из нее.

```kotlin
fun foo() {
    run loop@{
        listOf(1, 2, 3, 4, 5).forEach {
            if (it == 3) return@loop // нелокальный возврат из лямбды к вызывающему run
            print(it)
        }
    }
    print(" выполнится с использованием вложенной метки")
}
```

<!-- When returning a value, the parser gives preference to the qualified return: -->
При возвращении значения парсер отдаёт предпочтение специализированному возврату.

```kotlin
return@a 1
```

<!-- This means "return `1` at label `@a`" rather than "return a labeled expression `(@a 1)`". -->
что значит "верни `1` в метке `@a`", а не "верни выражение с меткой `(@a 1)`".
