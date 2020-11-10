---
type: doc
layout: reference
category: "Classes and Objects"
title: "Изолированные классы"
url: https://kotlinlang.ru/docs/reference/sealed-classes.html
---

<!--# Sealed Classes-->
# Изолированные классы

<!--Sealed classes are used for representing restricted class hierarchies, when a value can have one of the types from a
limited set, but cannot have any other type. They are, in a sense, an extension of enum classes: the set of values
for an enum type is also restricted, but each enum constant exists only as a single instance, whereas a subclass
of a sealed class can have multiple instances which can contain state.-->

Изолированные классы используются для отражения ограниченных иерархий классов, когда значение может иметь тип только из ограниченного набора, и никакой другой. Они являются, по сути, расширением enum-классов: набор значений enum типа также ограничен, но каждая enum-константа существует только в единственном экземпляре, в то время как наследник изолированного класса может иметь множество экземпляров, которые могут нести в себе какое-то состояние.

<!--To declare a sealed class, you put the `sealed` modifier before the name of the class. A sealed class can have
subclasses, but all of them must be declared in the same file as the sealed class itself. (Before Kotlin 1.1,
the rules were even more strict: classes had to be nested inside the declaration of the sealed class). -->

Чтобы описать изолированный класс, укажите модификатор `sealed` перед именем класса. Изолированный класс может иметь наследников, но все они должны быть объявлены в том же файле, что и сам изолированный класс. (До версии Kotlin 1.1 правила были ещё более строгими: классы должны были быть вложены в объявлении изолированного класса). 

```kotlin
sealed class Expr
data class Const(val number: Double) : Expr()
data class Sum(val e1: Expr, val e2: Expr) : Expr()
object NotANumber : Expr()

fun eval(expr: Expr): Double = when (expr) {
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NaN
}
```

<!--(The example above uses one additional new feature of Kotlin 1.1: the possibility for data classes to extend other
classes, including sealed classes.)-->
(Пример выше использует одну новую возможность Kotlin 1.1: расширение классов, включая изолированные, классами данных)

<!--A sealed class is abstract by itself, it cannot be instantiated directly and can have abstract members.-->
Сам по себе изолированный класс является абстрактным, он не может быть создан напрямую и может иметь абстрактные компоненты.

<!--Sealed classes are not allowed to have non-private constructors (their constructors are private by default).-->
Также конструктор изолированного класса не может быть **не приватным** (их конструкторы приватны по умолчанию).

<!--Note that classes which extend subclasses of a sealed class (indirect inheritors) can be placed anywhere, not necessarily in
the same file.-->
Обратите внимание, что классы, которые расширяют наследников изолированного класса (непрямые наследники) могут быть помещены где угодно, не обязательно в том же файле.

<!--The key benefit of using sealed classes comes into play when you use them in a [`when` expression](control-flow.html#when-expression). If it's possible to verify that the statement covers all cases, you don't need to add an `else` clause to the statement.-->

Ключевое преимущество от использования изолированных классов проявляется тогда, когда вы используете их в [выражении when](control-flow.html#when-expression). Если возможно проверить что выражение покрывает все случаи, то вам не нужно добавлять `else`.

```kotlin
fun eval(expr: Expr): Double = when(expr) {
    is Expr.Const -> expr.number
    is Expr.Sum -> eval(expr.e1) + eval(expr.e2)
    Expr.NotANumber -> Double.NaN
    // оператор `else` не требуется, потому что мы покрыли все возможные случаи
}
```
