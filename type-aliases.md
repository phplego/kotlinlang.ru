---
type: doc
layout: reference
category: "Other"
title: "Псевдонимы типов"
url: https://kotlinlang.ru/docs/reference/type-aliases.html
---

<!--## Type aliases-->
## Псевдонимы типов

_Примечание: Псевдонимы типов доступны в Kotlin начиная с версии 1.1_

<!--Type aliases provide alternative names for existing types.
If the type name is too long you can introduce a different shorter name and use the new one instead.-->
Псевдонимы типов предоставляют альтернативные имена для существующих типов.
Если имя типа слишком длинное, вы можете ввести другое, более короткое имя, и использовать его вместо первоначального.
 
<!--It's useful to shorten long generic types.
For instance, it's often tempting to shrink collection types:-->
Псевдонимы типов полезны, когда вы хотите сократить длинные имена типов, содержащих обобщения. 
К примеру, можно упрощать названия типов коллекций:

```kotlin
typealias NodeSet = Set<Network.Node>

typealias FileTable<K> = MutableMap<K, MutableList<File>>
```

<!--You can provide different aliases for function types:-->
Вы также можете объявить псевдонимы для функциональных типов:

```kotlin
typealias MyHandler = (Int, String, Any) -> Unit

typealias Predicate<T> = (T) -> Boolean
```

<!--You can have new names for inner and nested classes:-->
Вы можете ввести новые имена для внутренних или вложенных классов:

```kotlin
class A {
    inner class Inner
}
class B {
    inner class Inner
}

typealias AInner = A.Inner
typealias BInner = B.Inner
```

<!--Type aliases do not introduce new types. 
They are equivalent to the corresponding underlying types.
When you add `typealias Predicate<T>` and use `Predicate<Int>` in your code, the Kotlin compiler always expand it to `(Int) -> Boolean`. 
Thus you can pass a variable of your type whenever a general function type is required and vice versa:-->
Псевдонимы типов не вводят новых типов. Они эквивалентны соответствующим базовым типам. Когда вы добавляете `typealias Predicate<T>` и используете `Predicate<Int>` в своём коде, компилятор Kotlin всегда преобразовывает это в `(Int) -> Boolean`.
Таким образом, вы можете передать переменную своего типа в любое место, где требуется базовый тип (тот, которому был задан псевдоним), и наоборот:

```kotlin
typealias Predicate<T> = (T) -> Boolean

fun foo(p: Predicate<Int>) = p(42)

fun main(args: Array<String>) {
    val f: (Int) -> Boolean = { it > 0 }
    println(foo(f)) // выведет "true"

    val p: Predicate<Int> = { it > 0 }
    println(listOf(1, -2).filter(p)) // выведет "[1]"
}
```
