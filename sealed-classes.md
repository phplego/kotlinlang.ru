---
type: doc
layout: reference
category: "Classes and Objects"
title: "Изолированные классы"
url: https://kotlinlang.ru/docs/sealed-classes.html
---

<!-- При обновлении статьи использовалась официальная документация Kotlin, проверенная 24 May 2026 -->

<!-- # Sealed classes and interfaces -->
# Изолированные классы и интерфейсы

<!-- _Sealed_ classes and interfaces provide controlled inheritance of your class hierarchies.
All direct subclasses of a sealed class are known at compile time. No other subclasses may appear outside the module and
package within which the sealed class is defined. The same logic applies to sealed interfaces and their implementations:
once a module with a sealed interface is compiled, no new implementations can be created. -->
Изолированные классы и интерфейсы обеспечивают контролируемое наследование в иерархиях классов. Во время компиляции
известны все прямые наследники изолированного класса. Никакие другие наследники не могут появиться за пределами модуля
и пакета, в которых определён изолированный класс. Та же логика применима к изолированным интерфейсам и их реализациям:
после компиляции модуля с изолированным интерфейсом нельзя создать новые реализации.

<!-- > Direct subclasses are classes that immediately inherit from their superclass.
>
> Indirect subclasses are classes that inherit from more than one level down from their superclass. -->
> **Примечание.** Прямые наследники — это классы, которые непосредственно наследуют свой суперкласс.
>
> Непрямые наследники — это классы, которые находятся ниже суперкласса более чем на один уровень наследования.

<!-- When you combine sealed classes and interfaces with the `when` expression, you can cover the behavior of all possible
subclasses and ensure that no new subclasses are created to affect your code adversely. -->
Когда вы сочетаете изолированные классы и интерфейсы с выражением `when`, вы можете покрыть поведение всех возможных
наследников и убедиться, что новые наследники не появятся и не повлияют на код нежелательным образом.

<!-- Sealed classes are best used for scenarios when: -->
Изолированные классы лучше всего использовать в следующих случаях:

