---
type: doc
layout: reference
category: "Syntax"
title: "Объекты"
url: https://kotlinlang.ru/docs/object-declarations.html
---

<!-- При переводе статьи оригинальная версия была от 23 February 2025 -->

<!-- # Object declarations and expressions -->
# Объявления объектов и анонимные объекты

<!-- In Kotlin, objects allow you to define a class and create an instance of it in a single step.
This is useful when you need either a reusable singleton instance or a one-time object.
To handle these scenarios, Kotlin provides two key approaches: _object declarations_ for creating singletons and _object expressions_ for creating anonymous, one-time objects. -->
В Kotlin объекты позволяют определить класс и создать его экземпляр за один шаг.
Это удобно, когда вам нужен либо многократно используемый экземпляр-синглтон, либо одноразовый объект.
Для таких случаев Kotlin предоставляет два основных подхода: *объявления объектов* для создания синглтонов и
*анонимные объекты* для создания одноразовых объектов без имени.

<!-- > A singleton ensures that a class has only one instance and provides a global point of access to it. -->
> Синглтон гарантирует, что у класса есть только один экземпляр, и предоставляет глобальную точку доступа к нему.

<!-- Object declarations and object expressions are best used for scenarios when: -->
Объявления объектов и анонимные объекты лучше всего подходят для следующих сценариев:

