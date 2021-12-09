---
type: doc
layout: reference
title: "Основные типы"
url: https://kotlinlang.ru/docs/basic-types.html
---

<!-- При переводе статьи оригинальная версия была от 02 June 2021 -->

<!-- Functional (SAM) interfaces -->
# Функциональные (SAM) интерфейсы

<!-- An interface with only one abstract method is called a _functional interface_, or a _Single Abstract
Method (SAM) interface_. The functional interface can have several non-abstract members but only one abstract member. -->
Интерфейсы только с одним абстрактным методом называются *функциональными интерфейсами* или *Single Abstract
Method (SAM) интерфейсами*. Функциональный интерфейс может иметь несколько неабстрактных членов, но только один абстрактный.

<!-- To declare a functional interface in Kotlin, use the `fun` modifier. -->
Чтобы объявить функциональный интерфейс, используйте модификатор `fun`.

```kotlin
fun interface KRunnable {
   fun invoke()
}
```

<a name="sam-conversions"></a>
<!-- ## SAM conversions -->
## SAM преобразования

<!-- For functional interfaces, you can use SAM conversions that help make your code more concise and readable by using
[lambda expressions](lambdas.md#lambda-expressions-and-anonymous-functions). -->
Для функциональных интерфейсов вы можете использовать SAM преобразования, которые помогают сделать ваш код более лаконичным и читаемым,
используя [лямбда-выражения](lambdas.html#lambda-expressions-and-anonymous-functions).

<!-- Instead of creating a class that implements a functional interface manually, you can use a lambda expression.
With a SAM conversion, Kotlin can convert any lambda expression whose signature matches
the signature of the interface's single method into the code, which dynamically instantiates the interface implementation. -->
Вместо создания класса, который реализует функциональный интерфейс вручную, вы можете использовать лямбда-выражения.
Вместе с SAM преобразованиями Kotlin может преобразовывать лямбда-выражения, сигнатура которых совпадает с
сигнатурой единственного метода интерфейса, в код, который динамически создает экземпляр реализации интерфейса.

<!-- For example, consider the following Kotlin functional interface: -->
Например, рассмотрим следующий функциональный интерфейс Kotlin:

```kotlin
fun interface IntPredicate {
   fun accept(i: Int): Boolean
}
```

<!-- If you don't use a SAM conversion, you will need to write code like this: -->
Если вы не используете SAM преобразования, вам будет нужно написать следующий код:

```kotlin
// Создание экземпляра класса
val isEven = object : IntPredicate {
   override fun accept(i: Int): Boolean {
       return i % 2 == 0
   }
}
```

<!-- By leveraging Kotlin's SAM conversion, you can write the following equivalent code instead: -->
Используя SAM преобразование, вы можете использовать следующий эквивалентный код:

```kotlin
// Создание экземпляра, используя лямбду
val isEven = IntPredicate { it % 2 == 0 }
```

<!-- A short lambda expression replaces all the unnecessary code. -->
Короткое лямбда-выражение заменяет весь ненужный код.

```kotlin
fun interface IntPredicate {
   fun accept(i: Int): Boolean
}

val isEven = IntPredicate { it % 2 == 0 }

fun main() {
   println("Is 7 even? - ${isEven.accept(7)}")
}
```

<!-- You can also use [SAM conversions for Java interfaces](java-interop.md#sam-conversions). -->
Вы можете использовать [SAM преобразования для Java интерфейсов](java-interop.html#sam-conversions).

<a name="functional-interfaces-vs-type-aliases"></a>
<!-- ## Functional interfaces vs. type aliases -->
## Функциональные интерфейсы vs псевдонимы типов

<!-- Functional interfaces and [type aliases](type-aliases.md) serve different purposes.
Type aliases are just names for existing types – they don't create a new type, while functional interfaces do.
You can provide extensions that are specific to a particular functional interface to be inapplicable for plain functions or their type aliases. -->
Функциональные интерфейсы и [псевдонимы типов](type-aliases.html) служат разным целям.
Псевдонимы типов – это просто имена существующих типов, они не создают новый тип, в то время как функциональные интерфейсы делают это.
Вы можете предоставить расширения, специфичные для конкретного функционального интерфейса, которые будут неприменимы для простых функций или их псевдонимов типов.

<!-- Type aliases can have only one member, while functional interfaces can have multiple non-abstract members and one abstract member.
Functional interfaces can also implement and extend other interfaces. -->
Псевдонимы типов могут иметь только один элемент, в то время как функциональные интерфейсы могут иметь несколько неабстрактных элементов и один абстрактный элемент.
Функциональные интерфейсы также могут реализовывать и расширять другие интерфейсы.

<!-- Functional interfaces are more flexible and provide more capabilities than type aliases, but they can be more costly both syntactically and at runtime because they can require conversions to a specific interface.
When you choose which one to use in your code, consider your needs: -->
Функциональные интерфейсы более гибкие и предоставляют больше возможностей, чем псевдонимы типов, но они могут быть более дорогостоящими как синтаксически,
так и во время выполнения, поскольку могут потребовать преобразования в определенный интерфейс. Когда вы выбираете, какой из них использовать в своем коде,
учитывайте свои потребности:

<!-- * If your API needs to accept a function (any function) with some specific parameter and return types – use a simple functional type or define a type alias to give a shorter name to the corresponding functional type.
* If your API accepts a more complex entity than a function – for example, it has non-trivial contracts and/or operations on it that can't be expressed in a functional type's signature – declare a separate functional interface for it. -->
* Если вашему API необходимо принять функцию (любую функцию) с определенным параметром и типами возвращаемых значений –используйте простой функциональный тип
или определите псевдоним типа, чтобы дать более короткое имя соответствующему функциональному типу.
* Если ваш API принимает более сложную сущность, чем функция, – например, у него есть нетривиальные контракты и/или операции над ним,
которые не могут быть выражены в сигнатуре функционального типа, объявите для него отдельный функциональный интерфейс.