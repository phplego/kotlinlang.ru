---
type: doc
layout: reference
category: "Syntax"
title: "Свойства"
url: https://kotlinlang.ru/docs/properties.html
---

<!-- При переводе статьи оригинальная версия была от 08 December 2021 -->

<!-- # Properties -->
# Свойства

<a name="declaring-properties"></a>
<!-- ## Declaring properties -->
## Объявление свойств

<!-- Properties in Kotlin classes can be declared either as mutable, using the `var` keyword, or as read-only, using the `val` keyword. -->
Свойства в классах Kotlin могут быть объявлены либо как изменяемые (mutable) и неизменяемые (read-only) — `var` и `val` соответственно.

```kotlin
class Address {
    var name: String = "Holmes, Sherlock"
    var street: String = "Baker"
    var city: String = "London"
    var state: String? = null
    var zip: String = "123456"
}
```

<!-- To use a property, simply refer to it by its name: -->
Для того, чтобы воспользоваться свойством, просто обратитесь к нему по имени.

```kotlin
fun copyAddress(address: Address): Address {
    val result = Address() // в Kotlin нет никакого слова `new`
    result.name = address.name // вызов методов доступа
    result.street = address.street
    // ...
    return result
}
```

<a name="getters-and-setters"></a>
<!-- ## Getters and setters -->
## Геттеры и сеттеры

<!-- The full syntax for declaring a property is as follows: -->
Полный синтаксис объявления свойства выглядит так:

```kotlin
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```

<!-- The initializer, getter, and setter are optional. The property type is optional if it can be inferred from the initializer
or from the initializer’s or the getter’s return type, as shown below: -->
Инициализатор `property_initializer`, геттер и сеттер можно не указывать. Также необязательно указывать тип свойства,
если он может быть выведен из инициализатора или из возвращаемого типа инициализатора или геттера.

```kotlin
var initialized = 1 // имеет тип Int, стандартный геттер и сеттер
// var allByDefault // ошибка: необходима явная инициализация, 
                    // предусмотрены стандартные геттер и сеттер
```

<!--The full syntax of a read-only property declaration differs from a mutable one in two ways: it starts with `val` instead
of `var` and does not allow a setter:-->
Синтаксис объявления констант имеет два отличия от синтаксиса объявления изменяемых переменных:
во-первых, объявление константы начинается с ключевого слова `val` вместо `var`, а во-вторых, объявление сеттера запрещено.

```kotlin
val simple: Int? // имеет тип Int, стандартный геттер, 
                 // должен быть инициализирован в конструкторе
val inferredType = 1 // имеет тип Int и стандартный геттер
```

<!-- You can define custom accessors for a property. If you define a custom getter, it will be called every time you access
the property (this way you can implement a computed property). Here's an example of a custom getter: -->
Вы можете самостоятельно определить методы доступа для свойства.
Если вы определяете пользовательский геттер, он будет вызываться каждый раз,
когда вы обращаетесь к свойству (таким образом, вы можете реализовать вычисляемое свойство). Вот пример пользовательского геттера:

```kotlin
class Rectangle(val width: Int, val height: Int) {
    val area: Int
        get() = this.width * this.height
}
```

<!-- You can omit the property type if it can be inferred from the getter: -->
Вы можете опустить тип свойства, если его можно определить с помощью геттера.

```kotlin
val area get() = this.width * this.height
```

<!-- If you define a custom setter, it will be called every time you assign a value to the property, except its initialization.
A custom setter looks like this: -->
Если вы определяете пользовательский сеттер, он будет вызываться каждый раз, когда вы присваиваете значение свойству, за исключением его инициализации.
Пользовательский сеттер выглядит так:

```kotlin
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value) // парсит строку и устанавливает 
                                 // значения для других свойств
    }
```

<!-- By convention, the name of the setter parameter is `value`, but you can choose a different name if you prefer. -->
По договорённости, имя параметра сеттера - `value`, но вы можете использовать любое другое.

<!-- If you need to annotate an accessor or change its visibility, but you don't need to change the default implementation,
you can define the accessor without defining its body: -->
Если вам нужно изменить область видимости метода доступа или пометить его аннотацией, при этом не внося изменения в реализацию по умолчанию,
вы можете объявить метод доступа без объявления его тела.

