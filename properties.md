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
Классы в <b>Kotlin</b> могут иметь свойства: изменяемые (_mutable_) и неизменяемые (_read-only_) — **var** и **val** соответственно.

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
Полный синтаксис объявления свойства выглядит так:

``` kotlin
var <propertyName>: <PropertyType> [= <property_initializer>]
    [<getter>]
    [<setter>]
```

<!--The initializer, getter and setter are optional. Property type is optional if it can be inferred from the initializer or from the base class member being overridden.-->
Инициализатор *property_initializer*, геттер и сеттер можно не указывать. Также необязательно указывать тип свойства, если он может быть выведен из контекста или наследован от базового класса.

<!--Examples:-->
Примеры:

``` kotlin
var allByDefault: Int? // ошибка: необходима явная инициализация, предусмотрены стандартные геттер и сеттер
var initialized = 1 // имеет тип Int, стандартный геттер и сеттер
```

<!--The full syntax of a read-only property declaration differs from a mutable one in two ways: it starts with `val` instead of `var` and does not allow a setter:-->
Синтаксис объявления констант имеет два отличия от синтаксиса объявления изменяемых переменных: во-первых, объявление начинается с ключевого слова `val` вместо `var`, а во-вторых, объявление сеттера запрещено:

``` kotlin
val simple: Int? // имеет тип Int, стандартный геттер, должен быть инициализирован в конструкторе
val inferredType = 1 // имеет тип Int и стандартный геттер
```

<!--We can write custom accessors, very much like ordinary functions, right inside a property declaration. Here's an example of a custom getter:-->
Мы можем самостоятельно описать методы доступа, как и обычные функции, прямо при объявлении свойств. Например, пользовательский геттер:

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
По договорённости, имя параметра сеттера - `value`, но вы можете использовать любое другое.

<!--If you need to change the visibility of an accessor or to annotate it, but don't need to change the default implementation,
you can define the accessor without defining its body:-->
Если вам нужно изменить область видимости метода доступа или пометить его аннотацией, при этом не внося изменения в реализацию по умолчанию, вы можете объявить метод доступа без объявления его тела:

``` kotlin
var setterVisibility: String = "abc"
    private set // сеттер имеет private доступ и стандартную реализацию

var setterWithAnnotation: Any? = null
    @Inject set // аннотирование сеттера с помощью Inject
```

<a name="backing-fields"></a>

### Backing Fields

<!--Classes in Kotlin cannot have fields. However, sometimes it is necessary to have a backing field when using custom accessors. For these purposes, Kotlin provides
an automatic backing field which can be accessed using the `field` identifier:-->
Классы в <b>Kotlin</b> не могут иметь полей. Т.е. переменные, которые вы объявляете внутри класса только выглядят и ведут себя как поля из Java, хотя на самом деле являются _свойствами_, т.к. для них неявно реализуются методы get и set. А сама переменная, в которой находится значение свойства, называется **backing field**. Однако, иногда, при использовании пользовательских методов доступа, необходимо иметь доступ к _backing field_. Для этих целей <b>Kotlin</b> предоставляет автоматическое _backing field_, к которому можно обратиться с помощью идентификатора `field`:

``` kotlin
var counter = 0
    set(value) {
        if (value >= 0) field = value // значение при инициализации записывается прямиком в backing field
    }
```

<!--The `field` identifier can only be used in the accessors of the property.-->
Идентификатор `field` может быть использован только в методах доступа к свойству.

<!--A backing field will be generated for a property if it uses the default implementation of at least one of the accessors, or if a custom accessor references it through the `field` identifier.-->
_Backing field_ будет сгенерировано для свойства, если оно использует стандартную реализацию как минимум одного из методов доступа. Или в случае, когда пользовательский метод доступа ссылается на него через идентификатор `field`.

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

<!--In all respects, this is just the same as in Java since access to private properties with default getters and setters is optimized so that no function call overhead is introduced.-->
Такой подход ничем не отличается от подхода в Java, так как доступ к приватным свойствам со стандартными геттерами и сеттерами оптимизируется таким образом, что вызов функции не происходит.

<!--## Compile-Time Constants-->
## Константы времени компиляции

