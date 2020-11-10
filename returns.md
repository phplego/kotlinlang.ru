---
type: doc
layout: reference
category: "Syntax"
title: "Операторы перехода"
url: https://kotlinlang.ru/docs/reference/returns.html
---

<!--# Returns and Jumps-->
# Операторы перехода

<!--Kotlin has three structural jump operators-->
В <b>Kotlin</b> определено три оператора перехода:

<!--* **return** By default returns from the nearest enclosing function or [anonymous function](lambdas.html#anonymous-functions).
* **break**. Terminates the nearest enclosing loop.
* **continue**. Proceeds to the next step of the nearest enclosing loop.-->
* **return** по умолчанию производит возврат из ближайшей окружающей его функции или [анонимной функции](lambdas.md#anonymous-functions)
* **break** завершает выполнение цикла
* **continue** продолжает выполнение цикла со следующего его шага, без обработки оставшегося кода текущей итерации


<!--## Break and Continue Labels-->
## Метки операторов **break** и **continue**

<!--Any expression in Kotlin may be marked with a **label**.
Labels have the form of an identifier followed by the `@` sign, for example: `abc@`, `fooBar@` are valid labels (see the [grammar](grammar.html#label)).
To label an expression, we just put a label in front of it-->
Любое выражение в <b>Kotlin</b> может быть помечено меткой **label**.
Метки имеют идентификатор в виде знака `@`. Например: метки `abc@`, `fooBar@` являются корректными
(см. [грамматика](http://kotlinlang.org/docs/reference/grammar.html#label)). Для того, чтобы пометить выражение, мы просто ставим метку перед ним:

```kotlin
loop@ for (i in 1..100) {
  // ...
}
```

<!--Now, we can qualify a **break** or a **continue** with a label:-->
Теперь мы можем уточнить значения операторов **break** или **continue** с помощью меток:

```kotlin
loop@ for (i in 1..100) {
  for (j in 1..100) {
    if (...)
      break@loop
  }
}
```

<!--A **break** qualified with a label jumps to the execution point right after the loop marked with that label.
A **continue** proceeds to the next iteration of that loop.-->
Оператор **break**, отмеченный `@loop`, переводит выполнение кода к той его части, которая находится сразу после соответствующей метки `loop@`.
Оператор **continue** продолжает цикл со следующей его итерации.

<a name="return-at-labels"></a>

<!--## Return at Labels-->
## Возврат к меткам

<!--With function literals, local functions and object expression, functions can be nested in Kotlin. 
Qualified **return**s allow us to return from an outer function. 
The most important use case is returning from a lambda expression. Recall that when we write this:-->
В <b>Kotlin</b> функции могут быть вложены друг в друга с помощью анонимных объектов, локальных функций (ориг.:_local functions_) и _function literals_.
Подходящий **return** позволит вернуться из внешней функции.
Одним из самых удачных применений этой синтаксической конструкции служит возврат из лямбда-выражения. Подумайте над этим утверждением, читая данный пример:

```kotlin
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return // нелокальный возврат, непосредственно к объекту вызывающему функцию foo()
        print(it)
    }
    println("эта строка не достижима")
}
```

<!--The **return** expression returns from the nearest enclosing function, i.e. `foo`.
(Note that such non-local returns are supported only for lambda expressions passed to [inline functions](inline-functions.html).)
If we need to return from a lambda expression, we have to label it and qualify the **return**:-->
Опертор **return** возвращается из ближайшей функции, в нашем случае `foo`.
(Обратите внимание, что такой местный возврат поддерживается только лямбда-выражениями, переданными [инлайн-функциям](inline-functions.html).) Если нам надо вернуться из лямбда-выражения, к оператору стоит поставить метку и тем самым сделать уточнение для ключевого слова **return**:

```kotlin
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach lit@{
        if (it == 3) return@lit // локальный возврат внутри лямбды, то есть к циклу forEach
        print(it)
    }
    print(" выполнится с использованием явной метки(lit@)")
}
```

<!--Now, it returns only from the lambda expression. Oftentimes it is more convenient to use implicits labels:
such a label has the same name as the function to which the lambda is passed.-->
Теперь он возвращается только из лямда-выражения. Зачастую намного более удобно указывать метки неявно: такие метки имеют такое же имя, как и функция, к которой относится лямбда.  

```kotlin
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return@forEach // локальный возврат внутри лямбды, то есть к циклу forEach
        print(it)
    }
    print(" выполнится с использованием неявной метки(forEach@)")
}
```

<!--Alternatively, we can replace the lambda expression with an [anonymous function](lambdas.html#anonymous-functions).
A **return** statement in an anomymous function will return from the anonymous function itself.-->
Возможно также использование [анонимной функции](lambdas.html#anonymous-functions) в качестве альтернативы лямбда-выражениям. Оператор **return** возвращается из самой анонимной функции.

```kotlin
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach(fun(value: Int) {
        if (value == 3) return  // локальный возврат внутри анонимной функци, то есть к циклу forEach
        print(value)
    })
    print(" выполнится с использованием анонимной функции")
}
```
<!--Note that the use of local returns in previous three examples is similar to the use of continue in regular loops. There is no direct equivalent for break, but it can be simulated by adding another nesting lambda and non-locally returning from it:-->
Обратите внимание, что использование локальных возвратов в предыдущих трех примерах аналогично использованию **continue** в обычных циклах. Прямого эквивалента для **break** не существует, но его можно смоделировать - добавить еще одну вложенную лямбду и нелокально вернуться из нее:

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

<!--When returning a value, the parser gives preference to the qualified return, i.e.-->
При возвращении значения парсер отдаёт предпочтение специализированному возврату, типа

```kotlin
return@a 1
```

<!--means "return `1` at label `@a`" and not "return a labeled expression `(@a 1)`".-->
что значит "верни `1` в метке `@a`, а не "верни выражение с меткой `(@a 1)`".