```kotlin
var setterVisibility: String = "abc"
    private set // сеттер имеет private доступ и стандартную реализацию

var setterWithAnnotation: Any? = null
    @Inject set // аннотирование сеттера с помощью Inject
```

<a name="backing-fields"></a>
<!-- ### Backing fields -->
### Теневые поля

<!-- In Kotlin, a field is only used as a part of a property to hold its value in memory. Fields cannot be declared directly.
However, when a property needs a backing field, Kotlin provides it automatically. This backing field can be referenced in
the accessors using the `field` identifier: -->
В Kotlin поле используется только как часть свойства для хранения его значения в памяти. Поля не могут быть объявлены напрямую.
Однако, когда свойству требуется теневое поле (backing field), Kotlin предоставляет его автоматически.
На это теневое поле можно обратиться в методах доступа, используя идентификатор `field`:

```kotlin
var counter = 0 // инициализатор назначает резервное поле напрямую
    set(value) {
        if (value >= 0)
            field = value // значение при инициализации записывается 
                          // прямиком в backing field

            // counter = value // ERROR StackOverflow: Использование 'counter' сделало бы сеттер рекурсивным
    }
```

<!-- The `field` identifier can only be used in the accessors of the property. -->
Идентификатор `field` может быть использован только в методах доступа к свойству.

<!-- A backing field will be generated for a property if it uses the default implementation of at least one of the accessors,
or if a custom accessor references it through the `field` identifier. -->
Теневое поле будет сгенерировано для свойства, если оно использует стандартную реализацию как минимум одного из методов доступа,
либо если пользовательский метод доступа ссылается на него через идентификатор `field`.

<!-- For example, there would be no backing field in the following case: -->
Например, в примере ниже не будет никакого теневого поля:

```kotlin
val isEmpty: Boolean
    get() = this.size == 0
```

<a name="backing-properties"></a>
<!-- ### Backing properties -->
### Теневые свойства

<!-- If you want to do something that does not fit into this _implicit backing field_ scheme, you can always fall back to having
a _backing property_: -->
Если вы хотите предпринять что-то такое, что выходит за рамки вышеуказанной схемы *неявного теневого поля*,
вы всегда можете использовать *теневое свойство* (backing property).

```kotlin
private var _table: Map<String, Int>? = null
public val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap() // параметры типа вычисляются автоматически 
                               // (ориг.: "Type parameters are inferred")
        }
        return _table ?: throw AssertionError("Set to null by another thread")
    }
```

<!-- > On the JVM: Access to private properties with default getters and setters is optimized to avoid function call overhead. -->
> В JVM: доступ к приватным свойствам со стандартными геттерами и сеттерами оптимизируется таким образом,
> что вызов функции не происходит.

<a name="compile-time-constants"></a>
<!-- ## Compile-time constants -->
## Константы времени компиляции

<!-- If the value of a read-only property is known at compile time, mark it as a _compile time constant_ using the `const` modifier.
Such a property needs to fulfil the following requirements: -->
Если значение константного (read-only) свойства известно во время компиляции, пометьте его как *константы времени компиляции*,
используя модификатор `const`. Такие свойства должны соответствовать следующим требованиям:

