---
type: doc
layout: reference
category: "Classes and Objects"
title: "Классы данных"
url: https://kotlinlang.ru/docs/reference/data-classes.html
---

<!--# Data Classes-->
# Классы данных

<!--We frequently create classes that do nothing but hold data. In such classes some functionality is often mechanically
derivable from the data they hold. In Kotlin a class can be marked as `data`:-->
Нередко мы создаём классы, единственным назначением которых является хранение данных. Функционал таких классов зависит от самих данных, которые в них хранятся. В <b>Kotlin</b> класс может быть отмечен словом <b class="keyword">data</b>:

```kotlin
data class User(val name: String, val age: Int)
```

<!--This is called a _data class_. The compiler automatically derives the following members from all properties declared in
the primary constructor:
    - equals()/hashCode() pair;
    - toString() of the form "User(name=John, age=42)";
    - componentN() functions corresponding to the properties in their order of declaration;
    - copy() function (see below).
-->
Такой класс называется _классом данных_. Компилятор автоматически формирует следующие члены данного класса из свойств, объявленных в основном конструкторе:

  * пару функций `equals()`/`hashCode()`,
  * функцию `toString()` в форме `"User(name=John, age=42)"`,
  * компонентные функции [componentN()](multi-declarations.html), которые соответствуют свойствам, в соответствии с порядком их объявления,
  * функцию `copy()` (см. ниже)

<!--If any of these functions is explicitly defined in the class body or inherited from the base types, it will not be generated.-->
Если какая-либо из этих функций явно определена в теле класса (или унаследована от родительского класса), то генерироваться она не будет.

<!--To ensure consistency and meaningful behavior of the generated code, data classes have to fulfil the following requirements:
    - The primary constructor needs to have at least one parameter;
    - All primary constructor parameters need to be marked as val or var;
    - Data classes cannot be abstract, open, sealed or inner;
    - (before 1.1) Data classes may only implement interfaces.
-->
Для обеспечения согласованности и осмысленного поведения сгенерированного кода классы данных должны удовлетворять следующим требованиям:

  * Основной конструктор должен иметь как минимум один параметр;
  * Все параметры основного конструктора должны быть отмечены, как `val` или `var`;
  * Классы данных не могут быть абстрактными, open, sealed или inner;
  * (до версии 1.1) Классы данных не могут наследоваться от других классов (но могут реализовывать интерфейсы).

<!--
Additionally, the members generation follows these rules with regard to the members inheritance:
    - If there are explicit implementations of equals(), hashCode() or toString() in the data class body or final implementations in a superclass, then these functions are not generated, and the existing implementations are used;
    - If a supertype has the componentN() functions that are open and return compatible types, the corresponding functions are generated for the data class and override those of the supertype. If the functions of the supertype cannot be overridden due to incompatible signatures or being final, an error is reported;
    - Deriving a data class from a type that already has a copy(...) function with a matching signature is deprecated in Kotlin 1.2 and is prohibited in Kotlin 1.3.
    - Providing explicit implementations for the componentN() and copy() functions is not allowed.
-->
Дополнительно, генерация членов классов данных при наследовании подчиняется следующим правилам:
  * Если существуют явные реализации `equals()`, `hashCode()` или `toString()` в теле класса данных или конечные (<b class="keyword">final</b>) реализации в суперклассе, то эти функции не генерируются, а используются существующие реализации;
  * Если суперкласс включает функции `componentN()`, которые являются открытыми и возвращают совместимые типы, соответствующие компонентные функции создаются для класса данных и переопределяют функции суперкласса. Если функции суперкласса не могут быть переопределены из-за несовместимости сигнатур или являются конечными (<b class="keyword">final</b>), выдаётся сообщение об ошибке;
  * Наследование класса данных от типа, который уже имеет функцию `copy(...)` с совпадающей сигнатурой не рекомендуется в <b>Kotlin 1.2</b> и запрещена в <b>Kotlin 1.3</b>;
  * Предоставление явных реализаций для функций `componentN()` и `copy()` не допускается.

<!-- Since 1.1, data classes may extend other classes (see Sealed classes for examples). -->
Начиная с версии 1.1, классы данных могут расширять другие классы (см. примеры в статье [Изолированные классы](sealed-classes.html#sealed-classes-and-data-classes))

<!-- On the JVM, if the generated class needs to have a parameterless constructor, default values for all properties have to be specified (see Constructors). -->
> Для того, чтобы у сгенерированного в JVM класса был конструктор без параметров, значения всех свойств должны быть заданы по умолчанию
> (см. [Конструкторы](classes.html#constructors))
```kotlin
 data class User(val name: String = "", val age: Int = 0)
```

<!-- Properties Declared in the Class Body -->
## Свойства, объявленные в теле класса

<!-- Note that the compiler only uses the properties defined inside the primary constructor for the automatically generated functions. To exclude a property from the generated implementations, declare it inside the class body: -->
Обратите внимание, что компилятор использует только свойства, определенные в основном конструкторе для автоматически созданных функций. Чтобы исключить свойство из автоматически созданной реализации, объявите его в теле класса:
```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
```
<!-- Only the property name will be used inside the toString(), equals(), hashCode(), and copy() implementations, and there will only be one component function component1(). While two Person objects can have different ages, they will be treated as equal. --> 
Только свойство `name` будет учитываться в реализациях функций `toString()`, `equals()`, `hashCode()` и `copy()`, и будет создана только одна компонентная функция `component1()`. Даже если два объекта класса `Person` будут иметь разные значения свойств `age`, они будут считаться равными.
```kotlin
    val person1 = Person("John")
    val person2 = Person("John")
    person1.age = 10
    person2.age = 20
    println("${person1 == person2}") // выведет "true"
```

<!--## Copying-->
## Копирование

<!--It's often the case that we need to copy an object altering _some_ of its properties, but keeping the rest unchanged.
This is what `copy()` function is generated for. For the `User` class above, its implementation would be as follows:-->
Довольно часто нам приходится копировать объект с изменением только _некоторых_ его свойств. Для этой задачи генерируется функция `copy()`. Для написанного выше класса `User` такая реализация будет выглядеть следующим образом:

```kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
```

<!--This allows us to write-->
Это позволяет нам писать:
```kotlin
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

<!--## Data Classes and Destructuring Declarations-->
## Классы данных и мульти-декларации

<!--_Component functions_ generated for data classes enable their use in [destructuring declarations](multi-declarations.html):-->
Сгенерированные для классов данных _компонентные функции_ позволяют использовать их в [мульти-декларациях](multi-declarations.html):

```kotlin
val jane = User("Jane", 35)
val (name, age) = jane
println("$name, $age years of age") // выводит "Jane, 35 years of age"
```

<!--## Standard Data Classes-->
## Стандартные классы данных

<!--The standard library provides `Pair` and `Triple`. In most cases, though, named data classes are a better design choice,
because they make the code more readable by providing meaningful names for properties.-->
Стандартная библиотека предоставляет классы `Pair` и `Triple`. Однако, в большинстве случаев, проименованные классы данных являются лучшим решением, потому что делают код более читаемым, избегая малосодержательные имена для свойств.

## См. также
* [Статья "Вышел Kotlin M3"](https://habrahabr.ru/company/JetBrains/blog/152126/)