<!-- * **Using singletons for shared resources:** You need to ensure that only one instance of a class exists throughout the application.
For example, managing a database connection pool.
* **Creating factory methods:** You need a convenient way to create instances efficiently.
[Companion objects](#companion-objects) allow you to define class-level functions and properties tied to a class, simplifying the creation and management of these instances.
* **Modifying existing class behavior temporarily:** You want to modify the behavior of an existing class without the need to create a new subclass.
For example, adding temporary functionality to an object for a specific operation.
* **Type-safe design is required:** You require one-time implementations of interfaces or [abstract classes](classes.md#abstract-classes) using object expressions.
This can be useful for scenarios like a button click handler. -->
* **Использование синглтонов для общих ресурсов.** Нужно гарантировать, что во всём приложении существует только один
экземпляр класса. Например, при управлении пулом подключений к базе данных.
* **Создание фабричных методов.** Нужен удобный способ эффективно создавать экземпляры.
[Вспомогательные объекты](#companion-objects) позволяют определять функции и свойства уровня класса, связанные с
классом, что упрощает создание таких экземпляров и управление ими.
* **Временное изменение поведения существующего класса.** Нужно изменить поведение существующего класса без создания
нового подкласса. Например, добавить объекту временную функциональность для конкретной операции.
* **Необходимость типобезопасного дизайна.** Нужны одноразовые реализации интерфейсов или
[абстрактных классов](classes.html#abstract-classes) с помощью анонимных объектов. Это может пригодиться, например,
для обработчика нажатия кнопки.

<a name="object-declarations-overview"></a>

<!-- ## Object declarations -->
## Объявления объектов (ориг.: *Object declarations*)

<!-- You can create single instances of objects in Kotlin using object declarations, which always have a name following the `object` keyword.
This allows you to define a class and create an instance of it in a single step, which is useful for implementing singletons: -->
В Kotlin вы можете создавать единственные экземпляры объектов с помощью объявлений объектов, у которых после ключевого
слова `object` всегда указывается имя. Это позволяет определить класс и создать его экземпляр за один шаг, что удобно
для реализации синглтонов.

```kotlin
// Объявляет объект-синглтон для управления поставщиками данных
object DataProviderManager {
    private val providers = mutableListOf<DataProvider>()

    // Регистрирует нового поставщика данных
    fun registerDataProvider(provider: DataProvider) {
        providers.add(provider)
    }

    // Возвращает всех зарегистрированных поставщиков данных
    val allDataProviders: Collection<DataProvider>
        get() = providers
}

// Пример интерфейса поставщика данных
interface DataProvider {
    fun provideData(): String
}

// Пример реализации поставщика данных
class ExampleDataProvider : DataProvider {
    override fun provideData(): String {
        return "Example data"
    }
}

fun main() {
    // Создаёт экземпляр ExampleDataProvider
    val exampleProvider = ExampleDataProvider()

    // Чтобы обратиться к объекту, используйте его имя напрямую
    DataProviderManager.registerDataProvider(exampleProvider)

    // Получает и печатает всех поставщиков данных
    println(DataProviderManager.allDataProviders.map { it.provideData() })
    // [Example data]
}
```

<!-- > The initialization of an object declaration is thread-safe and done on first access. -->
> Инициализация объявления объекта потокобезопасна и выполняется при первом доступе.

<!-- To refer to the `object`, use its name directly: -->
Чтобы обратиться к `object`, используйте его имя напрямую:

```kotlin
DataProviderManager.registerDataProvider(exampleProvider)
```

<!-- Object declarations can also have supertypes,
similar to how [anonymous objects can inherit from existing classes or implement interfaces](#inherit-anonymous-objects-from-supertypes): -->
У объявлений объектов также могут быть супертипы, как и у
[анонимных объектов, которые могут наследоваться от существующих классов или реализовывать интерфейсы](#inherit-anonymous-objects-from-supertypes):

```kotlin
object DefaultListener : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { ... }

    override fun mouseEntered(e: MouseEvent) { ... }
}
```

<!-- Like variable declarations, object declarations are not expressions, so they cannot be used on the right-hand side
of an assignment statement: -->
Как и объявления переменных, объявления объектов не являются выражениями, поэтому их нельзя использовать в правой части
оператора присваивания:

```kotlin
// Синтаксическая ошибка: анонимный объект не может связывать имя
val myObject = object MySingleton {
    val name = "Singleton"
}
```

<!-- Object declarations cannot be local, which means they cannot be nested directly inside a function.
However, they can be nested within other object declarations or non-inner classes. -->
Объявления объектов не могут быть локальными, то есть их нельзя вкладывать непосредственно в функцию.
Однако они могут быть вложены в другие объявления объектов или в классы, которые не являются `inner`.

<a name="data-objects"></a>

<!-- ### Data objects -->
### Объекты данных (`data object`)

<!-- When printing a plain object declaration in Kotlin, the string representation contains both its name and the hash of the `object`: -->
Когда в Kotlin печатается обычное объявление объекта, его строковое представление содержит имя и хеш `object`:

```kotlin
object MyObject

fun main() {
    println(MyObject)
    // MyObject@hashcode
}
```

<!-- However, by marking an object declaration with the `data` modifier,
you can instruct the compiler to return the actual name of the object when calling `toString()`, the same way it works for [data classes](data-classes.md): -->
Однако если пометить объявление объекта модификатором `data`, можно указать компилятору возвращать фактическое имя
объекта при вызове `toString()`, как это работает для [классов данных](data-classes.html):

```kotlin
data object MyDataObject {
    val number: Int = 3
}

fun main() {
    println(MyDataObject)
    // MyDataObject
}
```

<!-- Additionally, the compiler generates several functions for your `data object`: -->
Кроме того, компилятор генерирует для вашего `data object` несколько функций:

<!-- * `toString()` returns the name of the data object
* `equals()`/`hashCode()` enables equality checks and hash-based collections -->
* `toString()` возвращает имя объекта данных;
* `equals()`/`hashCode()` позволяют выполнять проверки равенства и использовать хеш-коллекции.

<!-- > You can't provide a custom `equals` or `hashCode` implementation for a `data object`. -->
> Для `data object` нельзя предоставить собственную реализацию `equals` или `hashCode`.

<!-- The `equals()` function for a `data object` ensures that all objects that have the type of your `data object` are considered equal.
In most cases, you will only have a single instance of your `data object` at runtime, since a `data object` declares a singleton.
However, in the edge case where another object of the same type is generated at runtime (for example, by using platform
reflection with `java.lang.reflect` or a JVM serialization library that uses this API under the hood), this ensures that
the objects are treated as being equal. -->
Функция `equals()` у `data object` гарантирует, что все объекты с типом вашего `data object` считаются равными.
В большинстве случаев во время выполнения у вас будет только один экземпляр `data object`, потому что `data object`
объявляет синглтон. Однако в пограничном случае, когда во время выполнения создаётся ещё один объект того же типа
(например, с помощью платформенной рефлексии через `java.lang.reflect` или JVM-библиотеки сериализации, которая
использует этот API внутренне), это гарантирует, что такие объекты будут считаться равными.

<!-- > Make sure that you only compare `data objects` structurally (using the `==` operator) and never by reference (using the `===` operator).
> This helps you to avoid pitfalls when more than one instance of a data object exists at runtime. -->
> Сравнивайте `data object` только структурно (с помощью оператора `==`) и никогда не сравнивайте их по ссылке
> (с помощью оператора `===`). Это помогает избежать ошибок, когда во время выполнения существует больше одного
> экземпляра объекта данных.

```kotlin
import java.lang.reflect.Constructor

data object MySingleton

fun main() {
    val evilTwin = createInstanceViaReflection()

    println(MySingleton)
    // MySingleton

    println(evilTwin)
    // MySingleton

    // Даже когда библиотека принудительно создаёт второй экземпляр MySingleton,
    // его функция equals() возвращает true:
    println(MySingleton == evilTwin)
    // true

    // Не сравнивайте объекты данных с помощью ===
    println(MySingleton === evilTwin)
    // false
}

fun createInstanceViaReflection(): MySingleton {
    // Рефлексия Kotlin не разрешает создавать экземпляры объектов данных.
    // Здесь новый экземпляр MySingleton создаётся "принудительно" с помощью платформенной рефлексии Java.
    // Не делайте так в своём коде!
    return (MySingleton.javaClass.declaredConstructors[0].apply { isAccessible = true } as Constructor<MySingleton>).newInstance()
}
```

<!-- The generated `hashCode()` function has a behavior that is consistent with the `equals()` function, so that all runtime
instances of a `data object` have the same hash code. -->
Поведение сгенерированной функции `hashCode()` согласовано с функцией `equals()`, поэтому у всех экземпляров
`data object`, существующих во время выполнения, один и тот же хеш-код.

<a name="differences-between-data-objects-and-data-classes"></a>

<!-- #### Differences between data objects and data classes -->
#### Различия между объектами данных и классами данных

<!-- While `data object` and `data class` declarations are often used together and have some similarities, there are some
functions that are not generated for a `data object`: -->
Хотя объявления `data object` и `data class` часто используются вместе и в чём-то похожи, некоторые функции для
`data object` не генерируются:

<!-- * No `copy()` function. Because a `data object` declaration is intended to be used as singletons, no `copy()`
  function is generated. Singletons restrict the instantiation of a class to a single instance, which would
  be violated by allowing copies of the instance to be created.
* No `componentN()` function. Unlike a `data class`, a `data object` does not have any data properties.
  Since attempting to destructure such an object without data properties wouldn't make sense, no `componentN()` functions are generated. -->
* Нет функции `copy()`. Поскольку объявление `data object` предназначено для использования в качестве синглтона,
  функция `copy()` не генерируется. Синглтоны ограничивают создание экземпляров класса одним экземпляром, а возможность
  создавать копии этого экземпляра нарушила бы это ограничение.
* Нет функций `componentN()`. В отличие от `data class`, у `data object` нет свойств данных. Так как попытка
  деструктурировать такой объект без свойств данных не имела бы смысла, функции `componentN()` не генерируются.

<a name="use-data-objects-with-sealed-hierarchies"></a>

<!-- #### Use data objects with sealed hierarchies -->
#### Использование объектов данных с изолированными иерархиями

<!-- Data object declarations are particularly useful for sealed hierarchies like
[sealed classes or sealed interfaces](sealed-classes.md).
They allow you to maintain symmetry with any data classes you may have defined alongside the object. -->
Объявления `data object` особенно полезны для изолированных иерархий, например
[изолированных классов или интерфейсов](sealed-classes.html). Они позволяют сохранить симметрию с классами данных,
которые вы могли определить рядом с объектом.

<!-- In this example, declaring `EndOfFile` as a `data object` instead of a plain `object`
means that it will get the `toString()` function without the need to override it manually: -->
В этом примере объявление `EndOfFile` как `data object` вместо обычного `object` означает, что объект получит функцию
`toString()` без необходимости переопределять её вручную:

```kotlin
sealed interface ReadResult
data class Number(val number: Int) : ReadResult
data class Text(val text: String) : ReadResult
data object EndOfFile : ReadResult

fun main() {
    println(Number(7))
    // Number(number=7)
    println(EndOfFile)
    // EndOfFile
}
```

<a name="companion-objects"></a>

<!-- ### Companion objects -->
### Вспомогательные объекты

<!-- _Companion objects_ allow you to define class-level functions and properties.
This makes it easy to create factory methods, hold constants, and access shared utilities. -->
*Вспомогательные объекты* позволяют определять функции и свойства уровня класса. Это упрощает создание фабричных
методов, хранение констант и доступ к общим утилитам.

<!-- An object declaration inside a class can be marked with the `companion` keyword: -->
Объявление объекта внутри класса может быть отмечено ключевым словом `companion`:

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```

<!-- Members of the `companion object` can be called simply by using the class name as the qualifier: -->
Члены `companion object` можно вызывать, просто используя имя класса в качестве определителя:

```kotlin
class User(val name: String) {
    // Определяет вспомогательный объект, который выступает фабрикой для создания экземпляров User
    companion object Factory {
        fun create(name: String): User = User(name)
    }
}

fun main(){
    // Вызывает фабричный метод вспомогательного объекта, используя имя класса в качестве определителя.
    // Создаёт новый экземпляр User
    val userInstance = User.create("John Doe")
    println(userInstance.name)
    // John Doe
}
```

<!-- The name of the `companion object` can be omitted, in which case the name `Companion` is used: -->
Имя `companion object` можно опустить. В этом случае используется имя `Companion`:

```kotlin
class User(val name: String) {
    // Определяет вспомогательный объект без имени
    companion object { }
}

// Обращается к вспомогательному объекту
val companionUser = User.Companion
```

<!-- Class members can access `private` members of their corresponding `companion object`: -->
Члены класса могут обращаться к `private` членам соответствующего `companion object`:

```kotlin
class User(val name: String) {
    companion object {
        private val defaultGreeting = "Hello"
    }

    fun sayHi() {
        println(defaultGreeting)
    }
}
User("Nick").sayHi()
// Hello
```

<!-- When a class name is used by itself, it acts as a reference to the companion object of the class,
regardless of whether the companion object is named or not: -->
Когда имя класса используется само по себе, оно действует как ссылка на вспомогательный объект класса независимо от того,
имеет этот объект имя или нет:

```kotlin
class User1 {
    // Определяет именованный вспомогательный объект
    companion object Named {
        fun show(): String = "User1's Named Companion Object"
    }
}

// Ссылается на вспомогательный объект User1 с помощью имени класса
val reference1 = User1

class User2 {
    // Определяет вспомогательный объект без имени
    companion object {
        fun show(): String = "User2's Companion Object"
    }
}

// Ссылается на вспомогательный объект User2 с помощью имени класса
val reference2 = User2

fun main() {
    // Вызывает функцию show() из вспомогательного объекта User1
    println(reference1.show())
    // User1's Named Companion Object

    // Вызывает функцию show() из вспомогательного объекта User2
    println(reference2.show())
    // User2's Companion Object
}
```

<!-- Although members of companion objects in Kotlin look like static members from other languages,
they are actually instance members of the companion object, meaning they belong to the object itself.
This allows companion objects to implement interfaces: -->
Хотя члены вспомогательных объектов в Kotlin выглядят как статические члены из других языков, на самом деле это члены
экземпляра вспомогательного объекта, то есть они принадлежат самому объекту. Благодаря этому вспомогательные объекты
могут реализовывать интерфейсы:

```kotlin
interface Factory<T> {
    fun create(name: String): T
}

class User(val name: String) {
    // Определяет вспомогательный объект, который реализует интерфейс Factory
    companion object : Factory<User> {
        override fun create(name: String): User = User(name)
    }
}

fun main() {
    // Использует вспомогательный объект как Factory
    val userFactory: Factory<User> = User
    val newUser = userFactory.create("Example User")
    println(newUser.name)
    // Example User
}
```

<!-- However, on the JVM, you can have members of companion objects generated as real static methods and fields if you use
the `@JvmStatic` annotation. See the [Java interoperability](java-to-kotlin-interop.md#static-fields) section
for more detail. -->
Однако на JVM вы можете сделать так, чтобы члены вспомогательных объектов генерировались как настоящие статические
методы и поля, если используете аннотацию `@JvmStatic`. Подробнее см. в разделе
[Совместимость с Java](java-to-kotlin-interop.html#static-fields).

<a name="object-expressions"></a>

<!-- ## Object expressions -->
## Анонимные объекты (ориг.: *Object expressions*)

<!-- Object expressions declare a class and create an instance of that class, but without naming either of them.
These classes are useful for one-time use. They can either be created from scratch, inherit from existing classes,
or implement interfaces. Instances of these classes are also called _anonymous objects_ because they are defined by
an expression, not a name. -->
Анонимные объекты объявляют класс и создают экземпляр этого класса, не задавая имени ни классу, ни экземпляру.
Такие классы полезны для одноразового использования. Их можно создавать с нуля, наследовать от существующих классов
или использовать для реализации интерфейсов. Экземпляры таких классов также называются *анонимными объектами*, потому
что они определяются выражением, а не именем.

<a name="create-anonymous-objects-from-scratch"></a>
<a name="creating-anonymous-objects-from-scratch"></a>

<!-- ### Create anonymous objects from scratch -->
### Создание анонимных объектов с нуля

<!-- Object expressions start with the `object` keyword. -->
Анонимные объекты начинаются с ключевого слова `object`.

<!-- If the object doesn't extend any classes or implement interfaces, you can define an object's members directly inside curly braces after the `object` keyword: -->
Если объект не расширяет классы и не реализует интерфейсы, его члены можно определить непосредственно внутри фигурных
скобок после ключевого слова `object`:

```kotlin
fun main() {
    val helloWorld = object {
        val hello = "Hello"
        val world = "World"
        // Анонимные объекты расширяют класс Any, в котором уже есть функция toString(),
        // поэтому её нужно переопределить
        override fun toString() = "$hello $world"
    }

    print(helloWorld)
    // Hello World
}
```

<a name="inherit-anonymous-objects-from-supertypes"></a>
<a name="inheriting-anonymous-objects-from-supertypes"></a>

<!-- ### Inherit anonymous objects from supertypes -->
### Наследование анонимных объектов от супертипов

<!-- To create an anonymous object that inherits from some type (or types), specify this type after `object` and a
colon `:`. Then implement or override the members of this class as if you were [inheriting](inheritance.md) from it: -->
Чтобы создать анонимный объект, наследующийся от какого-либо типа или типов, укажите этот тип после `object` и
двоеточия (`:`). Затем реализуйте или переопределите члены этого класса так, как если бы вы
[наследовали](inheritance.html) от него:

```kotlin
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { /*...*/ }

    override fun mouseEntered(e: MouseEvent) { /*...*/ }
})
```

<!-- If a supertype has a constructor, pass the appropriate constructor parameters to it.
Multiple supertypes can be specified, separated by commas, after the colon: -->
Если у супертипа есть конструктор, передайте в него соответствующие параметры.
Несколько супертипов можно указать после двоеточия, разделяя их запятыми:

```kotlin
// Создаёт open-класс BankAccount со свойством balance
open class BankAccount(initialBalance: Int) {
    open val balance: Int = initialBalance
}

// Определяет интерфейс Transaction с функцией execute()
interface Transaction {
    fun execute()
}

// Функция для выполнения специальной транзакции с BankAccount
fun specialTransaction(account: BankAccount) {
    // Создаёт анонимный объект, который наследуется от класса BankAccount и реализует интерфейс Transaction
    // Баланс переданного счёта передаётся в конструктор суперкласса BankAccount
    val temporaryAccount = object : BankAccount(account.balance), Transaction {

        override val balance = account.balance + 500  // Временный бонус

        // Реализует функцию execute() из интерфейса Transaction
        override fun execute() {
            println("Executing special transaction. New balance is $balance.")
        }
    }
    // Выполняет транзакцию
    temporaryAccount.execute()
}

fun main() {
    // Создаёт BankAccount с начальным балансом 1000
    val myAccount = BankAccount(1000)
    // Выполняет специальную транзакцию для созданного счёта
    specialTransaction(myAccount)
    // Executing special transaction. New balance is 1500.
}
```

<a name="use-anonymous-objects-as-return-and-value-types"></a>
<a name="using-anonymous-objects-as-return-and-value-types"></a>

<!-- ### Use anonymous objects as return and value types -->
### Использование анонимных объектов в качестве возвращаемых типов и типов значений

<!-- When you return an anonymous object from a local or [`private`](visibility-modifiers.md#packages) function or property,
all the members of that anonymous object are accessible through that function or property: -->
Когда вы возвращаете анонимный объект из локальной или [`private`](visibility-modifiers.html#packages) функции либо
свойства, все члены этого анонимного объекта доступны через эту функцию или свойство:

```kotlin
class UserPreferences {
    private fun getPreferences() = object {
        val theme: String = "Dark"
        val fontSize: Int = 14
    }

    fun printPreferences() {
        val preferences = getPreferences()
        println("Theme: ${preferences.theme}, Font Size: ${preferences.fontSize}")
    }
}

fun main() {
    val userPreferences = UserPreferences()
    userPreferences.printPreferences()
    // Theme: Dark, Font Size: 14
}
```

<!-- This allows you to return an anonymous object with specific properties,
offering a simple way to encapsulate data or behavior without creating a separate class. -->
Это позволяет возвращать анонимный объект с конкретными свойствами и даёт простой способ инкапсулировать данные или
поведение без создания отдельного класса.

<!-- If a function or property that returns an anonymous object has `public`, `protected`, or `internal` visibility, its actual type is: -->
Если функция или свойство, возвращающие анонимный объект, имеют видимость `public`, `protected` или `internal`, их
фактический тип:

<!-- * `Any` if the anonymous object doesn't have a declared supertype.
* The declared supertype of the anonymous object, if there is exactly one such type.
* The explicitly declared type if there is more than one declared supertype. -->
* `Any`, если у анонимного объекта нет объявленного супертипа;
* объявленный супертип анонимного объекта, если существует ровно один такой тип;
* явно объявленный тип, если объявленных супертипов больше одного.

<!-- In all these cases, members added in the anonymous object are not accessible. Overridden members are accessible if they
are declared in the actual type of the function or property. For example: -->
Во всех этих случаях члены, добавленные в анонимный объект, недоступны. Переопределённые члены доступны, если они
объявлены в фактическом типе функции или свойства. Например:

```kotlin
interface Notification {
    // Объявляет notifyUser() в интерфейсе Notification
    fun notifyUser()
}

interface DetailedNotification

class NotificationManager {
    // Возвращаемый тип - Any. Свойство message недоступно.
    // Когда возвращаемый тип - Any, доступны только члены класса Any.
    fun getNotification() = object {
        val message: String = "General notification"
    }

    // Возвращаемый тип - Notification, потому что анонимный объект реализует только один интерфейс.
    // Функция notifyUser() доступна, потому что она является частью интерфейса Notification.
    // Свойство message недоступно, потому что оно не объявлено в интерфейсе Notification.
    fun getEmailNotification() = object : Notification {
        override fun notifyUser() {
            println("Sending email notification")
        }
        val message: String = "You've got mail!"
    }

    // Возвращаемый тип - DetailedNotification. Функция notifyUser() и свойство message недоступны.
    // Доступны только члены, объявленные в интерфейсе DetailedNotification.
    fun getDetailedNotification(): DetailedNotification = object : Notification, DetailedNotification {
        override fun notifyUser() {
            println("Sending detailed notification")
        }
        val message: String = "Detailed message content"
    }
}

fun main() {
    // Этот код ничего не выводит
    val notificationManager = NotificationManager()

    // Свойство message здесь недоступно, потому что возвращаемый тип - Any
    // Этот код ничего не выводит
    val notification = notificationManager.getNotification()

    // Функция notifyUser() доступна.
    // Свойство message здесь недоступно, потому что возвращаемый тип - Notification.
    val emailNotification = notificationManager.getEmailNotification()
    emailNotification.notifyUser()
    // Sending email notification

    // Функция notifyUser() и свойство message здесь недоступны, потому что возвращаемый тип - DetailedNotification.
    // Этот код ничего не выводит
    val detailedNotification = notificationManager.getDetailedNotification()
}
```

<a name="access-variables-from-anonymous-objects"></a>
<a name="accessing-variables-from-anonymous-objects"></a>

<!-- ### Access variables from anonymous objects -->
### Доступ к переменным из анонимных объектов

<!-- Code within the body of object expressions can access variables from the enclosing scope: -->
Код внутри тела анонимного объекта может обращаться к переменным из окружающей области видимости:

```kotlin
import java.awt.event.MouseAdapter
import java.awt.event.MouseEvent

fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    // MouseAdapter предоставляет реализации по умолчанию для функций событий мыши
    // Имитирует обработку событий мыши с помощью MouseAdapter
    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++
        }
    })
    // Переменные clickCount и enterCount доступны внутри анонимного объекта
}
```

<a name="behavior-difference-between-object-declarations-and-expressions"></a>
<a name="semantic-difference-between-object-expressions-and-declarations"></a>

<!-- ## Behavior difference between object declarations and expressions -->
## Различия в поведении объявлений объектов и анонимных объектов

<!-- There are differences in the initialization behavior between object declarations and object expressions: -->
Между объявлениями объектов и анонимными объектами есть различия в поведении при инициализации:

<!-- * Object expressions are executed (and initialized) _immediately_, where they are used.
* Object declarations are initialized _lazily_, when accessed for the first time.
* A companion object is initialized when the corresponding class is loaded (resolved) that matches the semantics of a Java
  static initializer. -->
* анонимные объекты выполняются и инициализируются *сразу* в месте использования;
* объявления объектов инициализируются *лениво*, при первом доступе;
* вспомогательный объект инициализируется, когда соответствующий класс загружается (разрешается), что соответствует
  семантике статического инициализатора Java.