<!-- * **Limited class inheritance is desired:** You have a predefined, finite set of subclasses that extend a class, all of which are known at compile time.
* **Type-safe design is required:** Safety and pattern matching are crucial in your project. Particularly for state management or handling complex conditional logic. For an example, check out [Use sealed classes with when expressions](#use-sealed-classes-with-when-expression).
* **Working with closed APIs:** You want robust and maintainable public APIs for libraries that ensure that third-party clients use the APIs as intended. -->
* **Нужно ограниченное наследование классов:** у вас есть заранее определённый конечный набор наследников, расширяющих
  класс, и все они известны во время компиляции.
* **Требуется типобезопасный дизайн:** в проекте важны безопасность и сопоставление с образцом, особенно при управлении
  состоянием или обработке сложной условной логики. Пример см. в разделе
  [Использование изолированных классов с выражениями when](#use-sealed-classes-with-when-expression).
* **Вы работаете с закрытыми API:** вам нужны надёжные и поддерживаемые публичные API библиотек, которые гарантируют,
  что сторонние клиенты используют эти API по назначению.

<!-- For more detailed practical applications, see [Use case scenarios](#use-case-scenarios). -->
Более подробные практические примеры см. в разделе [Сценарии использования](#use-case-scenarios).

<!-- > Java 15 introduced [a similar concept](https://docs.oracle.com/en/java/javase/15/language/sealed-classes-and-interfaces.html#GUID-0C709461-CC33-419A-82BF-61461336E65F),
> where sealed classes use the `sealed` keyword along with the `permits` clause to define restricted hierarchies. -->
> **Совет.** В Java 15 появилась [похожая концепция](https://docs.oracle.com/en/java/javase/15/language/sealed-classes-and-interfaces.html#GUID-0C709461-CC33-419A-82BF-61461336E65F):
> sealed-классы используют ключевое слово `sealed` вместе с секцией `permits`, чтобы определять ограниченные иерархии.

<!-- ## Declare a sealed class or interface -->
## Объявление изолированного класса или интерфейса

<!-- To declare a sealed class or interface, use the `sealed` modifier: -->
Чтобы объявить изолированный класс или интерфейс, используйте модификатор `sealed`.

```kotlin
// Создание изолированного интерфейса
sealed interface Error

// Создание изолированного класса, реализующего изолированный интерфейс Error
sealed class IOError(): Error

// Определение наследников, расширяющих изолированный класс 'IOError'
class FileReadError(val file: File): IOError()
class DatabaseError(val source: DataSource): IOError()

// Создание объекта-синглтона, реализующего изолированный интерфейс 'Error'
object RuntimeError : Error
```

<!-- This example could represent a library's API that contains error classes to let library users handle errors that it can throw.
If the hierarchy of such error classes includes interfaces or abstract classes visible in the public API, then nothing
prevents other developers from implementing or extending them in the client code.
Since the library doesn't know about errors declared outside of it, it can't treat them consistently with its own classes.
However, with a **sealed** hierarchy of error classes, library authors can be sure that they know all the possible error
types and that other error types can't appear later. -->
Этот пример может представлять API библиотеки, содержащий классы ошибок, чтобы пользователи библиотеки могли
обрабатывать ошибки, которые она может выбрасывать. Если иерархия таких классов ошибок включает интерфейсы или
абстрактные классы, видимые в публичном API, ничто не мешает другим разработчикам реализовывать или расширять их в
клиентском коде. Поскольку библиотека не знает об ошибках, объявленных за её пределами, она не может обрабатывать их
согласованно со своими собственными классами. Однако при **изолированной** иерархии классов ошибок авторы библиотеки
могут быть уверены, что знают все возможные типы ошибок и что другие типы ошибок не появятся позже.

<!-- The hierarchy of the example looks like this: -->
Иерархия из примера выглядит так:

![Иллюстрация иерархии изолированных классов и интерфейсов](https://kotlinlang.org/docs/images/sealed-classes-interfaces.svg)

<!-- ### Constructors -->
### Конструкторы

<!-- A sealed class itself is always an [abstract class](classes.md#abstract-classes), and as a result, can't be instantiated directly.
However, it may contain or inherit constructors. These constructors aren't for creating instances of the sealed class itself
but for its subclasses. Consider the following example with a sealed class called `Error` and its several subclasses,
which we instantiate: -->
Сам изолированный класс всегда является [абстрактным классом](classes.html#abstract-classes), поэтому его нельзя
создать напрямую. Однако он может содержать или наследовать конструкторы. Эти конструкторы предназначены не для создания
экземпляров самого изолированного класса, а для его наследников. Рассмотрим следующий пример с изолированным классом
`Error` и несколькими его наследниками, экземпляры которых мы создаём:

```kotlin
sealed class Error(val message: String) {
    class NetworkError : Error("Network failure")
    class DatabaseError : Error("Database cannot be reached")
    class UnknownError : Error("An unknown error has occurred")
}

fun main() {
    val errors = listOf(Error.NetworkError(), Error.DatabaseError(), Error.UnknownError())
    errors.forEach { println(it.message) }
}
// Network failure
// Database cannot be reached
// An unknown error has occurred
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.5"}

<!-- You can use [`enum`](enum-classes.md) classes within your sealed classes to use enum constants to represent states and provide
additional detail. Each enum constant exists only as a **single** instance, while subclasses of a sealed class may
have **multiple** instances.
In the example, the `sealed class Error` along with its several subclasses, employs an `enum` to denote error severity.
Each subclass constructor initializes the `severity` and can alter its state: -->
Внутри изолированных классов можно использовать [классы `enum`](enum-classes.html), чтобы представлять состояния с
помощью enum-констант и добавлять дополнительные сведения. Каждая enum-константа существует только как
**единственный** экземпляр, тогда как у наследников изолированного класса может быть **несколько** экземпляров. В
примере `sealed class Error` вместе со своими наследниками использует `enum` для обозначения серьёзности ошибки.
Конструктор каждого наследника инициализирует `severity` и может менять своё состояние:

```kotlin
enum class ErrorSeverity { MINOR, MAJOR, CRITICAL }

sealed class Error(val severity: ErrorSeverity) {
    class FileReadError(val file: File): Error(ErrorSeverity.MAJOR)
    class DatabaseError(val source: DataSource): Error(ErrorSeverity.CRITICAL)
    object RuntimeError : Error(ErrorSeverity.CRITICAL)
    // Здесь можно добавить другие типы ошибок
}
```

<!-- Constructors of sealed classes can have one of two [visibilities](visibility-modifiers.md): `protected` (by default) or
`private`: -->
Конструкторы изолированных классов могут иметь одну из двух [видимостей](visibility-modifiers.html): `protected` (по
умолчанию) или `private`.

```kotlin
sealed class IOError {
    // Конструктор изолированного класса по умолчанию имеет видимость protected.
    // Он виден внутри этого класса и его наследников.
    constructor() { /*...*/ }

    // Приватный конструктор виден только внутри этого класса.
    // Приватный конструктор в изолированном классе позволяет ещё строже контролировать создание экземпляров
    // и выполнять специальные процедуры инициализации внутри класса.
    private constructor(description: String): this() { /*...*/ }

    // Это вызовет ошибку, потому что public- и internal-конструкторы в изолированных классах не допускаются.
    // public constructor(code: Int): this() {}
}
```

<a name="location-of-direct-subclasses"></a>

<!-- ## Inheritance -->
## Наследование

<!-- Direct subclasses of sealed classes and interfaces must be declared in the same package. They may be top-level or nested
inside any number of other named classes, named interfaces, or named objects. Subclasses can have any [visibility](visibility-modifiers.md)
as long as they are compatible with normal inheritance rules in Kotlin. -->
Прямые наследники изолированных классов и интерфейсов должны быть объявлены в том же пакете. Они могут находиться на
верхнем уровне или быть вложенными в любое количество других именованных классов, именованных интерфейсов или
именованных объектов. У наследников может быть любая [видимость](visibility-modifiers.html), если она совместима с
обычными правилами наследования в Kotlin.

<!-- Subclasses of sealed classes must have a properly qualified name. They can't be local or anonymous objects. -->
Наследники изолированных классов должны иметь корректное квалифицированное имя. Они не могут быть локальными или
анонимными объектами.

<!-- > `enum` classes can't extend a sealed class, or any other class. However, they can implement sealed interfaces: -->
> **Примечание.** Классы `enum` не могут расширять изолированный класс или любой другой класс. Однако они могут
> реализовывать изолированные интерфейсы:
>
> ```kotlin
> sealed interface Error
>
> // enum-класс расширяет изолированный интерфейс Error
> enum class ErrorType : Error {
>     FILE_ERROR, DATABASE_ERROR
> }
> ```

<!-- These restrictions don't apply to indirect subclasses. If a direct subclass of a sealed class is not marked as sealed,
it can be extended in any way that its modifiers allow: -->
Эти ограничения не применяются к непрямым наследникам. Если прямой наследник изолированного класса не помечен как
`sealed`, его можно расширять любым способом, который допускают его модификаторы.

```kotlin
// Изолированный интерфейс 'Error' имеет реализации только в том же пакете и модуле
sealed interface Error

// Изолированный класс 'IOError' расширяет 'Error', и его можно расширять только в том же пакете
sealed class IOError(): Error

// Открытый класс 'CustomError' расширяет 'Error', и его можно расширять везде, где он виден
open class CustomError(): Error
```

<a name="inheritance-in-multiplatform-projects"></a>

<!-- ### Inheritance in multiplatform projects -->
### Наследование в мультиплатформенных проектах

<!-- There is one more inheritance restriction in [multiplatform projects](https://kotlinlang.org/docs/multiplatform/get-started.html): direct subclasses of sealed classes must
reside in the same [source set](https://kotlinlang.org/docs/multiplatform/multiplatform-discover-project.html#source-sets). It applies to sealed classes without the [expected and actual modifiers](https://kotlinlang.org/docs/multiplatform/multiplatform-expect-actual.html). -->
В [мультиплатформенных проектах](https://kotlinlang.org/docs/multiplatform/get-started.html) действует ещё одно
ограничение наследования: прямые наследники изолированных классов должны находиться в том же
[исходном наборе](https://kotlinlang.org/docs/multiplatform/multiplatform-discover-project.html#source-sets). Это
применимо к изолированным классам без [модификаторов `expect` и `actual`](https://kotlinlang.org/docs/multiplatform/multiplatform-expect-actual.html).

<!-- If a sealed class is declared as `expect` in a common source set and have `actual` implementations in platform source sets,
both `expect` and `actual` versions can have subclasses in their source sets. Moreover, if you use a hierarchical structure,
you can create subclasses in any source set between the `expect` and `actual` declarations. -->
Если изолированный класс объявлен как `expect` в общем исходном наборе и имеет реализации `actual` в платформенных
исходных наборах, и версия `expect`, и версии `actual` могут иметь наследников в своих исходных наборах. Более того,
если вы используете иерархическую структуру, вы можете создавать наследников в любом исходном наборе между объявлениями
`expect` и `actual`.

<!-- [Learn more about the hierarchical structure of multiplatform projects](https://kotlinlang.org/docs/multiplatform/multiplatform-hierarchy.html). -->
Подробнее об [иерархической структуре мультиплатформенных проектов](https://kotlinlang.org/docs/multiplatform/multiplatform-hierarchy.html).

<a name="sealed-classes-and-when-expression"></a>
<a name="use-sealed-classes-with-when-expression"></a>

<!-- ## Use sealed classes with when expression -->
## Использование изолированных классов с выражениями when

<!-- The key benefit of using sealed classes comes into play when you use them in a [`when`](control-flow.md#when-expressions-and-statements)
expression.
The `when` expression, used with a sealed class, allows the Kotlin compiler to check exhaustively that all possible cases are covered.
In such cases, you don't need to add an `else` clause: -->
Ключевое преимущество изолированных классов проявляется при использовании их в
[выражении `when`](control-flow.html#when-expressions-and-statements). Выражение `when`, используемое с изолированным
классом, позволяет компилятору Kotlin исчерпывающе проверить, что покрыты все возможные случаи. В таких случаях не
нужно добавлять ветку `else`:

```kotlin
// Изолированный класс и его наследники
sealed class Error {
    class FileReadError(val file: String): Error()
    class DatabaseError(val source: String): Error()
    object RuntimeError : Error()
}

//sampleStart
// Функция для логирования ошибок
fun log(e: Error) = when(e) {
    is Error.FileReadError -> println("Error while reading file ${e.file}")
    is Error.DatabaseError -> println("Error while reading from database ${e.source}")
    Error.RuntimeError -> println("Runtime error")
    // Ветка `else` не нужна, потому что покрыты все случаи
}
//sampleEnd

// Список всех ошибок
fun main() {
    val errors = listOf(
        Error.FileReadError("example.txt"),
        Error.DatabaseError("usersDatabase"),
        Error.RuntimeError
    )

    errors.forEach { log(it) }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.5"}

<!-- > To reduce repetition in `when` expressions, try out context-sensitive resolution (currently in preview).
> This feature allows you to omit the type name when matching sealed class members if the expected type is known.
>
> For more information, see [Preview of context-sensitive resolution](whatsnew22.md#preview-of-context-sensitive-resolution) or the related [KEEP proposal](https://github.com/Kotlin/KEEP/blob/improved-resolution-expected-type/proposals/context-sensitive-resolution.md). -->
> **Совет.** Чтобы сократить повторения в выражениях `when`, попробуйте контекстно-зависимое разрешение (сейчас
> доступно в предварительном режиме). Эта возможность позволяет опускать имя типа при сопоставлении членов
> изолированного класса, если ожидаемый тип известен.
>
> Подробнее см. в разделе [Preview of context-sensitive resolution](https://kotlinlang.org/docs/whatsnew22.html#preview-of-context-sensitive-resolution)
> или в соответствующем [KEEP-предложении](https://github.com/Kotlin/KEEP/blob/improved-resolution-expected-type/proposals/context-sensitive-resolution.md).

<!-- When using sealed classes with `when` expressions, you can also add guard conditions to include additional checks in a single branch.
For more information, see [Guard conditions in when expressions](control-flow.md#guard-conditions-in-when-expressions). -->
При использовании изолированных классов с выражениями `when` также можно добавлять guard conditions, чтобы включать
дополнительные проверки в одну ветку. Подробнее см. в разделе
[Guard conditions в выражениях when](control-flow.html#guard-conditions-in-when-expressions).

<!-- > In multiplatform projects, if you have a sealed class with a `when` expression as an
> [expected declaration](https://kotlinlang.org/docs/multiplatform/multiplatform-expect-actual.html) in your common code, you still need an `else` branch.
> This is because subclasses of `actual` platform implementations may extend sealed classes that
> aren't known in the common code. -->
> **Примечание.** В мультиплатформенных проектах, если в общем коде у вас есть выражение `when` для изолированного
> класса, объявленного как [ожидаемое объявление](https://kotlinlang.org/docs/multiplatform/multiplatform-expect-actual.html),
> ветка `else` всё равно нужна. Причина в том, что наследники платформенных реализаций `actual` могут расширять
> изолированные классы, которые неизвестны в общем коде.

<a name="use-case-scenarios"></a>

<!-- ## Use case scenarios -->
## Сценарии использования

<!-- Let's explore some practical scenarios where sealed classes and interfaces can be particularly useful. -->
Рассмотрим несколько практических сценариев, в которых изолированные классы и интерфейсы особенно полезны.

<!-- ### State management in UI applications -->
### Управление состоянием в UI-приложениях

<!-- You can use sealed classes to represent different UI states in an application.
This approach allows for structured and safe handling of UI changes.
This example demonstrates how to manage various UI states: -->
Изолированные классы можно использовать для представления разных состояний пользовательского интерфейса в приложении.
Такой подход позволяет структурированно и безопасно обрабатывать изменения UI. В этом примере показано, как управлять
разными состояниями UI:

```kotlin
sealed class UIState {
    data object Loading : UIState()
    data class Success(val data: String) : UIState()
    data class Error(val exception: Exception) : UIState()
}

fun updateUI(state: UIState) {
    when (state) {
        is UIState.Loading -> showLoadingIndicator()
        is UIState.Success -> showData(state.data)
        is UIState.Error -> showError(state.exception)
    }
}
```

<!-- ### Payment method handling -->
### Обработка способов оплаты

<!-- In practical business applications, handling various payment methods efficiently is a common requirement.
You can use sealed classes with `when` expressions to implement such business logic.
By representing different payment methods as subclasses of a sealed class, it establishes a clear and manageable
structure for processing transactions: -->
В реальных бизнес-приложениях часто требуется эффективно обрабатывать разные способы оплаты. Для реализации такой
бизнес-логики можно использовать изолированные классы с выражениями `when`. Представление разных способов оплаты как
наследников изолированного класса создаёт понятную и управляемую структуру для обработки транзакций:

```kotlin
sealed class Payment {
    data class CreditCard(val number: String, val expiryDate: String) : Payment()
    data class PayPal(val email: String) : Payment()
    data object Cash : Payment()
}

fun processPayment(payment: Payment) {
    when (payment) {
        is Payment.CreditCard -> processCreditCardPayment(payment.number, payment.expiryDate)
        is Payment.PayPal -> processPayPalPayment(payment.email)
        is Payment.Cash -> processCashPayment()
    }
}
```

<!-- `Payment` is a sealed class that represents different payment methods in an e-commerce system:
`CreditCard`, `PayPal`, and `Cash`. Each subclass can have its specific properties, like `number` and `expiryDate` for
`CreditCard`, and `email` for `PayPal`. -->
`Payment` — это изолированный класс, который представляет разные способы оплаты в системе электронной коммерции:
`CreditCard`, `PayPal` и `Cash`. У каждого наследника могут быть свои свойства, например `number` и `expiryDate` у
`CreditCard` или `email` у `PayPal`.

<!-- The `processPayment()` function demonstrates how to handle different payment methods.
This approach ensures that all possible payment types are considered, and the system remains flexible for new payment
methods to be added in the future. -->
Функция `processPayment()` показывает, как обрабатывать разные способы оплаты. Этот подход гарантирует, что учтены все
возможные типы платежей, а система остаётся достаточно гибкой, чтобы в будущем добавлять новые способы оплаты.

<!-- ### API request-response handling -->
### Обработка запросов и ответов API

<!-- You can use sealed classes and sealed interfaces to implement a user authentication system that handles API requests and responses.
The user authentication system has login and logout functionalities.
The `ApiRequest` sealed interface defines specific request types: `LoginRequest` for login, and `LogoutRequest` for logout operations.
The sealed class, `ApiResponse`, encapsulates different response scenarios: `UserSuccess` with user data, `UserNotFound`
for absent users, and `Error` for any failures. The `handleRequest` function processes these requests in a type-safe manner
using a `when` expression, while `getUserById` simulates user retrieval: -->
Изолированные классы и изолированные интерфейсы можно использовать для реализации системы аутентификации пользователей,
которая обрабатывает API-запросы и ответы. В системе аутентификации пользователей есть функции входа и выхода.
Изолированный интерфейс `ApiRequest` определяет конкретные типы запросов: `LoginRequest` для входа и `LogoutRequest`
для операций выхода. Изолированный класс `ApiResponse` инкапсулирует разные сценарии ответа: `UserSuccess` с данными
пользователя, `UserNotFound` для отсутствующих пользователей и `Error` для любых сбоев. Функция `handleRequest`
типобезопасно обрабатывает эти запросы с помощью выражения `when`, а `getUserById` имитирует получение пользователя:

```kotlin
// Импорт необходимых модулей
import io.ktor.server.application.*
import io.ktor.server.resources.*

import kotlinx.serialization.*

// Определение изолированного интерфейса для API-запросов с использованием ресурсов Ktor
@Resource("api")
sealed interface ApiRequest

@Serializable
@Resource("login")
data class LoginRequest(val username: String, val password: String) : ApiRequest


@Serializable
@Resource("logout")
object LogoutRequest : ApiRequest

// Определение изолированного класса ApiResponse с подробными типами ответов
sealed class ApiResponse {
    data class UserSuccess(val user: UserData) : ApiResponse()
    data object UserNotFound : ApiResponse()
    data class Error(val message: String) : ApiResponse()
}

// Класс данных пользователя для успешного ответа
data class UserData(val userId: String, val name: String, val email: String)

// Функция для проверки учётных данных пользователя (для демонстрации)
fun isValidUser(username: String, password: String): Boolean {
    // Некоторая логика проверки (это только заглушка)
    return username == "validUser" && password == "validPass"
}

// Функция для обработки API-запросов с подробными ответами
fun handleRequest(request: ApiRequest): ApiResponse {
    return when (request) {
        is LoginRequest -> {
            if (isValidUser(request.username, request.password)) {
                ApiResponse.UserSuccess(UserData("userId", "userName", "userEmail"))
            } else {
                ApiResponse.Error("Invalid username or password")
            }
        }
        is LogoutRequest -> {
            // В этом примере предполагаем, что операция выхода всегда успешна
            ApiResponse.UserSuccess(UserData("userId", "userName", "userEmail")) // Для демонстрации
        }
    }
}

// Функция, имитирующая вызов getUserById
fun getUserById(userId: String): ApiResponse {
    return if (userId == "validUserId") {
        ApiResponse.UserSuccess(UserData("validUserId", "John Doe", "john@example.com"))
    } else {
        ApiResponse.UserNotFound
    }
    // Обработка ошибок также привела бы к ответу Error.
}

// Главная функция для демонстрации использования
fun main() {
    val loginResponse = handleRequest(LoginRequest("user", "pass"))
    println(loginResponse)

    val logoutResponse = handleRequest(LogoutRequest)
    println(logoutResponse)

    val userResponse = getUserById("validUserId")
    println(userResponse)

    val userNotFoundResponse = getUserById("invalidId")
    println(userNotFoundResponse)
}
```
