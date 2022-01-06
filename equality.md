---
type: doc
layout: reference
category: "Other"
title: "Равенство"
url: https://kotlinlang.ru/docs/equality.html
---

<!-- При переводе статьи оригинальная версия была от 25 October 2021 -->

<!-- # Equality -->
# Равенство

<!-- In Kotlin there are two types of equality: -->
В Kotlin есть два типа равенства:

<!-- * _Structural_ equality (`==` - a check for `equals()`)
* _Referential_ equality (`===` - two references point to the same object) -->

* Равенство *структур* (`==` - проверка через `equals()`)
* Равенство *ссылок* (`===` - две ссылки указывают на один и тот же объект)

<a name="structural-equality"></a>

<!-- ## Structural equality -->
## Равенство структур

<!-- Structural equality is checked by the `==` operation and its negated counterpart `!=`.
By convention, an expression like `a == b` is translated to: -->
Структурное равенство проверяется оператором `==`  и его отрицанием `!=`. По соглашению выражение `a == b`
транслируется в:

```kotlin
a?.equals(b) ?: (b === null)
```

<!--If `a` is not `null`, it calls the `equals(Any?)` function, otherwise (`a` is `null`) it checks that `b`
is referentially equal to `null`.-->
Если `a` не `null`, вызывается функция `equals(Any?)`, иначе (т.е. если `a` указывает на `null`) `b` ссылочно
сравнивается с `null`.

<!-- Note that there's no point in optimizing your code when comparing to `null` explicitly:
`a == null` will be automatically translated to `a === null`. -->
Заметьте, что в явном сравнении с `null` для оптимизации нет смысла: `a == null` будет автоматически транслироваться в
`a === null`.

<!-- To provide a custom equals check implementation, override the
[`equals(other: Any?): Boolean`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/equals.html) function.
Functions with the same name and other signatures, like `equals(other: Foo)`, don't affect equality checks with
the operators `==` and `!=`. -->
Чтобы обеспечить вашу реализацию проверки равенства, переопределите функцию
[`equals(other: Any?): Boolean`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/equals.html). Функции с тем же
именем и другими сигнатурами, например `equals(other: Foo)`, не влияют на проверку равенства с помощью операторов `==` и
`!=`.

<!-- Structural equality has nothing to do with comparison defined by the `Comparable<...>` interface, so only a custom 
`equals(Any?)` implementation may affect the behavior of the operator. -->
Структурное равенство не имеет ничего общего со сравнением, определяемым интерфейсом `Comparable<...>`, поэтому только
пользовательская реализация `equals(Any?)` может повлиять на поведение оператора.

<a name="referential-equality"></a>

<!-- ## Referential equality -->
## Равенство ссылок

<!-- Referential equality is checked by the `===` operation and its negated counterpart `!==`. `a === b` evaluates to
true if and only if `a` and `b` point to the same object. For values represented by primitive types at runtime
(for example, `Int`), the `===` equality check is equivalent to the `==` check. -->
Равенство ссылок проверяется с помощью оператора `===` и его отрицания `!==`. Выражение `a === b` является истиной тогда
и только тогда, когда `a` и `b` указывают на один и тот же объект. Для значений, представленных примитивными типами во
время выполнения (например, `Int`), проверка равенства `===` эквивалентна проверке `==`.

<a name="floating-point-numbers-equality"></a>

<!-- ## Floating-point numbers equality -->
## Равенство чисел с плавающей точкой

<!-- When an equality check operands are statically known to be `Float` or `Double` (nullable or not), the check follows the
[IEEE 754 Standard for Floating-Point Arithmetic](https://en.wikipedia.org/wiki/IEEE_754). -->
Когда статически известно, что операнды проверки равенства являются `Float` или `Double` (nullable или нет), проверка
выполняется в соответствии со [стандартом IEEE 754 для арифметики с плавающей запятой](https://en.wikipedia.org/wiki/IEEE_754).

<!-- Otherwise, the structural equality is used, which disagrees with the standard so that `NaN` is equal to itself, and `-0.0` is not equal to `0.0`. -->
В противном случае используется структурное равенство, которое противоречит стандарту, так что `NaN` равно самому себе,
а `-0.0` не равно `0.0`.

<!-- See: [Floating-point numbers comparison](basic-types.md#floating-point-numbers-comparison). -->
См.: [Равенство чисел с плавающей точкой](basic-types.html#floating-point-numbers-comparison).
