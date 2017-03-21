---
type: doc
layout: reference
category: "Syntax"
title: "Свойства и поля"
url: https://kotlinlang.ru/docs/reference/properties.html
---

<!--# Properties and Fields-->
# Свойства и поля

<!--## Declaring Properties-->
## Объявление свойств

<!--Classes in Kotlin can have properties.
These can be declared as mutable, using the *var*{: .keyword } keyword or read-only using the *val*{: .keyword } keyword.-->
Классы в <b>Kotlin</b> могу иметь свойства. Они могут быть изменяемыми (_mutable_) и неизменяемыми (_read-only_): **var** и **val** соответственно.

``` kotlin
public class Address { 
    public var name: String = ...
    public var street: String = ...
    public var city: String = ...
    public var state: String? = ...
    public var zip: String = ...
}
```

<!--To use a property, we simply refer to it by name, as if it were a field in Java:-->
Для того, чтобы воспользоваться свойством, мы просто обращаемся к его имени (как в <b>Java</b>):

``` kotlin
fun copyAddress(address: Address): Address {
    val result = Address() // нет никакого слова `new`
    result.name = address.name // вызов методов доступа
    result.street = address.street
    // ...
    return result
}
```

<!--## Getters and Setters-->
## Геттеры и сеттеры

<!--The full syntax for declaring a property is-->
Полный синтаксис объявления свойства выглядит так

``` kotlin
var <propertyName>: <PropertyType> [= <property_initializer>]
    [<getter>]
    [<setter>]
```

<!--The initializer, getter and setter are optional. Property type is optional if it can be inferred from the initializer or from the base class member being overridden.-->
Инициализатор _property_initializer_, геттер и сеттер являются необязательными. Также необязательно указывать тип свойства, если он может быть вычислен из инициализатора или из переопределённого члена базового класса.

<!--Examples:-->
Примеры:

``` kotlin
var allByDefault: Int? // ошибка: необходима явная инициализация, предусмотрены стандартные геттер и сеттер
var initialized = 1 // имеет тип Int, стандартный геттер и сеттер
```

<!--The full syntax of a read-only property declaration differs from a mutable one in two ways: it starts with `val` instead of `var` and does not allow a setter:-->
Синтаксис объявления неизменяемых свойств отличается от изменяемых в двух местах: объявление начинается с ключвого слова `val` вместо `var` и оно не позволяет использование сеттера:

``` kotlin
val simple: Int? // имеет тип Int, стандартный геттер, должен быть инициализирован в конструкторе
val inferredType = 1 // имеет тип Int и стандартный геттер
```

<!--We can write custom accessors, very much like ordinary functions, right inside a property declaration. Here's an example of a custom getter:-->
Мы можем использовать пользовательские способы доступа, как и обычные функции, прямо при объявлении свойств. Вот пример пользовательского геттера:

``` kotlin
val isEmpty: Boolean
    get() = this.size == 0
```

<!--A custom setter looks like this:-->
Пользовательский сеттер выглядит примерно так:

``` kotlin
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value) // парсит строку и устанавливает значения для других свойств
    }
```

<!--By convention, the name of the setter parameter is `value`, but you can choose a different name if you prefer.-->
По договорённости, имя параметра для сеттера - `value`, но вы можете использовать любое другое на своё усмотрение.

<!--If you need to change the visibility of an accessor or to annotate it, but don't need to change the default implementation,
you can define the accessor without defining its body:-->
Если вам нужно изменить область видимости для метода доступа к свойству или аннотировать его, но не надо вносить изменения в родную имплементацию, вы можете объявить метод доступа без объявления его тела:

``` kotlin
var setterVisibility: String = "abc"
    private set // сеттер имеет private доступ и стандартную реализацию

var setterWithAnnotation: Any? = null
    @Inject set // аннотирование сеттера с помщью Inject
```

### _Backing Fields_

<!--Classes in Kotlin cannot have fields. However, sometimes it is necessary to have a backing field when using custom accessors. For these purposes, Kotlin provides
an automatic backing field which can be accessed using the `field` identifier:-->
Классы в <b>Kotlin</b> не могут иметь полей. Однако, иногда необходимо иметь _backing field_ при использовании пользовательских методов доступа. Для этих целей <b>Kotlin</b> предоставляет автоматическое _backing field_, к которому можно обратиться с помощью идентификатора `field`:

``` kotlin
var counter = 0 // значение при инициализации записывается прямиком в backing field
    set(value) {
        if (value >= 0) field = value
    }
```

<!--The `field` identifier can only be used in the accessors of the property.-->
Идентификатор `field` может быть использован только в методах доступа к свойству.

<!--A backing field will be generated for a property if it uses the default implementation of at least one of the accessors, or if a custom accessor references it through the `field` identifier.-->
Такое _backing field_ будет сгенерировано для свойства, если оно использует стандартную реализацию как минимум одного из методов доступа. Или в случае, когда пользовательский метод доступа ссылается на него через идентификатор `field`.

<!--For example, in the following case there will be no backing field:-->
Например, в нижестоящем примере не будет никакого _backing field_:

``` kotlin
val isEmpty: Boolean
    get() = this.size == 0
```

### _Backing Properties_

<!--If you want to do something that does not fit into this "implicit backing field" scheme, you can always fall back to having a *backing property*:-->
Если вы хотите предпринять что-то такое, что выходит за рамки вышеуказанной схемы "неявного _backing field_", вы всегда можете использовать _backing property_:

``` kotlin
private var _table: Map<String, Int>? = null
public val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap() // параметры типа вычисляются автоматически (ориг.: "Type parameters are inferred")
        }
        return _table ?: throw AssertionError("Set to null by another thread")
    }
```

_In all respects, this is just the same as in Java since access to private properties with default getters and setters is optimized so that no function call overhead is introduced._
<!--Во всех отношениях, такой подход является ничем иным, как доступом к закрытым свойствам с стандартными геттерами и сеттерами, который мы использовали в  <b>Java</b>. Так как он был оптимизирован, отпала необходимость вызова-->

<!--## Compile-Time Constants-->
## Константы времени компиляции

<!--Properties the value of which is known at compile time can be marked as _compile time constants_ using the `const` modifier.
Such properties need to fulfil the following requirements:-->
Свойства, значение которых известно во время компиляции, могут быть помечены как _константы времени компиляции_. Для этого используется модификатор `const`. Такие свойства необходимо заполнять в соответствии с определёнными требованиями, они должны:

  * Находится на самом высоком уровне или быть членом объекта `object`  
  * Быть проинициализированы значинем типа `String` или значением примитивного типа
  * Не иметь никаких геттеров

<!--Such properties can be used in annotations:-->
Такие свойства могут быть использованы в аннотациях:

``` kotlin
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"

@Deprecated(SUBSYSTEM_DEPRECATED) fun foo() { ... }
```


<!--## Late-Initialized Properties-->
## Свойства с поздней инициализацией

<!--Normally, properties declared as having a non-null type must be initialized in the constructor.
However, fairly often this is not convenient. For example, properties can be initialized through dependency injection,
or in the setup method of a unit test. In this case, you cannot supply a non-null initializer in the constructor,
but you still want to avoid null checks when referencing the property inside the body of a class.-->
Обычно свойства, объявленные non-null типом, должны быть проинициализированы в конструкторе. Однако, довольно часто это неприемлемо. К примеру, свойства могут быть инициализированы через внедрение зависимостей или же в установочном методе (ориг.: _"setup method"_) юнит 
теста. В таком случае вы не можете обеспечить non-null инициализацию в конструкторе, но всё-равно хотите избежать проверок на null при обращении внутри тела класса к такому свойству. 

<!--To handle this case, you can mark the property with the `lateinit` modifier:-->
Для того, чтобы справиться с такой задачей, вы можете пометить свойство модификатором `lateinit`:

``` kotlin
public class MyTest {
    lateinit var subject: TestSubject

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method()  // тут я долго тупил и всё-равно не смог перевести (ориг.: "dereference directly")
    }
}
```

<!--The modifier can only be used on `var` properties declared inside the body of a class (not in the primary constructor), and only
when the property does not have a custom getter or setter. The type of the property must be non-null, and it must not be
a primitive type.-->
Такой модификатор может быть использован только с `var` свойствами, объявленными внутри тела класса (не в главном конструкторе). И только тогда, когда свойство не имеет пользовательских геттеров и сеттеров. Тип такого свойства должен быть non-null и не должен быть примитивным.

<!--Accessing a `lateinit` property before it has been initialized throws a special exception that clearly identifies the property
being accessed and the fact that it hasn't been initialized.-->
Доступ к `lateinit` сойству до того, как оно было проинициализировано, вызывает специальный эксепшн, который чётко обозначает, что к свойству был произведён доступ в то время, пока оно ещё не было проинициализировано.

<!--## Overriding Properties-->
## Переопределение свойств

См. [переопределение свойств](http://kotlinlang.org/docs/reference/classes.html#overriding-properties)

<!--## Delegated Properties-->
## _Delegated Properties_ (Переданные свойства)
  
<!--The most common kind of properties simply reads from (and maybe writes to) a backing field. 
On the other hand, with custom getters and setters one can implement any behaviour of a property.
Somewhere in between, there are certain common patterns of how a property may work. A few examples: lazy values,
reading from a map by a given key, accessing a database, notifying listener on access, etc.-->
Самый распространённый тип свойств просто считывает данные из (а может и записывает в) _backing field_.
С другой стороны, с пользовательскими геттерами и сеттерами мы можем реализрвать совершенно любое поведение свойства.
Где-то посередине от этих двух вариантов существуют общепринятые шаблоны того, как могут работать свойства. Несколько примеров:
ленивые значения (ориг.: "_lazy values_"), которые читаются из коллекции map c помощью заданного ключа key, получают доступ в базе данных, оповещая при этом listener в момент доступа и т.п.


<!--Such common behaviours can be implemented as libraries using [_delegated properties_](delegated-properties.html).-->
Такие распространённые приёмы могут быть реализованы в библиотеках с помощью [_delegated properties_](http://kotlinlang.org/docs/reference/delegated-properties.html)
