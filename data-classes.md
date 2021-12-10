---
type: doc
layout: reference
category: "Classes and Objects"
title: "Классы данных"
url: https://kotlinlang.ru/docs/data-classes.html
---

<!-- При переводе статьи оригинальная версия была от 12 July 2021 -->

<!-- # Data classes -->
# Классы данных

<!-- It is not unusual to create classes whose main purpose is to hold data.
In such classes, some standard functionality and some utility functions are often mechanically
derivable from the data. In Kotlin, these are called _data classes_ and are marked with `data`: -->
Нередко мы создаём классы, единственным назначением которых является хранение данных.
Функционал и некоторые служебные функции таких классов зависят от самих данных, которые в них хранятся.
В Kotlin они называются *классами данных* и помечены `data`.

```kotlin
data class User(val name: String, val age: Int)
```

<!-- The compiler automatically derives the following members from all properties declared in the primary constructor: -->
Компилятор автоматически формирует следующие члены данного класса из свойств, объявленных в основном конструкторе:

<!-- * `equals()`/`hashCode()` pair
* `toString()` of the form `"User(name=John, age=42)"`
* [`componentN()` functions](destructuring-declarations.md) corresponding to the properties in their order of declaration.
* `copy()` function (see below). -->
* пару функций `equals()`/`hashCode()`,
* функцию `toString()` в форме `"User(name=John, age=42)"`,
* компонентные функции [componentN()](destructuring-declarations.html), которые соответствуют свойствам, в соответствии с порядком их объявления,
* функцию `copy()` (см. ниже).

<!-- To ensure consistency and meaningful behavior of the generated code, data classes have to fulfill the following requirements: -->
Для обеспечения согласованности и осмысленного поведения сгенерированного кода классы данных должны удовлетворять следующим требованиям:

<!-- * The primary constructor needs to have at least one parameter.
* All primary constructor parameters need to be marked as `val` or `var`.
* Data classes cannot be abstract, open, sealed, or inner. -->
* Основной конструктор должен иметь как минимум один параметр;
* Все параметры основного конструктора должны быть отмечены, как `val` или `var`;
* Классы данных не могут быть абстрактными, open, sealed или inner;

<!-- Additionally, the generation of data class members follows these rules with regard to the members’ inheritance: -->
Дополнительно, генерация членов классов данных при наследовании подчиняется следующим правилам:

<!-- * If there are explicit implementations of `equals()`, `hashCode()`, or `toString()` in the data class body or
  `final` implementations in a superclass, then these functions are not generated, and the existing
  implementations are used.
* If a supertype has `componentN()` functions that are `open` and return compatible types, the
  corresponding functions are generated for the data class and override those of the supertype. If the functions of the
  supertype cannot be overridden due to incompatible signatures or due to their being final, an error is reported.
* Providing explicit implementations for the `componentN()` and `copy()` functions is not allowed. -->
* Если существуют явные реализации `equals()`, `hashCode()` или `toString()` в теле класса данных или `final` реализации в суперклассе,
то эти функции не генерируются, а используются существующие реализации;
* Если суперкласс включает функции `componentN()`, которые являются открытыми и возвращают совместимые типы,
соответствующие компонентные функции создаются для класса данных и переопределяют функции суперкласса.
Если функции суперкласса не могут быть переопределены из-за несовместимости сигнатур или потому что они `final`, выдаётся сообщение об ошибке;
* Предоставление явных реализаций для функций `componentN()` и `copy()` не допускается.

<!-- Data classes may extend other classes (see [Sealed classes](sealed-classes.md) for examples). -->
Классы данных могут расширять другие классы (см. примеры в статье [Изолированные классы](sealed-classes.html)).

<!-- > On the JVM, if the generated class needs to have a parameterless constructor, default values for the properties have
> to be specified (see [Constructors](classes.md#constructors)). -->
> Для того, чтобы у сгенерированного в JVM класса был конструктор без параметров, значения всех свойств должны быть заданы по умолчанию
> (см. [Конструкторы](classes.html#constructors)).

```kotlin
data class User(val name: String = "", val age: Int = 0)
```

<a name="properties-declared-in-the-class-body"></a>
<!-- ## Properties declared in the class body -->
## Свойства, объявленные в теле класса

<!-- The compiler only uses the properties defined inside the primary constructor for the automatically generated
functions. To exclude a property from the generated implementations, declare it inside the class body: -->
Компилятор использует только свойства, определенные в основном конструкторе для автоматически созданных функций.
Чтобы исключить свойство из автоматически созданной реализации, объявите его в теле класса:

```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
```

<!-- Only the property `name` will be used inside the `toString()`, `equals()`, `hashCode()`, and `copy()` implementations,
and there will only be one component function `component1()`. While two `Person` objects can have different ages,
they will be treated as equal. --> 
Только свойство `name` будет учитываться в реализациях функций `toString()`, `equals()`, `hashCode()` и `copy()`,
и будет создана только одна компонентная функция `component1()`. Даже если два объекта класса `Person` будут иметь разные значения свойств `age`,
они будут считаться равными.

```kotlin
val person1 = Person("John")
val person2 = Person("John")
person1.age = 10
person2.age = 20
```

<a name="copying"></a>
<!-- ## Copying -->
## Копирование

<!-- Use the `copy()` function to copy an object, allowing you to alter _some_ of its properties while keeping the rest unchanged. The implementation of this function for the `User` class above would be as follows: -->
Используйте функцию `copy()`, что позволит изменить только *некоторые* его свойств, оставив остальные неизменными.
Для написанного выше класса `User` такая реализация будет выглядеть следующим образом:

```kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
```

<!-- You can then write the following: -->
Это позволяет вам писать:

```kotlin
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

<a name="data-classes-and-destructuring-declarations"></a>
<!-- ## Data classes and destructuring declarations -->
## Классы данных и мульти-декларации

<!-- _Component functions_ generated for data classes make it possible to use them in [destructuring declarations](destructuring-declarations.md): -->
Сгенерированные для классов данных *компонентные функции* позволяют использовать их в [мульти-декларациях](destructuring-declarations.html).

```kotlin
val jane = User("Jane", 35)
val (name, age) = jane
println("$name, $age years of age") // выводит "Jane, 35 years of age"
```

<a name="standard-data-classes"></a>
<!-- ## Standard data classes -->
## Стандартные классы данных

<!-- The standard library provides the `Pair` and `Triple` classes. In most cases, though, named data classes are a better design choice
because they make the code more readable by providing meaningful names for the properties. -->
Стандартная библиотека предоставляет классы `Pair` и `Triple`. Однако, в большинстве случаев, проименованные классы данных являются лучшим решением,
потому что делают код более читаемым, избегая малосодержательные имена для свойств.
