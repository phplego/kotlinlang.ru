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
Нередко мы создаём классы, единственным назначением которых является хранение данных. Функционал таких классов зависит от самих данных, которые в них хранятся. В <b>Kotlin</b> класс может быть отмечен словом `data`:

``` kotlin
data class User(val name: String, val age: Int)
```

<!--This is called a _data class_. The compiler automatically derives the following members from all properties declared in
the primary constructor:-->
Такой класс называется _классом данных_. Компилятор автоматически извлекает все члены данного класса из свойств, объявленных в первичном конструкторе:

  * пара функций `equals()`/`hashCode()`,
  * `toString()` в форме `"User(name=John, age=42)"`,
  * функции [componentN()](multi-declarations.html), которые соответствуют свойствам, в зависимости от их порядка либо объявления,
  * функция `copy()` (см. ниже)

<!--If any of these functions is explicitly defined in the class body or inherited from the base types, it will not be generated.-->
Если какая-либо из этих функций явно определена в теле класса (или унаследована от родительского класса), то генерироваться она не будет.

<!--To ensure consistency and meaningful behavior of the generated code, data classes have to fulfil the following requirements:-->
Для того, чтобы поведение генерируемого кода соответствовало здравому смыслу, классы данных должны быть оформлены с соблюдением некоторых требований:

  * Первичный конструктор должен иметь как минимум один параметр;
  * Все параметры первичного конструктора должны быть отмечены, как `val` или `var`;
  * Классы данных не могут быть абстрактными, open, sealed или inner;
  * Дата-классы не могут наследоваться от других классов (но могут реализовывать интерфейсы).
  
<!-- Since 1.1, data classes may extend other classes (see Sealed classes for examples). -->
Начиная с версии 1.1, классы данных могут расширять другие классы (см. примеры в [Sealed classes](sealed-classes.html#sealed-classes-and-data-classes))

> Для того, чтобы у сгенерированного в JVM класса был конструктор без параметров, значения всех свойств должны быть заданы по умолчанию
> (см. [Конструкторы](classes.html#constructors))
``` kotlin
 data class User(val name: String = "", val age: Int = 0)
 ```

<!--## Copying-->
## Копирование

<!--It's often the case that we need to copy an object altering _some_ of its properties, but keeping the rest unchanged.
This is what `copy()` function is generated for. For the `User` class above, its implementation would be as follows:-->
Довольно часто нам приходится копировать объект с изменением только _некоторых_ его свойств. Для этой задачи генерируется функция `copy()`. Для написанного выше класса `User` такая реализация будет выглядеть следующим образом:

``` kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
```

<!--This allows us to write-->
Это позволяет нам писать

``` kotlin
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

<!--## Data Classes and Destructuring Declarations-->
## Классы данных и мульти-декларации

<!--_Component functions_ generated for data classes enable their use in [destructuring declarations](multi-declarations.html):-->
Сгенерированные для классов данных _составные функции_ позволяют использовать их в [мульти-декларациях](multi-declarations.html):

``` kotlin
val jane = User("Jane", 35)
val (name, age) = jane
println("$name, $age years of age") // выводит "Jane, 35 years of age"
```

<!--## Standard Data Classes-->
## Стандартные классы данных

<!--The standard library provides `Pair` and `Triple`. In most cases, though, named data classes are a better design choice,
because they make the code more readable by providing meaningful names for properties.-->
Стандартная библиотека предоставляет `Pair` и `Triple`. Однако, в большинстве случаев, проименованные классы данных являются лучшим решением, потому что делают код более читаемым, избегая малосодержательные имена для свойств.

> [Статья на эту тему на Хабре](https://habrahabr.ru/company/JetBrains/blog/152126/)
