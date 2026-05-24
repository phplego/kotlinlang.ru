---
type: doc
layout: reference
category: "Classes and Objects"
title: "Классы данных"
url: https://kotlinlang.ru/docs/data-classes.html
---

<!-- При переводе статьи оригинальная версия была от 14 March 2026 -->

<!-- # Data classes -->
# Классы данных

<!-- Data classes in Kotlin are primarily used to hold data. For each data class, the compiler automatically generates
additional member functions that allow you to print an instance to readable output, compare instances, copy instances, and more.
Data classes are marked with `data`: -->
В Kotlin классы данных в первую очередь используются для хранения данных. Для каждого класса данных компилятор автоматически генерирует
дополнительные функции-члены, которые позволяют выводить экземпляр в читаемом виде, сравнивать экземпляры, копировать их и не только.
Классы данных помечаются ключевым словом `data`.

```kotlin
data class User(val name: String, val age: Int)
```

<!-- The compiler automatically derives the following members from all properties declared in the primary constructor: -->
Компилятор автоматически формирует следующие члены данного класса из свойств, объявленных в основном конструкторе:

<!-- * `equals()`/`hashCode()` pair.
* `toString()` of the form `"User(name=John, age=42)"`.
* [`componentN()` functions](destructuring-declarations.md) corresponding to the properties in their order of declaration.
* [`copy()` function](#copying). -->
* пару функций `equals()`/`hashCode()`;
* функцию `toString()` в форме `"User(name=John, age=42)"`;
* компонентные функции [componentN()](destructuring-declarations.html), соответствующие свойствам в порядке их объявления;
* функцию [`copy()`](#copying).

<!-- To ensure consistency and meaningful behavior of the generated code, data classes have to fulfill the following requirements: -->
Для обеспечения согласованности и осмысленного поведения сгенерированного кода классы данных должны удовлетворять следующим требованиям:

<!-- * The primary constructor must have at least one parameter.
* All primary constructor parameters must be marked as `val` or `var`.
* Data classes can't be abstract, open, sealed, or inner. -->
* Основной конструктор должен иметь как минимум один параметр.
* Все параметры основного конструктора должны быть отмечены как `val` или `var`.
* Классы данных не могут быть абстрактными, `open`, `sealed` или `inner`.

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
> to be specified (see [Constructors](classes.md#constructors-and-initializer-blocks)): -->
> Для того чтобы у сгенерированного в JVM класса был конструктор без параметров, значения всех свойств должны быть заданы по умолчанию
> (см. [Конструкторы](classes.html#constructors-and-initializer-blocks)):

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

<!-- In the example below, only the `name` property is used by default inside the `toString()`, `equals()`, `hashCode()`,
and `copy()` implementations, and there is only one component function, `component1()`.
The `age` property is declared inside the class body and is excluded.
Therefore, two `Person` objects with the same `name` but different `age` values are considered equal since `equals()`
only evaluates properties from the primary constructor: -->
В примере ниже только свойство `name` по умолчанию используется в реализациях функций `toString()`, `equals()`, `hashCode()` и `copy()`,
и создаётся только одна компонентная функция `component1()`. Свойство `age` объявлено в теле класса и исключено.
Поэтому два объекта `Person` с одинаковым значением `name`, но разными значениями `age` считаются равными, так как `equals()`
проверяет только свойства из основного конструктора.

```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
fun main() {
//sampleStart
    val person1 = Person("John")
    val person2 = Person("John")
    person1.age = 10
    person2.age = 20

    println("person1 == person2: ${person1 == person2}")
    // person1 == person2: true

    println("person1 with age ${person1.age}: ${person1}")
    // person1 with age 10: Person(name=John)

    println("person2 with age ${person2.age}: ${person2}")
    // person2 with age 20: Person(name=John)
//sampleEnd
}
```

<a name="copying"></a>
<!-- ## Copying -->
## Копирование

<!-- Use the `copy()` function to copy an object, allowing you to alter _some_ of its properties while keeping the rest unchanged. The implementation of this function for the `User` class above would be as follows: -->
Используйте функцию `copy()` для копирования объекта, что позволит изменить только *некоторые* его свойства, оставив остальные неизменными.
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

<!-- The `copy()` function creates a _shallow_ copy of the instance. In other words, it doesn't copy components recursively.
As a result, references to other objects are shared. -->
Функция `copy()` создаёт *поверхностную* копию экземпляра. Другими словами, она не копирует компоненты рекурсивно.
В результате ссылки на другие объекты остаются общими.

<!-- For example, if a property holds a mutable list, changes made through the "original" value are also visible through the copy,
and changes made through the copy are visible through the original: -->
Например, если свойство хранит изменяемый список, изменения, сделанные через исходное значение, также видны через копию,
а изменения, сделанные через копию, видны через исходное значение:

```kotlin
data class Employee(val name: String, val roles: MutableList<String>)

fun main() {
    val original = Employee("Jamie", mutableListOf("developer"))
    val duplicate = original.copy()

    duplicate.roles.add("team lead")

    println(original)
    // Employee(name=Jamie, roles=[developer, team lead])
    println(duplicate)
    // Employee(name=Jamie, roles=[developer, team lead])
}
```

<!-- As you can see, modifying the `duplicate.roles` property also changes the `original.roles` property because both properties share the same list reference. -->
Как видите, изменение свойства `duplicate.roles` также меняет свойство `original.roles`, потому что оба свойства ссылаются на один и тот же список.

<a name="data-classes-and-destructuring-declarations"></a>
<!-- ## Data classes and destructuring declarations -->
## Классы данных и мульти-декларации

<!-- _Component functions_ generated for data classes make it possible to use them in [destructuring declarations](destructuring-declarations.md): -->
Сгенерированные для классов данных *компонентные функции* позволяют использовать их в [мульти-декларациях](destructuring-declarations.html).

```kotlin
val jane = User("Jane", 35)
val (name, age) = jane
println("$name, $age years of age")
// Jane, 35 years of age
```

<a name="standard-data-classes"></a>
<!-- ## Standard data classes -->
## Стандартные классы данных

<!-- The standard library provides the `Pair` and `Triple` classes. In most cases, though, named data classes are a better design choice
because they make the code easier to read by providing meaningful names for the properties. -->
Стандартная библиотека предоставляет классы `Pair` и `Triple`. Однако, в большинстве случаев, именованные классы данных являются лучшим решением,
потому что делают код более читаемым, предоставляя осмысленные имена для свойств.
