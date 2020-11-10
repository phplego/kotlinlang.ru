---
type: doc
layout: reference
category: "Other"
title: "Равенство"
url: https://kotlinlang.ru/docs/reference/equality.html
---

<!--# Equality-->
# Равенство

<!--In Kotlin there are two types of equality:-->
В Kotlin есть два типа равенства:

<!--* Referential equality (two references point to the same object)
* Structural equality (a check for `equals()`)-->
* Равенство ссылок (две ссылки указывают на один и тот же объект)
* Равенство структур (проверка через `equals()`)

<!--## Referential equality-->
## Равенство ссылок

<!--Referential equality is checked by the `===` operation (and its negated counterpart `!==`). `a === b` evaluates to
true if and only if `a` and `b` point to the same object.-->
Равенство ссылок проверяется с помощью оператора `===` (и его отрицания `!==`). Выражение `a === b` является истиной тогда и только тогда, когда `a` и `b` указывают на один и тот же объект.

<!--## Structural equality-->
## Равенство структур

<!--Structural equality is checked by the `==` operation (and its negated counterpart `!=`). By convention, an expression like `a == b` is translated to -->
Структурное равенство проверяется оператором `==`  (и его отрицанием `!=`). Условно, выражение `a == b` транслируется в:

```kotlin
a?.equals(b) ?: (b === null)
```

<!--I.e. if `a` is not `null`, it calls the `equals(Any?)` function, otherwise (i.e. `a` is `null`) it checks that `b` is referentially equal to `null`.

Note that there's no point in optimizing your code when comparing to `null` explicitly: `a == null` will be automatically translated to `a === null`.-->

Т.е. если `a` не `null`, вызывается функция `equals(Any?)`, иначе (т.е. если `a` указывает на `null`) `b` ссылочно сравнивается с `null`.
Заметьте, что в явном сравнении с `null` для оптимизации нет смысла: `a == null` будет автоматически транслироваться в `a === null`.

