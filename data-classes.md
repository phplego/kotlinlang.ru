---
type: doc
layout: reference
category: "Classes and Objects"
title: "Классы данных (data classes)"
---

<!--# Data Classes-->

<!--We frequently create classes that do nothing but hold data. In such classes some functionality is often mechanically
derivable from the data they hold. In Kotlin a class can be marked as `data`:-->
Нередко мы создаём классы, которые ничего не делают, кроме как хранят данные. В подобного рода классах механика всего функционала продиктована данными, которые они содержат. В <b>Kotlin</b> класс может быть отмечен словом `data`:
 
``` kotlin
data class User(val name: String, val age: Int)
```

<!--This is called a _data class_. The compiler automatically derives the following members from all properties declared in
the primary constructor:-->
Такой класс называется _дата классом_ (классом данных). Компилятор автоматически получает все члены данного класса из свойств, объявленных в его первичном конструкторе:
    
  * пара функций `equals()`/`hashCide()`,
  * `toString()` в форме `"User(name=Jhon, age=42)"`,
  * функции [`componentN()`](https://kotlinlang.org/docs/reference/multi-declarations.html), которые соответствуют свойствам, в зависимости от их порядка либо объявления,
  * функция `copy()` (см. ниже)
  
<!--If any of these functions is explicitly defined in the class body or inherited from the base types, it will not be generated.-->
В случае, если что-то из этого списка явно определено в теле класса или же унаследованно от родительского типа, компилятор не будет это генерировать.

<!--To ensure consistency and meaningful behavior of the generated code, data classes have to fulfil the following requirements:-->
Для того, чтобы предотвратить бесполезное поведение сгенерированного кода, дата классы должны быть заполнены в соответствии с определёнными требованиями:

  * The primary constructor needs to have at least one parameter;
  * All primary constructor parameters need to be marked as `val` or `var`;
  * Data classes cannot be abstract, open, sealed or inner;
  * Data classes may not extend other classes (but may implement interfaces).
  
  * Первичный конструктор должен иметь как минимум один параметр;
  * Все параметры главного конструктора должны быть отмечены, как `val` или `var`;
  * Классы данных не могут быть абстрактными, open, seales или inner;
  * Дата классы не могут наследоваться от других классов (но могут реализовывать интерфейсы).
  
> В JVM, если сгенерированному классу нужен конструктор без параметров, должны быть указаны значения по умолчанию для всех свойств
>(см. [Конструкторы](http://kotlin.su/docs/reference/classes.html#constructors))
``` kotlin
 data class User(val name: String = "", val age: Int = 0)
 ```

## Copying
  
It's often the case that we need to copy an object altering _some_ of its properties, but keeping the rest unchanged. 
This is what `copy()` function is generated for. For the `User` class above, its implementation would be as follows:
     
``` kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)     
```     

This allows us to write

``` kotlin
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

## Data Classes and Destructuring Declarations

_Component functions_ generated for data classes enable their use in [destructuring declarations](multi-declarations.html):

``` kotlin
val jane = User("Jane", 35) 
val (name, age) = jane
println("$name, $age years of age") // prints "Jane, 35 years of age"
```

## Standard Data Classes

The standard library provides `Pair` and `Triple`. In most cases, though, named data classes are a better design choice, 
because they make the code more readable by providing meaningful names for properties.