<!--Properties the value of which is known at compile time can be marked as _compile time constants_ using the `const` modifier.
Such properties need to fulfil the following requirements:-->
Свойства, значение которых известно во время компиляции, могут быть помечены как _константы времени компиляции_. Для этого используется модификатор `const`. Такие свойства должны соответствовать следующим требованиям:

  * Находиться на самом высоком уровне или быть членом объекта `object`  
  * Быть проинициализированными значением типа `String` или значением примитивного типа
  * Не иметь переопределённого геттера

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
Обычно, свойства, объявленные non-null типом, должны быть проинициализированы в конструкторе. Однако, довольно часто это неосуществимо. К примеру, свойства могут быть инициализированы через внедрение зависимостей, в установочном методе (ориг.: _"setup method"_) юнит-теста или в методе `onCreate` в Android. В таком случае вы не можете обеспечить non-null инициализацию в конструкторе, но всё равно хотите избежать проверок на null при обращении внутри тела класса к такому свойству.

<!--To handle this case, you can mark the property with the `lateinit` modifier:-->
Для того, чтобы справиться с такой задачей, вы можете пометить свойство модификатором `lateinit`:

``` kotlin
public class MyTest {
    lateinit var subject: TestSubject

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method()  // объект инициализирован, проверять на null не нужно
    }
}
```
<!--
The modifier can be used on `var` properties declared inside the body of a class (not in the primary constructor, and only
when the property does not have a custom getter or setter) and, since Kotlin 1.2, for top-level properties and
local variables. The type of the property or variable must be non-null, and it must not be a primitive type.
-->
Такой модификатор может быть использован только с `var` свойствами, объявленными внутри тела класса (не в главном конструкторе, и только тогда, когда свойство не имеет пользовательских геттеров и сеттеров) и, начиная с Kotlin 1.2, со свойствами, расположенными на верхнем уровне, и локальными переменными. Тип такого свойства должен быть non-null и не должен быть примитивным.

<!--Accessing a `lateinit` property before it has been initialized throws a special exception that clearly identifies the property
being accessed and the fact that it hasn't been initialized.-->
Доступ к `lateinit` свойству до того, как оно проинициализировано, выбрасывает специальное исключение, которое чётко обозначает, что свойство не было определено.

<!--### Checking whether a lateinit var is initialized (since 1.2) -->
### Проверка инициализации lateinit var (начиная с версии 1.2)

<!--To check whether a `lateinit var` has already been initialized, use `.isInitialized` on
the [reference to that property](reflection.html#property-references):-->
Чтобы проверить, была ли проинициализировано `lateinit var` свойство, используйте `.isInitialized` метод [ссылки на это свойство](reflection.html#property-references):

```kotlin
if (foo::bar.isInitialized) {
    println(foo.bar)
}
```

<!--This check is only available for the properties that are lexically accessible, i.e. declared in the same type or in one of
the outer types, or at top level in the same file.-->
Эта проверка возможна только для лексически доступных свойств, то есть объявленных в том же типе, или в одном из внешних типов, или глобальных свойств, объявленных в том же файле.

<!--## Overriding Properties-->
## Переопределение свойств

См. [Переопределение членов класса](http://kotlinlang.ru/docs/reference/classes.html#overriding-properties)

<!--## Delegated Properties-->
## Делегированные свойства

<!--The most common kind of properties simply reads from (and maybe writes to) a backing field.
On the other hand, with custom getters and setters one can implement any behaviour of a property.
Somewhere in between, there are certain common patterns of how a property may work. A few examples: lazy values,
reading from a map by a given key, accessing a database, notifying listener on access, etc.-->
Самый распространённый тип свойств просто считывает (или записывает) данные из _backing field_.
Тем не менее, с пользовательскими геттерами и сеттерами мы можем реализовать совершенно любое поведение свойства.
В реальности, существуют общепринятые шаблоны того, как могут работать свойства. Несколько примеров:
* Вычисление значения свойства при первом доступе к нему (ленивые свойства)
* Чтение из ассоциативного списка с помощью заданного ключа
* Доступ к базе данных
* Оповещение listener'а в момент доступа
и т.п.


<!--Such common behaviours can be implemented as libraries using [_delegated properties_](delegated-properties.html).-->
Такие распространённые поведения свойств могут быть реализованы в виде библиотек с помощью [_делегированных свойств_](https://kotlinlang.ru/docs/reference/delegated-properties.html).
