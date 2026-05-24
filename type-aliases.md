---
type: doc
layout: reference
category: "Other"
title: "Псевдонимы типов"
url: https://kotlinlang.ru/docs/type-aliases.html
---

<!-- При переводе статьи оригинальная версия была от 02 December 2025 -->

<!-- # Type aliases -->
# Псевдонимы типов

<!-- Type aliases provide alternative names for existing types.
If the type name is too long you can introduce a different shorter name and use the new one instead. -->
Псевдонимы типов предоставляют альтернативные имена для существующих типов.
Если имя типа слишком длинное, вы можете ввести другое, более короткое имя, и использовать его вместо первоначального.

<!-- It's useful to shorten long generic types.
For instance, it's often tempting to shrink collection types: -->
Псевдонимы типов полезны, когда вы хотите сократить длинные имена типов, содержащих обобщения.
К примеру, можно упрощать названия типов коллекций:

```kotlin
typealias NodeSet = Set<Network.Node>

typealias FileTable<K> = MutableMap<K, MutableList<File>>
```

<!-- You can provide different aliases for function types: -->
Вы также можете объявить псевдонимы для функциональных типов:

```kotlin
typealias MyHandler = (Int, String, Any) -> Unit

typealias Predicate<T> = (T) -> Boolean
```

<!-- You can have new names for inner and nested classes: -->
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

<!-- Type aliases do not introduce new types.
They are equivalent to the corresponding underlying types.
When you add `typealias Predicate<T>` and use `Predicate<Int>` in your code, the Kotlin compiler always expands it to `(Int) -> Boolean`.
Thus you can pass a variable of your type whenever a general function type is required and vice versa: -->
Псевдонимы типов не вводят новых типов. Они эквивалентны соответствующим базовым типам. Когда вы добавляете
`typealias Predicate<T>` и используете `Predicate<Int>` в своём коде, компилятор Kotlin всегда преобразует это в
`(Int) -> Boolean`. Таким образом, вы можете передать переменную своего типа туда, где требуется обычный
функциональный тип, и наоборот:

```kotlin
typealias Predicate<T> = (T) -> Boolean

fun foo(p: Predicate<Int>) = p(42)

fun main() {
    val f: (Int) -> Boolean = { it > 0 }
    println(foo(f)) // выведет "true"

    val p: Predicate<Int> = { it > 0 }
    println(listOf(1, -2).filter(p)) // выведет "[1]"
}
```

<!-- ## Nested type aliases -->
## Вложенные псевдонимы типов

<!-- In Kotlin, you can define type aliases inside other declarations, as long as they
don't capture type parameters from their outer class: -->
В Kotlin вы можете определять псевдонимы типов внутри других объявлений, если они не захватывают параметры типа из
внешнего класса:

```kotlin
class Dijkstra {
    typealias VisitedNodes = Set<Node>

    private fun step(visited: VisitedNodes, ...) = ...
}
```

<!-- Capturing means that the type alias refers to a type parameter defined in the outer class: -->
Захват означает, что псевдоним типа ссылается на параметр типа, определённый во внешнем классе:

```kotlin
class Graph<Node> {
    // Некорректно, потому что захватывает Node
    typealias Path = List<Node>
}
```

<!-- To fix this issue, declare the type parameter directly in the type alias: -->
Чтобы исправить эту проблему, объявите параметр типа непосредственно в псевдониме типа:

```kotlin
class Graph<Node> {
    // Корректно, потому что Node является параметром псевдонима типа
    typealias Path<Node> = List<Node>
}
```

<!-- Nested type aliases allow for cleaner, more maintainable code by improving encapsulation, reducing package-level clutter,
and simplifying internal implementations. -->
Вложенные псевдонимы типов делают код чище и удобнее в сопровождении: они улучшают инкапсуляцию, уменьшают количество
объявлений на уровне пакета и упрощают внутренние реализации.

<!-- ### Rules for nested type aliases -->
### Правила для вложенных псевдонимов типов

<!-- Nested type aliases follow specific rules to ensure clear and consistent behavior: -->
Для вложенных псевдонимов типов действуют специальные правила, которые обеспечивают понятное и согласованное поведение:

<!-- * Nested type aliases must follow all existing type alias rules.
* In terms of visibility, the alias can't expose more than its referenced types allow.
* Their scope is the same as [nested classes](nested-classes.md). You can define them inside classes,
  and they hide any parent type aliases with the same name as they don't override.
* Nested type aliases can be marked as `internal` or `private` to limit their visibility.
* Nested type aliases are not supported in Kotlin Multiplatform's [`expect/actual` declarations](https://kotlinlang.org/docs/multiplatform/multiplatform-expect-actual.html). -->

* Вложенные псевдонимы типов должны соблюдать все существующие правила для псевдонимов типов.
* С точки зрения видимости псевдоним не может открывать больше доступа, чем позволяют типы, на которые он ссылается.
* Их область видимости такая же, как у [вложенных классов](nested-classes.html). Их можно определять внутри классов,
  и они скрывают любые родительские псевдонимы типов с тем же именем, поскольку не переопределяют их.
* Вложенные псевдонимы типов можно помечать как `internal` или `private`, чтобы ограничить их видимость.
* Вложенные псевдонимы типов не поддерживаются в
  [объявлениях `expect/actual`](https://kotlinlang.org/docs/multiplatform/multiplatform-expect-actual.html) Kotlin
  Multiplatform.
