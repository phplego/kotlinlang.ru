---
type: doc
layout: reference
category: "Syntax"
title: "Расширения"
url: https://kotlinlang.ru/docs/extensions.html
---

<!-- При обновлении статьи оригинальная версия была от 04 November 2025 -->

<!-- # Extensions -->
# Расширения (extensions)

*Расширения* Kotlin позволяют добавлять новую функциональность к классу или интерфейсу без наследования и без
паттернов вроде *Decorator*. Они полезны при работе со сторонними библиотеками, которые нельзя изменить напрямую.
После создания расширения вызываются так, как если бы они были членами исходного класса или интерфейса.

Наиболее распространённые формы расширений: [*функции-расширения*](#extension-functions) и
[*свойства-расширения*](#extension-properties).

Важно, что расширения не изменяют классы или интерфейсы, которые они расширяют. Когда вы объявляете расширение, вы не
добавляете новый член. Вы только делаете новую функцию вызываемой или новое свойство доступным с помощью того же
синтаксиса.

<a name="receivers"></a>

<!-- ## Receivers -->
## Приёмники

Расширения всегда вызываются на приёмнике. Приёмник должен иметь тот же тип, что и расширяемый класс или интерфейс.
Чтобы использовать расширение, укажите перед ним приёмник, затем `.` и имя функции или свойства.

Например, функция-расширение
[`.appendLine()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.text/append-line.html) из стандартной библиотеки
расширяет класс `StringBuilder`. В этом случае приёмник - это экземпляр `StringBuilder`, а *тип приёмника* -
`StringBuilder`:

```kotlin
fun main() {
//sampleStart
    // builder - это экземпляр StringBuilder
    val builder = StringBuilder()
        // Вызывает функцию-расширение .appendLine() на builder
        .appendLine("Hello")
        .appendLine()
        .appendLine("World")
    println(builder.toString())
    // Hello
    //
    // World
}
//sampleEnd
```

<a name="extension-functions"></a>

<!-- ## Extension functions -->
## Функции-расширения

Перед тем как создавать собственные функции-расширения, проверьте, нет ли нужной вам функции в
[стандартной библиотеке](https://kotlinlang.org/api/core/kotlin-stdlib/) Kotlin. Стандартная библиотека предоставляет
много полезных функций-расширений:

* Для работы с коллекциями:
  [`.map()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/map.html),
  [`.filter()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/filter.html),
  [`.reduce()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/reduce.html),
  [`.fold()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/fold.html),
  [`.groupBy()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/group-by.html).
* Для преобразования в строки:
  [`.joinToString()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/join-to-string.html).
* Для работы с null-значениями:
  [`.filterNotNull()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/filter-not-null.html).

Чтобы создать собственную функцию-расширение, добавьте к её имени префикс: тип приёмника и `.`. В этом примере функция
`.truncate()` расширяет класс `String`, поэтому тип приёмника - `String`:

```kotlin
fun String.truncate(maxLength: Int): String {
    return if (this.length <= maxLength) this else take(maxLength - 3) + "..."
}

fun main() {
    val shortUsername = "KotlinFan42"
    val longUsername = "JetBrainsLoverForever"

    println("Short username: ${shortUsername.truncate(15)}")
    // KotlinFan42
    println("Long username:  ${longUsername.truncate(15)}")
    // JetBrainsLov...
}
```

Функция `.truncate()` обрезает любую строку, на которой она вызвана, до длины, заданной аргументом `maxLength`, и
добавляет многоточие `...`. Если строка короче `maxLength`, функция возвращает исходную строку.

В следующем примере функция `.displayInfo()` расширяет интерфейс `User`:

```kotlin
interface User {
    val name: String
    val email: String
}

fun User.displayInfo(): String = "User(name=$name, email=$email)"

// Наследуется от интерфейса User и реализует его свойства
class RegularUser(override val name: String, override val email: String) : User

fun main() {
    val user = RegularUser("Alice", "alice@example.com")
    println(user.displayInfo())
    // User(name=Alice, email=alice@example.com)
}
```

Функция `.displayInfo()` возвращает строку с `name` и `email` экземпляра `RegularUser`. Такое расширение интерфейса
полезно, когда вы хотите один раз добавить функциональность для всех типов, реализующих этот интерфейс.

В следующем примере функция `.mostVoted()` расширяет тип `Map<String, Int>`:

```kotlin
fun Map<String, Int>.mostVoted(): String? {
    return maxByOrNull { (key, value) -> value }?.key
}

fun main() {
    val poll = mapOf(
        "Cats" to 37,
        "Dogs" to 58,
        "Birds" to 22
    )

    println("Top choice: ${poll.mostVoted()}")
    // Dogs
}
```

Функция `.mostVoted()` перебирает пары ключ-значение в карте, на которой она вызвана, и использует функцию
[`maxByOrNull()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/max-by-or-null.html), чтобы вернуть
ключ пары с наибольшим значением. Если карта пустая, функция `maxByOrNull()` возвращает `null`. Функция `mostVoted()`
использует безопасный вызов `?.`, чтобы обратиться к свойству `key` только тогда, когда `maxByOrNull()` вернула
non-null значение.

<a name="generic-extension-functions"></a>

<!-- ### Generic extension functions -->
### Обобщённые функции-расширения

Чтобы создать обобщённую функцию-расширение, объявите обобщённый тип-параметр перед именем функции. Так он будет
доступен в выражении типа приёмника. В этом примере функция `.endpoints()` расширяет `List<T>`, где `T` может быть
любым типом:

```kotlin
fun <T> List<T>.endpoints(): Pair<T, T> {
    return first() to last()
}

fun main() {
    val cities = listOf("Paris", "London", "Berlin", "Prague")
    val temperatures = listOf(21.0, 19.5, 22.3)

    val cityEndpoints = cities.endpoints()
    val tempEndpoints = temperatures.endpoints()

    println("First and last cities: $cityEndpoints")
    // (Paris, Prague)
    println("First and last temperatures: $tempEndpoints")
    // (21.0, 22.3)
}
```

Функция `.endpoints()` возвращает пару из первого и последнего элементов списка, на котором она вызвана. В теле функции
вызываются функции `first()` и `last()`, а их возвращаемые значения объединяются в `Pair` с помощью инфиксной функции
`to`.

Подробнее об обобщениях см. в разделе [Обобщённые функции](generics.html#generic-functions).

<a name="nullable-receiver"></a>
<a name="nullable-receivers"></a>

<!-- ### Nullable receivers -->
### Расширения null-допустимых типов

Вы можете определять функции-расширения для null-допустимого типа приёмника. Это позволяет вызывать их на переменной,
даже если её значение равно `null`. Когда приёмник равен `null`, `this` тоже равен `null`. Обрабатывайте
null-допустимость в таких функциях корректно: например, используйте проверки `this == null` в теле функции,
[безопасные вызовы `?.`](null-safety.html#safe-call-operator) или [Элвис-оператор `?:`](null-safety.html#elvis-operator).

В этом примере функцию `.toString()` можно вызвать без проверки на `null`, потому что проверка уже происходит внутри
функции-расширения:

```kotlin
fun main() {
    //sampleStart
    // Функция-расширение для null-допустимого Any
    fun Any?.toString(): String {
        if (this == null) return "null"
        // После проверки на null `this` smart cast к non-null Any
        // Поэтому этот вызов разрешается в обычную функцию toString()
        return toString()
    }

    val number: Int? = 42
    val nothing: Any? = null

    println(number.toString())
    // 42
    println(nothing.toString())
    // null
    //sampleEnd
}
```

<a name="extensions-are-resolved-statically"></a>
<a name="extension-or-member-functions"></a>

<!-- ### Extension or member functions? -->
### Функции-расширения или функции-члены?

Вызовы функций-расширений и функций-членов записываются одинаково, поэтому возникает вопрос: как компилятор понимает,
какую функцию использовать? Функции-расширения диспетчеризуются *статически*: компилятор определяет, какую функцию
вызывать, по типу приёмника на этапе компиляции. Например:

```kotlin
fun main() {
//sampleStart
    open class Shape
    class Rectangle: Shape()

    fun Shape.getName() = "Shape"
    fun Rectangle.getName() = "Rectangle"

    fun printClassName(shape: Shape) {
        println(shape.getName())
    }

    printClassName(Rectangle())
    // Shape
//sampleEnd
}
```

В этом примере компилятор вызывает функцию-расширение `Shape.getName()`, потому что параметр `shape` объявлен с типом
`Shape`. Так как функции-расширения разрешаются статически, компилятор выбирает функцию по объявленному типу, а не по
фактическому экземпляру.

Даже несмотря на то, что в примере передаётся экземпляр `Rectangle`, функция `.getName()` разрешается в
`Shape.getName()`, потому что переменная объявлена с типом `Shape`.

Если у класса есть функция-член и при этом объявлена функция-расширение с тем же типом приёмника, тем же именем и
совместимыми аргументами, приоритет имеет функция-член. Например:

```kotlin
fun main() {
//sampleStart
    class Example {
        fun printFunctionType() { println("Member function") }
    }

    fun Example.printFunctionType() { println("Extension function") }

    Example().printFunctionType()
    // Member function
//sampleEnd
}
```

Однако функции-расширения могут перегружать функции-члены с таким же именем, но *другой* сигнатурой:

```kotlin
fun main() {
//sampleStart
    class Example {
        fun printFunctionType() { println("Member function") }
    }

    // То же имя, но другая сигнатура
    fun Example.printFunctionType(index: Int) { println("Extension function #$index") }

    Example().printFunctionType(1)
    // Extension function #1
//sampleEnd
}
```

В этом примере в функцию `.printFunctionType()` передаётся `Int`, поэтому компилятор выбирает функцию-расширение: она
подходит по сигнатуре. Функция-член, которая не принимает аргументов, игнорируется.

<a name="anonymous-extension-functions"></a>

<!-- ### Anonymous extension functions -->
### Анонимные функции-расширения

Функции-расширения можно определять без имени. Это полезно, когда вы не хотите загромождать глобальное пространство имён
или когда нужно передать поведение расширения как параметр.

Например, предположим, что вы хотите расширить data class одноразовой функцией для расчёта доставки, не задавая ей имя:

```kotlin
fun main() {
    //sampleStart
    data class Order(val weight: Double)
    val calculateShipping = fun Order.(rate: Double): Double = this.weight * rate

    val order = Order(2.5)
    val cost = order.calculateShipping(3.0)
    println("Shipping cost: $cost")
    // Shipping cost: 7.5
}
```

Чтобы передать поведение расширения как параметр, используйте [лямбда-выражение](lambdas.html#lambda-expression-syntax)
с аннотацией типа. Например, вы хотите проверить, находится ли число в диапазоне, не объявляя именованную функцию:

```kotlin
fun main() {
    val isInRange: Int.(min: Int, max: Int) -> Boolean = { min, max -> this in min..max }

    println(5.isInRange(1, 10))
    // true
    println(20.isInRange(1, 10))
    // false
}
```

В этом примере переменная `isInRange` хранит функцию типа `Int.(min: Int, max: Int) -> Boolean`. Этот тип обозначает
функцию-расширение для класса `Int`, которая принимает параметры `min` и `max` и возвращает `Boolean`.

Тело лямбды `{ min, max -> this in min..max }` проверяет, попадает ли значение `Int`, на котором вызвана функция, в
диапазон между параметрами `min` и `max`. Если проверка успешна, лямбда возвращает `true`.

Подробнее см. в разделе [Лямбда-выражения и анонимные функции](lambdas.html).

<a name="extension-properties"></a>

<!-- ## Extension properties -->
## Свойства-расширения

Kotlin поддерживает свойства-расширения. Они полезны для преобразования данных или для создания вспомогательных
UI-свойств без засорения класса, с которым вы работаете.

Чтобы создать свойство-расширение, напишите имя класса, который хотите расширить, затем `.` и имя свойства.

Предположим, у вас есть data class, представляющий пользователя с именем и фамилией, и вы хотите создать свойство,
которое при обращении возвращает имя пользователя в стиле email. Код может выглядеть так:

```kotlin
data class User(val firstName: String, val lastName: String)

// Свойство-расширение для получения email-подобного имени пользователя
val User.emailUsername: String
    get() = "${firstName.lowercase()}.${lastName.lowercase()}"

fun main() {
    val user = User("Mickey", "Mouse")
    // Вызывает свойство-расширение
    println("Generated email username: ${user.emailUsername}")
    // Generated email username: mickey.mouse
}
```

Поскольку расширения фактически не добавляют члены в классы, у свойства-расширения нет эффективного способа иметь
[теневое поле](properties.html#backing-fields). Поэтому для свойств-расширений запрещены инициализаторы. Их поведение
можно определить только явным указанием геттеров и сеттеров. Например:

```kotlin
data class House(val streetName: String)

// Не компилируется, потому что нет геттера и сеттера
// var House.number = 1
// Ошибка: для свойств-расширений запрещены инициализаторы

// Успешно компилируется
val houseNumbers = mutableMapOf<House, Int>()
var House.number: Int
    get() = houseNumbers[this] ?: 1
    set(value) {
        println("Setting house number for ${this.streetName} to $value")
        houseNumbers[this] = value
    }

fun main() {
    val house = House("Maple Street")

    // Показывает значение по умолчанию
    println("Default number: ${house.number} ${house.streetName}")
    // Default number: 1 Maple Street

    house.number = 99
    // Setting house number for Maple Street to 99

    // Показывает обновлённое значение
    println("Updated number: ${house.number} ${house.streetName}")
    // Updated number: 99 Maple Street
}
```

В этом примере геттер использует [Элвис-оператор](null-safety.html#elvis-operator), чтобы вернуть номер дома, если он
есть в карте `houseNumbers`, или `1`. Подробнее о написании геттеров и сеттеров см. в разделе
[Пользовательские геттеры и сеттеры](properties.html#custom-getters-and-setters).

<a name="companion-object-extensions"></a>

<!-- ## Companion object extensions -->
## Расширения для companion object

Если класс объявляет [companion object](object-declarations.html#companion-objects), для этого объекта также можно
определять функции-расширения и свойства-расширения. Как и обычные члены companion object, их можно вызывать, используя
в качестве квалификатора только имя класса. По умолчанию компилятор называет companion object именем `Companion`:

```kotlin
class Logger {
    companion object { }
}

fun Logger.Companion.logStartupMessage() {
    println("Application started.")
}

fun main() {
    Logger.logStartupMessage()
    // Application started.
}
```

<a name="declaring-extensions-as-members"></a>

<!-- ## Declaring extensions as members -->
## Объявление расширений в качестве членов класса

Внутри одного класса можно объявлять расширения для другого класса. У таких расширений есть несколько *неявных
приёмников*. Неявный приёмник - это объект, к членам которого можно обращаться без квалификации с помощью
[`this`](this-expressions.html#qualified-this):

* Класс, в котором вы объявляете расширение, - это *диспетчерский приёмник*.
* Тип приёмника функции-расширения - это *приёмник расширения*.

Рассмотрим пример, где у класса `Connection` есть функция-расширение для класса `Host` с именем
`printConnectionString()`:

```kotlin
class Host(val hostname: String) {
    fun printHostname() { print(hostname) }
}

class Connection(val host: Host, val port: Int) {
    fun printPort() { print(port) }

    // Host - это приёмник расширения
    fun Host.printConnectionString() {
        // Вызывает Host.printHostname()
        printHostname()
        print(":")
        // Вызывает Connection.printPort()
        // Connection - это диспетчерский приёмник
        printPort()
    }

    fun connect() {
        /*...*/
        // Вызывает функцию-расширение
        host.printConnectionString()
    }
}

fun main() {
    Connection(Host("kotl.in"), 443).connect()
    // kotl.in:443

    // Вызывает ошибку, потому что функция-расширение недоступна вне Connection
    // Host("kotl.in").printConnectionString()
    // Unresolved reference 'printConnectionString'.
}
```

В этом примере функция `printConnectionString()` объявлена внутри класса `Connection`, поэтому класс `Connection` -
диспетчерский приёмник. Тип приёмника функции-расширения - класс `Host`, поэтому класс `Host` является приёмником
расширения.

Если у диспетчерского приёмника и приёмника расширения есть члены с одинаковыми именами, приоритет имеет член приёмника
расширения. Чтобы явно обратиться к диспетчерскому приёмнику, используйте
[синтаксис `this` с квалификатором](this-expressions.html#qualified-this):

```kotlin
class Connection {
    fun Host.getConnectionString() {
        // Вызывает Host.toString()
        toString()
        // Вызывает Connection.toString()
        this@Connection.toString()
    }
}
```

<a name="overriding-member-extensions"></a>

<!-- ### Overriding member extensions -->
### Переопределение расширений-членов

Расширения-члены можно объявлять как `open` и переопределять в подклассах. Это полезно, когда вы хотите настроить
поведение расширения для каждого подкласса. Компилятор обрабатывает каждый тип приёмника по-разному:

| Тип приёмника             | Время разрешения  | Тип диспетчеризации |
|---------------------------|-------------------|---------------------|
| Диспетчерский приёмник    | Время выполнения  | Виртуальная         |
| Приёмник расширения       | Время компиляции  | Статическая         |

Рассмотрим пример, где класс `User` объявлен как `open`, а класс `Admin` наследуется от него. Класс
`NotificationSender` определяет функции-расширения `sendNotification()` для классов `User` и `Admin`, а класс
`SpecialNotificationSender` переопределяет их:

```kotlin
open class User

class Admin : User()

open class NotificationSender {
    open fun User.sendNotification() {
        println("Sending user notification from normal sender")
    }

    open fun Admin.sendNotification() {
        println("Sending admin notification from normal sender")
    }

    fun notify(user: User) {
        user.sendNotification()
    }
}

class SpecialNotificationSender : NotificationSender() {
    override fun User.sendNotification() {
        println("Sending user notification from special sender")
    }

    override fun Admin.sendNotification() {
        println("Sending admin notification from special sender")
    }
}

fun main() {
    // Диспетчерский приёмник - NotificationSender
    // Приёмник расширения - User
    // Разрешается в User.sendNotification() из NotificationSender
    NotificationSender().notify(User())
    // Sending user notification from normal sender

    // Диспетчерский приёмник - SpecialNotificationSender
    // Приёмник расширения - User
    // Разрешается в User.sendNotification() из SpecialNotificationSender
    SpecialNotificationSender().notify(User())
    // Sending user notification from special sender

    // Диспетчерский приёмник - SpecialNotificationSender
    // Приёмник расширения - User, НЕ Admin
    // Функция notify() объявляет user с типом User
    // Статически разрешается в User.sendNotification() из SpecialNotificationSender
    SpecialNotificationSender().notify(Admin())
    // Sending user notification from special sender
}
```

Диспетчерский приёмник разрешается во время выполнения с помощью виртуальной диспетчеризации, поэтому поведение в
функции `main()` легко проследить. Но может удивить то, что при вызове функции `notify()` на экземпляре `Admin`
компилятор выбирает расширение по объявленному типу `user: User`, потому что приёмник расширения разрешается
статически.

<a name="note-on-visibility"></a>
<a name="extensions-and-visibility-modifiers"></a>

<!-- ## Extensions and visibility modifiers -->
## Расширения и модификаторы видимости

Расширения используют те же [модификаторы видимости](visibility-modifiers.html), что и обычные функции, объявленные в
той же области видимости, включая расширения, объявленные как члены других классов.

Например, расширение, объявленное на верхнем уровне файла, может обращаться к другим `private` объявлениям верхнего
уровня в том же файле:

```kotlin
// File: StringUtils.kt

private fun removeWhitespace(input: String): String {
    return input.replace("\\s".toRegex(), "")
}

fun String.cleaned(): String {
    return removeWhitespace(this)
}

fun main() {
    val rawEmail = "  user @example. com  "
    val cleaned = rawEmail.cleaned()
    println("Raw:     '$rawEmail'")
    // Raw:     '  user @example. com  '
    println("Cleaned: '$cleaned'")
    // Cleaned: 'user@example.com'
    println("Looks like an email: ${cleaned.contains("@") && cleaned.contains(".")}")
    // Looks like an email: true
}
```

Если расширение объявлено вне своего типа приёмника, оно не может обращаться к `private` или `protected` членам
приёмника:

```kotlin
class User(private val password: String) {
    fun isLoggedIn(): Boolean = true
    fun passwordLength(): Int = password.length
}

// Расширение объявлено вне класса
fun User.isSecure(): Boolean {
    // Нельзя обратиться к password, потому что оно private:
    // return password.length >= 8

    // Вместо этого опираемся на public-члены:
    return passwordLength() >= 8 && isLoggedIn()
}

fun main() {
    val user = User("supersecret")
    println("Is user secure: ${user.isSecure()}")
    // Is user secure: true
}
```

Если расширение помечено как `internal`, оно доступно только внутри своего
[модуля](visibility-modifiers.html#modules):

```kotlin
// Networking module
// JsonParser.kt
internal fun String.parseJson(): Map<String, Any> {
    return mapOf("fakeKey" to "fakeValue")
}
```

<a name="scope-of-extensions"></a>

<!-- ## Scope of extensions -->
## Область видимости расширений

В большинстве случаев расширения объявляют на верхнем уровне, непосредственно внутри пакетов:

```kotlin
package org.example.declarations

fun List<String>.getLongestString() { /*...*/}
```

Чтобы использовать расширение вне пакета, в котором оно объявлено, импортируйте его в месте вызова:

```kotlin
package org.example.usage

import org.example.declarations.getLongestString

fun main() {
    val list = listOf("red", "green", "blue")
    list.getLongestString()
}
```

Подробнее см. в разделе [Импорт](packages.html#imports).