<!-- * It must be a top-level property, or a member of an [`object` declaration](object-declarations.md#object-declarations-overview) or a _[companion object](object-declarations.md#companion-objects)_.
* It must be initialized with a value of type `String` or a primitive type
* It cannot be a custom getter -->
* Находиться на самом высоком уровне или быть членами [объявления `object`](object-declarations.md#object-declarations-overview)
или [вспомогательного объекта](object-declarations.md#companion-objects);
* Быть проинициализированными значением типа `String` или значением примитивного типа;
* Не иметь переопределённого геттера.

<!-- Such properties can be used in annotations: -->
Такие свойства могут быть использованы в аннотациях.

```kotlin
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"

@Deprecated(SUBSYSTEM_DEPRECATED) fun foo() { ... }
```

<a name="late-initialized-properties-and-variables"></a>
<!-- ## Late-initialized properties and variables -->
## Свойства и переменные с поздней инициализацией

<!-- Normally, properties declared as having a non-null type must be initialized in the constructor.
However, it is often the case that doing so is not convenient. For example, properties can be initialized through dependency
injection, or in the setup method of a unit test. In these cases, you cannot supply a non-null initializer in the constructor,
but you still want to avoid null checks when referencing the property inside the body of a class. -->
Обычно, свойства, объявленные non-null типом, должны быть проинициализированы в конструкторе.
Однако часто бывает так, что делать это неудобно. К примеру, свойства могут быть инициализированы через внедрение зависимостей
или в установочном методе (ориг.: setup method) юнит-теста. В таком случае вы не можете обеспечить non-null инициализацию в конструкторе,
но всё равно хотите избежать проверок на null при обращении внутри тела класса к такому свойству.

<!-- To handle such cases, you can mark the property with the `lateinit` modifier: -->
Для того, чтобы справиться с такой задачей, вы можете пометить свойство модификатором `lateinit`.

```kotlin
public class MyTest {
    lateinit var subject: TestSubject

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method() // объект инициализирован, проверять на null не нужно
    }
}
```
<!-- This modifier can be used on `var` properties declared inside the body of a class (not in the primary constructor,
and only when the property does not have a custom getter or setter), as well as for top-level properties and local variables.
The type of the property or variable must be non-null, and it must not be a primitive type. -->
Такой модификатор может быть использован только с `var` свойствами, объявленными внутри тела класса (не в основном конструкторе,
и только тогда, когда свойство не имеет пользовательских геттеров и сеттеров), со свойствами верхнего уровня и локальными переменными.
Тип такого свойства должен быть non-null и не должен быть примитивным.

<!-- Accessing a `lateinit` property before it has been initialized throws a special exception that clearly identifies the property
being accessed and the fact that it hasn't been initialized. -->
Доступ к `lateinit` свойству до того, как оно проинициализировано, выбрасывает специальное исключение, которое чётко обозначает,
свойство, к которому осуществляется доступ, и тот факт, что оно не было инициализировано.

<a name="checking-whether-a-lateinit-var-is-initialized"></a>
<!-- ### Checking whether a `lateinit var` is initialized  -->
### Проверка инициализации lateinit var

<!-- To check whether a `lateinit var` has already been initialized, use `.isInitialized` on the [reference to that property](reflection.md#property-references): -->
Чтобы проверить, было ли проинициализировано `lateinit var` свойство, используйте `.isInitialized` метод [ссылки на это свойство](reflection.html#property-references).

```kotlin
if (foo::bar.isInitialized) {
    println(foo.bar)
}
```

<!-- This check is only available for properties that are lexically accessible when declared in the same type, in one of the
outer types, or at top level in the same file. -->
Эта проверка возможна только для лексически доступных свойств, то есть объявленных в том же типе, или в одном из внешних типов,
или глобальных свойств, объявленных в том же файле.

<a name="overriding-properties"></a>
<!-- ## Overriding properties -->
## Переопределение свойств

<!-- See [Overriding properties](inheritance.md#overriding-properties). -->
См. [Переопределение свойств класса](inheritance.html#overriding-properties).

<a name="delegated-properties"></a>
<!-- ## Delegated properties -->
## Делегированные свойства

<!-- The most common kind of property simply reads from (and maybe writes to) a backing field, but custom getters and setters
allow you to use properties so one can implement any sort of behavior of a property.
Somewhere in between the simplicity of the first kind and variety of the second, there are common patterns for what properties
can do. A few examples: lazy values, reading from a map by a given key, accessing a database, notifying a listener on access. -->
Самый простой тип свойств просто считывает (или записывает) данные из теневого поля.
Тем не менее, с пользовательскими геттерами и сеттерами мы можем реализовать совершенно любое поведение свойства.
Где-то между простотой первого вида и разнообразием второго существуют общепринятые шаблоны того, что могут делать свойства.
Несколько примеров: вычисление значения свойства при первом доступе к нему (ленивые значения),
чтение из ассоциативного списка с помощью заданного ключа, доступ к базе данных, оповещение listener'а в момент доступа.

<!-- Such common behaviors can be implemented as libraries using [delegated properties](delegated-properties.md). -->
Такие распространённые поведения свойств могут быть реализованы в виде библиотек с помощью [делегированных свойств](delegated-properties.html).
