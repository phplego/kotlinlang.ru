---
type: doc
layout: reference
category: "Syntax"
title: "Исключения"
url: https://kotlinlang.ru/docs/exceptions.html
---

<!-- При переводе статьи оригинальная версия была актуализирована по официальной документации от 23 March 2026 -->

<!-- # Exception and error handling -->
# Обработка исключений и ошибок

Исключения помогают коду работать предсказуемее, даже когда во время выполнения возникают ошибки, которые могут
нарушить работу программы. По умолчанию Kotlin считает все исключения *непроверяемыми*. Непроверяемые исключения
упрощают обработку: вы можете перехватывать исключения, но не обязаны явно обрабатывать или
[объявлять](java-to-kotlin-interop.html#checked-exceptions) их.

> Подробнее о том, как Kotlin обрабатывает исключения при взаимодействии с Java, Swift и Objective-C, см. в разделе
> [Совместимость исключений с Java, Swift и Objective-C](#exception-interoperability-with-java-swift-and-objective-c).

Работа с исключениями состоит из двух основных действий:

* **Выбрасывание исключений:** указывает, что возникла проблема.
* **Перехват исключений:** вручную обрабатывает неожиданное исключение, устраняя проблему или уведомляя разработчика
  либо пользователя приложения.

Исключения представлены подклассами класса
[`Exception`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-exception/), который является подклассом
[`Throwable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-throwable/). Подробнее об иерархии см. в разделе
[Иерархия исключений](#exception-hierarchy). Так как `Exception` является
[`open class`](inheritance.html), вы можете создавать [собственные исключения](#create-custom-exceptions) под
конкретные нужды приложения.

<a name="throw-exceptions"></a>

<!-- ## Throw exceptions -->
## Выбрасывание исключений

Вы можете вручную выбрасывать исключения с помощью ключевого слова `throw`. Выбрасывание исключения показывает, что
в коде произошла неожиданная ошибка времени выполнения. Исключения являются [объектами](classes.html#creating-instances),
а выбрасывание исключения создаёт экземпляр класса исключения.

Исключение можно выбросить без каких-либо параметров:

```kotlin
throw IllegalArgumentException()
```

Чтобы лучше понять источник проблемы, добавьте дополнительную информацию, например собственное сообщение и исходную
причину:

```kotlin
val cause = IllegalStateException("Original cause: illegal state")

// Выбрасывает IllegalArgumentException, если userInput отрицателен
// Дополнительно показывает исходную причину, представленную cause IllegalStateException
if (userInput < 0) {
    throw IllegalArgumentException("Input must be non-negative", cause)
}
```

В этом примере `IllegalArgumentException` выбрасывается, когда пользователь вводит отрицательное значение. Вы можете
создавать собственные сообщения об ошибках и сохранять исходную причину (`cause`) исключения, которая будет включена в
[трассировку стека](#stack-trace).

<a name="throw-exceptions-with-precondition-functions"></a>

<!-- ### Throw exceptions with precondition functions -->
### Выбрасывание исключений с помощью функций проверки предусловий

Kotlin предлагает дополнительные способы автоматически выбрасывать исключения с помощью функций проверки предусловий.
К таким функциям относятся:

| Функция проверки предусловия       | Назначение                                      | Выбрасываемое исключение                                                                                       |
|------------------------------------|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| [`require()`](#require-function)   | Проверяет корректность пользовательского ввода  | [`IllegalArgumentException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-argument-exception/) |
| [`check()`](#check-function)       | Проверяет корректность состояния объекта или переменной | [`IllegalStateException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-state-exception/) |
| [`error()`](#error-function)       | Указывает на недопустимое состояние или условие | [`IllegalStateException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-state-exception/)       |

Эти функции подходят для ситуаций, когда выполнение программы не может продолжаться, если не выполнены определённые
условия. Они упрощают код и позволяют эффективнее обрабатывать такие проверки.

<a name="require-function"></a>

<!-- #### require() function -->
#### Функция require()

Используйте функцию [`require()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/require.html), чтобы проверять
входные аргументы, если они важны для работы функции и функция не может продолжить выполнение с некорректными
аргументами.

Если условие в `require()` не выполнено, выбрасывается
[`IllegalArgumentException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-argument-exception/):

```kotlin
fun getIndices(count: Int): List<Int> {
    require(count >= 0) { "Count must be non-negative. You set count to $count." }
    return List(count) { it + 1 }
}

fun main() {
    // Завершается с IllegalArgumentException
    println(getIndices(-1))

    // Раскомментируйте строку ниже, чтобы увидеть рабочий пример
    // println(getIndices(3))
    // [1, 2, 3]
}
```

> Функция `require()` позволяет компилятору выполнять [smart cast](typecasts.html#smart-casts). После успешной проверки
> переменная автоматически приводится к non-null типу. Такие функции часто используют для проверок null-допустимости,
> чтобы убедиться, что переменная не равна `null`, прежде чем продолжить выполнение. Например:
>
> ```kotlin
> fun printNonNullString(str: String?) {
>     // Проверка null-допустимости
>     require(str != null)
>     // После успешной проверки гарантируется, что 'str'
>     // не равен null и автоматически smart cast к non-null String
>     println(str.length)
> }
> ```

<a name="check-function"></a>

<!-- #### check() function -->
#### Функция check()

Используйте функцию [`check()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/check.html), чтобы проверять
состояние объекта или переменной. Если проверка завершается неудачно, это указывает на логическую ошибку, которую
нужно исправить.

Если условие, указанное в функции `check()`, равно `false`, выбрасывается
[`IllegalStateException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-state-exception/):

```kotlin
fun main() {
    var someState: String? = null

    fun getStateValue(): String {

        val state = checkNotNull(someState) { "State must be set beforehand!" }
        check(state.isNotEmpty()) { "State must be non-empty!" }
        return state
    }
    // Если раскомментировать строку ниже, программа завершится с IllegalStateException
    // getStateValue()

    someState = ""

    // Если раскомментировать строку ниже, программа завершится с IllegalStateException
    // getStateValue()
    someState = "non-empty-state"

    // Выводит "non-empty-state"
    println(getStateValue())
}
```

> Функция `check()` позволяет компилятору выполнять [smart cast](typecasts.html#smart-casts). После успешной проверки
> переменная автоматически приводится к non-null типу. Такие функции часто используют для проверок null-допустимости,
> чтобы убедиться, что переменная не равна `null`, прежде чем продолжить выполнение. Например:
>
> ```kotlin
> fun printNonNullString(str: String?) {
>     // Проверка null-допустимости
>     check(str != null)
>     // После успешной проверки гарантируется, что 'str'
>     // не равен null и автоматически smart cast к non-null String
>     println(str.length)
> }
> ```

<a name="error-function"></a>

<!-- #### error() function -->
#### Функция error()

Функция [`error()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/error.html) используется для указания на
недопустимое состояние или условие в коде, которое логически не должно возникать. Она подходит для сценариев, где нужно
намеренно выбросить исключение, например когда код попадает в неожиданное состояние. Эта функция особенно полезна в
выражениях `when`, где она даёт понятный способ обработать ветки, которые логически невозможны.

В следующем примере функция `error()` используется для обработки неопределённой роли пользователя. Если роль не входит
в число заранее заданных, выбрасывается
[`IllegalStateException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-state-exception/):

```kotlin
class User(val name: String, val role: String)

fun processUserRole(user: User) {
    when (user.role) {
        "admin" -> println("${user.name} is an admin.")
        "editor" -> println("${user.name} is an editor.")
        "viewer" -> println("${user.name} is a viewer.")
        else -> error("Undefined role: ${user.role}")
    }
}

fun main() {
    // Работает как ожидается
    val user1 = User("Alice", "admin")
    processUserRole(user1)
    // Alice is an admin.

    // Выбрасывает IllegalStateException
    val user2 = User("Bob", "guest")
    processUserRole(user2)
}
```

<a name="handle-exceptions-using-try-catch-blocks"></a>

<!-- ## Handle exceptions using try-catch blocks -->
## Обработка исключений с помощью блоков try-catch

Когда исключение выбрасывается, оно прерывает обычное выполнение программы. Вы можете аккуратно обрабатывать исключения
с помощью ключевых слов `try` и `catch`, чтобы сохранить стабильность программы. Блок `try` содержит код, который может
выбросить исключение, а блок `catch` перехватывает и обрабатывает исключение, если оно возникает. Исключение
перехватывается первым блоком `catch`, который соответствует его конкретному типу или [суперклассу](inheritance.html)
исключения.

Вот как можно использовать ключевые слова `try` и `catch` вместе:

```kotlin
try {
    // Код, который может выбросить исключение
} catch (e: SomeException) {
    // Код для обработки исключения
}
```

Распространённый подход - использовать `try-catch` как выражение, чтобы оно могло вернуть значение либо из блока `try`,
либо из блока `catch`:

```kotlin
fun main() {
    val num: Int = try {

        // Если count() завершится успешно, его возвращаемое значение будет присвоено num
        count()

    } catch (e: ArithmeticException) {

        // Если count() выбросит исключение, блок catch вернёт -1,
        // и это значение будет присвоено num
        -1
    }
    println("Result: $num")
}

// Имитирует функцию, которая может выбросить ArithmeticException
fun count(): Int {

    // Измените это значение, чтобы вернуть другое значение в num
    val a = 0

    return 10 / a
}
```

Для одного блока `try` можно использовать несколько обработчиков `catch`. Вы можете добавлять столько блоков `catch`,
сколько нужно, чтобы по-разному обрабатывать разные исключения. Если у вас несколько блоков `catch`, важно располагать
их от наиболее конкретного исключения к наименее конкретному, сверху вниз в коде. Такой порядок соответствует потоку
выполнения программы.

Рассмотрим пример с [собственными исключениями](#create-custom-exceptions):

```kotlin
open class WithdrawalException(message: String) : Exception(message)
class InsufficientFundsException(message: String) : WithdrawalException(message)

fun processWithdrawal(amount: Double, availableFunds: Double) {
    if (amount > availableFunds) {
        throw InsufficientFundsException("Insufficient funds for the withdrawal.")
    }
    if (amount < 1 || amount % 1 != 0.0) {
        throw WithdrawalException("Invalid withdrawal amount.")
    }
    println("Withdrawal processed")
}

fun main() {
    val availableFunds = 500.0

    // Измените это значение, чтобы проверить разные сценарии
    val withdrawalAmount = 500.5

    try {
        processWithdrawal(withdrawalAmount.toDouble(), availableFunds)

    // Порядок блоков catch важен!
    } catch (e: InsufficientFundsException) {
        println("Caught an InsufficientFundsException: ${e.message}")
    } catch (e: WithdrawalException) {
        println("Caught a WithdrawalException: ${e.message}")
    }
}
```

Общий блок `catch`, который обрабатывает `WithdrawalException`, перехватывает все исключения этого типа, включая более
конкретные, такие как `InsufficientFundsException`, если они не были перехвачены ранее более конкретным блоком `catch`.

<a name="the-finally-block"></a>

<!-- ### The finally block -->
### Блок finally

Блок `finally` содержит код, который выполняется всегда, независимо от того, завершился ли блок `try` успешно или
выбросил исключение. С помощью блока `finally` можно выполнить очистку после выполнения блоков `try` и `catch`. Это
особенно важно при работе с ресурсами, такими как файлы или сетевые подключения, потому что `finally` гарантирует, что
они будут корректно закрыты или освобождены.

Обычно блоки `try-catch-finally` используются вместе так:

```kotlin
try {
    // Код, который может выбросить исключение
}
catch (e: YourException) {
    // Обработчик исключения
}
finally {
    // Код, который выполняется всегда
}
```

Возвращаемое значение выражения `try` определяется последним выполненным выражением либо в блоке `try`, либо в блоке
`catch`. Если исключений нет, результат берётся из блока `try`; если исключение обработано, результат берётся из блока
`catch`. Блок `finally` всегда выполняется, но не изменяет результат блока `try-catch`.

Рассмотрим пример:

```kotlin
fun divideOrNull(a: Int): Int {

    // Блок try выполняется всегда
    // Исключение здесь (деление на ноль) сразу передаёт выполнение в блок catch
    try {
        val b = 44 / a
        println("try block: Executing division: $b")
        return b
    }

    // Блок catch выполняется из-за ArithmeticException (деления на ноль, если a == 0)
    catch (e: ArithmeticException) {
        println("catch block: Encountered ArithmeticException $e")
        return -1
    }
    finally {
        println("finally block: The finally block is always executed")
    }
}

fun main() {

    // Измените это значение, чтобы получить другой результат. ArithmeticException вернёт: -1
    divideOrNull(0)
}
```

> В Kotlin идиоматичный способ управлять ресурсами, реализующими интерфейс
> [`AutoCloseable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-auto-closeable/), например файловыми потоками
> `FileInputStream` или `FileOutputStream`, - использовать функцию
> [`.use()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/use.html). Эта функция автоматически закрывает ресурс
> после завершения блока кода независимо от того, было ли выброшено исключение, и тем самым устраняет необходимость в
> блоке `finally`. Поэтому Kotlin не требует специального синтаксиса вроде
> [Java try-with-resources](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html) для
> управления ресурсами.
>
> ```kotlin
> FileWriter("test.txt").use { writer ->
>     writer.write("some text")
>     // После этого блока функция .use автоматически вызовет writer.close(),
>     // как в блоке finally
> }
> ```

Если коду нужна очистка ресурсов без обработки исключений, также можно использовать `try` с блоком `finally` без блоков
`catch`:

```kotlin
class MockResource {
    fun use() {
        println("Resource being used")
        // Имитирует использование ресурса
        // Выбрасывает ArithmeticException, если происходит деление на ноль
        val result = 100 / 0

        // Эта строка не выполняется, если выброшено исключение
        println("Result: $result")
    }

    fun close() {
        println("Resource closed")
    }
}

fun main() {
    val resource = MockResource()
//sampleStart
    try {

        // Пытается использовать ресурс
        resource.use()

    } finally {

        // Гарантирует, что ресурс будет закрыт, даже если возникнет исключение
        resource.close()
    }

    // Эта строка не выводится, если выброшено исключение
    println("End of the program")
//sampleEnd
}
```

Как видно, блок `finally` гарантирует закрытие ресурса независимо от того, возникло ли исключение.

В Kotlin можно использовать только блок `catch`, только блок `finally` или оба блока - в зависимости от конкретных
потребностей. Но блок `try` всегда должен сопровождаться хотя бы одним блоком `catch` или блоком `finally`.

<a name="create-custom-exceptions"></a>

<!-- ## Create custom exceptions -->
## Создание собственных исключений

В Kotlin можно определять собственные исключения, создавая классы, которые расширяют встроенный класс `Exception`. Это
позволяет создавать более конкретные типы ошибок под нужды приложения.

Чтобы создать собственное исключение, можно определить класс, расширяющий `Exception`:

```kotlin
class MyException: Exception("My message")
```

В этом примере есть сообщение об ошибке по умолчанию, `"My message"`, но при необходимости его можно не указывать.

> Исключения в Kotlin - это объекты с состоянием: они хранят информацию, относящуюся к контексту их создания, то есть
> [трассировку стека](#stack-trace). Не создавайте исключения с помощью
> [объявлений object](object-declarations.html#object-declarations-overview). Вместо этого создавайте новый экземпляр
> исключения каждый раз, когда оно нужно. Так состояние исключения будет точно отражать конкретный контекст.

Собственные исключения также могут быть подклассами любого уже существующего подкласса исключения, например подкласса
`ArithmeticException`:

```kotlin
class NumberTooLargeException: ArithmeticException("My message")
```

> Если вы хотите создавать подклассы собственных исключений, нужно объявить родительский класс как `open`, потому что
> [классы по умолчанию final](inheritance.html) и иначе не могут иметь подклассы.
>
> Например:
>
> ```kotlin
> // Объявляет собственное исключение как open class, чтобы его можно было наследовать
> open class MyCustomException(message: String): Exception(message)
>
> // Создаёт подкласс собственного исключения
> class SpecificCustomException: MyCustomException("Specific error message")
> ```

Собственные исключения ведут себя так же, как встроенные. Их можно выбрасывать ключевым словом `throw` и обрабатывать
блоками `try-catch-finally`. Рассмотрим пример:

```kotlin
class NegativeNumberException: Exception("Parameter is less than zero.")
class NonNegativeNumberException: Exception("Parameter is a non-negative number.")

fun myFunction(number: Int) {
    if (number < 0) throw NegativeNumberException()
    else if (number >= 0) throw NonNegativeNumberException()
}

fun main() {

    // Измените значение в этой функции, чтобы получить другое исключение
    myFunction(1)
}
```

В приложениях с разными сценариями ошибок иерархия исключений помогает сделать код понятнее и точнее. Для этого можно
использовать [абстрактный класс](classes.html#abstract-classes) или
[sealed class](sealed-classes.html#constructors) как базу для общих возможностей исключений и создавать конкретные
подклассы для детальных типов исключений. Кроме того, собственные исключения с параметрами, имеющими значения по
умолчанию, дают гибкость: их можно инициализировать разными сообщениями, что позволяет точнее обрабатывать ошибки.

Рассмотрим пример, где `sealed class` `AccountException` используется как основа иерархии исключений, а класс
`APIKeyExpiredException` является подклассом и показывает использование параметров со значениями по умолчанию для более
подробного описания исключения:

```kotlin
//sampleStart
// Создаёт sealed class как основу иерархии исключений для ошибок, связанных с аккаунтом
sealed class AccountException(message: String, cause: Throwable? = null) :
    Exception(message, cause)

// Создаёт подкласс AccountException
class InvalidAccountCredentialsException : AccountException("Invalid account credentials detected")

// Создаёт подкласс AccountException, который позволяет добавлять собственные сообщения и причины
class APIKeyExpiredException(message: String = "API key expired", cause: Throwable? = null) :
    AccountException(message, cause)

// Измените значения функций-заглушек, чтобы получить разные результаты
fun areCredentialsValid(): Boolean = true
fun isAPIKeyExpired(): Boolean = true
//sampleEnd

// Проверяет учётные данные аккаунта и API-ключ
fun validateAccount() {
    if (!areCredentialsValid()) throw InvalidAccountCredentialsException()
    if (isAPIKeyExpired()) {
        // Пример выбрасывания APIKeyExpiredException с конкретной причиной
        val cause = RuntimeException("API key validation failed due to network error")
        throw APIKeyExpiredException(cause = cause)
    }
}

fun main() {
    try {
        validateAccount()
        println("Operation successful: Account credentials and API key are valid.")
    } catch (e: AccountException) {
        println("Error: ${e.message}")
        e.cause?.let { println("Caused by: ${it.message}") }
    }
}
```

<a name="the-nothing-type"></a>

<!-- ## The Nothing type -->
## Тип Nothing

В Kotlin у каждого выражения есть тип. Тип выражения `throw IllegalArgumentException()` -
[`Nothing`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-nothing.html), встроенный тип, который является
подтипом всех остальных типов. Он также известен как
[нижний тип](https://en.wikipedia.org/wiki/Bottom_type). Это означает, что `Nothing` можно использовать как тип
возвращаемого значения или обобщённый тип там, где ожидается любой другой тип, и это не вызовет ошибок типов.

`Nothing` - специальный тип Kotlin, который представляет функции или выражения, никогда не завершающиеся успешно: они
либо всегда выбрасывают исключение, либо входят в бесконечный путь выполнения, например бесконечный цикл. Вы можете
использовать `Nothing`, чтобы пометить функции, которые ещё не реализованы или специально всегда выбрасывают
исключение, явно показывая свои намерения компилятору и читателям кода. Если компилятор выведет тип `Nothing` в
сигнатуре функции, он выдаст предупреждение. Явное указание `Nothing` как возвращаемого типа может убрать это
предупреждение.

Этот код Kotlin демонстрирует использование типа `Nothing`: компилятор отмечает код после вызова функции как
недостижимый:

```kotlin
class Person(val name: String?)

fun fail(message: String): Nothing {
    throw IllegalArgumentException(message)
    // Эта функция никогда не вернётся успешно.
    // Она всегда выбрасывает исключение.
}

fun main() {
    // Создаёт экземпляр Person с null в качестве 'name'
    val person = Person(name = null)

    val s: String = person.name ?: fail("Name required")

    // В этой точке гарантируется, что 's' инициализирована
    println(s)
}
```

Функция Kotlin [`TODO()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-t-o-d-o.html), которая также использует
тип `Nothing`, служит заглушкой и выделяет участки кода, которые нужно реализовать позже:

```kotlin
fun notImplementedFunction(): Int {
    TODO("This function is not yet implemented")
}

fun main() {
    val result = notImplementedFunction()
    // Выбрасывает NotImplementedError
    println(result)
}
```

Как видно, функция `TODO()` всегда выбрасывает исключение
[`NotImplementedError`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-not-implemented-error/).

<a name="exception-classes"></a>

<!-- ## Exception classes -->
## Классы исключений

Рассмотрим несколько распространённых типов исключений в Kotlin. Все они являются подклассами класса
[`RuntimeException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-runtime-exception/):

* [`ArithmeticException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-arithmetic-exception/): возникает, когда
  арифметическую операцию невозможно выполнить, например при делении на ноль.

    ```kotlin
    val example = 2 / 0 // выбрасывает ArithmeticException
    ```

* [`IndexOutOfBoundsException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-index-out-of-bounds-exception/):
  выбрасывается, когда индекс какого-либо элемента, например массива или строки, выходит за допустимые границы.

    ```kotlin
    val myList = mutableListOf(1, 2, 3)
    myList.removeAt(3)  // выбрасывает IndexOutOfBoundsException
    ```

    > Чтобы избежать этого исключения, используйте более безопасную альтернативу, например функцию
    > [`getOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-null.html):
    >
    > ```kotlin
    > val myList = listOf(1, 2, 3)
    > // Возвращает null вместо IndexOutOfBoundsException
    > val element = myList.getOrNull(3)
    > println("Element at index 3: $element")
    > ```

* [`NoSuchElementException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-no-such-element-exception/):
  выбрасывается при обращении к элементу, которого нет в конкретной коллекции. Это происходит при использовании
  методов, которые ожидают определённый элемент, например
  [`first()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html) или
  [`last()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html).

    ```kotlin
    val emptyList = listOf<Int>()
    val firstElement = emptyList.first()  // выбрасывает NoSuchElementException
    ```

    > Чтобы избежать этого исключения, используйте более безопасную альтернативу, например функцию
    > [`firstOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-or-null.html):
    >
    > ```kotlin
    > val emptyList = listOf<Int>()
    > // Возвращает null вместо NoSuchElementException
    > val firstElement = emptyList.firstOrNull()
    > println("First element in empty list: $firstElement")
    > ```

* [`NumberFormatException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-number-format-exception/): возникает
  при попытке преобразовать строку к числовому типу, если строка имеет неподходящий формат.

    ```kotlin
    val string = "This is not a number"
    val number = string.toInt() // выбрасывает NumberFormatException
    ```

    > Чтобы избежать этого исключения, используйте более безопасную альтернативу, например функцию
    > [`toIntOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/to-int-or-null.html):
    >
    > ```kotlin
    > val nonNumericString = "not a number"
    > // Возвращает null вместо NumberFormatException
    > val number = nonNumericString.toIntOrNull()
    > println("Converted number: $number")
    > ```

* [`NullPointerException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-null-pointer-exception/): выбрасывается,
  когда приложение пытается использовать ссылку на объект со значением `null`. Хотя возможности null безопасности Kotlin
  значительно снижают риск `NullPointerException`, оно всё же может возникнуть при намеренном использовании оператора
  `!!` или при взаимодействии с Java, где нет null безопасности Kotlin.

    ```kotlin
    val text: String? = null
    println(text!!.length)  // выбрасывает NullPointerException
    ```

Хотя все исключения в Kotlin непроверяемые и вам не нужно явно их перехватывать, вы всё равно можете делать это при
необходимости.

<a name="exception-hierarchy"></a>

<!-- ### Exception hierarchy -->
### Иерархия исключений

Корень иерархии исключений Kotlin - класс
[`Throwable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-throwable/). У него есть два прямых подкласса:
[`Error`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-error/) и
[`Exception`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-exception/):

* Подкласс `Error` представляет серьёзные фундаментальные проблемы, от которых приложение, возможно, не сможет
  восстановиться самостоятельно. Обычно такие проблемы не пытаются обрабатывать, например
  [`OutOfMemoryError`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-out-of-memory-error/) или
  `StackOverflowError`.

* Подкласс `Exception` используется для условий, которые может понадобиться обработать. Подтипы типа `Exception`,
  такие как [`RuntimeException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-runtime-exception/) и
  `IOException` (исключение ввода-вывода), работают с исключительными событиями в приложениях.

![Иерархия исключений - класс Throwable](https://kotlinlang.org/docs/images/throwable.svg)

`RuntimeException` обычно вызывается недостаточными проверками в программном коде, и его можно предотвратить
программно. Kotlin помогает предотвращать распространённые `RuntimeExceptions`, такие как `NullPointerException`, и
выдаёт предупреждения во время компиляции о потенциальных ошибках времени выполнения, например о делении на ноль. На
следующей иллюстрации показана иерархия подтипов, происходящих от `RuntimeException`:

![Иерархия RuntimeException](https://kotlinlang.org/docs/images/runtime-exception.svg)

<a name="stack-trace"></a>

<!-- ## Stack trace -->
## Трассировка стека

*Трассировка стека* - это отчёт, созданный средой выполнения и используемый для отладки. Он показывает
последовательность вызовов функций, которая привела к определённой точке программы, особенно к месту, где возникла
ошибка или исключение.

Рассмотрим пример, где трассировка стека автоматически выводится из-за исключения в JVM-среде:

```kotlin
fun main() {
//sampleStart
    throw ArithmeticException("This is an arithmetic exception!")
//sampleEnd
}
```

Запуск этого кода в JVM-среде приводит к следующему выводу:

```text
Exception in thread "main" java.lang.ArithmeticException: This is an arithmetic exception!
    at MainKt.main(Main.kt:3)
    at MainKt.main(Main.kt)
```

Первая строка - это описание исключения, которое включает:

* Тип исключения: `java.lang.ArithmeticException`
* Поток: `main`
* Сообщение исключения: `"This is an arithmetic exception!"`

Каждая последующая строка, которая после описания исключения начинается с `at`, является трассировкой стека. Отдельная
строка называется *элементом трассировки стека* или *кадром стека*:

* `at MainKt.main (Main.kt:3)`: показывает имя метода (`MainKt.main`) и исходный файл с номером строки, где был вызван
  метод (`Main.kt:3`).
* `at MainKt.main (Main.kt)`: показывает, что исключение возникает в функции `main()` файла `Main.kt`.

<a name="checked-exceptions"></a>
<a name="java-interoperability"></a>
<a name="exception-interoperability-with-java-swift-and-objective-c"></a>

<!-- ## Exception interoperability with Java, Swift, and Objective-C -->
## Совместимость исключений с Java, Swift и Objective-C

Так как Kotlin считает все исключения непроверяемыми, это может привести к сложностям, когда такие исключения
вызываются из языков, которые различают проверяемые и непроверяемые исключения. Чтобы устранить это расхождение в
обработке исключений между Kotlin и языками вроде Java, Swift и Objective-C, можно использовать аннотацию
[`@Throws`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-throws/). Эта аннотация предупреждает вызывающий код о
возможных исключениях. Подробнее см. в разделах [Вызов Kotlin из Java](java-to-kotlin-interop.html#checked-exceptions)
и [Совместимость со Swift/Objective-C](https://kotlinlang.org/docs/native-objc-interop.html#errors-and-exceptions).
