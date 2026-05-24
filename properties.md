---
type: doc
layout: reference
category: "Syntax"
title: "Свойства"
url: https://kotlinlang.ru/docs/properties.html
---

<!-- При переводе статьи оригинальная версия была от 10 September 2025 -->

<!-- # Properties -->
# Свойства

<!-- In Kotlin, properties let you store and manage data without writing functions to access or change the data.
You can use properties in [classes](classes.md), [interfaces](interfaces.md), [objects](object-declarations.md), [companion objects](object-declarations.md#companion-objects),
and even outside these structures as top-level properties. -->
В Kotlin свойства позволяют хранить данные и управлять ими без написания функций для доступа к этим данным или их изменения.
Вы можете использовать свойства в [классах](classes.html), [интерфейсах](interfaces.html), [объектах](object-declarations.html),
[объектах-компаньонах](object-declarations.html#companion-objects) и даже вне этих структур — как свойства верхнего уровня.

<!-- Every property has a name, a type, and an automatically generated `get()` function called a getter. You can use the getter
to read the property's value. If the property is mutable, it also has a `set()` function called a setter, which allows
you to change the property's value. -->
У каждого свойства есть имя, тип и автоматически сгенерированная функция `get()`, которая называется геттером.
С помощью геттера можно прочитать значение свойства. Если свойство изменяемое, у него также есть функция `set()`,
которая называется сеттером и позволяет изменить значение свойства.

<!-- > Getters and setters are called _accessors_. -->
> Геттеры и сеттеры называются *методами доступа*.

<a name="declaring-properties"></a>

<!-- ## Declaring properties -->
## Объявление свойств

<!-- Properties can be mutable (`var`) or read-only (`val`).
You can declare them as a top-level property in a `.kt` file. Think of a top-level property as a global variable
that belongs to a package: -->
Свойства могут быть изменяемыми (`var`) или доступными только для чтения (`val`).
Их можно объявлять как свойства верхнего уровня в файле `.kt`. Свойство верхнего уровня можно считать глобальной переменной,
которая принадлежит пакету:

```kotlin
// Файл: Constants.kt
package my.app

val pi = 3.14159
var counter = 0
```

<!-- You can also declare properties inside a class, interface, or object: -->
Вы также можете объявлять свойства внутри класса, интерфейса или объекта:

```kotlin
// Класс со свойствами
class Address {
    var name: String = "Holmes, Sherlock"
    var street: String = "Baker"
    var city: String = "London"
}

// Интерфейс со свойством
interface ContactInfo {
    val email: String
}

// Объект со свойствами
object Company {
    var name: String = "Detective Inc."
    val country: String = "UK"
}

// Класс, реализующий интерфейс
class PersonContact : ContactInfo {
    override val email: String = "sherlock@example.com"
}
```

<!-- To use a property, refer to it by its name: -->
Чтобы использовать свойство, обратитесь к нему по имени:

```kotlin
class Address {
    var name: String = "Holmes, Sherlock"
    var street: String = "Baker"
    var city: String = "London"
}

interface ContactInfo {
    val email: String
}

object Company {
    var name: String = "Detective Inc."
    val country: String = "UK"
}

class PersonContact : ContactInfo {
    override val email: String = "sherlock@example.com"
}

fun copyAddress(address: Address): Address {
    val result = Address()
    // Обращается к свойствам экземпляра result
    result.name = address.name
    result.street = address.street
    result.city = address.city
    return result
}

fun main() {
    val sherlockAddress = Address()
    val copy = copyAddress(sherlockAddress)
    // Обращается к свойствам экземпляра copy
    println("Copied address: ${copy.name}, ${copy.street}, ${copy.city}")
    // Copied address: Holmes, Sherlock, Baker, London

    // Обращается к свойствам объекта Company
    println("Company: ${Company.name} in ${Company.country}")
    // Company: Detective Inc. in UK

    val contact = PersonContact()
    // Обращается к свойствам экземпляра contact
    println("Email: ${contact.email}")
    // Email: sherlock@email.com
}
```

<!-- In Kotlin, we recommend initializing properties when you declare them to keep your code safe and easy to read. However,
you can [initialize them later](#late-initialized-properties-and-variables) in special cases. -->
В Kotlin рекомендуется инициализировать свойства при объявлении, чтобы код оставался безопасным и простым для чтения.
Однако в особых случаях свойства можно [инициализировать позже](#late-initialized-properties-and-variables).

<!-- Declaring the property type is optional if the compiler can infer it from the initializer or the getter's return type: -->
Указывать тип свойства необязательно, если компилятор может вывести его из инициализатора или из возвращаемого типа геттера:

```kotlin
var initialized = 1 // выведенный тип — Int
var allByDefault    // ошибка: свойство должно быть инициализировано
```

<a name="custom-getters-and-setters"></a>

<!-- ## Custom getters and setters -->
## Пользовательские геттеры и сеттеры

<!-- By default, Kotlin automatically generates getters and setters. You can define your own custom accessors when
you need extra logic, such as validation, formatting, or calculations based on other properties. -->
По умолчанию Kotlin автоматически генерирует геттеры и сеттеры.
Вы можете определить собственные методы доступа, когда нужна дополнительная логика: например, валидация, форматирование
или вычисления на основе других свойств.

<!-- A custom getter runs every time the property is accessed: -->
Пользовательский геттер выполняется при каждом обращении к свойству:

```kotlin
class Rectangle(val width: Int, val height: Int) {
    val area: Int
        get() = this.width * this.height
}

fun main() {
    val rectangle = Rectangle(3, 4)
    println("Width=${rectangle.width}, height=${rectangle.height}, area=${rectangle.area}")
}
```

<!-- You can omit the type if the compiler can infer it from the getter: -->
Тип можно опустить, если компилятор может вывести его из геттера:

```kotlin
val area get() = this.width * this.height
```

<!-- A custom setter runs every time you assign a value to the property, except during initialization.
By convention, the name of the setter parameter is `value`, but you can choose a different name: -->
Пользовательский сеттер выполняется при каждом присваивании значения свойству, кроме инициализации.
По соглашению параметр сеттера называется `value`, но вы можете выбрать другое имя:

```kotlin
class Point(var x: Int, var y: Int) {
    var coordinates: String
        get() = "$x,$y"
        set(value) {
            val parts = value.split(",")
            x = parts[0].toInt()
            y = parts[1].toInt()
        }
}

fun main() {
    val location = Point(1, 2)
    println(location.coordinates)
    // 1,2

    location.coordinates = "10,20"
    println("${location.x}, ${location.y}")
    // 10, 20
}
```

<a name="changing-visibility-or-adding-annotations"></a>

<!-- ### Changing visibility or adding annotations -->
### Изменение видимости или добавление аннотаций

<!-- In Kotlin, you can change accessor visibility or add [annotations](annotations.md) without replacing the default implementation.
You don't have to make these changes within a body `{}`. -->
В Kotlin можно изменить видимость метода доступа или добавить [аннотации](annotations.html), не заменяя реализацию по умолчанию.
Для таких изменений не нужно объявлять тело `{}`.

<!-- To change the visibility of an accessor, use the modifier before the `get` or `set` keyword: -->
Чтобы изменить видимость метода доступа, укажите модификатор перед ключевым словом `get` или `set`:

```kotlin
class BankAccount(initialBalance: Int) {
    var balance: Int = initialBalance
        // Только класс может изменять баланс
        private set

    fun deposit(amount: Int) {
        if (amount > 0) balance += amount
    }

    fun withdraw(amount: Int) {
        if (amount > 0 && amount <= balance) balance -= amount
    }
}

fun main() {
    val account = BankAccount(100)
    println("Initial balance: ${account.balance}")
    // 100

    account.deposit(50)
    println("After deposit: ${account.balance}")
    // 150

    account.withdraw(70)
    println("After withdrawal: ${account.balance}")
    // 80

    // account.balance = 1000
    // Ошибка: присваивание невозможно, потому что сеттер приватный
}
```

<!-- To annotate an accessor, use the annotation before the `get` or `set` keyword: -->
Чтобы аннотировать метод доступа, укажите аннотацию перед ключевым словом `get` или `set`:

```kotlin
// Определяет аннотацию, которую можно применить к геттеру
@Target(AnnotationTarget.PROPERTY_GETTER)
annotation class Inject

class Service {
    var dependency: String = "Default Service"
        // Аннотирует геттер
        @Inject get
}

fun main() {
    val service = Service()
    println(service.dependency)
    // Default service
    println(service::dependency.getter.annotations)
    // [@Inject()]
    println(service::dependency.setter.annotations)
    // []
}
```

<!-- This example uses [reflection](reflection.md) to show which annotations are present on the getter and setter. -->
В этом примере используется [рефлексия](reflection.html), чтобы показать, какие аннотации присутствуют у геттера и сеттера.

<a name="backing-fields"></a>

<!-- ### Backing fields -->
### Теневые поля

<!-- In Kotlin, accessors use backing fields to store the property's value in memory. Backing fields are useful
when you want to add extra logic to a getter or setter, or when you want to trigger an additional action whenever the property
changes. -->
В Kotlin методы доступа используют теневые поля для хранения значения свойства в памяти.
Теневые поля полезны, когда нужно добавить дополнительную логику в геттер или сеттер либо выполнить дополнительное действие
при каждом изменении свойства.

<!-- You can't declare backing fields directly. Kotlin generates them only when necessary. You can reference the backing field
in accessors using the `field` keyword. -->
Нельзя объявлять теневые поля напрямую. Kotlin генерирует их только при необходимости.
В методах доступа можно обратиться к теневому полю с помощью ключевого слова `field`.

<!-- Kotlin only generates backing fields if you use the default getter or setter, or if you use `field` in at least one custom accessor. -->
Kotlin генерирует теневые поля только в том случае, если используется геттер или сеттер по умолчанию
либо если `field` используется хотя бы в одном пользовательском методе доступа.

<!-- For example, the `isEmpty` property has no backing field because it uses a custom getter without the `field` keyword: -->
Например, у свойства `isEmpty` нет теневого поля, потому что оно использует пользовательский геттер без ключевого слова `field`:

```kotlin
val isEmpty: Boolean
    get() = this.size == 0
```

<!-- In this example, the `score` property has a backing field because the setter uses the `field` keyword: -->
В этом примере у свойства `score` есть теневое поле, потому что сеттер использует ключевое слово `field`:

```kotlin
class Scoreboard {
    var score: Int = 0
        set(value) {
            field = value
            // Добавляет логирование при обновлении значения
            println("Score updated to $field")
        }
}

fun main() {
    val board = Scoreboard()
    board.score = 10
    // Score updated to 10
    board.score = 20
    // Score updated to 20
}
```

<a name="backing-properties"></a>

<!-- ### Backing properties -->
### Теневые свойства

<!-- Sometimes you might need more flexibility than using a [backing field](#backing-fields) can provide. For example, if you have an API
where you want to be able to modify the property internally but not externally. In such cases, you can use a coding pattern
called a _backing property_. -->
Иногда может потребоваться больше гибкости, чем может дать [теневое поле](#backing-fields).
Например, если в API нужно разрешить изменять свойство внутри класса, но запретить изменять его извне.
В таких случаях можно использовать шаблон кода, который называется *теневым свойством*.

<!-- In the following example, the `ShoppingCart` class has an `items` property that represents everything in the shopping cart.
You want the `items` property to be read-only outside the class but still allow one "approved" way for the user to modify
the `items` property directly. To achieve this, you can define a private backing property called `_items` and a public property
called `items` that delegates to the backing property's value. -->
В следующем примере у класса `ShoppingCart` есть свойство `items`, которое представляет содержимое корзины покупок.
Нужно, чтобы свойство `items` было доступно только для чтения вне класса, но при этом оставался один "разрешённый" способ
напрямую изменять это свойство. Для этого можно определить приватное теневое свойство `_items` и публичное свойство `items`,
которое делегирует значение теневому свойству.

```kotlin
class ShoppingCart {
    // Теневое свойство
    private val _items = mutableListOf<String>()

    // Публичное представление только для чтения
    val items: List<String>
        get() = _items

    fun addItem(item: String) {
        _items.add(item)
    }

    fun removeItem(item: String) {
        _items.remove(item)
    }
}

fun main() {
    val cart = ShoppingCart()
    cart.addItem("Apple")
    cart.addItem("Banana")

    println(cart.items)
    // [Apple, Banana]

    cart.removeItem("Apple")
    println(cart.items)
    // [Banana]
}
```

<!-- In this example, the user can only add items to the cart through the `addItem()` function, but can still access the
`items` property to see what's inside. -->
В этом примере пользователь может добавлять элементы в корзину только через функцию `addItem()`, но всё ещё может обращаться
к свойству `items`, чтобы посмотреть содержимое корзины.

<!-- > Use a leading underscore when naming backing properties to follow Kotlin [coding conventions](coding-conventions.md#names-for-backing-properties). -->
> Используйте подчёркивание в начале имени теневого свойства, чтобы следовать [соглашениям по оформлению кода](coding-conventions.html#names-for-backing-properties) Kotlin.

<!-- On the JVM, the compiler optimizes access to private properties with default accessors to avoid function call overhead. -->
На JVM компилятор оптимизирует доступ к приватным свойствам со стандартными методами доступа, чтобы избежать накладных расходов
на вызов функции.

<!-- Backing properties are also useful when you want more than one public property to share a state. For example: -->
Теневые свойства также полезны, когда нужно, чтобы несколько публичных свойств разделяли одно состояние. Например:

```kotlin
class Temperature {
    // Теневое свойство, которое хранит температуру в градусах Цельсия
    private var _celsius: Double = 0.0

    var celsius: Double
        get() = _celsius
        set(value) { _celsius = value }

    var fahrenheit: Double
        get() = _celsius * 9 / 5 + 32
        set(value) { _celsius = (value - 32) * 5 / 9 }
}

fun main() {
    val temp = Temperature()
    temp.celsius = 25.0
    println("${temp.celsius}°C = ${temp.fahrenheit}°F")
    // 25.0°C = 77.0°F

    temp.fahrenheit = 212.0
    println("${temp.celsius}°C = ${temp.fahrenheit}°F")
    // 100.0°C = 212.0°F
}
```

<!-- In this example, the `_celsius` backing property is accessed by both the `celsius` and `fahrenheit` properties. This setup
provides a single source of truth with two public views. -->
В этом примере к теневому свойству `_celsius` обращаются оба свойства: `celsius` и `fahrenheit`.
Такая схема предоставляет единый источник истины с двумя публичными представлениями.

<a name="compile-time-constants"></a>

<!-- ## Compile-time constants -->
## Константы времени компиляции

<!-- If the value of a read-only property is known at compile time, mark it as a _compile-time constant_ using the `const` modifier.
Compile-time constants are inlined at compile time, so each reference is replaced with its actual value. They are accessed
more efficiently because no getter is called: -->
Если значение свойства, доступного только для чтения, известно во время компиляции, пометьте его как *константу времени компиляции*
с помощью модификатора `const`. Константы времени компиляции подставляются в код во время компиляции, поэтому каждая ссылка
заменяется фактическим значением. Доступ к ним эффективнее, потому что геттер не вызывается:

```kotlin
// Файл: AppConfig.kt
package com.example

// Константа времени компиляции
const val MAX_LOGIN_ATTEMPTS = 3
```

<!-- Compile-time constants must meet the following requirements: -->
Константы времени компиляции должны соответствовать следующим требованиям:

<!-- * They must be either a top-level property, or a member of an [`object` declaration](object-declarations.md#object-declarations-overview) or a [companion object](object-declarations.md#companion-objects).
* They must be initialized with a value of type `String` or a [primitive type](types-overview.md).
* They can't have a custom getter. -->
* Они должны быть свойством верхнего уровня либо членом [объявления `object`](object-declarations.html#object-declarations-overview)
или [объекта-компаньона](object-declarations.html#companion-objects).
* Они должны быть инициализированы значением типа `String` или [примитивного типа](https://kotlinlang.org/docs/basic-types.html).
* У них не может быть пользовательского геттера.

<!-- Compile-time constants still have a backing field, so you can interact with them using [reflection](reflection.md). -->
У констант времени компиляции всё равно есть теневое поле, поэтому с ними можно работать с помощью [рефлексии](reflection.html).

<!-- You can also use these properties in annotations: -->
Такие свойства также можно использовать в аннотациях:

```kotlin
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"

@Deprecated(SUBSYSTEM_DEPRECATED) fun processLegacyOrders() { ... }
```

<a name="late-initialized-properties-and-variables"></a>

<!-- ## Late-initialized properties and variables -->
## Свойства и переменные с поздней инициализацией

<!-- Normally, you must initialize properties in the constructor.
However, this isn't always convenient. For example, you might initialize properties through dependency
injection or inside the setup method of a unit test. -->
Обычно свойства нужно инициализировать в конструкторе.
Однако это не всегда удобно. Например, свойства можно инициализировать через внедрение зависимостей
или внутри установочного метода модульного теста.

<!-- To handle these situations, mark the property with the `lateinit` modifier: -->
Чтобы обработать такие ситуации, пометьте свойство модификатором `lateinit`:

```kotlin
public class OrderServiceTest {
    lateinit var orderService: OrderService

    @SetUp fun setup() {
        orderService = OrderService()
    }

    @Test fun processesOrderSuccessfully() {
        // Вызывает orderService напрямую, без проверки на null
        // или порядок инициализации
        orderService.processOrder()
    }
}
```

<!-- You can use the `lateinit` modifier on `var` properties declared as: -->
Модификатор `lateinit` можно использовать со свойствами `var`, объявленными как:

<!-- * Top-level properties.
* Local variables.
* Properties inside the body of a class. -->
* Свойства верхнего уровня.
* Локальные переменные.
* Свойства внутри тела класса.

<!-- For class properties: -->
Для свойств класса:

<!-- * You can't declare them in the primary constructor.
* They must not have a custom getter or setter. -->
* Их нельзя объявлять в основном конструкторе.
* У них не должно быть пользовательского геттера или сеттера.

<!-- In all cases, the property or variable must be non-nullable and must not be a [primitive type](types-overview.md). -->
Во всех случаях свойство или переменная должны иметь тип, не допускающий `null`, и не должны быть [примитивного типа](https://kotlinlang.org/docs/basic-types.html).

<!-- If you access a `lateinit` property before initializing it, Kotlin throws a specific exception that identifies the uninitialized
property being accessed: -->
Если обратиться к свойству `lateinit` до его инициализации, Kotlin выбросит специальное исключение, которое указывает
на неинициализированное свойство:

```kotlin
class ReportGenerator {
    lateinit var report: String

    fun printReport() {
        // Выбрасывает исключение, потому что свойство используется
        // до инициализации
        println(report)
    }
}

fun main() {
    val generator = ReportGenerator()
    generator.printReport()
    // Exception in thread "main" kotlin.UninitializedPropertyAccessException: lateinit property report has not been initialized
}
```

<!-- To check whether a `lateinit var` has already been initialized, use the [`isInitialized`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin/is-initialized.html)
property on the [reference to that property](reflection.md#property-references): -->
Чтобы проверить, было ли свойство `lateinit var` уже инициализировано, используйте свойство
[`isInitialized`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin/is-initialized.html)
на [ссылке на это свойство](reflection.html#property-references):

```kotlin
class WeatherStation {
    lateinit var latestReading: String

    fun printReading() {
        // Проверяет, инициализировано ли свойство
        if (this::latestReading.isInitialized) {
            println("Latest reading: $latestReading")
        } else {
            println("No reading available")
        }
    }
}

fun main() {
    val station = WeatherStation()

    station.printReading()
    // No reading available
    station.latestReading = "22°C, sunny"
    station.printReading()
    // Latest reading: 22°C, sunny
}
```

<!-- You can only use `isInitialized` on a property if you can already access that property in your code. The property must be declared
in the same class, in an outer class, or as a top-level property in the same file. -->
Использовать `isInitialized` для свойства можно только в том случае, если это свойство уже доступно из вашего кода.
Свойство должно быть объявлено в том же классе, во внешнем классе или как свойство верхнего уровня в том же файле.

<a name="overriding-properties"></a>

<!-- ## Overriding properties -->
## Переопределение свойств

<!-- See [Overriding properties](inheritance.md#overriding-properties). -->
См. [Переопределение свойств](inheritance.html#overriding-properties).

<a name="delegated-properties"></a>

<!-- ## Delegated properties -->
## Делегированные свойства

<!-- To reuse logic and reduce code duplication, you can delegate the responsibility of getting and setting a property to a
separate object. -->
Чтобы переиспользовать логику и уменьшить дублирование кода, можно делегировать ответственность за получение и установку
значения свойства отдельному объекту.

<!-- Delegating accessor behavior keeps the property's accessor logic centralized, making it easier to reuse. This approach
is useful when implementing behaviors like: -->
Делегирование поведения методов доступа централизует логику доступа к свойству, упрощая её переиспользование.
Такой подход полезен при реализации поведения вроде:

<!-- * Computing a value lazily.
* Reading from a map by a given key.
* Accessing a database.
* Notifying a listener when a property is accessed. -->
* Ленивого вычисления значения.
* Чтения из ассоциативного массива по заданному ключу.
* Доступа к базе данных.
* Уведомления слушателя при обращении к свойству.

<!-- You can implement these common behaviors in libraries yourself or use existing delegates provided by external libraries.
For more information, see [delegated properties](delegated-properties.md). -->
Эти распространённые варианты поведения можно самостоятельно реализовать в библиотеках или использовать существующие делегаты
из внешних библиотек. Подробнее см. в разделе [делегированные свойства](delegated-properties.html).
